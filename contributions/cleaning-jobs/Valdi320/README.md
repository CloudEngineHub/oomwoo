# Valdi320 — cleaning-jobs Phase-0 headless reference

**Source repository:** [Valdi320/oomwoo-cleaning-jobs](https://github.com/Valdi320/oomwoo-cleaning-jobs)

**Scope:** a simulation-first, hardware-independent state and checkpoint core for the [cleaning-jobs RFC](../README.md). This is a *proposal for review*, not a completed RFC implementation.

### Implemented
- Whole-map, manually defined room, and spot-radius target selection from occupancy grids.
- No-go masks, conservative cell clearance, four-connected routing through known-free cell centres and explicit unreachable-goal errors.
- Durable checkpoint/resume with a map fingerprint, acknowledged-progress-only bookkeeping and fail-closed restart.
- Start/pause/resume/cancel/status; resource pauses for battery, dust bin and mop service.
- No ROS 2 runtime dependencies; standard-library Python 3.10+ tests.

### Run

```sh
git clone https://github.com/Valdi320/oomwoo-cleaning-jobs
cd oomwoo-cleaning-jobs
PYTHONPATH=src python -m unittest discover -s tests -v
PYTHONPATH=src python -m oomwoo_cleaning_jobs.demo
```

**Evidence:** 35 deterministic unittest cases passed on Linux, including interruption/restart, invalid transitions and map/no-go mismatch. GitHub Actions also checks Python 3.10 and 3.12. The demo completes all eligible cell-centre targets after a simulated low-battery interruption.

### Limitations / review requests

- **Not a ROS 2 node** and **not tested in Gazebo or on hardware**. There is no Nav2 action, real map subscription, localization adapter, coverage feedback, dock orchestration or automatic room segmentation yet.
- A grid-centre route does **not** prove a real robot can safely traverse it. It does not account for footprint, wheel slip, obstacle motion, cleaning swath, or lidar uncertainty; Nav2 inflated keepout and recovery integration are needed.
- The core **does not send stop or drive commands**. A future adapter must cancel any outstanding Nav2 action during a service/safety pause. Independent bumper, cliff, wheel-drop and motor safety must remain MCU-owned.
- Request maintainer guidance on the ROS 2 job action/status interface and on which existing `clean-and-map` implementation should emit trusted coverage acknowledgements.

Implementation is **Apache-2.0** and was developed with ChatGPT assistance, with human-controlled local execution/tests. No OOMWOO interface or shared RFC file was modified.
