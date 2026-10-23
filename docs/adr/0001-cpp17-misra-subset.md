# ADR-0001: C++17 with a MISRA-inspired subset

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-10-23 |
| Deciders | Antonio Zerbato |
| Related | ADR-0003 |

## Context
The flight software must run deterministically on an STM32H7 and also build on the host for SIL. The owner knows C++ well and targets aerospace GNC roles, where MISRA C++ / AUTOSAR C++14 / JSF-style subsets are the norm. A full MISRA certification toolchain is out of budget.

## Decision
- Language: **C++17**. Compilers: GCC (`arm-none-eabi`) for the target; GCC/Clang for the host.
- **No dynamic allocation after initialisation.** No `new`, `malloc` or allocating STL containers in flight code. Static or init-time storage only.
- **No exceptions, no RTTI** (`-fno-exceptions -fno-rtti` on target, and also enforced on the host for `fsw/core`). Errors are returned as status enums or a small `Result<T>` type.
- **Fixed-size containers only**: `std::array`, an in-house `StaticVector<T, N>` or ring buffer, Eigen fixed-size types (if Eigen is adopted, see Follow-ups).
- No recursion, no `goto`. Bounded loops in flight code.
- Static analysis: clang-tidy + cppcheck in CI, with a written rule list in `docs/` (macro-area 0). Deviations are annotated inline with a justification.
- `fsw/core` must compile warning-free with `-Wall -Wextra -Wpedantic -Wconversion -Wshadow`.

## Alternatives considered
| Option | Why not |
|---|---|
| C (C99/C11) | Weaker type safety; templates and `constexpr` help with fixed-size math and units |
| C++20 | Toolchain support on embedded is uneven; concepts are not needed yet |
| Rust (embedded) | Good fit technically, but off the owner's career path (C++ dominates aerospace GNC) and adds a learning axis on top of embedded |
| Full MISRA C++:2023 checker | Commercial tools are out of budget; a documented subset is enough for the portfolio story |

## Consequences
- Positive: deterministic memory and timing; the same code runs in SIL and on target; credible industry practice.
- Negative: no `std::vector`/`std::string`/`std::function` in core; more boilerplate for error handling.
- Follow-ups: ADR on **Eigen (fixed-size, `EIGEN_NO_MALLOC`) vs. an in-house math library**; coding-standard doc + `.clang-tidy` in macro-area 0.
