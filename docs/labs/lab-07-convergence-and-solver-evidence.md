# Lab 7: Convergence and Solver Evidence

This lab asks for a direct stated-criteria conclusion from a complete synthetic solver-evidence packet.
The exercise separates termination, storage convergence, local transients, numerical stability, mass balance, edge behavior, hot-start evidence, and hydraulic adequacy.

## Prerequisites

Complete these chapters before starting:

- [Time Stepping and Stability](../03-2d-hydraulics/03-time-stepping-and-stability.md)
- [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md)
- [LISFLOOD-FP and SFINCS](../03-2d-hydraulics/05-lisflood-fp-and-sfincs.md)

Read [Lab Conventions](README.md), [Source Authority](../reference/source-authority.md), [Glossary](../reference/glossary.md), and [Equations and Units](../reference/equations-and-units.md).

## Learning objectives

After completing this lab, the learner should be able to:

- calculate storage and a storage-change ratio from saved depth summaries;
- distinguish a controller-triggered stop from solver exit and scientific convergence;
- calculate interval and cumulative mass-balance residuals;
- interpret local, edge, and numerical-stability evidence separately;
- assess a proposed depth-only alternate hot start without attributing it to the reviewed run; and
- issue a bounded stated-criteria conclusion with explicit evidence conditions.

## Synthetic scenario and criteria

Use a square grid with cell width \(\Delta x=20.0 \text{m}\).
Use constant inflow \(Q_{in}=50.0 \text{m3/s}\), saved-output interval \(\Delta t_s=900 \text{s}\), and storage-change tolerance \(C_{tol}=0.001\).
Assume no distributed source or sink.
The reviewed run starts dry with zero represented water storage.

Use:

\[
V_t=(\Delta x)^2\sum h_{i,t}
\]

\[
C_V=\frac{|V_t-V_{t-\Delta t_s}|}{Q_{in}\Delta t_s}
\]

The run controller requests a stop after the first saved interval with \(C_V<C_{tol}\).
The evidence review requires three consecutive saved intervals below the threshold before calling the storage history quasi-steady.

The review also requires:

| Evidence category | Review criterion |
| --- | --- |
| Mass balance | Absolute residual no greater than 0.5 percent of inflow for the final interval and cumulative run |
| Local state | Absolute WSE change no greater than 0.005 m at every review point over each of the final two saved intervals |
| Edge behavior | Zero wet cells on closed or unintended perimeter segments |
| Numerical verification | A grid or time-step comparison within stated output tolerances |
| Hydraulic validation | Comparison with observations or an accepted benchmark under predeclared criteria |

## Saved-depth summaries

| Saved model time | Sum of positive cell depths | Stored controller value |
| ---: | ---: | ---: |
| 900 s | 225.0000 m | 1.0 sentinel because no prior grid exists |
| 1,800 s | 281.2500 m | To calculate |
| 2,700 s | 315.0000 m | To calculate |
| 3,600 s | 326.2500 m | To calculate |
| 4,500 s | 328.5000 m | To calculate |
| 5,400 s | 328.5900 m | To calculate |

## Synthetic run log

| Model time or event | Log observation |
| --- | --- |
| Start | Dry depth state loaded, forcing opened, and controller began watching saved grids. |
| 900 s | First saved grid read, and the controller stored the 1.0 sentinel. |
| 1,800 through 4,500 s | Saved grids read, storage-change ratios recorded, and the run continued. |
| 5,400 s | The controller recorded \(C_V=0.0008\) and requested a controlled stop. |
| Stop handling | The numerical process acknowledged the request within 0.4 s, wrote final outputs, and returned exit code 0. |
| Final record | Termination reason states storage threshold reached, and simulated time states 5,400 s. |

The log supplies the process exit result and controlled-stop outcome.
It does not turn the controller threshold into a solver-issued scientific acceptance decision.

## Numerical-stability summary

| Diagnostic | Supplied value |
| --- | ---: |
| Internal time-step range | 0.70 to 3.20 s |
| Maximum recorded Courant value | 0.76 |
| Configured Courant target | 0.80 |
| Non-finite depth or velocity values | 0 |
| Rejected internal steps | 0 |
| Negative-depth corrections | 14 corrections totalling 0.60 m3 |
| Grid or time-step sensitivity comparison | Not supplied |

