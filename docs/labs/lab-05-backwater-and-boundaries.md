# Lab 5: Backwater and Boundaries

This lab compares normal-depth and specified-stage downstream controls through a complete synthetic profile packet.
It then applies the comparison to river, confluence, lake, coastal, and structure-controlled contexts.

## Prerequisites

Complete these chapters before starting:

- [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md)
- [Backwater and Boundary Control](../02-open-channel-flow/04-backwater-and-boundary-control.md)

Read [Lab Conventions](README.md), [Source Authority](../reference/source-authority.md), [Glossary](../reference/glossary.md), and [Equations and Units](../reference/equations-and-units.md).

## Learning objectives

After completing this lab, the learner should be able to:

- calculate a normal-depth reference stage and section-scale Froude number;
- compare two downstream controls from aligned profile evidence;
- distinguish normal-depth outflow, specified stage, imposed discharge, closed edges, transferred stage, and initial conditions;
- predict backwater direction under stated subcritical assumptions; and
- make conditional boundary recommendations without converting context into a universal rule.

## Synthetic reach packet

Use this rectangular reach:

| Quantity | Value |
| --- | ---: |
| Width, \(b\) | 12.0 m |
| Discharge, \(Q\) | 50.0 m3/s |
| Manning roughness, \(n\) | 0.035 s/m^(1/3) |
| Friction slope, \(S_f\) | 0.0010 m/m |
| Downstream bed elevation, \(z_b\) | 100.00 m in datum `VD-1` |
| Calculated normal depth, \(y_n\) | 2.934 m |
| Candidate specified downstream WSE | 103.80 m in `VD-1` |
| Gravitational acceleration, \(g\) | 9.81 m/s2 |

Assume steady, gradually varied, subcritical flow in a prismatic section for the profile interpretation.
The learner must verify the normal-depth WSE, stage difference, mean velocity, hydraulic depth, and Froude number.

## Synthetic profile and run-evidence packet

Both profiles use the same geometry, roughness, discharge, reach length, and initial water state.
The normal-depth profile uses a downstream normal-depth relationship with \(S_f=0.0010 \text{m/m}\).
The specified-stage profile imposes \(WSE=103.80 \text{m VD-1}\) at the downstream boundary.

| Distance upstream from boundary | Bed elevation | Normal-depth profile WSE | Specified-stage profile WSE |
| ---: | ---: | ---: | ---: |
| 0 m | 100.000 m VD-1 | 102.934 m VD-1 | 103.800 m VD-1 |
| 250 m | 100.250 m VD-1 | 103.184 m VD-1 | 103.880 m VD-1 |
| 500 m | 100.500 m VD-1 | 103.434 m VD-1 | 104.000 m VD-1 |
| 1,000 m | 101.000 m VD-1 | 103.934 m VD-1 | 104.280 m VD-1 |
| 2,000 m | 102.000 m VD-1 | 104.934 m VD-1 | 105.060 m VD-1 |
| 4,000 m | 104.000 m VD-1 | 106.934 m VD-1 | 106.950 m VD-1 |

The run record supplies these observations:

| Evidence item | Normal-depth profile | Specified-stage profile |
| --- | --- | --- |
| Termination | Numerical profile tolerance reached | Numerical profile tolerance reached |
| Last-iteration maximum WSE change | 0.0007 m | 0.0009 m |
| Maximum discharge residual relative to 50.0 m3/s | 0.12 percent | 0.15 percent |
| Unexpected wet boundary locations | None reported | None reported |
| Observation comparison | Not supplied | Not supplied |
| Geometry, roughness, and boundary sensitivity | Not supplied | Not supplied |

The table contains the complete profile and run evidence available for this exercise.
It does not establish physical validity or an acceptable boundary for a real reach.

## Synthetic boundary-choice contexts

| Context | Evidence supplied | Missing evidence |
| --- | --- | --- |
| Ordinary river reach | No observed downstream stage is available, and the boundary can be placed 8 km below the interpretation area. | Energy-slope evidence, geometry continuity, roughness calibration, and boundary sensitivity. |
| Tributary at a confluence | A datum-compatible downstream profile gives 104.60 m VD-1 at the tributary outlet, while local normal depth gives 103.10 m VD-1. | Time pairing, transfer geometry, source identity, uncertainty, and observed confluence WSE. |
| Lake outlet | Observed lake WSE ranges from 106.00 to 106.40 m VD-1, while a normal-depth estimate gives 103.00 m VD-1. | Operations, wind setup, bathymetry, time support, and connection geometry. |
| Coastal reach | A datum-compatible tide-and-surge series ranges from 102.20 to 104.00 m VD-1, and the river and coastal peaks may be offset by 4 h. | Joint timing, wave scope, density effects, event validation, and time-series boundary support are missing, so the direction of error from a fixed-stage or normal-depth substitute is indeterminate. |
| Structure-controlled reach | A gated structure sits at the outlet. | Gate opening, crest geometry, submergence, operating rule, and stage-discharge evidence. |

