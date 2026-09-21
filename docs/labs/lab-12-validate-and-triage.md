# Lab 12: Validate and Triage

This lab evaluates a synthetic R-200 validation packet with favorable evidence, failed criteria, and intentional gaps.
The exercise requires a bounded conclusion rather than a general readiness claim.

## Prerequisites

Complete [Validation Framework](../06-validation-and-qc/01-validation-framework.md), [Diagnostic Workflow](../06-validation-and-qc/02-diagnostic-workflow.md), [Sensitivity and Uncertainty](../06-validation-and-qc/03-sensitivity-and-uncertainty.md), and [Applied Evidence Catalog](../06-validation-and-qc/04-case-issue-and-experiment-catalog.md).
Read [Evidence Boundaries in Scientific Software](../07-system-design/02-current-target-and-evidence-boundaries.md), [MX-013](../reference/decision-code-artifact-crosswalk.md#mx-013-validation), [MX-014](../reference/decision-code-artifact-crosswalk.md#mx-014-sensitivity), [MX-015](../reference/decision-code-artifact-crosswalk.md#mx-015-uncertainty), [CQ-003](../reference/conflicts-and-open-questions.md#cq-003-incompatible-datums), and [CQ-009](../reference/conflicts-and-open-questions.md#cq-009-validation-gaps).

## Execution boundary

All required evidence is in this prompt.
Do not run a solver, inspect another record, or invent a missing threshold.
Optional tools may be used only for generic arithmetic.

## Learning objectives

After completing this lab, the learner should be able to:

- frame validation around a stated use and predeclared criteria;
- keep software verification, numerical verification, hydraulic validation, uncertainty, provenance, materialization, and monitoring distinct;
- calculate convergence, balance, and observation residuals;
- distinguish calibration data from independent validation data;
- preserve an unavailable result when support or provenance is missing;
- select a discriminating next check before tuning; and
- issue one bounded validation conclusion.

## Intended use and exclusions

The packet asks whether scenario R200-Q200-G10 can support screening of areas where modeled maximum depth is at least 0.30 m for prioritizing later field review.
The requested use is limited to the supplied R-200 domain, the 200 m3/s scenario, and the exact model and scenario generations in this packet.

The packet excludes structure design, regulatory mapping, evacuation decisions, property-level decisions, velocity claims, arrival-time claims, and transfer to another reach or discharge.
Every threshold below is a synthetic predeclared exercise criterion.
No threshold is presented as a universal hydraulic standard.

## Predeclared criteria

| Evidence lane | Criterion for the stated screening use |
| --- | --- |
| Identity and provenance | The exact model, scenario, source, method, producer, solver, observation, and datum-transformation identities are recorded immutably. |
| Software verification | Every named input, identity, mask, publication, and observer contract check passes on the represented producer build. |
| Convergence | The storage-change ratio is below \(10^{-3}\) for three consecutive saved intervals, and absolute local WSE change is no greater than 0.02 m at every monitoring point in the final interval. |
| Conservation | The absolute cumulative balance residual is no greater than 1.0 percent of cumulative inflow. |
| Grid comparison | The 95th percentile absolute WSE difference between the 10 m result and a 5 m comparison is no greater than 0.05 m over a predeclared common mask. |
| Edge containment | No connected wet component reaches an unintended edge. |
| Independent hydraulic comparison | Absolute WSE residual is no greater than 0.10 m at every independent validation point after compatible datum transformation. |
| Sensitivity and uncertainty | Material input, parameter, structural, numerical, referent, and operational uncertainties are represented, and tested interactions do not reverse the screening classification. |
| Materialization | The exact scenario record and every required artifact are independently observed in one generation with matching integrity values. |
| Monitoring | Drift and failure signals, thresholds, reassessment triggers, retained evidence, and response responsibility are defined for repeated use. |

## Identity and provenance packet

| Item | Supplied evidence |
| --- | --- |
| Prepared network | N-1 |
| Model generation | R-200-M2 |
| Scenario generation | R200-Q200-G10 |
| Terrain content | T-200-A with a recorded full content checksum |
| Roughness content and lookup | M-200-A and RL-3 with recorded full content checksums |
| Method records | Model method MB-2 and scenario method DS-2 |
| Producer | Readable build label SB-12; no immutable executable digest is supplied |
| Solver | Solver family and settings are listed; no immutable executable digest is supplied |
| Observations | Survey collection OBS-V2; point coordinates and source timestamps are supplied |
| Observation datum | Values are labeled VD-1 after transformation |
| Datum transformation | Method name and reported uncertainty are supplied; transformation-grid identity and parameters are absent |

## Software-verification packet

A synthetic signed report states that 18 named checks passed for build label SB-12.
The checks cover required fields, unknown-field rejection, unit validation, boundary-allocation totals, identity change under controlled input changes, dry and nodata masks, isolated staging, atomic pointer promotion, required-role observation, and integrity mismatch rejection.

The report includes the check names and expected results.
It does not include an immutable executable digest, the executed inputs and outputs, or a failure-injection result for concurrent readers during promotion.

## Numerical packet

The saved-output interval is 900 s.
The convergence history is:

| Saved time | Storage-change ratio | Point A absolute WSE change | Point B absolute WSE change |
| ---: | ---: | ---: | ---: |
| 18,000 s | 0.0013 | 0.022 m | 0.041 m |
| 18,900 s | 0.0009 | 0.015 m | 0.031 m |
| 19,800 s | 0.0007 | 0.011 m | 0.026 m |
| 20,700 s | 0.0006 | 0.008 m | 0.024 m |

The cumulative balance covers model time zero through 20,700 s.
Inflow and source volumes are positive additions.
Outflow and sink volumes are positive removals.

| \(\Delta S\) | \(V_{in}\) | \(V_{out}\) | \(V_{source}\) | \(V_{sink}\) |
| ---: | ---: | ---: | ---: | ---: |
| 752,680 m3 | 3,780,000 m3 | 3,050,000 m3 | 0 m3 | 0 m3 |

Use:

\[
R_P=100\frac{\Delta S-\left(V_{in}-V_{out}+V_{source}-V_{sink}\right)}{V_{in}}
\]

The grid-comparison summary reports a 95th percentile absolute WSE difference of 0.04 m.
The packet does not supply the raw 5 m and 10 m fields, common mask, cell population, weighting, refinement procedure, failed cells, or calculation record.

The edge summary reports no connected wet component at an unintended edge.
The packet does not supply the component map, boundary-role map, wet threshold output, or edge-scan record.

## Hydraulic comparison packet

Points C1 and C2 were used to choose the roughness lookup and are calibration points.
Points V1 and V2 were held out and are the independent validation points.

| Point | Use | Observed WSE after stated transformation | Modeled WSE | Model minus observation |
| --- | --- | ---: | ---: | ---: |
| C1 | Calibration | 102.20 m | 102.22 m | +0.02 m |
| C2 | Calibration | 102.48 m | 102.44 m | -0.04 m |
| V1 | Independent validation | 102.44 m | 102.49 m | +0.05 m |
| V2 | Independent validation | 101.98 m | 102.12 m | +0.14 m |

All four values are labeled VD-1.
The transformation-grid identity and transformation parameters are missing as stated in the provenance packet.
No independent depth, extent, velocity, arrival-time, or structure observation is supplied.

## Sensitivity and uncertainty packet

| Source | Representation | Supplied result |
| --- | --- | --- |
| Discharge | Two alternatives at -8 percent and +8 percent | V2 WSE changes by -0.05 m and +0.06 m. |
| Downstream stage | Two alternatives at -0.10 m and +0.10 m | V2 WSE changes by -0.08 m and +0.09 m. |
| Terrain elevation | Stated uncertainty of plus or minus 0.15 m | No sensitivity run is supplied. |
| Roughness | Lookup RL-3 was calibrated with C1 and C2 | No held-out roughness sensitivity is supplied. |
| Grid | One reported 5 m comparison | Raw fields and support are missing. |
| Initial state | Dry-start result only | No alternate-start result is supplied. |
| Factor interactions | Discharge, stage, terrain, roughness, grid, and initial state | No interaction run is supplied. |
| Observation datum transformation | Reported uncertainty of 0.04 m | Transformation identity and parameters are missing. |
| Operational completeness | Materialization observation supplied | No stale-source or deleted-artifact drill is supplied. |

The discharge and stage alternatives are scenario sensitivities.
The packet supplies no probability distributions and does not authorize combining the values into one probabilistic uncertainty interval.

## Materialization and monitoring packet

An independent observer found the scenario record, final depth, maximum depth, inundation, diagnostics, and integrity inventory in generation R200-Q200-G10.
All recorded and observed integrity values match.
The observation time, required roles, generation, model identity, and scenario identity are recorded.

The repeated-operation monitor counts completed scenarios, runtime, and materialization failures.
It has no threshold for source drift, residual distribution, local WSE behavior, edge contact, screening-area change, or validation drift.
No reassessment trigger or response responsibility is assigned.

## Part A: Frame the evidence decision

Restate the intended use, exact quantity, scope, excluded uses, and evidence cutoff.
Explain why the conclusion cannot be generalized to another discharge, reach, quantity, or decision.

## Part B: Audit each evidence lane

Classify every predeclared lane as Passed, Failed, Insufficient evidence, or Out of scope.
Use only the supplied packet.
Do not give a passing status to a reported summary whose required support is absent.

## Part C: Perform the calculations

Determine whether the global and local convergence criteria pass.
Calculate the signed cumulative balance residual and percentage.
Calculate the independent validation residuals and compare them with the predeclared criterion.

## Part D: Assess validation and uncertainty

Explain why C1 and C2 cannot serve as independent validation evidence.
Interpret the V1 and V2 result within the missing datum-transformation provenance.
Classify each uncertainty entry as quantified sensitivity, stated but unevaluated uncertainty, or missing interaction evidence.

## Part E: Triage the V2 high result

Define at least three competing hypotheses for the +0.14 m V2 residual.
Include:

1. A datum, transformation, coordinate, or sampling-support hypothesis.
2. A residual transient or numerical-resolution hypothesis.
3. A physically real downstream-control, terrain, roughness, or forcing hypothesis under correctly mapped inputs.

Choose one immediate read-only check that best separates those hypotheses.
Do not tune roughness or another parameter before the check.

## Part F: Issue the validation conclusion

Choose one conclusion from [Validation Framework](../06-validation-and-qc/01-validation-framework.md):

- MEETS THE STATED CRITERIA
- MEETS THE STATED CRITERIA WITH RESTRICTIONS
- DOES NOT MEET THE STATED CRITERIA
- INSUFFICIENT EVIDENCE TO ASSESS

State the conclusion first.
Then identify the failed criteria, unavailable evidence, permitted statements, prohibited uses, and smallest packet needed for another review.

## Deliverable

Submit a short review with these sections:

1. Intended use and scope.
2. Evidence-lane table.
3. Numerical calculations.
4. Validation and uncertainty assessment.
5. V2 hypothesis table and next check.
6. Direct validation conclusion.

## Competency criteria

The lab is complete when the answer:

- keeps the seven evidence types distinct;
- finds three consecutive global ratios below \(10^{-3}\) but fails the local Point B criterion;
- calculates a signed cumulative balance residual of +22,680 m3 and +0.60 percent;
- excludes C1 and C2 from independent validation;
- finds V1 within and V2 outside the synthetic residual criterion;
- withholds grid and edge passes because their support is missing;
- identifies provenance, interaction, monitoring, and responsibility gaps;
- selects a read-only comparability and raw-support check before tuning; and
- concludes DOES NOT MEET THE STATED CRITERIA without extending the result beyond the supplied use.

After completing the lab, compare the reasoning with [Lab 12 Solution](solutions/lab-12-validate-and-triage-solution.md).
