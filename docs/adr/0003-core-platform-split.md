# ADR-0003: Hardware-independent core library and platform layer

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-10-23 |
| Deciders | Antonio Zerbato |
| Related | ADR-0001, ADR-0002, ADR-0004, ADR-0005 |

## Context
The V-model needs SIL, PIL and HIL on the same algorithm code. If estimation and control code touches registers or HAL headers, it can't run in the simulator or in host unit tests.

## Decision
- **`fsw/core`**: estimation, control, guidance, mixer, state machine/failsafes, parameter and logging data types. **Zero hardware dependencies.** Pure C++17 (ADR-0001), no OS calls, no HAL headers. Builds for the **host** (GoogleTest, SIL) and the **target** (`arm-none-eabi`).
- **`fsw/platform`**: drivers, scheduler, clock, board support, and an implementation of the core's hardware interfaces per target (`platform/stm32h7`, `platform/host_sim`).
- Core uses data (sensor samples with timestamps in, actuator commands out) through small abstract interfaces or plain structs defined in core. **Dependency direction: platform → core, never the reverse**, enforced by CMake target visibility.
- The core API is **step-based**: `step(inputs, t_us) → outputs`, with no hidden threads or timers, so SIL, PIL and HIL can drive it deterministically.

## Alternatives considered
| Option | Why not |
|---|---|
| One monolithic firmware | Makes SIL impossible |
| Adopt the PX4/ArduPilot architecture | Defeats the "from scratch" goal |
| uORB-style pub/sub middleware in core | Over-engineered for a single vehicle at this scale; may revisit |

## Consequences
- Positive: SIL and CI test the exact flight code; PIL runs core on the H7 against the host simulator.
- Negative: interface design takes effort up front; some duplication in host vs. target platform shims.
- Follow-ups: ICD for core ↔ platform and sim ↔ FSW messages (macro-area 2); CMake check that `fsw/core` links nothing from `fsw/platform`.
