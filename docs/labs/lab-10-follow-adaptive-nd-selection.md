# Lab 10: Follow Adaptive ND Selection

This lab uses a synthetic metric sequence to reconstruct the current adaptive ND algorithm.
The discharge values and hydraulic metrics are invented for instruction and are not project data, a recommended band calibration, or evidence for any real reach.

## Prerequisites

Complete [Normal-Depth Libraries](../05-scenario-libraries/01-normal-depth-libraries.md) and [Adaptive Discharge Selection](../05-scenario-libraries/02-adaptive-discharge-selection.md).
Read [Source Authority](../reference/source-authority.md) and the [Lab Conventions](README.md).

## Learning objectives

After completing this lab, the learner should be able to:

- calculate current max-depth, median-depth, and flooded-area comparisons;
- assign `reject_low`, `accept`, and `reject_high` with the current priority rules;
- derive one acceptance window from monotone response curves;
- select a q-grid-constrained next trial;
- re-judge one finished run against a changed reference;
- distinguish publication from selected membership;
- identify algorithm and evidence limitations; and
- issue a direct readiness verdict with evidence labels.

## Execution label

**Core inspection:** Complete the lab from the supplied tables and equations.
Do not run a project job, solver, test, build, or infrastructure command.

## Synthetic assumptions

Use the following exercise settings.

| Setting | Synthetic value |
| --- | ---: |
| Minimum discharge | 100 m3/s |
| Maximum discharge | 220 m3/s |
| Authored opening increment | 50 m3/s |
| q-grid resolution | 10 m3/s, anchored to zero |
| Maximum-depth band | 0.75 to 1.25 m |
| Median-depth band | 0.25 to 0.50 m |
| Flooded-area band | 10 to 15 percent of reference area |

Assume every listed scenario except an adopted scenario was newly simulated without `edge_error` and therefore published before its verdict under current code.
Assume all rasters use square cells in a metre-based projected CRS and all listed metrics came from strictly positive-depth cells in the final processed saved grid.
Assume no value decreases, so the monotone proposal curves equal the supplied metric values.

## Synthetic scenario packet

The first four rows occur in this order.

| Event | Discharge | Maximum depth | Median depth | Flooded area | State before judgment |
| --- | ---: | ---: | ---: | ---: | --- |
| Baseline | 100 m3/s | 2.00 m | 0.50 m | 1.000 km2 | No reference exists yet. |
| Authored opening trial | 150 m3/s | 3.36 m | 0.80 m | 1.140 km2 | Reference 100, position 100. |
| Curve-proposed trial after the high result | 140 m3/s | 3.00 m | 0.78 m | 1.120 km2 | Reference 100, position 100. |
| Re-judgment, no rerun | 150 m3/s | 3.36 m | 0.80 m | 1.140 km2 | Reference 140 after 140 is accepted. |

After the re-judgment, use all finished points at 100, 140, and 150 m3/s to calculate the next proposal.

The lab then supplies two later measurements.

| Event | Discharge | Maximum depth | Median depth | Flooded area |
| --- | ---: | ---: | ---: | ---: |
| Next proposed trial | Your calculated value | 3.85 m | 0.84 m | 1.170 km2 |
| Later maximum endpoint | 220 m3/s | 5.30 m | 1.10 m | 1.320 km2 |

For the maximum-endpoint comparison, assume the next proposed trial became the reference and assume the control flow later reached 220 m3/s as the maximum.

## Part A: Reproduce the first verdicts

**Core inspection:** Calculate all three changes from 100 to 150 m3/s.
Apply ceiling priority and assign the exact current verdict.

Then calculate the three changes from 100 to 140 m3/s and assign the verdict.
State how the reference and position change after each judgment.

## Part B: Re-judge a finished run

**Core inspection:** Recalculate the 150 m3/s changes against the new 140 m3/s reference.
Assign the new verdict and explain why the scenario can change category without another simulation.

State whether 150 m3/s becomes the reference, the position, both, or neither.
State which final depth raster will hot-start the next newly simulated trial.

## Part C: Derive the next trial

**Core inspection:** Use the finished scenario points at 100, 140, and 150 m3/s.
The current reference is 140 m3/s and the position is 150 m3/s.

For each criterion:

