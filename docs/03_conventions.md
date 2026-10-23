# 03 — Conventions

| Field | Value |
|---|---|
| Status | Draft |
| Last updated | 2026-10-23 |

> **Precedence rule (ADR-0008):** the notation in `docs/theory/` is authoritative. If a symbol table there disagrees with this file, this file is wrong and must be updated to match. Items marked **[TO CONFIRM]** must be checked against the TTK4250 (Brekke) and TTK4190 (Fossen) course notation.

## 1. Reference frames

| Symbol | Frame | Definition |
|---|---|---|
| `E` | ECEF | Earth-centred, Earth-fixed. Used only to convert GNSS fixes |
| `N` | Navigation | Local **NED** tangent plane (flat-earth). Origin at the home position, latched at the first valid GNSS fix after arming (phase 1: at arming) |
| `B` | Body | **FRD**: x forward, y right, z down. Origin at the centre of mass |
| `S_x` | Sensor x | For example `S_imu`, `S_mag`, `S_gnss`. Mounting is fixed: `R_BS`, lever arm `r_BS_B` |

- Gravity in `N`: `g_N = [0, 0, +g]`, with `g = 9.80665 m/s²` (Trondheim local value in the model params **[TO CONFIRM]**).
- The accelerometer measures **specific force**, `f_B = R_BN (a_N − g_N)`. At rest and level, it reads `f_B ≈ [0, 0, −g]`.
- Magnetometer: the field reference `m_N` comes from WMM at the operating site. Declination is handled in `m_N`, never as an ad-hoc yaw offset.
- GNSS: geodetic (lat, lon, h) → ECEF → NED around home, in double precision. Lever arm `r_BS_B` is compensated in the measurement model.

## 2. Attitude representation

**Proposal (accepted in the interview; [TO CONFIRM] against TTK4250 notes):**

- **Hamilton** quaternion product, **scalar-first** `q = [w, x, y, z]` = `[η, εᵀ]ᵀ`.
- Attitude state `q_NB` rotates body vectors into the navigation frame: `v_N = q_NB ⊗ [0; v_B] ⊗ q_NB*`, equivalently `v_N = R_NB v_B` with `R_NB = R(q_NB)`.
- Kinematics: `q̇_NB = ½ q_NB ⊗ [0; ω_B]`, where `ω_B` is the body rate of B relative to N, expressed in B.
- **ESKF error state** is defined **locally** (body side): `q_NB = q̂_NB ⊗ δq(δθ)`, with `δq(δθ) ≈ [1; ½δθ]` and `δθ ∈ ℝ³` in the body frame.
- Hemisphere: keep `w ≥ 0` after normalisation where the controller needs it. The Lyapunov controller handles `q ≡ −q` explicitly (unwinding), which is documented in `docs/theory/attitude_lyapunov.md`.
- Euler angles: ZYX (yaw ψ, pitch θ, roll φ), **only** for display, logs, RC set-points and parameters. Never used for propagation.

**Why:** Hamilton/scalar-first matches Solà ("Quaternion kinematics for the error-state Kalman filter"), Fossen's `q = [η, εᵀ]ᵀ`, PX4 and Eigen's algebra. A local error state keeps the covariance in body coordinates, which is natural for IMU errors. Avoid the JPL convention (Trawny & Roumeliotis; parts of Markley/Crassidis), the most common source of sign bugs in MEKF/ESKF code.

> **Eigen trap:** `Eigen::Quaternion` uses Hamilton algebra and its constructor takes `(w, x, y, z)`, but it **stores** `[x, y, z, w]` (`coeffs()`). Never `memcpy` or `Map` raw arrays into it without an explicit conversion helper.

## 3. Units and time

- SI everywhere internally: m, s, kg, rad, rad/s, N, T (magnetic field), Pa.
- Non-SI values carry a unit suffix: `_deg`, `_us`, `_ms`, `_pct`, `_mG`, `_hPa`. They exist only at interfaces (RC, parameters, logs, UI).
- Time: `uint64_t` microseconds from a monotonic clock, `t_us`. Durations as `float dt_s` inside algorithms.
- Floating point: `float` in the flight-critical loops on target. `double` for GNSS geodetic maths and in the simulator truth model.

## 4. Naming

### Frame-annotated variables

| Pattern | Meaning | Example |
|---|---|---|
| `x_F` | vector `x` expressed in frame F | `v_N`, `omega_B`, `f_B` |
| `R_AB`, `q_AB` | rotation from B to A (`x_A = R_AB x_B`) | `R_NB`, `q_NB` |
| `p_AB_C` | position of B relative to A, expressed in C | `p_NB_N` |
| `*_hat`, `*_ref`, `*_meas` | estimate, reference, measurement | `q_NB_hat`, `omega_B_ref` |
| `P`, `Q`, `R_cov` | covariance matrices (`R_cov` avoids a clash with rotation `R`) | |

### Code style [TO CONFIRM]

Proposal: types `PascalCase`, functions and variables `snake_case`, constants `kPascalCase`, members `snake_case_`, namespaces `fcs::core::…`. Enforced by `.clang-format` and `.clang-tidy` (macro-area 0).

## 5. Vehicle conventions [TO CONFIRM]

- Layout: quad-X.
- **Motor numbering and spin direction:** choose PX4 (1 front-right CCW, 2 rear-left CCW, 3 front-left CW, 4 rear-right CW) or Betaflight (1 rear-right, 2 front-right, 3 rear-left, 4 front-left; props-in by default). Proposal: **PX4 numbering**, documented in the ICD and checked with a props-off motor-order test before first flight.
- Positive yaw torque = rotation about +z_B (clockwise seen from above, since z points down).
- Normalised throttle `u ∈ [0, 1]`. Thrust in N inside the controller; the mixer maps to the actuator command.

## 6. Requirement and document IDs

`SYS-xxx`, `EST-xxx`, `CTL-xxx`, `GNC-xxx`, `SAF-xxx`, `REG-xxx`, `SW-xxx`, `HW-xxx`, `IF-xxx` (ICD). ADRs: `ADR-NNNN`. Reviews: `docs/reviews/<phase>-<gate>.md`, e.g. `p1-srr.md`.
