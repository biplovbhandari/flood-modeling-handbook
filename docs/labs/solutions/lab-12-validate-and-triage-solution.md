# Lab 12 Solution: Validate and Triage

This solution evaluates only the packet in [Lab 12](../lab-12-validate-and-triage.md).
It applies the bounded evidence rules in [Validation Framework](../../06-validation-and-qc/01-validation-framework.md) and [Evidence Boundaries in Scientific Software](../../07-system-design/02-current-target-and-evidence-boundaries.md).

## 1. Intended use and scope

The decision is whether the exact R200-Q200-G10 generation can screen areas with modeled maximum depth of at least 0.30 m for prioritizing later field review.
The scope is one R-200 model generation, one 200 m3/s scenario, one domain, and the supplied maximum-depth quantity.

The evidence cutoff is the software report, convergence and balance tables, grid and edge summaries, four WSE comparisons, sensitivity table, provenance table, materialization observation, and monitoring description in the prompt.
No result transfers automatically to another reach, discharge, model generation, quantity, or decision.
The excluded design, regulatory, evacuation, property, velocity, arrival-time, and cross-reach uses remain prohibited.

## 2. Evidence-lane table

| Evidence lane | Classification | Reason |
| --- | --- | --- |
| Identity and provenance | Insufficient evidence | Source and method records are named, but immutable producer and solver digests plus the datum-transformation grid and parameters are missing. |
| Software verification | Insufficient evidence | A signed summary lists eighteen passing checks, but the exact executable identity and executed inputs and outputs are unavailable. |
| Convergence | Failed | Three global ratios pass, but Point B exceeds the local 0.02 m criterion in the final interval. |
| Conservation | Passed for the stated cumulative calculation | The supplied complete terms give an absolute residual of 0.60 percent, below 1.0 percent. |
| Grid comparison | Insufficient evidence | The favorable 0.04 m summary lacks raw fields, the common mask, population, weighting, refinement procedure, failures, and a calculation record. |
| Edge containment | Insufficient evidence | The no-contact summary lacks the component map, boundary roles, wet-threshold output, and scan record. |
| Independent hydraulic comparison | Failed | V1 passes, but V2 has a +0.14 m residual against a 0.10 m criterion, the transformation provenance is incomplete, and no independent depth or extent observation directly tests the screening quantity. |
| Sensitivity and uncertainty | Insufficient evidence | Two one-factor sensitivities are supplied, but terrain, roughness, initial-state, grid-support, interaction, referent, and operational evidence is incomplete. |
| Materialization | Passed for the supplied storage contract | The observer found one exact generation and every required role with matching integrity values. |
| Monitoring | Failed | The monitor omits scientific drift thresholds, reassessment triggers, retained scientific evidence, and response responsibility. |

The materialization pass cannot compensate for the failed or unavailable scientific lanes.
The conservation pass is bounded to the supplied cumulative terms and does not replace convergence, grid, edge, or validation evidence.

## 3. Numerical calculations

### Convergence

The final three storage-change ratios are 0.0009, 0.0007, and 0.0006.
All three are below \(10^{-3}\), so the global consecutive-ratio condition passes.

At the final interval, Point A changes by 0.008 m and passes the 0.02 m local criterion.
Point B changes by 0.024 m and fails it.
The combined convergence criterion therefore fails.

### Cumulative balance

The recorded net input is:

\[
3780000-3050000+0-0=730000\ \text{m3}
\]

The signed residual is:

\[
752680-730000=22680\ \text{m3}
\]

The percentage is:

\[
100\frac{22680}{3780000}=0.60\%
\]

The positive sign means storage increased by 22,680 m3 more than the recorded net input accounts for.
The absolute 0.60 percent result passes the synthetic 1.0 percent cumulative criterion.
It does not explain the residual or establish local steady behavior.

### Independent WSE comparison

V1 has a +0.05 m residual and passes the absolute 0.10 m criterion.
V2 has a +0.14 m residual and fails it.
The independent hydraulic comparison therefore fails even before considering the missing transformation-grid provenance.

## 4. Validation and uncertainty assessment

C1 and C2 influenced selection of RL-3.
They are calibration evidence and cannot be counted again as independent validation evidence.
Their small residuals show agreement under the tuning use only.