1. Calculate the absolute floor and ceiling response values relative to the 140 m3/s reference.
2. Use the last segment from 140 to 150 m3/s to extrapolate the floor and ceiling crossings that lie above 150 m3/s.
3. Identify the earliest floor crossing and earliest ceiling crossing.
4. List every 10 m3/s grid value inside the resulting window.
5. Select the next trial according to the current midpoint-and-clamp rule.

Show arithmetic and units.

## Part D: Judge the next trial and maximum

**Core inspection:** Use the supplied metrics at your next-trial discharge and compare them with the 140 m3/s reference.
Assign the verdict and update reference and position.

Then compare the 220 m3/s maximum with that new reference.
Assign the measured verdict first.
Explain separately what current maximum-discharge control flow does with the endpoint and whether the loop stops or continues.

## Part E: Classify publication and membership

**Core inspection:** For 100, 140, 150, your next trial, and 220 m3/s, classify each scenario as:

- published under the supplied current-code assumptions;
- an ordinary accepted selected member;
- a forced endpoint member;
- a published search point that is not currently selected; or
- a hot-start position that is not currently selected.

Use more than one label when needed.
Explain why storage presence alone cannot reconstruct this classification.

## Part F: Identify limitations and missing evidence

**Core inspection:** Identify at least four limitations or missing evidence categories.
Your answer must include:

1. One limitation of the straight-line and monotone proposal curves around a floodplain transition.
2. One consequence of sequential depth-only warm starts.
3. One scale limitation of the flooded-area percentage.
4. One publication, membership, q-grid, or re-judgment evidence gap in current outputs.
5. The current `save_zarr` behavior for the public ND job and the generic true branch.

Also state what the current metrics and bands do not prove about DR-030 alignment.

## Part G: Apply evidence labels

**Core inspection:** Apply one of the handbook's six evidence labels to each real project or scientific claim type.
For the invented metric values, use `Synthetic exercise assumption` or state that they are outside the project evidence taxonomy.

| Claim type | Label to supply and defend |
| --- | --- |
| The metric values in this lab | `Synthetic exercise assumption` or `Outside the project evidence taxonomy` |
| DR-030 ALT-C selection | Your label |
| Current verdict and publication branches | Your label |
| Target storage observation and emergent `q_set` | Your label |
| The absent DR-041 row and unresolved grid authority | Your label |

Do not force the synthetic values into one of the six evidence classes or present them as project evidence.

## Part H: Issue a readiness verdict

**Core inspection:** Choose exactly one verdict.

- `READY FOR DOWNSTREAM SCIENTIFIC USE` means the packet establishes authorized bounds, metric and band alignment, explicit selected membership, q-grid authority and enforcement, storage materialization, numerical evidence, hydraulic adequacy, and relevant sensitivity.
- `NOT READY` means one or more of those conditions remain unsupported.

State the verdict first.
Then list the smallest follow-up evidence set that could change it.

## Deliverable

Submit a short answer with these sections:

1. First verdict calculations.
2. Re-judgment and state update.
3. Acceptance-window and next-trial arithmetic.
4. Next-trial and maximum behavior.
5. Publication and membership classification.
6. Limitations and evidence labels.
7. Direct readiness verdict.

## Competency criteria

The lab is complete when the answer:

- assigns `reject_high` to 150 m3/s against 100 m3/s;
- assigns `accept` to 140 m3/s against 100 m3/s;
- re-judges 150 m3/s as `reject_low` against 140 m3/s and makes it the position;
- derives an acceptance window of approximately 160.83 through 174.72 m3/s;
- selects 170 m3/s as the only q-grid value inside that window;
- assigns `accept` to 170 m3/s against 140 m3/s;
- assigns measured `reject_high` to 220 m3/s against 170 m3/s while preserving its forced-endpoint behavior and continued gap filling;
- separates publication from membership for the 150 m3/s search point;
- states that the 150 m3/s rejected-low position remains a nonmember under the current monotone control flow and distinguishes it from higher finished `reject_high` points that can later pass free re-judgment;
- identifies the required limitation categories;
- identifies that the public ND job ignores `save_zarr` and that the generic true branch cannot complete manifest construction with file-only hashing of a directory store;
- uses the six evidence labels for real claims while marking synthetic values outside the project evidence taxonomy; and
- issues `NOT READY` because the synthetic packet cannot establish the production readiness boundary.

After completing the lab, compare the reasoning with [Lab 10 Solution](solutions/lab-10-follow-adaptive-nd-selection-solution.md).
