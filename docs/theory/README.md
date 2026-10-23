# docs/theory — owner's theory summaries

These files are **the authoritative notation** for all algorithms (ADR-0008). Code follows the symbols defined here, and `docs/03_conventions.md` is updated to match them.

- **Author:** Antonio. These are summaries in his own words of TTK4250, TTK4150 and TTK4190.
- **Source material:** course PDFs and slides live in `references/`. That folder is gitignored and never committed, and nothing from it is quoted verbatim here.
- Claude reviews these files for consistency and can propose tests derived from them. Claude does not write the math.

Each file starts with a **symbol table**, then derivations, assumptions and limits, and then the testable properties that SIL tests should check.

| File | Topic | Course | Phase |
|---|---|---|---|
| `mahony.md` | Nonlinear complementary filter (Mahony) | TTK4250 / lit. | 1 |
| `attitude_pid.md` | Cascaded PID attitude/rate baseline | — | 1 |
| `attitude_lyapunov.md` | Quaternion attitude controller and Lyapunov proof | TTK4150 | 1 |
| `eskf.md` | Error-state KF aided INS: propagation, aiding interface | TTK4250 | 2 |
| `aiding_models.md` | GNSS, magnetometer and barometer measurement models | TTK4250 | 2 |
| `nees_nis.md` | Consistency tests, χ² bounds | TTK4250 | 2 |
| `guidance_los.md` | Path-following guidance laws (LOS) | TTK4190 | 2 |
