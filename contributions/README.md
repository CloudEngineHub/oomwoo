# OOMWOO Contributions — RFC board

Each folder here is a **Request for Contribution (RFC)** — a self-contained module
(software, firmware, hardware, or procurement) you can pick up and build. Submit
your work under `contributions/<rfc>/<your-github-username>/`. New here? Read
[CONTRIBUTING](../docs/CONTRIBUTING.md), and see the
[RFC lifecycle](../docs/CONTRIBUTING.md#rfc-lifecycle) for how RFCs progress and
retire.

**Status legend** — *Active:* `exploratory` → `ready to start work` /
`design-first` → `in progress`. *Retired* (closed to new contributions, but
**kept in place** for provenance): `completed` · `superseded` · `descoped` ·
`merged`.

**This board is canonical** for every RFC's status and progress. An RFC's own
README describes its scope and how to get started; where its `> Status —` line
disagrees with the row below, the row below wins. Update this board when you land
work.

**Progress** is a rough, self-reported percentage of the RFC's scope — a sense of
"how much is left", not a promise:
![>=75%](https://img.shields.io/badge/%3E%3D75%25-brightgreen) nearly there ·
![25-74%](https://img.shields.io/badge/25--74%25-yellow) under way ·
![<25%](https://img.shields.io/badge/%3C25%25-red) barely started / open.

| RFC | What it is | Progress | Status |
|-----|------------|:--------:|--------|
| [clean-and-map](clean-and-map) | First clean: coverage + SLAM + exploration | ![15%](https://img.shields.io/badge/15%25-red) | coverage meter + regression harness + drive-to-goal cleaning; coverage-while-mapping not started |
| [nav-localize](nav-localize) | Localization & navigation on a known map | ![75%](https://img.shields.io/badge/75%25-brightgreen) | global relocalizer (96/96 in sim), lost-detection, slam_toolbox tuned, shrugs off large obstacles; needs real-robot validation |
| [floor-care](floor-care) | Wall/edge following, surfaces, mop lift | ![35%](https://img.shields.io/badge/35%25-yellow) | reactive LiDAR contour follower (phase 1), bump-out wall clean, side sensors + mop modules modelled |
| [cleaning-jobs](cleaning-jobs) | Cleaning modes, zones, job orchestration | ![0%](https://img.shields.io/badge/0%25-red) | ready to start work |
| [recovery-safety](recovery-safety) | Recovery behaviors & safety | ![10%](https://img.shields.io/badge/10%25-red) | lost-detect to relocalize recovery + one escape reflex; recovery ladder, e-stop, reporting open |
| [dock-cycle](dock-cycle) | Undock, dock, recharge & station services | ![10%](https://img.shields.io/badge/10%25-red) | dock modelled in sim + charging-dock schematic; docking behaviours not started |
| [obstacle-avoidance](obstacle-avoidance) | Near-field camera + ToF avoidance | ![15%](https://img.shields.io/badge/15%25-red) | front stereo + ToF modelled; per-ray static/dynamic scan scoring + placeholder detector |
| [control-app](control-app) | Control app & UX | ![0%](https://img.shields.io/badge/0%25-red) | ready to start work (design track) |
| [live-robot-bringup](live-robot-bringup) | Live robot bring-up & validation | ![0%](https://img.shields.io/badge/0%25-red) | ready to start work |
| [health-monitor](health-monitor) | Stack health monitor & software watchdog | ![0%](https://img.shields.io/badge/0%25-red) | design-first: contracts drafted, ready to start |
| [compute-benchmark](compute-benchmark) | Compute benchmark & memory reduction | ![70%](https://img.shields.io/badge/70%25-yellow) | tentatively fits 2 GB Pi CM4/CM5; repeatable Pi benchmark CLI + guide |
| [mcu-io-firmware](mcu-io-firmware) | MCU I/O board firmware (STM32G473) | ![5%](https://img.shields.io/badge/5%25-red) | architecture RFC written; repo has no code yet |
| [io-board-interface](io-board-interface) | I/O board software interface | ![30%](https://img.shields.io/badge/30%25-yellow) | SPEC.md (GPIO/pinout contract) drafted; ROS2 bridge mapping + validation open |
| [urdf-gazebo-sim](urdf-gazebo-sim) | oomwoo URDF + Gazebo simulation | ![85%](https://img.shields.io/badge/85%25-brightgreen) | full sensor suite, living-room + kitchen-dining worlds, docks, LiDAR moved forward |
| [mac-dev-env](mac-dev-env) | macOS (Apple Silicon) dev environment (pixi) | ![40%](https://img.shields.io/badge/40%25-yellow) | initial working pixi recipe (experimental) |
| [io-pcb](io-pcb) | I/O + motor-driver PCB (KiCad) | ![35%](https://img.shields.io/badge/35%25-yellow) | KiCad schematic WIP (charging, side sensors, edge cuts, STEP); not validated, do not fabricate |
| [dust-bin](dust-bin) | Dust bin (mechanical module) | ![0%](https://img.shields.io/badge/0%25-red) | ready to start work |
| [vacuum-fan](vacuum-fan) | Blower fan assembly (mechanical module) | ![35%](https://img.shields.io/badge/35%25-yellow) | fan module scanned and modelled (OOM-F02) |
| [part-specs](part-specs) | Procure part specs & datasheets | ![70%](https://img.shields.io/badge/70%25-yellow) | most components reverse-engineered; specs for LiDAR, mop, I/O board |
| [source-3d-models](source-3d-models) | Source 3D models (STEP) for BOM parts | ![70%](https://img.shields.io/badge/70%25-yellow) | brushes, gearboxes, mops, fan, wheels modelled to STEP |
| [stair-climbing](stair-climbing) | Multi-floor: stair climbing (drive-in exoskeleton) | ![0%](https://img.shields.io/badge/0%25-red) | exploratory, for later |
| [esp32-p4](esp32-p4) | ESP32-P4 experimental compute + safety track | ![0%](https://img.shields.io/badge/0%25-red) | exploratory, for later |
