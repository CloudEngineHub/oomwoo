# Valdi320 — cleaning-jobs: measured coverage, rooms, real Nav2 keepout

**Independent Apache-2.0 source:** https://github.com/Valdi320/oomwoo-cleaning-jobs
**Verified latest commit:** https://github.com/Valdi320/oomwoo-cleaning-jobs/commit/ee470804c2c1531dfe5cc3ed71975bc7cd0fb73c
**Real Nav2 filter + OOMWOO Gazebo:** https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/REAL_NAV2_KEEPOUT_GAZEBO.md
**Evidence, independent simulator repeats:** https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/evidence/real_nav2_keepout_repeat_20261027.json

### Tested increments

- Persistent experimental private ROS2 cleaning controller: durable
  checkpoints, cancellation gating, safe failure states, start/pause/
  resume/cancel for small spots. Not a public official job API.
- Unmodified OOMWOO simulated ground-truth swept-coverage meter
  drives verified new-cell accounting; completion never follows
  from Nav2 goal success alone. Delayed meter and no-progress
  cases handled.
- **3/3** independently cold-started official OOMWOO Gazebo trials
  completed one small 9/9-cell spot with two Nav2 goals each,
  using the real external coverage meter. Not a room/home result.
- Offline saved-map room partitioning, persistently named rooms,
  synthetic selected-room restart + battery interruption regression,
  and no-go polygon/virtual-wall JSON mask generation.
- **NEW: actual Nav2 Jazzy KeepoutFilter**, activated for BOTH
  global and local costmaps in an **isolated OOMWOO test runtime**.
  220 mask cells gave lethal cost 100 in both costmaps (outside 0);
  a real 108-pose planner path had zero forbidden crossings.
- Real OOMWOO Gazebo NavigateToPose path around the rectangle:
  two cold tests, ~1.73m movement each, **zero mask entries** and
  minimum recorded centre clearance 0.2315m / 0.2636m.
- Nav2 may produce a successful **truncated** path to a goal
  inside the forbidden region (stopping 0.40m before the goal).
  An independent application-side fail-closed admission guard now
  rejects that goal BEFORE sending any Nav2 action.
- Live private ROS controller requires optional filter_info type=0,
  live publishers and all mask cells lethal in real global costmap.
  After deliberately terminating the private FilterInfoServer
  while paused, it refused resume and sent no new action.
- **198/198** Python unit/fault-injection cases PASS, plus real
  ROS2 middleware and official Nav2/Gazebo integration runs.
- All tests use the official OOMWOO Jazzy/Gazebo image and Linux.

### Boundaries requiring maintainer review

- The **stock OOMWOO navigation config remains UNMODIFIED** and
  does NOT yet enable KeepoutFilter. The tested overlay has to be
  integrated and reviewed in the canonical bringup before this
  protection exists in OOMWOO's standard robot workflow.
- Only one static rectangle has been proven at runtime; arbitrary
  edited maps, people, SLAM changes, dynamic no-go areas, and
  physical wheel/cliff/bumper/current safety remain unverified.
- Actual full-room/multi-room sweeps, clean-and-map's exclusive
  Nav2 ownership, canonical public job API, dock-cycle services
  and restart-time unknown goal reconciliation remain pending.
- Simulator ground-truth floor coverage is NOT physical dirt
  removal. Battery tests are explicitly synthetic in test_room.

Maintainers: please advise the canonical ROS2 job API, single
Nav2 action owner and safe Bringup/KeepoutFilter integration.
This PR changes only our contributor-owned pointer, not shared code.
