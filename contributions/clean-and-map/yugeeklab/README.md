# Clean-and-map by yugeeklab

Pointer to a self-hosted implementation of the `clean-and-map` RFC: **coverage
while mapping**. The robot starts with no map, sweeps the floor slam_toolbox has
drawn so far, replans as more of the room appears, decides it is finished and saves
the map.

| Repo | What |
|---|---|
| [oomwoo-clean-and-map](https://github.com/yugeeklab/oomwoo-clean-and-map) | ROS 2 Jazzy package: the coverage-while-mapping behaviour, a map-completeness meter, a SLAM-mode launch, a pass/fail session scorer, an offline planner bench, a studio world with LiDAR-invisible obstacles, and 115 tests |

Coordination thread: [discussion #66](https://github.com/makerspet/oomwoo/discussions/66).

> Every simulated number here was measured on an **arm64 rebuild** of
> `makerspet/oomwoo:jazzy-dev`, because the published image is amd64 only and this
> work was done on Apple Silicon. The upstream baseline reproduces on it: `test_room`
> gives 96.4 % against 97.0 %. The tests and the planner bench pass on x86-64 in the
> repo's GitHub Actions job. Its graded simulation session has not been run there yet.

## How this relates to the existing work

- [@Arkz-Deepak](../Arkz-Deepak)'s track extends `oomwoo_coverage`'s
  `coverage_planner`. This one is a separate node. The difference that matters is the belief of what has been
  cleaned: `coverage_planner` is told it from outside, while this node keeps its own
  and publishes it, so it can be checked against the grader.
- It reuses `oomwoo_sim_support` for ground truth, `coverage_meter` and the
  regression runner, from
  [oomwoo-ros2-tools](https://github.com/makerspet/oomwoo-ros2-tools), and composes
  upstream's own launches unmodified.
- Interfaces follow [SOFTWARE_INTERFACES.md](../../../docs/SOFTWARE_INTERFACES.md).

## Against the acceptance criteria

| Criterion | State |
|---|---|
| Full coverage of the reachable floor, from multiple initial poses | **yes**: five start poses on `living_room`, all five cross 0.90 |
| A complete map of the reachable area | **yes**: map completeness 0.985 to 0.994 across the five |
| A clear done condition, and the map saved | **yes**: defined in the repo README, and the scorer checks the map reached disk |
| Robust to a dynamic obstacle in the path | **yes**: a box crossed the robot's floor four times in one session, and the scorer's four checks all passed |
| LiDAR-invisible obstacles: bumper, mark, replan, never stuck | **partly**, see *Known limits* |
| Left / right / front bumper | **yes**: `oomwoo_one`'s two contact sensors are the two halves of the front bumper, and both are handled |
| Headless regression of coverage *and* map completeness | **yes**: `map_completeness_meter`, plus `regression.sh` and `score_run.py`, which give one session a pass/fail exit code |
| Additional multi-room / different floorplan world | **yes**: a new `studio.sdf` of 19.4 m², and `oomwoo_gazebo`'s `multi_room.sdf` of 52.0 m² |
| Documented, reproducible by someone else | the repo README and `docs/` |

## Results

Headless, no map at start, scored by `coverage_meter` at `cleaning_radius` 0.20,
which is what `coverage_regression.launch.py` uses.

**Five start poses on `living_room`:**

| start | crossing 0.90 at | efficiency there | final coverage | map |
|---|---|---|---|---|
| 0.32, 1.59 | 58.5 m | 0.582 | 0.951 | 0.987 |
| -1.30, 1.64 | 63.4 m | 0.537 | 0.967 | 0.990 |
| 1.85, 1.14 | 53.1 m | 0.641 | 0.934 | 0.994 |
| -1.85, -0.31 | 64.0 m | 0.532 | 0.928 | 0.985 |
| 1.80, -0.46 | **46.2 m** | **0.737** | 0.928 | 0.990 |

On the same world and harness, `deploy/run_coverage_livingroom.sh` with the stock
`coverage_planner` reaches **0.8772 and never crosses 0.90**.

**Three worlds, same code and parameters:**

| world | reachable floor | crossing 0.90 at | efficiency there |
|---|---|---|---|
| `living_room` | 13.6 m² | 46.2 to 64.0 m | 0.532 to 0.737 |
| `studio` | 19.4 m² | 89.1 m | 0.545 |
| `multi_room` | 52.0 m² | 143.5 m | **0.906** |

### Why `living_room` stays below the runner's 0.80 efficiency

Efficiency is not an acceptance criterion, but it is what
`coverage_regression_runner.py` defaults to, so here is the arithmetic. The sweeping
itself is 84 to 96 % of the ideal path in every world. What differs is the turning
and travelling between pieces of passes, and furniture cuts `living_room`'s passes
into pieces of about 2.5 m against `multi_room`'s 6 m.

The passes are also 0.35 m apart rather than the full 0.40 m swath. Localisation
error on these runs is 11 mm mean and 26 mm at p90, and passes laid exactly one swath
apart open strips of missed floor between them. `coverage_regression.launch.py` sets
`row_overlap` 0.05, which on a 0.05 m grid rounds to an 8-cell step. That is a whole
swath, with no real overlap. **Upstream's planner takes the wide spacing and falls
short on coverage. This node takes the narrow one, reaches coverage, and pays for it
in efficiency.** The working record is
[docs/PATH_EFFICIENCY.md](https://github.com/yugeeklab/oomwoo-clean-and-map/blob/main/docs/PATH_EFFICIENCY.md).

## Known limits

- **Repeated contacts with LiDAR-invisible obstacles.** On `studio` a session escapes
  63 to 112 times, most of them at the two 7 cm boxes. The SLAM map never shows them,
  so a pass that grazes one is planned again after every escape. Keeping a
  twice-bumped spot as an obstacle cut escapes by about a third, but it handed the
  hops it blocked to Nav2, which stalled on short tight ones, and one session in six
  ended at 0.83. Adding a Nav2 KeepoutFilter stalled one session in two. Both were
  measured and neither was merged. The numbers are in the repo's
  [run record](https://github.com/yugeeklab/oomwoo-clean-and-map/blob/main/docs/results/arm64-mac/clean_and_map_RUNS.md).
  Upstream's `oomwoo_clean/bump_map_node` already builds a tactile layer, and is the
  natural source if this comes back.
- Simulation only, with no real-robot runs.

## Interfaces

| Direction | Name | Type | Note |
|---|---|---|---|
| sub | `/map`, `/tf` | standard | from slam_toolbox |
| sub | `bumper_left/contact`, `bumper_right/contact` | `ros_gz_interfaces/msg/Contacts` | |
| action | `/navigate_to_pose` | Nav2 | long or blocked hops. Short clear ones are driven on `/cmd_vel` |
| pub | `/cmd_vel` | `geometry_msgs/Twist` | pass waypoints and the bump escape. Arbitration with Nav2 is in the repo's `docs/CMD_VEL_ARBITRATION.md` |
| pub | `~/status` | `std_msgs/String`, JSON | `state`, `reason_code`, `message`, `recoverable`, `source` |
| pub | `~/cleaning_active` | `std_msgs/Bool` | latched. `coverage_meter` starts and stops its accounting on it |
| pub | `~/plan`, `~/covered_belief` | `nav_msgs/Path`, `nav_msgs/OccupancyGrid` | the current sweep, and what the node believes it has cleaned |
| pub | `/map_completeness_meter/ratio` | `std_msgs/Float32` | sim only, based on ground truth |

## Running it

In the `makerspet/oomwoo:jazzy-dev` container:

```bash
cd /ros_ws/src && git clone https://github.com/yugeeklab/oomwoo-clean-and-map
cd /ros_ws && colcon build --symlink-install --packages-select oomwoo_clean_and_map
source install/setup.bash
ros2 launch oomwoo_clean_and_map clean_and_map.launch.py
```

`deploy/regression.sh` runs one session and judges it, `run_poses.sh` runs
the five start poses, and `run_ab.sh` runs two configurations alternately, which the
run-to-run spread demands. `deploy/Dockerfile.arm64` rebuilds the image for Apple
Silicon.
