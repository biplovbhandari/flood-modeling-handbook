# Lab 5: Backwater and Boundaries

This lab asks the learner to compare boundary choices for a normal river reach, confluence, lake outlet, coastal reach, and structure-controlled reach.
All numerical and site descriptions are synthetic instructional givens outside the project authority hierarchy.
They do not establish project policy or describe a real reach.

## Prerequisites

Complete these chapters before starting:

- [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md)
- [Backwater and Boundary Control](../02-open-channel-flow/04-backwater-and-boundary-control.md)

Read [Source Authority](../reference/source-authority.md), [Conflicts and Open Questions](../reference/conflicts-and-open-questions.md), and [Lab Conventions](README.md).

## Execution boundary

All required steps are **Core inspection**.
No command or production access is required, and any independent tooling remains optional and user-run without changing the evidence boundary.

## Learning objectives

After completing this lab, the learner should be able to:

- compare imposed discharge, imposed WSE, slope-based outflow, closed edges, transfer, and initial conditions;
- calculate a normal-depth reference and compare it with a known stage;
- predict the direction of backwater influence under stated subcritical assumptions;
- choose a conditional boundary candidate without converting context into a universal prescription; and
- preserve the authority boundaries among scientific foundation, decisions, current code, and open questions.

## Synthetic instructional givens

### Shared normal-reach calculation

Use the synthetic rectangular reach from the Manning chapter:

- width \(b=12.0\ \text{m}\);
- discharge \(Q=50.0\ \text{m3/s}\);
- Manning's \(n=0.035\ \text{s/m}^{1/3}\);
- friction slope \(S_f=0.0010\ \text{m/m}\);
- downstream bed elevation \(z_b=100.00\ \text{m}\) in synthetic datum `SYN-1`; and
- candidate known downstream WSE \(=103.80\ \text{m SYN-1}\).

The calculated normal depth is \(2.934\ \text{m}\), but the learner must verify the resulting normal-depth WSE, stage difference, mean velocity, and Froude number.

### Boundary-choice contexts

| Context | Synthetic evidence provided | Missing evidence that remains |
| --- | --- | --- |
| Ordinary river reach | No observed downstream stage is available. The downstream boundary can be placed 8 km below the interpretation area. Surveyed geometry is prismatic over only the final 500 m. | Energy-slope evidence over the full adjustment length, roughness calibration, and boundary-sensitivity results. |
| Tributary at a confluence | A compatible downstream model reports mainstem WSE \(104.60\ \text{m SYN-1}\) at the tributary outlet for the same nominal time and a related flow scenario. The tributary's local normal-depth WSE estimate is \(103.10\ \text{m SYN-1}\). | Scenario-pairing rule, timing tolerance, transfer geometry, asset identity, and observed confluence WSE. |
| Lake outlet | Lake observations range from \(106.00\) to \(106.40\ \text{m SYN-1}\) during the modeled period. A slope-based estimate gives \(103.00\ \text{m SYN-1}\). | Outlet operations, wind setup, bathymetry, temporal sampling, and verified connection geometry. |
| Coastal reach | A datum-compatible tide-and-surge series ranges from \(102.20\) to \(104.00\ \text{m SYN-1}\). The river peak and coastal peak may be offset by 4 hours. | Joint timing, wave effects, salinity assumptions, observed event validation, and implementation support for the intended time series. |
| Structure-controlled reach | A gated structure sits at the outlet. Gate opening, crest geometry, submergence, and operations are unavailable. | The structure's stage-discharge relation and operating state. |

## Part A: Calculate the two reference stages

**Core inspection:** For the shared normal reach:

1. Calculate normal-depth WSE as \(z_b+y_n\).
2. Subtract normal-depth WSE from the candidate known WSE.
3. Calculate hydraulic area, mean velocity, hydraulic depth, and section-scale Froude number at \(y_n=2.934\ \text{m}\).
4. Under the stated subcritical approximation, predict the direction of profile change and influence if the 103.80 m known WSE is imposed.

