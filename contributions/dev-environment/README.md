# Reproducible cross-platform dev environment (Mac, Linux, Pi)

A one-command, self-contained developer setup for the OOMWOO simulation + ROS 2
stack that gives everyone the *same* toolbox from a locked list of versions,
isolated from whatever is already on their machine — and, notably, one that runs
on **Apple Silicon Macs** as well as Linux and Raspberry Pi. This RFC is the home
for that line of work; it complements, rather than replaces, the first-party
Docker dev image.

> **Status — in progress (experimental).** There is an initial working recipe:
> [@DingoOz](DingoOz)'s [pixi](https://pixi.sh) + [RoboStack](https://robostack.github.io)
> setup that brings up the Gazebo sim with teleop on Linux, Pi 5, and Apple
> Silicon. It lives on a branch and wants cross-platform testing and hardening —
> see [DingoOz/](DingoOz) and the
> [discussions](https://github.com/makerspet/oomwoo/discussions).

# Why this exists

The canonical dev environment is the [oomwoo-install](https://github.com/makerspet/oomwoo-install)
Docker image (Linux/Windows) plus an apt-based Pi runtime. Those work well, but
they leave gaps:

- *macOS.* There is no easy Docker/apt path to a working ROS 2 + Gazebo build on
  Apple Silicon, so Mac users are effectively shut out of contributing to the sim.
- *"Works on my machine".* apt/system installs entangle with whatever ROS,
  Python, or libraries a contributor already has.
- *Reproducibility.* Pinning the *exact* set of versions (a lockfile) makes a
  build others can reproduce months later.

A per-project, host-isolated toolchain solved with a lockfile fixes all three and
lowers the barrier to first contribution — you clone, run one install command, and
drive a simulated robot.

# Recommended approach — pixi + RoboStack

[pixi](https://pixi.sh) installs a project's entire toolbox (here: ROS 2, Gazebo,
and their dependencies) into a self-contained folder from a locked manifest. It
does not touch or depend on anything already installed; an existing ROS install is
left untouched. The ROS 2 binaries come from [RoboStack](https://robostack.github.io)
(ROS packaged on conda-forge), which is what makes the same manifest resolve on
Linux, Apple Silicon, and Pi.

The RFC is open to other reproducible approaches (e.g. Nix, Dev Containers,
`vcs` + a pinned conda env), but pixi is the direction with a working submission —
make the case if you bring an alternative.

# Considerations / open questions

- *ROS distro alignment.* The project's canonical branches standardize on **ROS 2
  Jazzy**; the initial recipe targets **ROS 2 Lyrical** (what RoboStack currently
  ships cleanly across these platforms). Whether to pin the dev env to Jazzy for
  parity, or track a newer distro and accept the drift, is an open decision — call
  it out and justify it in a submission.
- *Keeping it from bit-rotting.* A locked env still needs the `oomwoo-one`
  description/launch files it builds against to stay compatible. A small CI job
  that runs `pixi install` + `pixi run build` (and, where a runner allows, a
  headless `pixi run sim` smoke test) would catch drift.
- *Sim rendering on macOS.* Gazebo's rendering path on Apple Silicon (and headless
  runners) is the usual sharp edge; document what works and any flags needed.
- *Scope.* This is a *developer/sim* environment. The on-robot runtime (Pi, apt)
  and the Docker image remain the first-party paths; this sits alongside them.

# Important References

- [DingoOz/](DingoOz) — the initial pixi + RoboStack recipe (this RFC's first
  submission).
- [urdf-gazebo-sim](../urdf-gazebo-sim) — the robot description + Gazebo world this
  environment builds and runs.
- [live-robot-bringup](../live-robot-bringup) · [compute-benchmark](../compute-benchmark)
  — the on-hardware / Pi paths this complements (the Pi 5 is a shared target).
- [oomwoo-install](https://github.com/makerspet/oomwoo-install) — the first-party
  Docker dev image + apt Pi runtime this sits beside.
- [pixi](https://pixi.sh) · [RoboStack](https://robostack.github.io) — the tools
  the recommended approach builds on.
- [Project discussions](https://github.com/makerspet/oomwoo/discussions) ·
  [Discord](https://discord.gg/3y2JKz5T25)

# Request for Contribution — Instructions

Per the [contribution model](../../docs/CONTRIBUTING.md#how-contributions-are-structured),
the environment *code* (pixi manifest, lockfile, tasks) lives in **your** repo/branch;
submit a **link** plus in-tree notes:

- *bring it up and report* — run the documented steps on a target platform
  (Linux, Pi 5, or Apple Silicon Mac) and report whether the sim + teleop come up
  cleanly, with versions, platform, and any fixes. First-run reports on **Pi 5**
  and **Mac** are the most valuable right now.
- *harden it* — pin/refresh the lockfile, smooth the rough edges (rendering flags,
  missing deps), and note platform-specific gotchas.
- *align the distro* — make the Jazzy-vs-Lyrical call (above) and, if aligning to
  Jazzy, show it building against the canonical description.
- *keep it honest* — propose the small CI job that runs `pixi install` +
  `pixi run build` so the env can't silently rot.

Submit a PR adding your notes under
`contributions/dev-environment/<your-github-username>/` — a short README with your
repo/branch link, the exact install + run + test steps, your platform matrix, and
any videos. Announce it in
[Project Discussions](https://github.com/makerspet/oomwoo/discussions).

# Acceptance criteria

- The documented steps bring up the **Gazebo sim with a drivable robot + working
  teleop** from a clean machine on at least one target platform, with **Apple
  Silicon** support being the standout goal.
- The setup is **reproducible** (a committed lockfile) and **host-isolated** (does
  not touch or require a system ROS/Python).
- A stated position on **ROS distro** (Jazzy parity vs. newer) and how it builds
  against the `oomwoo-one` description.
- Reproducible by someone else, on the platform(s) claimed, with versions recorded.
- TBD; expect criteria to evolve as platforms are validated.

The maintainer selects among compliant candidates using these criteria. Multiple
attempts are welcome — a non-selected approach is still a useful fallback.
