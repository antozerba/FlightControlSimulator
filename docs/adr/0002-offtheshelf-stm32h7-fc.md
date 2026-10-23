# ADR-0002: Off-the-shelf STM32H7 flight controller and COTS airframe

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-10-23 |
| Deciders | Antonio Zerbato |
| Related | ADR-0001, ADR-0003, ADR-0005, ADR-0007 |

## Context
The project's goal is the firmware and GNC, not electronics design. Budget is €300–600, including spares and a GNSS/compass module. Stretch goals (ESKF with several aiding sensors, onboard MPC/iLQR) need compute and RAM headroom. The owner wants register/LL-level driver experience.

## Decision
- **Only the firmware is the owner's.** Flight controller board, frame, motors, ESCs (AM32/BLHeli_32 with DShot), RC link and batteries are commercial off-the-shelf parts. **No custom PCB.**
- **MCU family: STM32H7** (Cortex-M7, ~480 MHz, double-precision FPU, ≥ 512 KB RAM, D-cache, DMA).
- The flight controller board **must have**:
  - an onboard IMU on SPI with an accessible data-ready interrupt
  - an onboard **barometer**
  - **≥ 2 spare UARTs** (GNSS, telemetry/HIL) and **≥ 1 spare I2C** (compass)
  - SWD pads/header for a debug probe
  - published schematics or pinout (needed for register-level work)
- External **GNSS + compass module** (u-blox M10 class + QMC5883/IST8310/RM3100-class magnetometer, ~€25–45), mounted on a mast.
- Debug probe (ST-Link V3 or J-Link EDU Mini) is part of the budget.

## Alternatives considered
| Option | Why not |
|---|---|
| STM32F405/F722 | Single-precision FPU only, 128–256 KB RAM; tight for ESKF + MPC |
| Custom PCB | Weeks of hardware work outside the career focus; risk to the schedule |
| Pixhawk-class (FMU) board | More expensive and heavier; less low-level learning |
| ESP32 / RP2040 | Off the industry path for flight controllers; weaker tooling for this purpose |

## Consequences
- Positive: headroom for every stretch goal; real H7 bring-up skills (clock tree, MPU, cache coherency with DMA).
- Negative: harder bring-up than F4 (cache/DMA pitfalls); fewer beginner tutorials.
- Follow-ups: board selection trade study in macro-area 7; ICD pin/peripheral map; ADR on RTOS vs. bare-metal scheduler.
