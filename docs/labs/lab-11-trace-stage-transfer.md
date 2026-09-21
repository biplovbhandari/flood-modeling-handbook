# Lab 11: Trace a Two-Reach Stage Transfer

This lab traces a complete synthetic stage transfer from downstream reach R-200 to upstream reach R-100.
The packet supplies source identities, raster references, point mappings, wet masks, coverage, scenario identity, publication events, and storage observations.

## Prerequisites

Complete [Downstream-Stage-Aware Libraries and Stage Transfer](../05-scenario-libraries/03-downstream-stage-and-transfer.md) and [Compositing, Identity, and Provenance](../05-scenario-libraries/05-compositing-identity-and-provenance.md).
Read [MX-004](../reference/decision-code-artifact-crosswalk.md#mx-004-stage-transfer), [MX-008](../reference/decision-code-artifact-crosswalk.md#mx-008-identity), [MX-010](../reference/decision-code-artifact-crosswalk.md#mx-010-publication), [MX-011](../reference/decision-code-artifact-crosswalk.md#mx-011-materialization), [CQ-003](../reference/conflicts-and-open-questions.md#cq-003-incompatible-datums), and [CQ-008](../reference/conflicts-and-open-questions.md#cq-008-missing-provenance).

## Execution boundary

Complete the trace from the supplied text and tables.
No raster, solver, or external service is required.
Optional tools may be used only for generic arithmetic.

## Learning objectives

After completing this lab, the learner should be able to:

- verify the downstream-to-upstream topology and source-scenario binding;
- calculate interpolation weights from source-stage coordinates;
- reconstruct point water-surface elevations from depth and terrain;
- apply strict positive-depth masks and the common-wet-support rule;
- calculate interface coverage and apply the inclusive threshold;
- audit stage-aware scenario identity;
- separate publication from observed materialization; and
- distinguish a reconstructible transfer from hydraulic acceptance.

## Network and target request

| Field | Supplied value |
| --- | --- |
| Prepared network | N-1 |
| Upstream reach | R-100 |
| Immediate downstream reach | R-200 |
| Upstream model generation | R-100-M1 |
| Upstream discharge | 100 m3/s |
| Nominal downstream stage | 102.5 m |
| Nominal stage datum | VD-1 |
| Intended interface | Ten ordered points P1 through P10 |
| Minimum transfer coverage | At least 90 percent |
| Initial state | Dry start, identity INIT-DRY-1 |
| Numerical settings | NS-4 |
| Stage-transfer method | ST-2 |
| Producer build | PB-9 |

The network record explicitly identifies R-100 as immediately upstream of R-200.
No other reach participates in this transfer.

## Downstream source scenarios

The nominal target 102.5 m lies between two transfer-support stage coordinates from accepted R-200 scenarios.

| Source scenario | R-200 discharge | Transfer-support stage coordinate | Scenario generation | Model generation | Boundary family | Acceptance state |
| --- | ---: | ---: | --- | --- | --- | --- |
| Source L | 175 m3/s | 102.4 m in VD-1 | R200-Q175-G3 | R-200-M1 | Terminal normal depth | Accepted for this synthetic transfer method |
| Source U | 200 m3/s | 102.7 m in VD-1 | R200-Q200-G4 | R-200-M1 | Terminal normal depth | Accepted for this synthetic transfer method |

Both sources use the same 10 m raster grid, affine transform, bounds, horizontal reference SP-1, terrain content T-200-A, and vertical datum VD-1.
Each depth value is a vertical distance in metres above the represented terrain cell.
Nodata is -9999 for terrain and depth.
The wet rule is valid terrain, valid depth, and depth strictly greater than zero.

| Spatial property | R-100 target grid | R-200 source grid |
| --- | --- | --- |
| Resolution | 10 m square cells | 10 m square cells |
| Affine transform | \((21600,10,0,50000,0,-10)\) | \((21020,10,0,49620,0,-10)\) |
| Bounds | \([21600,48800,22400,50000]\) m | \([21020,48100,22640,49620]\) m |
| Horizontal reference | SP-1, projected metres | SP-1, projected metres |
| Terrain vertical reference | VD-1, metres | VD-1, metres |
| Depth definition | Metres above represented target terrain | Metres above represented source terrain |
| Nodata | -9999 | -9999 |

The upper-source interpolation weight is:

\[
w=\frac{102.5-102.4}{102.7-102.4}
\]

The realized target value at a common-wet point is:

\[
\eta_i^*=(1-w)\left(z_i+h_{L,i}\right)+w\left(z_i+h_{U,i}\right)
\]

## Source publication and observation

Both source generations were promoted before the R-100 request was planned.
An independent observer read these roles from final storage and found matching generations and full integrity values.

| Source | Role | Observed SHA-256 | Observation result |
| --- | --- | --- | --- |
| Source L | Scenario record | c9ae8dd687aa4013c7bb23bbbac3b9489ce7b8b323c5571aa1d9ffb0359eb3fb | Match |
| Source L | Final depth | addafd8530ff4695a29b8e45b6a8f54f919472e7b3a514ae0a4912807937f1ff | Match |
| Source L | Terrain | 1d50d412ceafcf530804bdf4c39498d42690c19d78bd486ab85d96a90dbbf1d8 | Match |
| Source U | Scenario record | f30bde14929da9dfe1752e0be54447661eff089ec4d68ff1317b4d0dd19d0299 | Match |
| Source U | Final depth | c1591d724a59d0559e76f94a000c95ecc339b8a597379c7f64d33398b95c878c | Match |
| Source U | Terrain | 1d50d412ceafcf530804bdf4c39498d42690c19d78bd486ab85d96a90dbbf1d8 | Match |

The observer also confirmed the shared transform, bounds, SP-1 reference, VD-1 terrain metadata, depth units and definition, nodata value, and scenario acceptance links.

## Transfer geometry, mappings, and masks

Every upstream interface point maps to the downstream cell with the same listed coordinate.
The mapping was range-checked before sampling, and no index lies outside either source raster.

| Point | Coordinate in SP-1 | Terrain \(z_i\) | Source L depth | Source L mask | Source U depth | Source U mask |
| --- | --- | ---: | ---: | --- | ---: | --- |
| P1 | \((22105,49005)\) m | 100.80 m | 1.50 m | Valid and wet | 1.80 m | Valid and wet |
| P2 | \((22115,49005)\) m | 100.82 m | 1.50 m | Valid and wet | 1.83 m | Valid and wet |
| P3 | \((22125,49005)\) m | 100.85 m | 1.52 m | Valid and wet | 1.85 m | Valid and wet |
| P4 | \((22135,49005)\) m | 100.87 m | 1.55 m | Valid and wet | 1.87 m | Valid and wet |
| P5 | \((22145,49005)\) m | 100.90 m | 1.57 m | Valid and wet | 1.90 m | Valid and wet |
| P6 | \((22155,49005)\) m | 100.93 m | 1.59 m | Valid and wet | 1.92 m | Valid and wet |
| P7 | \((22165,49005)\) m | 100.96 m | 1.61 m | Valid and wet | 1.94 m | Valid and wet |
| P8 | \((22175,49005)\) m | 100.98 m | 1.63 m | Valid and wet | 1.97 m | Valid and wet |
| P9 | \((22185,49005)\) m | 101.00 m | 1.65 m | Valid and wet | 2.00 m | Valid and wet |
| P10 | \((22195,49005)\) m | 101.02 m | 1.68 m | Valid and wet | 0.00 m | Valid and dry |

The common-wet mask is the intersection of the two source masks.
No value is emitted where either source is dry or nodata.
The intended-interface denominator remains all ten points.

## Complete upstream scenario identity

The canonical identity for scenario R100-Q100-S1025-U7 contains:

- prepared network N-1 and the R-100 to R-200 relationship;
- upstream model generation R-100-M1, target transform, bounds, SP-1, and VD-1;
- upstream discharge 100 m3/s and its named inflow geometry;
- nominal target 102.5 m and datum VD-1;
- source scenario generations R200-Q175-G3 and R200-Q200-G4;
- source-stage coordinates 102.4 and 102.7 m and their interpolation weights;
- all source record, depth, and terrain integrity values;
- source transforms, bounds, SP-1, VD-1, depth units and definition, nodata, and wet rules;
- transfer geometry, ordered interface points, mapping rule, and range-check rule;
- both source masks, common-wet mask, ten-point denominator, 90 percent threshold, and omitted-point reasons;
- stage-transfer method ST-2;
- initial state INIT-DRY-1;
- numerical settings NS-4; and
- producer build PB-9.

The scenario record reports nine emitted boundary values, omission of P10 because Source U is dry, minimum transferred WSE 102.400 m, and maximum transferred WSE 102.767 m.

## Upstream publication and materialization

The producer staged five required roles for upstream generation U7.
Staged verification passed at 10:00 UTC.
An atomic pointer promotion made U7 visible at 10:02 UTC.
An independent observer inspected final storage at 10:04 UTC.

| Role | Recorded SHA-256 | Observed generation | Observed SHA-256 | Result |
| --- | --- | --- | --- | --- |
| Scenario record | 625e39d506d8a774d0caeb9ad98a145385e2a55bc1073dcca5595594a221c4c2 | U7 | 625e39d506d8a774d0caeb9ad98a145385e2a55bc1073dcca5595594a221c4c2 | Match |
| Transferred boundary | b851af3fc6e3e05641f5ceff26d305ae9d309521b60d86cd452331f1b04b39d9 | U7 | b851af3fc6e3e05641f5ceff26d305ae9d309521b60d86cd452331f1b04b39d9 | Match |
| Final depth | 49215a9ebb99e1f9ed640f4be615ab3f890ee67c4e42440c9bc0e505ec94d7b7 | U7 | 49215a9ebb99e1f9ed640f4be615ab3f890ee67c4e42440c9bc0e505ec94d7b7 | Match |
| Inundation | f36c45d1cb62a67ae0df9d4edeab1afe4a92160bc4d7bb03575eb13713130ea9 | U7 | f36c45d1cb62a67ae0df9d4edeab1afe4a92160bc4d7bb03575eb13713130ea9 | Match |
| Diagnostics | 375ddad2d9d55d9d522be7e1b5e9ca129d204d94446f45c98a80635551a17157 | U7 | 375ddad2d9d55d9d522be7e1b5e9ca129d204d94446f45c98a80635551a17157 | Match |

The observer found one generation, every required role, matching integrity values, the requested scenario identity, and the nine-point boundary summary.
The packet does not provide the diagnostic contents, numerical verification, alternate-start sensitivity, independent hydraulic validation, or an acceptance decision for the R-100 result.

## Part A: Verify source binding and weights

Confirm the direction of stage information.
Calculate the upper and lower interpolation weights.
Explain why the 102.5 m nominal label is not the point field.

## Part B: Apply masks and coverage

Identify the common-wet points and the omitted point.
Calculate coverage against the intended ten-point denominator.
Apply the inclusive 90 percent rule.
State why the dry point cannot be filled from Source L alone.

## Part C: Reconstruct the transferred field

For P1 through P9, calculate both source WSE values and the interpolated target.
Compare the calculated minimum and maximum with the scenario-record summary.

## Part D: Audit identity

Group the identity fields into topology, source provenance, spatial and datum compatibility, mask and coverage policy, upstream forcing, initial state, numerical method, and producer information.
State which collision or provenance risks those groups prevent.
Do not infer that the list proves that no other output-affecting field exists.

## Part E: Separate publication, materialization, and acceptance

State what staged verification, atomic promotion, and independent observation establish separately.
Determine whether U7 satisfies the supplied materialization contract.
State why that result does not establish hydraulic acceptance.

## Part F: Issue a bounded verdict

Choose exactly one verdict.

- TRANSFER RECONSTRUCTED AND MATERIALIZED; HYDRAULIC ACCEPTANCE NOT ESTABLISHED
- STAGE-AWARE SCENARIO READY FOR UNRESTRICTED USE

State the verdict first.
Then list the smallest evidence needed for a new hydraulic acceptance review.

## Deliverable

Submit a short trace with these sections:

1. Source binding and weights.
2. Mask and coverage result.
3. Point calculation table.
4. Identity audit.
5. Publication and materialization assessment.
6. Direct bounded verdict.

## Competency criteria

The lab is complete when the answer:

- moves stage information from downstream R-200 to upstream R-100;
- calculates lower and upper weights of two-thirds and one-third;
- includes P1 through P9 in common wet support and excludes P10;
- calculates 90 percent coverage and applies the inclusive threshold correctly;
- reconstructs target WSE values from 102.400 m through 102.767 m;
- recognizes the nominal stage as an interpolation coordinate rather than a constant boundary;
- finds U7 materialized under the supplied contract;
- keeps materialization separate from scientific acceptance; and
- issues TRANSFER RECONSTRUCTED AND MATERIALIZED; HYDRAULIC ACCEPTANCE NOT ESTABLISHED.

After completing the lab, compare the reasoning with [Lab 11 Solution](solutions/lab-11-trace-stage-transfer-solution.md).
