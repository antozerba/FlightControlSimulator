# ADR-0008: docs/theory is the notation source of truth; course material stays out of git

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-10-23 |
| Deciders | Antonio Zerbato |
| Related | ADR-0005, ADR-0006, `docs/03_conventions.md` |

## Context
The algorithms follow NTNU courses (TTK4250, TTK4150, TTK4190). Mixed notation between papers, courses and code causes sign and frame bugs. Course PDFs and slides are copyrighted and must not be published in the repo.

## Decision
- **`references/`** holds course material locally and is **gitignored** (only its README is tracked). Nothing is copied verbatim from it into tracked files.
- **`docs/theory/`** holds the owner's own summaries and derivations. They are **authoritative for notation**: every algorithm implementation uses the symbols, frames and sign conventions defined there.
- `docs/03_conventions.md` must agree with `docs/theory/`. On conflict, theory wins and conventions get updated in the same change.
- Code comments reference the theory section, e.g. `// docs/theory/eskf.md §3, eq. (7)`.
- Claude may read `docs/theory/` and review it. It doesn't write the math, and it doesn't put `references/` content into tracked files.

## Alternatives considered
| Option | Why not |
|---|---|
| Commit course PDFs | Copyright |
| Use paper/library notation per algorithm | Inconsistent; the source of bugs this ADR prevents |

## Consequences
- Positive: one notation from derivation to code to test; a portfolio-quality theory write-up.
- Negative: writing time for the theory summaries (vision R1).
- Follow-ups: a CI/doc check that each `fsw/core` algorithm file cites a theory section (macro-area 0, optional).
