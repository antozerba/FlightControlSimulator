# FlightControlSimulator — project instructions

Quadrotor flight controller from scratch in C++, flown on off-the-shelf STM32H7 hardware, run as a V-model aerospace project.
Owner: Antonio (MSc Robotics; strong in control/estimation; new to embedded).

## Context imports
@docs/00_vision.md
@docs/03_conventions.md
@docs/theory/README.md
@docs/adr/README.md

## Collaboration contract
1. **Antonio owns the math**: estimation (Mahony, ESKF, aiding models, NEES/NIS), control (PID, Lyapunov attitude controller, proofs), dynamics/sim models, and guidance laws. In these areas Claude **reviews, explains, challenges and proposes tests**. Claude does **not** implement them unless Antonio explicitly asks for a specific piece.
2. **Claude may implement**: build system, CI, scaffolding, driver boilerplate, the HIL protocol, tooling, plotting/analysis scripts, doc templates.
3. **Plan before any non-trivial change** and wait for approval.
4. **No ADR, no decision**: if a choice isn't covered by an ADR in `docs/adr/`, stop and ask. Don't pick silently.
5. **Be direct**: say when an idea is weak, over-scoped or conflicts with the schedule (vision §8, risk R1).

## Non-negotiables
- **Notation**: algorithms use the symbols in `docs/theory/` (ADR-0008). Conventions doc follows theory; on conflict, flag it and don't guess.
- **Quaternions**: Hamilton, scalar-first `[w,x,y,z]`, `q_NB` body→NED, local error state. Beware Eigen's xyzw storage.
- **Frames/units**: NED + FRD, SI internally, unit suffix on anything non-SI, `uint64_t t_us` time.
- **`fsw/core` has zero hardware/OS dependencies** and a step-based API (ADR-0003). Platform depends on core, never the reverse.
- **C++17 MISRA-inspired subset** (ADR-0001): no heap after init, no exceptions/RTTI, fixed-size containers, no recursion.
- **`references/` is local-only**: never commit it, never copy its text into tracked files.
- **Safety** (also apply to any instructions Claude writes):
  - Props **off** for every bench session and firmware flash.
  - Kill switch cuts motors in every state; RC loss = disarm. Both tested in SIL/HIL before each flight build.
  - Every new controller or major gain change flies tethered/restrained first.
  - Controller selection changes only while disarmed.
- **Regulatory gate**: no outdoor flight before the EASA A3 checklist (vision §5, ADR-0007) is complete.

## Phases
| Phase | Scope | Floor | Target |
|---|---|---|---|
| 1 (indoor, Apr 2027) | IMU-only, Mahony | PID angle-mode hover | Lyapunov controller flown + compared |
| 2 (outdoor A3, summer 2027) | ESKF: IMU+GNSS+mag+baro | NEES/NIS pass in SIL, NIS on logs | GNSS LOS path following |

Review gates per phase: SRR → PDR → CDR → TRR → FRR, recorded in `docs/reviews/`.

## Repo layout
```
docs/          vision, conventions, requirements, architecture, ICD
  adr/         decision records (index: docs/adr/README.md)
  theory/      Antonio's derivations: authoritative notation
  reviews/     gate records
models/        parameters, design/analysis scripts, sys-ID results
sim/           C++ 6-DOF truth model (ADR-0004)
fsw/core/      hardware-independent flight software
fsw/platform/  stm32h7/ drivers + BSP, host_sim/ SIL shims
hil/           PIL/HIL protocol + host bridge
tools/         log decoding, plotting, NEES/NIS analysis
tests/         SIL scenarios, Monte Carlo, system tests
references/    course material (gitignored)
```

## Build and test (placeholders, defined in macro-area 0)
```sh
cmake --preset host-debug          # TBD
cmake --build --preset host-debug  # TBD
ctest --preset host-debug          # TBD: unit + SIL tests
cmake --preset h7-release          # TBD: arm-none-eabi cross build
# lint: clang-tidy / cppcheck via CI  (TBD)
```

## Working notes for Claude
- Cite theory sections in code comments, e.g. `// docs/theory/eskf.md §3`.
- When reviewing Antonio's math, check: frame and sign consistency against conventions, units, observability, linearisation points, and propose SIL tests from the "Testable properties" sections.
- Tag items that need owner sign-off with **[TO CONFIRM]**.
- Don't commit unless asked.