Show all arithmetic, units, assumptions, and datum labels.

## Part B: Compare boundary concepts

**Core inspection:** Complete this comparison.

| Concept | Prescribed quantity or relationship | Continues throughout simulation? | Can directly act as downstream control? | Failure if misapplied |
| --- | --- | --- | --- | --- |
| Imposed discharge | ? | ? | ? | ? |
| Imposed WSE or known stage | ? | ? | ? | ? |
| Slope-based or normal-depth outflow | ? | ? | ? | ? |
| Closed edge | ? | ? | ? | ? |
| Transfer boundary | ? | ? | ? | ? |
| Initial condition or hot start | ? | ? | ? | ? |

Explain why free outflow is not a complete boundary specification.
Explain why an initial condition does not replace a continuing downstream boundary condition.

## Part C: Make conditional context decisions

**Core inspection:** For each of the five contexts, identify:

1. The best-supported boundary candidate from the supplied synthetic evidence.
2. At least one plausible alternative.
3. The expected direction of bias or failure if a poorly supported normal-depth, known-stage, transfer, or closed condition is used.
4. The missing evidence that prevents production use.
5. One sensitivity comparison that would discriminate among the candidates.

Use conditional language.
Do not present one answer as a universal prescription for every confluence, lake, coast, or structure.

## Part D: Map project words to current behavior

**Core inspection:** Use these local sources:

- [DR-003](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-decision-record/02_Decisions/DR-003%20-%20Where%20and%20What%20Boundary%20Conditions%20to%20Apply%20Along%20the%20Edge%20Cells%20of%20a%20KWSE%20Run.md)
- [DR-039](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-decision-record/02_Decisions/DR-039%20-%20Where%20and%20What%20Boundary%20Conditions%20to%20Apply%20Along%20the%20Edge%20Cells%20of%20a%20Normal%20Depth%20Run.md)
- [`RunKWSEScenariosJob`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/run_kwse_scenarios.py)
- [`RunNDScenariosJob`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/run_nd_scenarios.py)
- [Boundary-condition models](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/solvers.py)

Complete a source-boundary trace for:

- DR-003 freefall and its registered status;
- DR-039 ALT-F and its missing registered status;
- current `FREE` schema semantics;
- current ND slope derivation;
- current KWSE `TRANSFER` point selection, the role of nominal `bc_value`, and the treatment of dry, zero-WSE, or negative-WSE source cells; and
- current KWSE slope value of 0.5 m/m over one contiguous span per touched cardinal edge, including dry gaps between disjoint polygon-edge intersections.

Label each claim as **Selected methodology**, **Current implementation**, or **Open question**.
Do not silently standardize `FREE`, freefall, steep-slope outflow, and normal depth.
Keep the difference between the contiguous current span and DR-003's downstream-FIM-informed edge-cell wording open and unvalidated.

## Part E: Readiness review

**Core inspection:** Write a short readiness statement for each context.
Each statement must name the remaining physical evidence, implementation evidence, and validation evidence.
State whether the boundary is ready, conditionally supportable for a learning comparison, or blocked for production use.

## Deliverable

Submit a short answer with these sections:

1. Reference-stage and Froude calculations.
2. Boundary-concept comparison.
3. Five context decisions and sensitivities.
4. Project terminology and behavior trace.
5. Readiness statements.

## Competency criteria

The lab is complete when the answer:

- calculates normal-depth WSE \(102.934\ \text{m SYN-1}\), a 0.866 m raised-stage difference, and \(Fr\approx0.265\);
- predicts a deeper downstream profile with possible upstream influence under the stated subcritical assumptions;
- distinguishes all six boundary and initial-condition concepts;
- avoids universal prescriptions for the five contexts;
- preserves DR-003, DR-039, `FREE`, transfer, and steep-slope evidence scopes; and
- identifies every context as unready for production from the synthetic evidence alone.

After completing the lab, compare the reasoning with [Lab 5 Solution](solutions/lab-05-backwater-and-boundaries-solution.md).
