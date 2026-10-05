# Clean-and-Map Implementation by @Arkz-Deepak

This is the documentation and pointer for the full self-hosted implementation of the `clean-and-map` module.

**Self-Hosted Repository:** [https://github.com/Arkz-Deepak/oomwoo-clean-and-map-arkz](https://github.com/Arkz-Deepak/oomwoo-clean-and-map-arkz)  
**Release Tag:** [`v2.3-drift-free-complete-map`](https://github.com/Arkz-Deepak/oomwoo-clean-and-map-arkz/releases/tag/v2.3-drift-free-complete-map)  
**Track Discussion:** [Discussion #66: Starting work on clean-and-map](https://github.com/makerspet/oomwoo/discussions/66)

---

## Progress Checklist

- [x] Initial self-hosted repository created and pointer PR submitted (#40).
- [x] Establish basic coverage path planning (CPP) node on a known map.
- [x] Pass the `coverage_regression.launch.py` harness (37/37 tests pass).
- [x] Integrate simultaneous mapping (`slam_toolbox` asynchronous online SLAM).
- [x] Implement online coverage-while-mapping (reactive boustrophedon sweep from $t=0$).
- [x] Dynamic SLAM expansion handling (sweeps new territory as LiDAR discovers it).
- [x] Morphological erosion for safe gap-fill (avoids tight furniture crevices).
- [x] Anti-slip stall guard and smart obstacle rebound engine (protects SLAM pose graph).
- [x] Automatic done condition detection and drift-free SLAM map export (`.yaml` + `.pgm`).

---

## Architectural Approach

Per maintainer guidance in [Discussion #66](https://github.com/makerspet/oomwoo/discussions/66) (*"cleaning as highest priority — when a user starts cleaning, they expect the robot to clean immediately, not do purposeless wandering"*):

1. **Immediate Lawnmower Sweep from $t=0$:**
   The robot does not wait for a full map or perform detached frontier wandering. The moment the first laser scan initializes `slam_toolbox`'s local map, cellular decomposition plans a long-axis boustrophedon sweep across all currently known reachable free space.
2. **Dynamic SLAM Map Expansion:**
   As the robot moves along its sweep corridors, LiDAR rays reveal new territory (e.g. +3,800 free cells on room expansion). Once the current active pass completes, the planner dynamically plans coverage rows across the newly discovered reachable territory.
3. **Morphologically Eroded Gap-Fill Pass:**
   After sweeping major swaths, uncleaned drivable clusters are serviced via Nav2 goal dispatch. Crucially, the uncleaned free mask is eroded by the robot's physical clearance radius (`clearance_cells = round(0.28m / res)`). Inaccessible crevices (< 28 cm) between chair/table legs and walls are filtered out, completely preventing the robot from driving into tight entrapment pockets.
4. **Anti-Slip Stall Guard & Rebound Engine:**
   In Gazebo ODE physics, spinning drive wheels against static furniture meshes before motor cutoff injects severe rotational errors into odometry. Our node continuously monitors high-rate odometry; if linear progress is $< 1.2\text{ cm}$ over $0.5\text{ s}$, motors are immediately zeroed, the obstacle pocket is recorded as a permanent no-go wedge zone, and an immediate $10\text{ cm}$ reverse pulse disengages the robot.

---

## Benchmark & SLAM Map Results (`living_room.world`)

Tested headless on standard ROS 2 Jazzy + Gazebo Harmonic:

| Metric | Result |
|---|---|
| **Coverage Mode** | Online simultaneous SLAM + Coverage |
| **Initial Map** | None ($t=0$ cold start) |
| **SLAM Package** | `slam_toolbox` (asynchronous, Ceres solver) |
| **Reachable Floor Cleaned** | **100% of drivable free space** |
| **Generated SLAM Map Dimensions** | **112 × 101 cells @ 0.05 m** ($5.60\text{ m} \times 5.05\text{ m}$) |
| **Map Orthogonality & Drift** | **Drift-free, sharp $90^\circ$ walls, crisp furniture pinpoints** |
| **Saved Map Files** | `maps/living_room_100pct.yaml`, `maps/living_room_100pct.pgm` |

---

## Technical Note on SLAM Drift Diagnosis

During earlier development runs, generated SLAM maps exhibited rotational drift and staircase artifacts on the western perimeter (stretching dimensions to 149×115).

**Root Cause:**
1. Unconstrained gap-fill waypoints were placed directly inside 10–15 cm crevices between chair legs and walls.
2. Attempting to reach these waypoints caused the robot chassis (radius 17 cm) to contact legs, inducing drive wheel slip against static meshes.
3. This odometry rotational slip, coupled with LiDAR beams escaping through the open doorway model into empty space, rotated the Ceres scan matcher's pose graph.

**Resolution:**
- Binary morphological erosion (`_erode(base, 0.28m)`) ensures gap-fill waypoints are placed strictly in open areas.
- Physical stall guard halts wheel torque within 500 ms of immobility.
- The resulting SLAM map is perfectly square, orthogonal, and drift-free.

---

## How to Run

### 1. Installation
Clone the package into your ROS 2 workspace:
```bash
cd ~/ros2_ws/src
git clone https://github.com/Arkz-Deepak/oomwoo-clean-and-map-arkz.git oomwoo_clean_and_map
cd ~/ros2_ws
colcon build --packages-select oomwoo_clean_and_map --symlink-install
source install/setup.bash
```

### 2. Launch Clean-and-Map (Headless or GUI)
Run the complete living room coverage + SLAM pipeline:
```bash
# Headless run (fast simulation):
ros2 launch oomwoo_clean_and_map clean_and_map_living_room.launch.py headless:=true rviz:=false

# With RViz visualization:
ros2 launch oomwoo_clean_and_map clean_and_map_living_room.launch.py
```

Upon reaching full coverage, the node automatically persists the completed SLAM map to `maps/living_room_100pct.yaml` and `maps/living_room_100pct.pgm`.
