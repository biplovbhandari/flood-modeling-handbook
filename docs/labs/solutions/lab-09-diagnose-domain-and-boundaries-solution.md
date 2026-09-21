# Lab 9 Solution: Diagnose Domain and Boundaries

This solution uses only the packet in [Lab 9](../lab-09-diagnose-domain-and-boundaries.md) and the linked neutral handbook records.
The diagnosis is bounded to the supplied synthetic scenario.

## 1. Boundary audit

The realized additions are:

\[
118+72+10=200\ \text{m3/s}
\]

The sum matches the scenario total.
The packet also shows that the two upstream inflows remain separately identified and that all three additions connect to the main wet component.
The final-interval integrated forcing is therefore internally consistent with the stated 200 m3/s total.

The south-edge contact occurs on the twenty-six faces assigned the terminal-outflow role.
It is intended boundary contact.
The east-edge contact occurs on twenty-three cells with no assigned boundary role.
It is unintended boundary contact.
The four northwest cells belong to a separate shallow component with no connection to the modeled channel or forcing.
They require review under the stated wet threshold, but they do not show that the main hydraulic pathway reaches that corner.

The integrated values do not reveal how flow is distributed among the selected faces.
Face-level fluxes, depths, velocities, signs, and time histories would be required to establish that distribution.

## 2. Edge-evidence classification

The following statements are direct observations from the packet:

- the main component connects both inflows, the local source, the channel, and the intended outlet;
- the same component reaches twenty-three unassigned east-edge cells;
- those cells have depths from 0.08 to 0.31 m and WSE from 102.54 to 102.62 m;
- nineteen local depth gradients point toward the east boundary;
- the east edge is intended to be closed; and
- the diagnostic executed and persisted cell roles and component identity.

It is a supported inference that the supplied scenario violates a criterion requiring no connected wet component at an unintended edge.
The observation is materially stronger than a missing warning or a visually plausible map because the packet identifies connectivity and boundary role directly.

It is not yet established that the finite domain is the cause.
Terrain or roughness error, forcing or boundary error, and a physically real pathway that extends beyond the chosen domain remain competing explanations.
The distinction matters for the corrective action.

The isolated northwest cells are less consequential to the modeled pathway because they are not connected to the channel, forcing, or outlet.
They still require a wetting, terrain, or initialization explanation if the acceptance contract covers every wet boundary cell.

## 3. Convergence and balance calculations

Only the ratios at 20,700 s and 21,600 s are below \(10^{-3}\).
The 19,800 s value is 0.0012, so the three-consecutive-interval rule is not satisfied.
Process success at the configured duration does not change that arithmetic.

The recorded net volume entering storage during the final interval is:

\[
180000-169200+0-0=10800\ \text{m3}
\]

The signed residual is:

\[
R_V=9900-10800=-900\ \text{m3}
\]

The percentage is:

\[
R_P=100\frac{-900}{180000}=-0.5\%
\]

The negative sign means the recorded storage increase is 900 m3 smaller than the recorded net input.
Possible explanations include omitted removal, timing or aggregation mismatch, or numerical balance error.
The one-interval packet does not choose among them.

The magnitude is bounded evidence for the final 900 s interval only.
It does not establish cumulative conservation.
The east-edge WSE also changes by 0.033 m in the final interval, so a small domain-total storage ratio would not establish local steady behavior even if the consecutive rule had passed.
[MX-002](../../reference/decision-code-artifact-crosswalk.md#mx-002-convergence) requires these evidence lanes to remain separate.

## 4. Selected discriminating check

**Check B is the immediate next check.**

The controlled eastward expansion changes the suspected truncation while holding the other named factors fixed.
Its possible outcomes have the following diagnostic value.

| Hypothesis | Expected observation if supported | Expected observation if challenged |
| --- | --- | --- |
| Domain clips a connected pathway | The main component continues into the added area, contact moves away from the original east boundary, and WSE, storage, or flux in the original area changes materially. | The original-area component and hydraulic quantities remain insensitive to the added space. |
| Terrain or roughness creates an artificial pathway | The wet tongue continues to follow the same suspect terrain or roughness feature in both domains, and source inspection identifies an incompatible or implausible feature. | The pathway disappears or the original-area result stabilizes solely because the artificial boundary moved. |
| Forcing or boundary realization is wrong | The expanded run preserves a broader inconsistency tied to realized forcing or boundary fluxes, while an input audit finds a mismatch. | The realized forcing remains complete and the changed result is localized to relief from the original domain edge. |

The expansion most directly tests the leading clipping explanation.
It will not by itself distinguish every terrain error from every forcing error, so a result that challenges clipping should lead to the next source or boundary check rather than acceptance.

Check A substitutes process completion for hydraulic evidence.
Check C tunes a parameter before identifying the cause.
Check D changes the physical boundary meaning without evidence that an outlet belongs there.

## 5. Direct domain verdict

**SCENARIO REJECTED FOR DOMAIN EVIDENCE.**

The rejection follows from the observed main component at an unassigned edge.
It does not assert that domain clipping is the proven cause of that contact.

The smallest packet for a new review includes:

1. The controlled eastward expansion comparison with the original-area component, WSE, depth, storage, and flux differences.
2. Terrain, roughness, forcing, and face-level boundary checks sufficient to explain any persistent pathway.
3. Complete convergence histories, local hydraulic histories, cumulative balance terms, and edge results.
4. Equivalent containment evidence for the largest planned discharge and other boundary conditions in the intended scenario range.
5. A recorded domain decision that either accepts the evidence, revises the domain, or excludes affected scenarios.

Until those items are available, [CQ-004](../../reference/conflicts-and-open-questions.md#cq-004-domain-clipping) and [CQ-005](../../reference/conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence) remain open for the planned use.