The diagnostics are summary records rather than complete internal histories.

## Mass-balance packet

Use the sign convention:

\[
R=V_{in}-V_{out}+V_{source}-V_{sink}-(V_{final}-V_{initial})
\]

| Balance term | Final 900 s interval | Cumulative 5,400 s run |
| --- | ---: | ---: |
| Inflow | 45,000 m3 | 270,000 m3 |
| Outflow | 44,900 m3 | 138,100 m3 |
| Distributed source | 0 m3 | 0 m3 |
| Distributed sink | 0 m3 | 0 m3 |
| Initial storage | 131,400 m3 | 0 m3 |
| Final storage | 131,436 m3 | 131,436 m3 |

Calculate the residual and its absolute percentage of inflow for both periods.

## Edge-observation packet

The downstream outlet segment is intended to become wet.
Every other perimeter segment is classified as closed or unintended for this scenario.

| Saved time | Edge-check state | Wet intended-outlet cells | Wet unintended-perimeter cells | Maximum unintended-edge depth |
| ---: | --- | ---: | ---: | ---: |
| 900 s | Withheld because the downstream endpoint remained dry | Not available | Not available | Not available |
| 1,800 s | Completed | 3 | 0 | 0 m |
| 2,700 s | Completed | 5 | 0 | 0 m |
| 3,600 s | Completed | 6 | 1 | 0.030 m |
| 4,500 s | Completed | 6 | 1 | 0.020 m |
| 5,400 s | Completed | 6 | 1 | 0.018 m |

No automatic edge termination rule was configured.
The edge observation therefore records a failed review criterion without changing the controller's termination reason.

## Local hydraulic histories

| Saved time | Point A WSE | Point A speed | Point B WSE | Point B speed |
| ---: | ---: | ---: | ---: | ---: |
| 3,600 s | 102.400 m VD-1 | 0.42 m/s | 101.800 m VD-1 | 0.50 m/s |
| 4,500 s | 102.404 m VD-1 | 0.41 m/s | 101.835 m VD-1 | 0.56 m/s |
| 5,400 s | 102.405 m VD-1 | 0.41 m/s | 101.860 m VD-1 | 0.60 m/s |

Final summary metrics are maximum depth 3.20 m, median wet-cell depth 0.65 m, and flooded area 1.84 km2.
No observation comparison, benchmark comparison, parameter sensitivity, or boundary sensitivity is supplied.

## Proposed alternate hot-start packet

The reviewed run did not use this packet.
A proposed alternate run would start from a prior scenario's final depth raster instead of the reviewed run's dry state.
The proposed source and target use the same grid dimensions, alignment, terrain identity, and vertical-reference metadata.
The proposed source inflow was 45.0 m3/s, while the target inflow would remain 50.0 m3/s.
The proposed source downstream condition, convergence history, velocities, fluxes, mass balance, and hydraulic acceptance are not supplied.
No result from the proposed alternate run is supplied.
No paired comparison between the reviewed dry-start run and the proposed alternate run is supplied.

## Part A: Calculate the storage-change history

**Core inspection:** For every saved time:

1. Calculate storage in m3.
2. Calculate absolute storage change from the preceding saved grid where available.
3. Calculate interval inflow volume.
4. Calculate \(C_V\).
5. Apply the strict comparison \(C_V<C_{tol}\).

Identify the first output that meets the controller threshold.
Count the consecutive outputs that meet the threshold.
Explain why the first output's value of 1.0 is a sentinel rather than a calculated ratio.

## Part B: Separate termination, exit, and convergence

**Core inspection:** State:

1. Why the controller requested a stop.
2. Whether controlled stop handling completed.
3. What the exit code establishes.
4. Whether the three-interval review criterion is satisfied.
5. Why the stored termination reason does not override the review criterion.

## Part C: Calculate mass balance

**Core inspection:** Calculate final-interval storage change, residual, and residual percentage.
Calculate cumulative storage change, residual, and residual percentage.
Compare both percentages with the 0.5 percent criterion.
Explain why passing these two balance summaries would not prove local steady state, numerical verification, or hydraulic validity.

## Part D: Assess stability, local state, and edges

