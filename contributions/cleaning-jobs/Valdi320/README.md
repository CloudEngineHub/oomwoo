# Valdi320 — cleaning-jobs: measured small spots, offline rooms and virtual walls

**Independent Apache-2.0 source:** https://github.com/Valdi320/oomwoo-cleaning-jobs
**Verified version:** https://github.com/Valdi320/oomwoo-cleaning-jobs/commit/01fbc8078dfb57dd52c9ef71fbe501d8b53a7bcb
**Measured completion:** https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/SMALL_SPOT_MEASURED_COMPLETION.md
**Room/label workflow:** https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/ROOM_PARTITION_AND_RESTART.md
**Offline virtual walls:** https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/VIRTUAL_WALLS_AND_ZONES.md

Incremental independent candidate for OOMWOO's cleaning-jobs RFC.
Not a completed RFC or production robot/motor safety system.

### Tested features

- Durable whole/room/spot cell-scope core, resource pauses and restart
  checkpoints; the private long-running ROS2 experimental controller
  currently supports **small spots**, not full rooms.
- Real ROS2 Nav2 ActionClient with one active action, terminal-result
  cancel barriers, lifecycle/costmap gates and fault-latched states.
- Unmodified OOMWOO Gazebo ground-truth coverage meter connected
  to the durable job. Bounded 3s confirmation tail accepts only
  independent, source/map/token-scoped measured cells after a Nav2
  success; never credits mere arrival.
- Costmap-admitted through-and-beyond missing-cell targets; bounded
  retry/no-progress behavior.
- **3/3 cold Gazebo runs** completed the same nine-cell small spot
  at 9/9 measured cells, two Nav2 goals each and confirmed terminal
  cancellation. Not a whole-room or hardware result.
- Offline deterministic room proposals, persisted human labels,
  exact source-map fingerprint checks and room-only job states.
- Offline polygon and virtual-wall raster mask candidates,
  conservative clearance and persistent map-bound JSON.
- **188 pure-Python tests PASS** and real ROS2 middleware tests.
  Synthetic two-room workflow completes its selected room across a
  battery pause and controller restart using test coverage events.
- Read-only official /map checks: 12,394 safe test-room cells proposed
  as one open area; offline polygon+virtual wall marked 116 cells
  (11,761 safe cells after 5-cell inflation).

### Important limits / review request

- No public, maintainer-approved ROS2 cleaning-jobs action/status API.
- Upstream Nav2 costmap parameters checked for the simulation do NOT
  configure KeepoutFilter; this candidate mask is NOT an enforced
  virtual wall and should not be used to drive physical hardware.
- No integration yet with the existing oomwoo_coverage boustrophedon
  whole-room executor, nor an operator zone editor / dock-cycle services.
- No complete room cleaned in Gazebo or physical dirt removal proved.
  Simulator BatteryState is explicitly synthetic in the relevant tests.
- Crash-time reconciliation of unknown old Nav2 goals is still
  fail-closed, not ready for automatic operation. No MCU safety tested.

Please advise the canonical job API, exclusive owner of Nav2 action
goals, and how KeepoutFilter/mask servers and trusted coverage should
be wired in the full integration. Upstream pointer changes ONLY this
contributor path, not shared contracts.
