# Lab 7 Solution: Convergence and Solver Evidence

This solution demonstrates one evidence-disciplined answer to the synthetic exercise.
It does not validate a project scenario, approve a solver, or select production acceptance criteria.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, and readiness stopping condition are defined in [Lab 7](../lab-07-convergence-and-solver-evidence.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and does not turn storage convergence into full mass-balance or hydraulic-adequacy evidence.
Stop when the answer satisfies the prompt's competency criteria or retains `NOT READY` with the smallest evidence set that could change the verdict.

## 1. Convergence calculation table

The square-cell area is

\[
A_c=(20.0\ \text{m})^2=400\ \text{m2}
\]

The inflow volume during every saved-output interval is

\[
V_{in}=Q_{in}\Delta t_s
=(50.0\ \text{m3/s})(900\ \text{s})
=45{,}000\ \text{m3}
\]

The complete arithmetic is:

| Time | Positive-depth sum | Storage \(V_t\) | Absolute storage change | \(C_V\) | Strictly below 0.001? |
| ---: | ---: | ---: | ---: | ---: | --- |
| 900 s | 225.0000 m | 90,000 m3 | Not available. | 1.0 sentinel. | No. |
| 1,800 s | 281.2500 m | 112,500 m3 | 22,500 m3 | 0.5 | No. |
| 2,700 s | 315.0000 m | 126,000 m3 | 13,500 m3 | 0.3 | No. |
| 3,600 s | 326.2500 m | 130,500 m3 | 4,500 m3 | 0.1 | No. |
| 4,500 s | 328.5000 m | 131,400 m3 | 900 m3 | 0.02 | No. |
| 5,400 s | 328.5900 m | 131,436 m3 | 36 m3 | 0.0008 | Yes. |

For example, the final ratio is

\[
C_V=\frac{|131{,}436-131{,}400|\ \text{m3}}{45{,}000\ \text{m3}}
=0.0008
\]

The 5,400 s output is the first supplied output that satisfies the current strict comparison \(C_V<0.001\).
The 900 s value is a sentinel because the watcher has no prior saved array for a two-grid comparison.
It is not a ratio calculated from 900 s of storage change.

The sequence supports a synthetic conclusion that domain-total storage change decreased relative to interval inflow.
It does not explain the boundary fluxes or local changes that produced those totals.

## 2. Evidence-separation table

| Category | What the packet supports | Missing evidence | Pass? |
| --- | --- | --- | --- |
| Numerical stability | A series of readable saved depth grids exists. | Internal-step history, limiter activity, oscillation checks, warnings, and a space-time sensitivity comparison. | No. |
| Residual transient behavior | Domain-total storage changes decrease through the supplied outputs. | Local depth, WSE, velocity, flux, and wet-extent histories. | No. |
| Quasi-steady behavior | The selected storage criterion is below its stated threshold at 5,400 s. | Evidence that all quantities relevant to the intended use meet their own tolerances over a sufficient interval. | Partial only. |
| Storage change | The final net change is 36 m3 over 900 s, normalized to 0.0008. | Cellwise signed changes and sensitivity to output interval. | Yes for this calculation only. |
| Inflow-outflow mass-balance closure | Inflow volume is 45,000 m3 per interval. | Outflow, source, sink, and signed residual terms over the same interval and control volume. | No. |
| Solver exit status | The watcher requested termination and recorded `volume_convergence`. | Raw process return code and a separate record of controlled termination versus solver-declared success. | No raw-exit pass. |
| Wall-time and controlled-termination handling | The stored reason is not `max_wall_time`. | Configured wall-time limit, termination-signal outcome, and whether terminate escalated to kill after three seconds. | No. |
| Boundary or edge behavior | The stored reason is not `edge_error`. | Whether the first-grid sentinel or a dry downstream endpoint withheld the check, the `allow_water_on_edges` value, saved check results, endpoint WSE values, edge maps or rasters, intended-open-edge definition, and boundary influence assessment. | No. |
| Hydraulic adequacy | Final summary depth and extent metrics exist. | Boundary suitability, observations, benchmarks, local state, sensitivities, uncertainty, and intended-use criteria. | No. |
| Acceptance | No acceptance record is supplied. | Authorized criteria, evidence review, decision owner, and disposition of every missing category. | No. |

A readable depth sequence is not enough to call the internal numerical solution stable.
The lack of `edge_error` is also not proof of acceptable edges.
The first saved grid returns before boundary checking, and later checks return no result until the downstream centerline endpoint is wet.
Once active, the check flags only wet perimeter cells whose WSE lies inclusively between the two centerline-endpoint WSE values, and `edge_error` requires `allow_water_on_edges` to be false.
The packet does not distinguish those skipped, withheld, out-of-range, allowed, or completed-clean cases.

The scenario manifest's `volume_convergence` termination reason is process-control evidence from the watcher.
It is not a solver-issued scientific acceptance statement.
The current stored result also does not preserve the raw return code, termination-signal outcome, or whether termination escalated from terminate to kill.

## 3. Hot-start assessment and sensitivity comparison

The packet supports one initial-state claim: the target began with a prior scenario's final water-depth raster on the same stated grid, alignment, terrain asset, and vertical datum label.
It does not support continuation of velocity, momentum, face flux, or the complete solver state.

The source inflow of \(45.0\ \text{m3/s}\) is close to the target inflow of \(50.0\ \text{m3/s}\), so the source depth can plausibly reduce the target's initial filling and wetting-front adjustment.
That statement is reasoning about efficiency, not proof of compatibility or final-state independence.

Missing source evidence includes its downstream condition, run identity, convergence history, motion state, balance, and hydraulic adequacy.
Missing target evidence includes a cold-start result and any alternative compatible start.
The target's current termination result does not repair those omissions.

A discriminating comparison would run the same target forcing and boundaries from both the supplied compatible depth start and a defensible cold start.
Both paths should use adequate duration limits and the same review criteria.
Compare complete convergence histories, final and local depth or WSE, wet extent, velocities or fluxes where available, edge behavior, balance residuals, and termination reasons.
Material final disagreement or different persistent local behavior would show target sensitivity to the initial-condition pathway.

## 4. Solver-support classification

| Statement | Classification | Reason |
| --- | --- | --- |
| The official LISFLOOD-FP manual for its documented release defines `startfile` as initial water depth. | **External solver capability** | It describes the documented release and supports the meaning of that file, not current project validation. |
| The reviewed checkout launches `lisflood` for scenario execution. | **Current implementation** | The checked-out runner calls that executable with the generated parameter file. |
| The local project solver-comparison document describes both solvers as interchangeable. | **Target or candidate material** | The statement records an intended or historical comparison but conflicts with current code support. |
| The current SFINCS writer raises `NotImplementedError` before export. | **Current implementation** | This is the checked-out behavior and blocks the candidate writer. |
| An enum value and unreachable writer prove current SFINCS support. | **Unsupported conclusion** | No executable SFINCS input-to-artifact path exists in the reviewed checkout. |
| Official SFINCS documentation describes water-level boundaries, discharge sources, masks, initial water levels, and restart state. | **External solver capability** | The documentation describes SFINCS capabilities for its documented version, not project implementation. |

The direct current-support verdict is that LISFLOOD-FP is the implemented scenario execution path in the reviewed checkout.
SFINCS is candidate or target support because preprocessing references exist but export, version discovery, execution, watching, post-processing, artifacts, and end-to-end validation are not available as an equivalent current path.

## 5. Readiness verdict and missing evidence

**NOT READY.**

The synthetic arithmetic shows that the selected storage-change proxy crosses its stated threshold.
The evidence packet does not establish numerical stability, local quasi-steady behavior, full inflow-outflow balance, acceptable edge behavior, initial-condition independence, observed agreement, uncertainty, or authorized acceptance.

The smallest additional evidence set that could change the verdict includes:

1. Internal numerical diagnostics and a relevant time-step or grid sensitivity result.
2. Local depth or WSE and wet-extent change histories across the interpretation area and boundaries.
3. Inflow, outflow, source, sink, storage, and residual terms for one common interval and control volume.
4. Persisted boundary-check activation, endpoint WSE values, result details, edge-allowance setting, and a map of intended and unintended wet edges.
5. A target cold-start versus compatible-hot-start comparison.
6. Boundary and parameter sensitivities material to the scenario.
7. Observation or accepted-benchmark comparisons for the intended hydraulic quantities.
8. Explicit acceptance criteria, uncertainty bounds, and an authorized review decision.

The solution does not require a SFINCS run to reject the supplied scenario evidence as insufficient.
It also does not infer that SFINCS would produce better or worse results.
