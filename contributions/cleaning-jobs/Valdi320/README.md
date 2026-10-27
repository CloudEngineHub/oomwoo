# Valdi320 — cleaning-jobs: measured coverage + completed Gazebo spot

**Independent source:** https://github.com/Valdi320/oomwoo-cleaning-jobs
**Validated commit:** https://github.com/Valdi320/oomwoo-cleaning-jobs/commit/e4d53fde549a794342a2a12fec84e171bb55c412
**Reproducible Gazebo report:** https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/SMALL_SPOT_MEASURED_COMPLETION.md
**Machine evidence (three runs):** https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/evidence/ros_verified_small_spot_repeats_20261027.json

Independent Apache-2.0 candidate for the
[cleaning-jobs RFC](../README.md). Not a completed RFC or a
production-ready physical vacuum/motor safety controller.

### Delivered and tested

- Durable scope/recovery checkpoints, pause/resume/cancel and conservative
  map/keepout-aware candidate routes (headless library).
- Real ROS2 Jazzy NavigateToPose ActionClient + persistent **private**
  experimental control node with Nav2 lifecycle and costmap safety gates.
- Original **OOMWOO ground-truth Gazebo coverage meter**
  (/coverage_meter/covered_grid plus /coverage_meter/sim_unstable)
  connected to the checkpoint, not inferred from Nav2 arrival.
- Bounded 3-second coverage confirmation tail after Nav2 terminal success:
  accepts only independently measured new cells on matching source, map,
  job and completed-goal token. Pause, cancel, new goal and expiry disarm.
- Costmap-admitted routes through/beyond missed cells, avoiding repeated
  stops just short of the cleaning area because of Nav2 goal tolerance.
- Bounded retries with explicit COVERAGE_NO_PROGRESS pause, not infinite
  motion or unverified cleaning credits.

### Evidence

- **148/148** pure-Python tests, including fault injection and replay.
- Full real ROS2 middleware action/status tests, including a synthetic
  meter sample published AFTER a successful terminal Nav2 result.
- **3/3 independent cold-started official OOMWOO Gazebo runs**: exactly
  9/9 measured centre cells in a small test-room spot, two Nav2 goals
  and terminal canceled result in every trial.
- Another real-Gazebo run verified measured partial progress preserved
  through synthetic battery-low pause and resume; not full spot cleanup.
- The official Gazebo world does not publish real BatteryState; battery
  tests use explicitly **synthetic** ROS2 telemetry. Coverage comes from
  the original OOMWOO meter in the real Gazebo runs.

### Not yet complete

The long-running private ROS2 test node currently handles one small spot.
The production/public job action/service API, automatic room segmentation,
editable/persisted Nav2 KeepoutFilter zones, upstream boustrophedon
whole-room sweeping, dock-cycle coordination, safe crash-time outstanding
goal reconciliation and full floor/multi-room regressions remain open.
No physical MCU/motor safety validated.

Maintainer review: please advise the normative ROS2 job contract and
which upstream planner owns Nav2 goals; we should avoid two competing
autonomous executors. A real robot needs its own actuator-state-gated
coverage estimator, not Gazebo ground truth.

Developed using ChatGPT assistance, reproducibly tested in the official
OOMWOO ROS2 Jazzy/Gazebo image. Upstream PR changes only this pointer.
