# Architecture Decision Records

Any decision not covered here: stop and ask the owner before proceeding. New ADRs copy `0000-template.md`.

| ADR | Title | Status |
|---|---|---|
| [0001](0001-cpp17-misra-subset.md) | C++17 with a MISRA-inspired subset | Accepted |
| [0002](0002-offtheshelf-stm32h7-fc.md) | Off-the-shelf STM32H7 FC and COTS airframe | Accepted |
| [0003](0003-core-platform-split.md) | Hardware-independent core / platform layer | Accepted |
| [0004](0004-simulator-own-cpp-6dof.md) | Own C++ 6-DOF simulator as first truth model | Accepted |
| [0005](0005-eskf-aided-ins-pluggable-aiding.md) | ESKF aided INS with pluggable aiding modules | Accepted |
| [0006](0006-attitude-control-lyapunov-with-pid-baseline.md) | Lyapunov quaternion attitude control + PID baseline | Accepted |
| [0007](0007-phased-operations-indoor-then-outdoor-a3.md) | Indoor first, then outdoor EASA A3 | Accepted |
| [0008](0008-theory-docs-as-notation-source.md) | docs/theory as notation source; references/ gitignored | Accepted |

Pending ADRs: math library (Eigen vs. own), RTOS vs. bare-metal scheduler, flight controller board selection, telemetry/HIL protocol.
