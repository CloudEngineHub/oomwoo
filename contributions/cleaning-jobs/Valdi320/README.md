# Valdi320 — cleaning-jobs: durable jobs and ROS2/Nav2 cancel validation

**Source:** [Valdi320/oomwoo-cleaning-jobs](https://github.com/Valdi320/oomwoo-cleaning-jobs)  
**Verified code:** [c84c181](https://github.com/Valdi320/oomwoo-cleaning-jobs/commit/c84c181da082e2cc44824c45f69462a6cda6b95e)  
**Evidence:** [ROS2 / real Nav2 / Gazebo validation](https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/ROS2_GAZEBO_VALIDATION.md) · [machine-readable observations](https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/evidence/gazebo_nav2_20261027.json)

Scope: one independently maintained, Apache-2.0 contribution for the
[cleaning-jobs RFC](../README.md). This is not a completed RFC or production
robot safety system.

### Implemented

- Whole-map / manual rooms / spot selection; conservative grid clearance
  and no-go cells; persistent progress checkpoint and resume validation.
- Pause, resume, cancel, service pauses and explicit recovery states.
- Real ROS2 Jazzy NavigateToPose ActionClient transport, including asynchronous
  goal acceptance, cancel-before-accept race, real cancellation requests,
  terminal-result gating and stale callback rejection.
- Faults and cancel timeouts fail closed, requiring independent backend
  idle verification before new navigation commands.
- Three **actual Gazebo robot + real Nav2** tests in the upstream OOMWOO
  test-room: user pause while moving, battery-low while moving, and cancel
  before goal acceptance. All returned canceled and no second goal was sent.
  Simulator odometry checked that movement settled after cancellation.
- A real ROS2 middleware test against a safe ActionServer, plus pure-Python
  deterministic fault-injection tests. No artificial cleaning credit for
  merely reaching a navigation goal.

### Reproduce

~~~sh
git clone https://github.com/Valdi320/oomwoo-cleaning-jobs
cd oomwoo-cleaning-jobs
PYTHONPATH=src python -m unittest discover -s tests -q
PYTHONPATH=src python -m oomwoo_cleaning_jobs.demo
~~~

**Evidence:** 92/92 pure-Python regressions passed; also checked inside the
official ROS2 Jazzy image; ROS2 action server probe passed. The repo's
ROS2_GAZEBO_VALIDATION.md includes reproducible isolated Podman commands and
the exact image ID used for the three Gazebo results.

### Limitations / open architecture decisions

- This is a **real tested ActionClient transport and integration harness**,
  not yet a persistent ROS2 cleaning-jobs node with the official
  Start/Pause/Resume/Cancel/status service/action API.
- No physical motors or MCU safety layer were tested. A successful Nav2 cancel
  is not proof of a physical emergency stop; independent MCU bumper/cliff/
  wheel-drop/current protections remain required.
- Only OOMWOO test_room single-target cancellations were checked in Gazebo.
  Not a claim of full room cleaning, docking, dynamic keepout performance,
  or measured swept-area completeness.
- The next step requires a maintainer-approved job API and a trusted
  clean-and-map measured coverage producer. A production adapter must
  also implement safe backend goal reconciliation after a process restart.

Developed using ChatGPT assistance, with Linux and official ROS2/Gazebo
runtime testing. No other contributor's files or shared contracts changed.
