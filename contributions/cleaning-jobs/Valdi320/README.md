# Valdi320 — cleaning-jobs checkpoints + Nav2 action interlock

**Source:** [Valdi320/oomwoo-cleaning-jobs](https://github.com/Valdi320/oomwoo-cleaning-jobs)
**Verified commit:** [a62de0c](https://github.com/Valdi320/oomwoo-cleaning-jobs/commit/a62de0cd77a08b7f21d5b5e8909856516983fc5f)

**Scope:** a simulation-first, hardware-independent contribution for the
[cleaning-jobs RFC](../README.md). A testable prototype, **not** a finished
ROS2 package or a robot safety controller.

### Implemented

- Whole-map, manually defined room and spot target selection with unknown
  cells/keepouts rejected and conservative grid clearance.
- Crash-resistant coverage checkpoints: count only externally acknowledged
  coverage, check saved map fingerprint, require a fresh pose after restart.
- Pause/resume/cancel/status and battery, bin and mop-service interruptions.
- **New: Nav2 action handoff interlock.** One active goal at most; request
  cancellation on pause, service fault or recovery; wait for **terminal goal
  result** before any new motion request. Old action results cannot unlock a
  newer goal; uncertain transport and timeout remain blocked until external
  backend-idle confirmation.
- Design notes on asynchronous action handles, cancel-before-accept races and
  reboot recovery: [navigation handoff contract](https://github.com/Valdi320/oomwoo-cleaning-jobs/blob/main/docs/NAVIGATION_HANDOFF.md).

### Reproduce

~~~sh
git clone https://github.com/Valdi320/oomwoo-cleaning-jobs
cd oomwoo-cleaning-jobs
PYTHONPATH=src python -m unittest discover -s tests -q
PYTHONPATH=src python -m oomwoo_cleaning_jobs.demo
~~~

**Evidence:** 67 deterministic tests passed (35 original, 32 interlock
fault-injection tests), also passed with optimized Python execution and on an
offline Python 3.12 container. GitHub Actions checks Python 3.10 and 3.12.

### Still missing / review needed

- **No running Nav2 ActionClient or Gazebo integration yet.** The ROS2 adapter
  must map tokens to actual Nav2 handles, deal with asynchronous acceptance,
  verify terminal cancellation and independently clear old goals on restart.
- This interlock gates new navigation commands but **does not physically stop
  motors**. Never equate cancel-request delivery with a confirmed stop.
  MCU-owned safety for bumper, cliff, wheel-drop and motor current is separate.
- Advisory grid cell-centre routing does not prove footprint/keepout/collision
  safety or actual swept coverage. Full costmap, sensor and localization
  validation is required before hardware use.
- Maintainer guidance requested for the cleaning-job action/service API and
  the trusted clean-and-map coverage-producer interface.

Apache-2.0. Developed with ChatGPT assistance and subjected to local tests.
No other contributors' paths, shared RFC or architecture files modified.
