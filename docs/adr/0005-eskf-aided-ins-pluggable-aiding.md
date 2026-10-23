# ADR-0005: Sensor-agnostic ESKF aided INS with pluggable aiding modules

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-10-23 |
| Deciders | Antonio Zerbato |
| Related | ADR-0003, ADR-0006, ADR-0008; theory: `docs/theory/eskf.md`, `mahony.md`, `aiding_models.md`, `nees_nis.md` |

## Context
Phase 1 flies IMU-only; phase 2 adds GNSS, magnetometer and barometer; optical flow is a stretch goal. The estimator design follows TTK4250 (Sensor Fusion). Hard-coding each sensor into one filter would make adding sensors a rewrite and make FDIR hard.

## Decision
- **Phase 2 navigation estimator:** an **error-state Kalman filter (ESKF) aided INS** per TTK4250. Nominal state propagated with IMU strapdown mechanisation; error state and covariance propagated alongside; corrections from aiding sensors; reset after each injection. The state set is defined in `docs/theory/eskf.md`.
- **Aiding modules are pluggable.** Each sensor provides a module implementing a common interface (shape to be fixed at CDR). Proposed contents:
  - predicted measurement from the nominal state
  - measurement Jacobian with respect to the error state
  - measurement noise covariance
  - innovation and NIS computation, with χ² gating (reject + flag)
  - validity/timestamp handling, including out-of-sequence policy
  Phase 2 modules: GNSS position (+ velocity), magnetometer, barometer. Stretch: optical flow + range.
- **Phase 1 estimator: Mahony** nonlinear complementary filter, behind the **same estimator output interface** (attitude, rates, validity), so control code doesn't change when the ESKF replaces it.
- Mahony stays available as a fallback and cross-check estimator in phase 2.
- Fixed-size matrices only (ADR-0001). Error-state dimension is a compile-time constant.
- **NEES/NIS consistency tests in SIL are a phase 2 floor criterion.** NIS logging is mandatory on hardware.

## Alternatives considered
| Option | Why not |
|---|---|
| Single monolithic EKF/MEKF | Adding sensors means a rewrite; FDIR is harder |
| UKF / invariant EKF | Off-course; compute cost; maybe later as a comparison study |
| Complementary filters only | No covariance, so no NEES/NIS, which weakens the V&V story |

## Consequences
- Positive: adding a sensor is a new module plus tests; FDIR (stretch) builds on per-module NIS; maps directly onto the TTK4250 material.
- Negative: the interface must be designed in phase 1 although only Mahony flies (budgeted, see vision R1).
- Follow-ups: CDR fixes the interface; requirements `EST-xxx`; SIL Monte Carlo harness (Claude may scaffold).