**Core inspection:** Create one row for numerical stability, local transient behavior, edge behavior, and numerical verification.
For each row, state what the packet supports, what fails or remains missing, and whether the stated review criterion passes.
Calculate the last two WSE changes at Points A and B.
Explain why a decreasing domain-storage ratio can coexist with a local transient and an unintended wet edge.
Use [MX-002](../reference/decision-code-artifact-crosswalk.md#mx-002-convergence) and [CQ-005](../reference/conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence).

## Part E: Assess the proposed alternate hot start

**Core inspection:** State the reviewed run's initial state and keep it separate from the proposed alternate state.
State exactly which initial-state component the proposed alternate packet supplies.
List the proposed compatibility evidence that is present.
List the proposed source and alternate-run evidence that remains missing.
Explain why a prior depth field may reduce initial filling time without proving complete-state continuation or final-state independence.
Propose one comparison between the reviewed dry-start run and the proposed alternate run that would discriminate between efficient initialization and path dependence.

## Part F: Preserve public solver-source boundaries

**Core inspection:** For each statement, identify whether it is supported by a public source, supported only by this applied packet, or unsupported.
Then assign the appropriate public evidence label.

1. The LISFLOOD-FP manual describes its documented initial-depth input.
2. The SFINCS manual describes initial water levels and restart files for its documented version.
3. Either manual proves which numerical engine produced this unnamed synthetic packet.
4. The supplied exit code and stop log establish controlled process completion for this packet.
5. Public documentation for one solver proves hydraulic adequacy for another solver or scenario.

Use [SCI-032](../reference/bibliography.md#sci-032-sfincs-user-manual) and [SCI-033](../reference/bibliography.md#sci-033-lisflood-fp-user-manual).

## Part G: Issue a stated-criteria conclusion

Choose exactly one conclusion from the Validation Framework vocabulary:

- `MEETS THE STATED CRITERIA` applies when every required criterion passes within the recorded evidence scope.
- `MEETS THE STATED CRITERIA WITH RESTRICTIONS` applies when the criteria explicitly permit a bounded use and every restriction is visible and enforceable.
- `DOES NOT MEET THE STATED CRITERIA` applies when a required criterion fails or a material condition lies outside the permitted range.
- `INSUFFICIENT EVIDENCE TO ASSESS` applies when the packet cannot support either acceptance or a bounded rejection.

State the conclusion first.
Then list the smallest additional evidence set that could change it.
Keep process completion, numerical evidence, and physical adequacy separate.

## Deliverable

Submit a short answer with these sections:

1. Storage-change calculation table.
2. Termination, exit, and convergence assessment.
3. Mass-balance calculation.
4. Stability, local-state, and edge assessment.
5. Hot-start assessment.
6. Public-source boundary table.
7. Direct stated-criteria conclusion and required evidence.

## Competency criteria

The lab is complete when the answer:

- calculates interval inflow volume as \(45{,}000 \text{m3}\);
- calculates storage-change ratios 0.5, 0.3, 0.1, 0.02, and 0.0008 after the sentinel;
- identifies 5,400 s as the first output satisfying the controller threshold;
- identifies that the three-interval quasi-steady criterion is not satisfied;
- calculates a 64 m3 final-interval residual and a 464 m3 cumulative residual;
- distinguishes the bounded mass-balance pass from local, edge, numerical-verification, and hydraulic evidence;
- identifies the Point B local-state criterion and the unintended-edge criterion as failed;
- identifies depth as the only proposed alternate hot-start state without changing the reviewed run's zero initial storage; and
- issues `DOES NOT MEET THE STATED CRITERIA` because the storage, Point B, and edge criteria fail.

After completing the lab, compare the reasoning with [Lab 7 Solution](solutions/lab-07-convergence-and-solver-evidence-solution.md).

## Source notes

- **Scientific foundation:** Continuity and two-dimensional flow concepts are supported by [SCI-016](../reference/bibliography.md#sci-016-hec-ras-continuity-equation) and [SCI-027](../reference/bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics).
- **Scientific foundation:** Numerical space-time evidence is supported by [SCI-029](../reference/bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance).
- **Design principle:** Verification, validation, uncertainty, and acceptance should remain distinct, as supported by [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance).
- **Evidence note:** Every log, summary, criterion, and result in this prompt is constructed teaching material.
