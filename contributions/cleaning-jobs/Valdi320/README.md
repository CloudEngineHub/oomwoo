# Valdi320 — cleaning-jobs: experimental long-running ROS2/Nav2 node

**Source:** [Valdi320/oomwoo-cleaning-jobs](https://github.com/Valdi320/oomwoo-cleaning-jobs)
**Verified revision:** [f3f3028](https://github.com/Valdi320/oomwoo-cleaning-jobs/commit/f3f3028547c44c748d6bdce8e9c8fb6568cace36)
**Phase-3 design and reproduction:** [EXPERIMENTAL_ROS_NODE.md](https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/EXPERIMENTAL_ROS_NODE.md)
**Live Gazebo evidence:** [ros_persistent_gazebo_20261027.json](https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/evidence/ros_persistent_gazebo_20261027.json)

Independent module proposal under the [cleaning-jobs RFC](../README.md).
This is **not a finished cleaning-jobs RFC**, a canonical OOMWOO API, or a
physical robot safety system.

### Delivered

- Phase 0: whole-map/manual-room/spot target selection and durable
  acknowledged-only coverage checkpoints, user/service pauses, restart
  revalidation and no-go masks.
- Phase 1: one-active-goal Nav2 cancellation barrier; asynchronous
  cancel-before-accept, stale-callback rejection, fail-closed timeouts.
- Phase 2: genuine ROS2 NavigateToPose ActionClient; real OOMWOO Nav2
  cancellation and robot stopping in Gazebo.
- **Phase 3: persistent experimental ROS2 node**, with isolated
  /valdi/cleaning_jobs private JSON command/status topics (start spot,
  pause, resume, cancel, status), real /map and map-to-base_link TF,
  live Nav2 costmap footprint admission, battery subscription and
  BT Navigator GetState lifecycle-ACTIVE gate.

### Reproducible evidence

- **104 pure-Python tests PASS** plus real ROS2 middleware tests.
- Live official OOMWOO Gazebo/Nav2 test_room with the long-running node:
  **3 accepted Nav2 goals**, manual pause/terminal cancel,
  low-battery automated terminal cancel, simulated resource restoration
  followed by resume, and final cancel.
- Battery feed in the Gazebo regression is deliberately **synthetic**:
  the official test-room world does not publish valid BatteryState.
  The node itself refuses movement without fresh battery telemetry.
- Robot odometry: ~0.0675 m before user pause, **0.000 m maximum
  displacement** during the measured post-stop simulation window.
  **0 unverified cells** claimed clean, and no extra action dispatched.
- A lifecycle-negative ROS2 regression confirms that absence of Nav2
  lifecycle-ACTIVE prevents starting a job.
- Detailed launch, tests, validation limits and exact container image ID
  are included in the source repository.

### Remaining integration / maintainer questions

- The JSON topics are explicitly **experimental and private**, NOT a
  proposed canonical OOMWOO action/service API.
- The experimental ROS2 node supports **spot navigation** only;
  full multi-room sweep, zone editing, docking, recharge, and measured
  cleaned-area feedback still require integration.
- No real hardware / MCU safety test. Cancel acknowledgment alone is
  not a physical emergency-stop guarantee; hard bumper/cliff/current
  safety remains MCU-owned.
- Crash-time reconciliation of any unknown outstanding Nav2 goal is
  still needed before safe persistent reboot/resume; restart fails closed.
- Please advise the official job API and which clean-and-map producer
  should provide trusted measured cleaning feedback.

Apache-2.0. Developed with ChatGPT assistance and Linux/official OOMWOO
ROS2/Gazebo tests. No other contributor path or shared architecture file
is modified by this upstream pointer PR.
