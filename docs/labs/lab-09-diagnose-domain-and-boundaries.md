# Lab 9: Diagnose Domain and Boundaries

This lab uses a synthetic R-200 scenario packet to diagnose boundary realization, unintended edge water, convergence evidence, and domain adequacy.
Every required observation is supplied below.

## Prerequisites

Complete [Domain and Boundary Geometry](../04-model-development/04-domain-and-boundary-geometry.md), [The Model-Building Operation](../04-model-development/05-build-model-job.md), and [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md).
Read [MX-001](../reference/decision-code-artifact-crosswalk.md#mx-001-boundary-conditions), [MX-002](../reference/decision-code-artifact-crosswalk.md#mx-002-convergence), [MX-007](../reference/decision-code-artifact-crosswalk.md#mx-007-domain-adequacy), [CQ-004](../reference/conflicts-and-open-questions.md#cq-004-domain-clipping), and [CQ-005](../reference/conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence).

## Execution boundary

Complete the diagnosis from the packet.
No model run or external artifact is required.
Optional tools may be used only for generic arithmetic.

## Learning objectives

After completing this lab, the learner should be able to:

- verify forcing totals and boundary roles;
- distinguish intended outlet contact from unintended wet-edge contact;
- separate direct observations from causal hypotheses;
- calculate a signed balance residual with stated support;
- apply a consecutive-interval convergence rule;
- choose a discriminating next check without tuning a parameter first; and
- issue a bounded domain verdict.

## Synthetic scenario identity

| Field | Supplied value |
| --- | --- |
| Reach | R-200 |
| Prepared network | N-1 |
| Model generation | R-200-M2, generation G-03 |
| Scenario total discharge | 200 m3/s |
| Scenario duration | 21,600 s |
| Saved-output interval | 900 s |
| Grid | 10 m square cells |
| Domain bbox | \([21020,48100,22640,49620]\) m |
| Horizontal reference | SP-1, projected metres |
| Vertical reference | VD-1, metres |
| Wet threshold | Depth strictly greater than 0.01 m |
| Intended terminal outlet | South-edge faces from x = 22,380 m through x = 22,640 m |

## Boundary packet

| Boundary role | Selected support | Planned discharge | Realized final-interval mean discharge | Evidence supplied |
| --- | --- | ---: | ---: | --- |
| R-100 inflow | Six named north-edge faces | 118 m3/s | 118 m3/s | Selected faces intersect the R-100 conveyance corridor and connect to the main wet component. |
| R-300 inflow | Four named west-edge faces | 72 m3/s | 72 m3/s | Selected faces intersect the R-300 conveyance corridor and connect to the main wet component. |
| Local contribution | One named interior source region | 10 m3/s | 10 m3/s | The source region lies inside the active mask and connects to the main wet component. |
| Terminal outflow | Twenty-six named south-edge faces | Not prescribed | 188 m3/s outward | The selected faces carry outward flux and belong to the main wet component. |
| East edge | No boundary role | 0 m3/s prescribed | No flux record is defined | The active edge is intended to be closed. |

The two inflow lines remain separate in the scenario record.
The final-interval inflow values are means over the same 900 s interval.

## Edge-water packet

The edge diagnostic executed on the final saved grid and stored the component, role, depth, WSE, and local flow-direction evidence for every wet boundary cell.

| Component | Boundary contact | Cell count | Depth range | WSE range | Connectivity and role |
| --- | --- | ---: | --- | --- | --- |
| Main | Intended south outlet | 26 | 0.22 to 0.88 m | 101.90 to 102.34 m | Connected to both inflows, the local source, and the modeled channel. |
| Main | Unassigned east edge from y = 48,820 m through y = 49,040 m | 23 | 0.08 to 0.31 m | 102.54 to 102.62 m | Connected to the modeled channel and the intended outlet. |
| Isolated | Northwest corner | 4 | 0.012 to 0.020 m | 104.01 to 104.03 m | Not connected to the channel, any inflow, or the main component. |

At the 23 east-edge cells, the depth gradient points toward the east boundary in 19 cells and is tangential in 4 cells.
The record assigns no inflow, outflow, open-water, or transfer role to those cells.
The model record also contains the earlier observation that one connected terrain corridor is 30 m from the east edge, below a 50 m review threshold.
No controlled domain-expansion result is supplied.

## Convergence and balance packet

The instructional convergence rule requires a storage-change ratio below \(10^{-3}\) for three consecutive saved intervals.

| Saved time | Storage-change ratio | Main-channel WSE change from prior save | East-edge maximum WSE change from prior save |
| ---: | ---: | ---: | ---: |
| 18,000 s | 0.0040 | 0.020 m | 0.081 m |
| 18,900 s | 0.0024 | 0.014 m | 0.064 m |
| 19,800 s | 0.0012 | 0.009 m | 0.050 m |
| 20,700 s | 0.0008 | 0.006 m | 0.041 m |
| 21,600 s | 0.0007 | 0.004 m | 0.033 m |

The process reached the configured duration and returned success.
It did not record a scientific acceptance decision.

The final 900 s interval contains these volume terms.
Inflow and source volumes are positive additions.
Outflow and sink volumes are positive removals.

| Term | Value |
| --- | ---: |
| Inflow volume | 180,000 m3 |
| Outflow volume | 169,200 m3 |
| Other source volume | 0 m3 |
| Sink volume | 0 m3 |
| Storage change | 9,900 m3 |

Use:

\[
R_V=\Delta S-\left(V_{in}-V_{out}+V_{source}-V_{sink}\right)
\]

\[
R_P=100\frac{R_V}{V_{in}}
\]

The balance table covers only the final saved interval.
No cumulative balance, grid-refinement comparison, or alternate-domain scenario is supplied.

## Diagnostic hypotheses

Consider at least these three explanations for the main component reaching the unassigned east edge.

1. The domain clips a hydraulically connected floodplain pathway.
2. A terrain or roughness error creates an artificial pathway toward the edge.
3. The supplied forcing or boundary realization is wrong even though the final interval totals add to 200 m3/s.

The packet does not authorize choosing among those causes solely from the symptom.
It does establish whether the affected scenario can pass a domain-containment criterion.

## Part A: Audit the boundaries

Add the three realized inflow contributions and compare the result with the scenario total.
Classify each boundary contact as intended, unintended, or unrelated to the main hydraulic component.
State what the integrated fluxes establish and what face-level distribution evidence they do not establish.

## Part B: Diagnose the edge observation

Separate direct observations, inferences, and unknown causes.
Explain why the connected east-edge contact is more consequential than the isolated northwest cells.
State whether the east-edge result passes a criterion that requires no connected wet component at an unintended edge.

## Part C: Evaluate convergence and balance

Determine whether the three-consecutive-interval rule is satisfied.
Calculate the signed final-interval residual and percentage.
Interpret the sign.
State why the result cannot establish cumulative conservation, local steady behavior, or hydraulic adequacy.

## Part D: Choose the next discriminating check

Choose exactly one immediate next check.

- A. Accept the scenario because the process returned success.
- B. Repeat the same scenario on a controlled eastward domain expansion while holding terrain source, roughness, forcing, grid, boundaries, initial state, and numerical settings fixed, then compare the connected component, edge gradients, local WSE, storage, and fluxes.
- C. Increase roughness near the east edge until the component retreats.
- D. Open the east edge and reinterpret the contact as an outlet.

Explain the expected observation under each of the three supplied hypotheses if your selected check is performed.

## Part E: Issue a domain verdict

Choose exactly one verdict.

- SCENARIO REJECTED FOR DOMAIN EVIDENCE
- DOMAIN ADEQUATE FOR THE PLANNED RANGE

State the verdict first.
Then identify the smallest additional evidence needed for a new review across the planned scenario range.

## Deliverable

Submit a short diagnosis with these sections:

1. Boundary audit.
2. Edge-evidence classification.
3. Convergence and balance calculations.
4. Selected discriminating check.
5. Direct domain verdict.

## Competency criteria

The lab is complete when the answer:

- confirms \(118+72+10=200\) m3/s;
- treats the south-edge contact as intended and the connected east-edge contact as unintended;
- does not treat the isolated shallow component as evidence that the main channel is clipped;
- finds only two consecutive ratios below \(10^{-3}\);
- calculates a signed residual of -900 m3 and -0.5 percent;
- does not treat process success or one interval balance as hydraulic acceptance;
- chooses the controlled eastward expansion; and
- issues SCENARIO REJECTED FOR DOMAIN EVIDENCE without claiming that clipping is the proven cause.

After completing the lab, compare the reasoning with [Lab 9 Solution](solutions/lab-09-diagnose-domain-and-boundaries-solution.md).