V1 and V2 were held out, so they are the independent comparisons in the supplied packet.
Their values are labeled VD-1, but the missing transformation-grid identity and parameters prevent an independent comparability audit.
That gap does not turn V2 into a pass.
It means the cause and exact interpretation of its failure remain unresolved.
Even a passing WSE comparison would not directly validate the 0.30 m maximum-depth classification or mapped extent without comparable depth or extent evidence.

The uncertainty entries classify as follows.

| Entry | Classification | Evidence limit |
| --- | --- | --- |
| Discharge alternatives | Quantified one-factor sensitivity | The values are not probabilities and do not include interactions. |
| Downstream-stage alternatives | Quantified one-factor sensitivity | The values are not probabilities and do not include interactions. |
| Terrain plus or minus 0.15 m | Stated but unevaluated uncertainty | No response calculation is supplied. |
| Roughness after calibration | Missing held-out sensitivity | Calibration agreement does not provide independent robustness evidence. |
| Grid comparison | Reported but unsupported numerical summary | Raw fields and common support are missing. |
| Initial state | Missing sensitivity | Only a dry start is supplied. |
| Factor interactions | Missing interaction evidence | Separate discharge and stage changes cannot establish joint behavior. |
| Datum-transformation uncertainty | Stated but incompletely supported referent uncertainty | The transformation identity and parameters are missing. |
| Stale-source or deleted-artifact behavior | Missing operational uncertainty evidence | No drill or response result is supplied. |

The packet does not support combining these entries into one probabilistic interval.
[MX-015](../../reference/decision-code-artifact-crosswalk.md#mx-015-uncertainty) requires their meanings and dependencies to remain distinct.

## 5. V2 hypothesis table and next check

| Hypothesis | Expected observation if supported | Expected observation if challenged |
| --- | --- | --- |
| Datum, transformation, coordinate, or support mismatch | Reconstructing the transformation and identical sampling support changes the V2 observed or modeled value materially. | Immutable transformation and same-support sampling reproduce the +0.14 m residual. |
| Residual transient or numerical-resolution effect | The local time history has not settled, or the supported 5 m and 10 m values differ materially at V2. | Local history is stable and the supported refinement result agrees at V2. |
| Physically or numerically real control under correctly mapped inputs | The residual persists after comparability and numerical checks and changes coherently under the relevant downstream-stage, terrain, roughness, or forcing sensitivity. | The residual disappears when mapping or numerical support is corrected. |

The immediate check is a read-only V2 comparability and raw-support audit.
It should obtain the immutable datum transformation, parameters, observation coordinate and support, raw observation, exact model sampling rule, raw 10 m and 5 m values on the same support, and the Point B time history.

This check first determines whether the compared quantities occupy the same location, datum, and support.
If they do, the raw refinement and time history separate a numerical explanation from a persistent physical or input-control explanation.
It is more discriminating than changing roughness because tuning could hide a mapping or transient problem.

## 6. Direct validation conclusion

**DOES NOT MEET THE STATED CRITERIA.**

The conclusion follows from two explicit failures.
Point B exceeds the final local-change criterion, and V2 exceeds the independent WSE residual criterion.
Identity, software execution, grid support, edge support, uncertainty interactions, and repeated-use monitoring also remain insufficient.

The packet supports narrower statements:

- the exact artifact set was observed under the supplied materialization contract;
- the cumulative balance calculation gives +0.60 percent;
- the final three global storage-change ratios are below \(10^{-3}\);
- V1 lies within the synthetic residual threshold; and
- the supplied one-factor discharge and stage alternatives quantify limited sensitivities at V2.

Those statements do not authorize the requested screening use.
Every excluded use remains prohibited.

The smallest packet for another review includes:

1. Immutable producer, solver, observation, and datum-transformation provenance.
2. Executed software-verification records tied to the immutable build.
3. Resolved Point B local behavior and raw grid-refinement evidence on a predeclared common mask.
4. A reproducible component-level edge scan.
5. Resolution of the V2 residual through the selected comparability check and any resulting hydraulic diagnosis.
6. Terrain, roughness, initial-state, grid, and material interaction evidence.
7. Monitoring thresholds, reassessment triggers, evidence retention, and assigned response responsibility.
8. A new application of the predeclared criteria to the complete packet.

Additional evidence creates a basis for another decision.
It does not predetermine that decision.
