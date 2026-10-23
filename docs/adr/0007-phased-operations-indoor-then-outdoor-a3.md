# ADR-0007: Phased operations — indoor first, then outdoor under EASA open A3

| Field | Value |
|---|---|
| Status | Accepted |
| Date | 2026-10-23 |
| Deciders | Antonio Zerbato |
| Related | ADR-0002, vision §5 |

## Context
Phase 1 (IMU-only hover) doesn't need GNSS and suits indoor flight in Trondheim's winter. Phase 2 (GNSS aided INS, path following) must fly outdoors. Norway and Italy both apply EASA UAS rules. A privately built drone over 250 g without a class mark is limited to the open category, subcategory A3.

## Decision
- **Phase 1: indoor only** (hall/gym/lab), with written owner permission, guarded props, and tethered/restrained first flights.
- **Phase 2: outdoor, EASA open category A3 only.** No flight until the **regulatory gate** is complete:
  1. Operator registration in the country of principal residence (Luftfartstilsynet or ENAC/d-flight) **[TO CONFIRM]**
  2. A1/A3 online training and exam passed
  3. Operator ID on the vehicle
  4. Insurance requirements checked
  5. Field meets A3: ≥ 150 m from residential/commercial/industrial/recreational areas, no uninvolved people, VLOS, ≤ 120 m AGL, local airspace restrictions checked (e.g. near Værnes)
- The phase 2 TRR checks the gate. Missing items = no-go.
- Outdoor-specific failsafes (geofence/max distance, GNSS loss, low battery) are added in the hazard analysis before the phase 2 CDR.

## Alternatives considered
| Option | Why not |
|---|---|
| Outdoor from the start | Winter, regulatory overhead before any flying; not needed for IMU-only hover |
| Indoor only, ever | GNSS aiding is impossible indoors |
| Specific category | Operational authorisation effort far beyond scope |

## Consequences
- Positive: fast start indoors; regulatory work done well before it's needed.
- Negative: two test environments and two sets of procedures; outdoor weather risk in summer windows.
- Follow-ups: `REG-xxx` and `SAF-xxx` requirements; flight test card template for each environment.
