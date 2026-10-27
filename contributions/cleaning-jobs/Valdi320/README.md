# Valdi320 — cleaning-jobs: measured coverage, real keepout and crash recovery

**Independent Apache-2.0 source:** https://github.com/Valdi320/oomwoo-cleaning-jobs
**Validated commit:** https://github.com/Valdi320/oomwoo-cleaning-jobs/commit/5f035caf8b127b3f8ab2f47056f5de93597de4e7
**Crash-recovery evidence:** https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/DURABLE_NAV2_CRASH_RECOVERY.md
**Two-node Gazebo process-crash run:** https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/evidence/persistent_node_crash_recovery_20261027.json
**Real Nav2 KeepoutFilter run:** https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/REAL_NAV2_KEEPOUT_GAZEBO.md

### Completed/tested contributions to date

- Hardware-independent durable cleaning-job checkpoints, manual
  rooms/whole-map/spot target scopes and safe recovery state transitions.
- Experimental private ROS2 controller with real NavigateToPose action
  client, cancel-before-accept race handling, terminal-result gating,
  fake/stale cleaning-evidence rejection, lifecycle, footprint and
  costmap admission.
- Official OOMWOO simulated ground-truth coverage meter integration:
  measured 9/9 small spot cells across 3/3 independent cold Gazebo
  tests, two real Nav2 goals per run and no false cleaning credit.
- Deterministic offline room segmentation with human labels,
  persisted room scopes, no-go polygons and virtual-wall mask files.
- An isolated Nav2 Jazzy overlay with REAL KeepoutFilter in global
  and local costmaps; 220 forbidden cells marked 100; real 108-pose
  route and 2 OOMWOO Gazebo drives avoided that static rectangle.
  A private job-level gate rejects forbidden destinations up-front.
- Fail-closed behavior when FilterInfoServer disappears while a
  cleaning job is paused: no unsafe resumption or extra goal.
- **NEW: durable preallocated ROS2 action UUID journal**, fsynced
  BEFORE send_goal_async; a restarted controller cannot lose the
  identity of an accepted Nav2 goal and blindly issue a new one.
- **NEW: actual OOMWOO/Gazebo process crash and restart validation:**
  first long-running ROS2 cleaning node was killed while Nav2 moved,
  replacement node loaded paused checkpoint, rejected premature
  resume, canceled the exact old UUID, required terminal canceled
  and >1.2s fresh quiet odometry, then explicitly resumed a second
  real Nav2 goal with a NEW UUID and terminal canceled it.
- **233/233** pure Python regression cases + ROS2 real-middleware
  tests pass, including Python optimized and Python 3.12.

### Still NOT finished

The private simulation node still primarily covers small spot work.
No maintainer-approved public job action/status interface, standard
upstream KeepoutFilter bringup, complete room/multi-room sweep
ownership, dock-cycle coordination or physical robot safety tests.
Unknown/never-received Nav2 UUID states and multi-client shared
server arbitration are deliberately fail-closed. Battery telemetry
in the simulator harness is synthetic; ground-truth coverage does
NOT demonstrate vacuum dirt removal.

Maintainers: please advise the definitive cleaning-jobs API, exclusive
Nav2 action owner, canonical KeepoutFilter configuration and
trusted coverage-estimator integration. This PR changes only
the contributor-owned pointer, NOT upstream runtime or shared contracts.