## Part A: Calculate the reference stages

**Core inspection:** Calculate:

1. Normal-depth WSE as \(z_b+y_n\).
2. The candidate specified-stage WSE minus normal-depth WSE.
3. Hydraulic area at \(y_n\).
4. Mean velocity.
5. Hydraulic depth.
6. Section-scale Froude number.

Show all arithmetic, units, assumptions, and datum labels.
Under the stated subcritical approximation, predict the direction of downstream profile change when the higher specified stage is imposed.

## Part B: Compare the two profiles

**Core inspection:** At every station, calculate the specified-stage WSE minus the normal-depth-profile WSE.
Identify the largest and smallest differences.
Describe how the difference changes with distance upstream.
State the farthest supplied station at which the difference exceeds 0.10 m.
Explain what the profile supports about upstream influence and what it cannot establish beyond 4 km.

## Part C: Separate numerical completion from hydraulic adequacy

**Core inspection:** Create one row for termination, last-iteration change, discharge residual, edge observation, sensitivity, and observation comparison.
For each row, state what the packet supports and what remains missing.
Explain why two numerically completed profiles can still leave the boundary choice unresolved.
Use [CQ-005](../reference/conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence) to check the evidence boundary.

## Part D: Compare boundary concepts

Complete this table.

| Concept | Prescribed quantity or relationship | Continues through the calculation? | Can directly control downstream state? | Failure if misapplied |
| --- | --- | --- | --- | --- |
| Imposed discharge |  |  |  |  |
| Specified stage |  |  |  |  |
| Normal-depth outflow |  |  |  |  |
| Closed edge |  |  |  |  |
| Transferred stage |  |  |  |  |
| Initial condition |  |  |  |  |

Explain why the phrase free outflow is not a complete mathematical boundary specification.
Explain why an initial condition does not replace a continuing downstream boundary.
Use [MX-001](../reference/decision-code-artifact-crosswalk.md#mx-001-boundary-conditions) and [CQ-001](../reference/conflicts-and-open-questions.md#cq-001-terminology-behavior-mismatch).

## Part E: Make conditional context decisions

**Core inspection:** For each of the five contexts, identify:

1. The best-supported boundary candidate from the supplied evidence.
2. At least one plausible alternative.
3. The likely direction of error from a poorly supported normal-depth, specified-stage, transferred-stage, or closed condition.
4. The evidence that prevents operational use.
5. One sensitivity comparison that would discriminate among candidates.

Use conditional language.
Do not present one answer as a universal prescription for every river, confluence, lake, coast, or structure.
Use [CQ-003](../reference/conflicts-and-open-questions.md#cq-003-incompatible-datums) when stage evidence depends on vertical-reference compatibility.

## Part F: Apply the five evidence labels

Classify each statement as **Scientific foundation**, **Applied example**, **Design principle**, **Evidence note**, or **Open question**.

1. A downstream control can influence an upstream subcritical profile.
2. The specified-stage profile is 0.866 m above the normal-depth profile at the supplied downstream station.
3. Boundary records should preserve quantity, geometry, value, units, datum, and time support.
4. Both synthetic profiles reached their stated numerical tolerance, but neither was compared with observations.
5. The correct boundary for the structure-controlled reach is unresolved.

## Deliverable

Submit a short answer with these sections:

1. Reference-stage and Froude calculations.
2. Profile-difference table and backwater interpretation.
3. Numerical-evidence assessment.
4. Boundary-concept comparison.
5. Five context decisions and sensitivities.
6. Evidence-label table and readiness statements.

## Competency criteria

The lab is complete when the answer:

- calculates normal-depth WSE as \(102.934 \text{m VD-1}\);
- calculates a 0.866 m downstream stage difference;
- calculates \(Fr\approx0.265\);
- identifies a difference greater than 0.10 m through the supplied 2,000 m station;
- distinguishes numerical termination from hydraulic adequacy;
- distinguishes all six boundary and initial-condition concepts;
- avoids universal prescriptions for the five contexts; and
- identifies every context as unready for operational use from the supplied evidence alone.

After completing the lab, compare the reasoning with [Lab 5 Solution](solutions/lab-05-backwater-and-boundaries-solution.md).

## Source notes

- **Scientific foundation:** Flow-regime boundary guidance is supported by [SCI-022](../reference/bibliography.md#sci-022-hec-ras-flow-regime-boundary-guidance).
- **Scientific foundation:** Normal-depth computations are supported by [SCI-023](../reference/bibliography.md#sci-023-hec-ras-uniform-flow-computations).
- **Scientific foundation:** Downstream boundary concepts are supported by [SCI-024](../reference/bibliography.md#sci-024-hec-ras-downstream-boundary-conditions) and [SCI-026](../reference/bibliography.md#sci-026-hec-ras-2d-external-boundary-conditions).
- **Evidence note:** The reach, profiles, run observations, and context packets are constructed teaching material.
