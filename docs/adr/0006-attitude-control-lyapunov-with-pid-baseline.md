# ADR-0006: Lyapunov quaternion attitude controller, with cascaded PID baseline

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-10-23 |
| Deciders | Antonio Zerbato |
| Related | ADR-0005, vision §3; theory: `docs/theory/attitude_pid.md`, `attitude_lyapunov.md` |

## Context
The owner wants a nonlinear attitude controller with a formal stability argument (TTK4150), plus a classical baseline to compare it against. Phase 1 budget is ~240 h, and two flown controllers don't fit inside it (vision R1).

## Decision
- **Baseline:** cascaded PID (attitude-angle outer loop → body-rate inner loop). **Flown first. Phase 1 floor.**
- **Primary controller:** quaternion attitude controller with a **Lyapunov stability proof** written by the owner in `docs/theory/attitude_lyapunov.md`. **Phase 1 target**, which may slip to May–Jun 2027.
- Both implement the same controller interface (attitude/rate reference + estimate → body torque/thrust demand) and are selectable by parameter **only while disarmed**.
- The proof's assumptions (e.g. actuator limits, estimate error bounds, unwinding handling) become SIL test cases and are listed in the theory doc.
- The comparison uses the same manoeuvre set and metrics on both controllers, in SIL and in flight.

## Alternatives considered
| Option | Why not |
|---|---|
| Lyapunov controller as floor, PID sim-only | First flights on the less-proven controller; rejected by owner |
| LQR as primary | Linearisation-based; less aligned with the TTK4150 learning goal (could be added as a third comparison later) |
| INDI | Non-goal (vision §7) |

## Consequences
- Positive: a guaranteed flying baseline; a nonlinear-control showcase with proof and hardware comparison.
- Negative: two controllers to test and tune; switching logic needs a safety review (hazard analysis).
- Follow-ups: `CTL-xxx` requirements; manoeuvre set definition; hazard entry for controller switching.
