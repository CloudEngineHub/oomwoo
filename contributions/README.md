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
`merged`. Each RFC's own `> **Status —**` line is the source of truth; this table
is the at-a-glance view.

| RFC | What it is | Status |
|-----|------------|--------|
| [clean-and-map](clean-and-map) | First clean: coverage + SLAM + exploration | 0%, ready to start |
| [nav-localize](nav-localize) | Localization & navigation on a known map | 50%, global localizer, slam_toolbox, works in simulation |
| [floor-care](floor-care) | Wall/edge following, surfaces, mop lift | 20%, side distance sensors added, wall bump-out, wall follow before test |
| [cleaning-jobs](cleaning-jobs) | Cleaning modes, zones, job orchestration | 0%, ready to start work |
| [recovery-safety](recovery-safety) | Recovery behaviors & safety | 0%, ready to start work |
| [dock-cycle](dock-cycle) | Undock, dock, recharge & station services | 5%, ready to start work |
| [obstacle-avoidance](obstacle-avoidance) | Near-field camera + ToF avoidance |5%, front cameras, ToF added |
| [control-app](control-app) | Control app & UX | 0% ready (design track) |
| [live-robot-bringup](live-robot-bringup) | Live robot bring-up & validation | 0%, ready to start work |
| [health-monitor](health-monitor) | Stack health monitor & software watchdog | design-first, ready |
| [compute-benchmark](compute-benchmark) | Compute benchmark & memory reduction | 60%, firmware tentatively fits in 2GB Raspberry Pi CM4/CM5 |
| [mcu-io-firmware](mcu-io-firmware) | MCU I/O board firmware (STM32G473) | 0%, ready to start work |
| [io-board-interface](io-board-interface) | I/O board software interface | active |
| [urdf-gazebo-sim](urdf-gazebo-sim) | oomwoo URDF + Gazebo simulation | 75% complete |
| [mac-dev-env](mac-dev-env) | macOS (Apple Silicon) dev environment (pixi) | in progress (experimental) |
| [io-pcb](io-pcb) | I/O + motor-driver PCB (KiCad) | 20%, architecture, schematic (being checked), initial placement |
| [dust-bin](dust-bin) | Dust bin (mechanical module) | 0% |
| [vacuum-fan](vacuum-fan) | Blower fan assembly (mechanical module) | 25%, fan modules scanned to STEP |
| [part-specs](part-specs) | Procure part specs & datasheets | 60%, over half components reverse-engineered |
| [source-3d-models](source-3d-models) | Source 3D models (STEP) for BOM parts | 60%, over half components converted to STEP |
| [stair-climbing](stair-climbing) | Multi-floor: stair climbing (drive-in exoskeleton) | exploratory, for later |
| [esp32-p4](esp32-p4) | ESP32-P4 experimental compute + safety track | exploratory, for later |
