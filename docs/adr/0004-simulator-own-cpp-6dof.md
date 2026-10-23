# ADR-0004: Own C++ 6-DOF simulator as the first truth model

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-10-23 |
| Deciders | Antonio Zerbato |
| Related | ADR-0003, ADR-0005 |

## Context
MIL/SIL needs a truth model for the rigid body, rotors, sensors (IMU, baro, mag, GNSS) and environment (gravity, wind, magnetic field). NEES tests need exact ground truth and repeatable Monte Carlo runs. Longer term the owner wants experience with industry tools (Simulink, Isaac Sim).

## Decision
- Phase 1–2 truth model: **own C++ 6-DOF** in `sim/`. The owner writes the dynamics; Claude may scaffold the integrator harness, I/O and plotting.
- It is deterministic (seeded RNG), runs faster than real time and headless in CI, and links `fsw/core` directly for SIL.
- Python bindings or log export for analysis and plotting in `tools/`.
- **The sim ↔ FSW boundary is a versioned interface** (sensor-sample and actuator-command messages, defined in the ICD). Other truth models plug in behind the same interface.
- Roadmap (post phase 2 floor): **Simulink** model → **Isaac Sim** / other industry simulators. Isaac Sim needs an NVIDIA RTX GPU, so not the Mac.

## Alternatives considered
| Option | Why not (now) |
|---|---|
| MuJoCo from day 1 | Heavier CI dependency; less control over integrator details; can be added later behind the same interface |
| Python model for MIL + C++ for SIL | Two models to keep in sync |
| Gazebo / jMAVSim | Tied to PX4 conventions; less learning |

## Consequences
- Positive: full control and exact truth for NEES; fast CI.
- Negative: the owner builds visualisation and contact handling, or does without.
- Follow-ups: sim ↔ FSW ICD; model validation against sys-ID data (macro-area 9).
