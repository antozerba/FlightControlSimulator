# 00 — Vision

| Field | Value |
|---|---|
| Status | Draft (pre-SRR) |
| Owner | Antonio Zerbato |
| Last updated | 2026-10-23 |

Items marked **[TO CONFIRM]** need the owner's sign-off before SRR.

## 1. Purpose

Build a quadrotor flight controller from scratch in C++ and fly it on real, off-the-shelf hardware.
Run it like an aerospace project, following the V-model
(requirements → architecture → MIL → design → implementation → SIL → PIL/HIL → sys-ID → flight test → V&V closure),
with review gates SRR, PDR, CDR, TRR and FRR held for each phase.

## 2. Why

- **Career:** GNC / control / estimation engineer for autonomous aerial and underwater vehicles.
- **Close the gap:** the owner has strong theory and simulation skills but has never worked with embedded systems or real hardware. This project closes that gap.
- **Course alignment:** the algorithms follow the NTNU courses TTK4250 (Sensor Fusion), TTK4150 (Nonlinear Control) and TTK4190 (Guidance, Navigation and Control), so the theory gets applied on hardware.
- **Learning priority:** where learning and speed conflict on the **embedded** side, learning wins. Drivers are written at register/LL level by the owner.

## 3. Success criteria

Each phase has a **floor** (the minimum result that counts as success) and a **target** (the intended result, which may slip).

### Phase 1 — Indoor, IMU-only (floor due Apr 2027)

| | Criterion | Draft threshold |
|---|---|---|
| **Floor** | Stable manual angle-mode hover using the owner's Mahony filter and **cascaded PID** attitude controller, through the full V-model up to FRR | ≥ 60 s hover; roll/pitch tracking error < 3° RMS; 5 consecutive flights with no safety-pilot intervention **[TO CONFIRM]** |
| **Target** (may slip to May–Jun 2027) | **Quaternion attitude controller with Lyapunov stability proof** (TTK4150) flown and compared against the PID baseline on the same manoeuvre set | Proof written in `docs/theory/`; comparison report covering step response, disturbance rejection and RMS error **[TO CONFIRM metrics]** |

### Phase 2 — Outdoor, aided INS (summer 2027, expected to run into autumn)

| | Criterion | Draft threshold |
|---|---|---|
| **Floor** | ESKF aided INS (IMU + GNSS + magnetometer + barometer) passes NEES/NIS consistency tests in SIL and NIS checks on flight logs | NEES/NIS inside 95 % χ² bounds over Monte Carlo SIL runs **[TO CONFIRM run count]** |
| **Target** | GNSS path following with TTK4190 guidance laws (e.g. LOS) under EASA A3 | Cross-track error vs GNSS-reported position < 2 m RMS on a ~100 m path at ≤ 3 m/s **[TO CONFIRM]** |

### Verification evidence and its limits

| Evidence | Where | Notes |
|---|---|---|
| NEES | SIL only | Needs ground truth, so it can't be computed on hardware. |
| NIS (per aiding sensor) | SIL and hardware logs | The main consistency evidence for hardware. |
| Attitude tracking (phase 1) | Onboard estimate | Acceptable: roll and pitch are referenced to gravity and don't drift. |
| Path following (phase 2) | Compared against GNSS | **Not independent**, because GNSS is fused into the estimate. Valid only at GNSS accuracy (about 1–3 m). RTK is the upgrade path but is a non-goal. |

## 4. Constraints

| Constraint | Value |
|---|---|
| Effort | 8–10 h/week, part-time alongside the thesis search (~40 h/month) |
| Budget | €300–600 total hardware, including spares, charger, batteries, radio and GNSS/compass module |
| Vehicle | 3.5–4" with prop guards or ducts, ~300–450 g, 4S. **Re-check at PDR** for outdoor wind and the mass of the GNSS mast |
| Flight controller | Off-the-shelf STM32H7 board with barometer and ≥ 2 spare UARTs + I2C (ADR-0002) |
| Environment | Phase 1: indoor hall with the owner's permission. Phase 2: outdoor field under EASA A3 (ADR-0007) |
| Language | C++17, MISRA-inspired subset (ADR-0001) |

## 5. Regulatory gate (before any outdoor flight)

- [ ] Operator registration in the country of principal residence (Norway: Luftfartstilsynet; Italy: ENAC / d-flight) **[TO CONFIRM residence for summer 2027]**
- [ ] EASA A1/A3 online training and exam passed
- [ ] Operator ID displayed on the vehicle
- [ ] Insurance requirements checked for the country of operation
- [ ] A3 field identified: ≥ 150 m from residential, commercial and industrial areas, no uninvolved people, ≤ 120 m AGL

## 6. Stretch goals (only after the phase 2 floor)

1. Optical flow + range sensor as an extra **indoor aiding module** for the ESKF.
2. NIS-based sensor fault detection, isolation and recovery (FDIR), with fault injection in SIL/HIL.
3. Onboard MPC / iLQR on the H7.
4. Further truth models: Simulink, then Isaac Sim or other industry tools (Isaac needs an NVIDIA GPU, so not the Mac).

## 7. Non-goals

- Custom PCB, or custom ESC firmware (use AM32/BLHeli with DShot)
- INDI, or a frequency-domain system-identification campaign
- RTK GNSS
- BVLOS, specific category, or any operation outside EASA open A3
- FPV video, or a companion computer
- Simulink or Isaac Sim work before the phase 2 floor is met

## 8. Top risks

| # | Risk | Mitigation |
|---|---|---|
| R1 | Schedule: phase 1 is an estimated 30–60 % over the 240 h budget | PID floor, Lyapunov controller as target; track hours per macro-area |
| R2 | First-time H7 bring-up (cache/DMA coherency, MPU, clocks) takes longer than planned | Bring-up starts early; HIL protocol defined before flight builds |
| R3 | Solo flight testing (no second person) | Tethered first flights, kill switch, test card template. Reconsider for outdoor flights |
| R4 | Outdoor phase blocked by regulations, weather or field access | Regulatory gate checklist done by May 2027 |
| R5 | Magnetometer interference from ESC and battery currents | External compass on a mast; calibration procedure; mag NIS monitoring |
| R6 | Phase 2 compressed by phase 1 overrun | Phase 2 floor is SIL-heavy and can progress in parallel with flight testing |

## 9. Open questions

1. Numeric thresholds for phase 1 and phase 2 (drafts above).
2. Where the owner will be in summer 2027 (sets registration authority and A3 field).
3. Indoor venue and permission — **blocks phase 1 TRR**.
4. Vehicle size for outdoor wind (PDR).
5. Check `docs/03_conventions.md` against TTK4250/TTK4190 notation.
6. Eigen (fixed-size, no allocation) vs. an in-house math library; RTOS vs. bare-metal scheduler.
7. A second person for outdoor flights.
