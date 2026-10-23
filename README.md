# FlightControlSimulator

A quadrotor flight controller written from scratch in C++ and flown on off-the-shelf STM32H7 hardware. It is developed like an aerospace project: requirements, architecture, MIL/SIL/PIL/HIL, system identification, flight test and V&V, with formal review gates.

- **Phase 1 (indoor):** IMU-only attitude estimation (Mahony) and attitude control. A cascaded PID baseline is compared against a quaternion controller with a Lyapunov stability proof.
- **Phase 2 (outdoor, EASA A3):** an error-state Kalman filter aided INS (IMU + GNSS + magnetometer + barometer), NEES/NIS consistency testing, and GNSS path following.

## Documents
- [Vision, success criteria, non-goals](docs/00_vision.md)
- [Conventions: frames, quaternions, units, naming](docs/03_conventions.md)
- [Architecture decision records](docs/adr/README.md)
- [Theory notes](docs/theory/README.md)

Status: kickoff. Build instructions will follow in macro-area 0.
