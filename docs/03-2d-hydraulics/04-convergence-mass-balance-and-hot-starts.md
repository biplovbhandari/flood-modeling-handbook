# Convergence, Mass Balance, and Hot Starts

A time-dependent hydraulic model can stop changing enough for one purpose without becoming exactly steady, closing its water balance, or representing the real reach adequately.
The evidence must therefore be separated before a scenario is accepted.

## Why this topic matters

An automated termination rule saves computation only when its diagnostic is interpreted within its real scope.
A hot start can shorten the adjustment period, but it can also carry an incompatible state into the target scenario.
Neither a small storage change nor a completed process replaces boundary, sensitivity, or observation evidence.

## Prerequisites

Read [Conservation, Discharge, and Storage](../02-open-channel-flow/01-conservation-discharge-and-storage.md), [Backwater and Boundary Control](../02-open-channel-flow/04-backwater-and-boundary-control.md), and all three preceding chapters in this section.
Retain the distinction among internal numerical time steps, saved-output intervals, boundary conditions, and initial conditions.

## Learning objectives

After this chapter, the reader should be able to:

- distinguish numerical stability, residual transient behavior, quasi-steady behavior, storage change, mass-balance closure, solver exit status, hydraulic adequacy, and acceptance;
- calculate and dimension the current project storage-change ratio;
- state exactly what the ratio measures and what it leaves unmeasured;
- identify complementary evidence needed before accepting a scenario;
- explain how a hot start changes only the initial-value problem; and
- trace the current implementation from saved depth grids to manifest fields without inferring unavailable evidence.

## Evidence that answers different questions

| Evidence | Question answered | What it does not establish |
| --- | --- | --- |
| Numerical stability | Did numerical error remain controlled under the tested method, grid, state, and settings? | Quasi-steady behavior, mass balance, accuracy, or physical adequacy. |
| Residual transient behavior | Which relevant depths, WSEs, velocities, fluxes, or extents still change through time? | Whether the remaining change is acceptable without a criterion and use case. |
| Quasi-steady behavior | Are the stated quantities changing slowly enough over the stated interval and tolerance for the intended use? | Exact steady state, complete balance closure, or independence from the initial state. |
| Storage change | How much modeled water volume changed inside the domain between two times? | Why it changed or whether inflow, outflow, and sources close. |
| Inflow-outflow mass-balance closure | Does storage change agree with every material inflow, outflow, source, and sink over the same control volume and interval? | Hydraulic plausibility, numerical convergence under refinement, or agreement with observations. |
| Solver exit status | Did the process end normally, fail, reach a limit, or receive a termination request? | Stability, convergence, complete artifacts, or scientific acceptability. |
| Hydraulic adequacy | Do inputs, boundaries, states, outputs, sensitivities, and observations support the intended hydraulic use? | Formal approval unless the applicable acceptance criteria are also satisfied. |
| Acceptance | Has the authorized evidence set met the stated criteria for this use? | Suitability for a different use, reach class, solver, or uncertainty tolerance. |

The evidence categories can support one another, but none is a synonym for another.
For example, a smooth and stable calculation can keep filling slowly, a storage ratio can become small while inflow and outflow are both wrong, and a process can exit with code zero while its boundary condition is unsuitable.

## Residual transients and quasi-steady behavior

An unsteady solver advances a state through time even when the forcing is constant.
At first, water can fill channels and floodplain depressions, propagate to the downstream boundary, wet new cells, and adjust velocities and fluxes.
Later changes may become small without becoming zero.

Residual transient behavior is the change that remains in the quantities relevant to the decision.
The quantities must be named because domain-total storage can change little while one local WSE, wetting front, edge, or flow path still changes materially.

A quasi-steady claim therefore needs four parts:

1. Name the quantity or quantities being assessed.
2. Name the spatial support, such as the full domain, an interpretation area, a boundary, or selected locations.
3. Name the comparison interval and tolerance.
4. Explain why the resulting change is small enough for the intended product.

The word `converged` is incomplete without those parts.

## The project storage-change convergence ratio

For saved output times \(t-\Delta t_s\) and \(t\), the current project diagnostic is

\[
C_V=
\frac{|V_t-V_{t-\Delta t_s}|}
{Q_{in}\Delta t_s}
\]

For square raster cells of width \(\Delta x\), the code estimates each stored volume as

\[
V_t=(\Delta x)^2\sum_{i\in P_t}h_{i,t}
\]

where \(P_t\) is the set of cells whose saved depth is positive at time \(t\).
The code uses `numpy.nansum`, so `NaN` cells do not contribute to the sum.

The numerator has units

\[
[|V_t-V_{t-\Delta t_s}|]=\text{m3}
\]

and the denominator has units

\[
[Q_{in}\Delta t_s]
=(\text{m3/s})(\text{s})
=\text{m3}
\]

so \(C_V\) is dimensionless.
The ratio is the magnitude of net modeled storage change between consecutive saved depth grids, normalized by the imposed inflow volume over that saved-output interval.

### Worked calculation

Suppose a square-grid scenario has \(\Delta x=20\ \text{m}\), \(Q_{in}=50\ \text{m3/s}\), and \(\Delta t_s=900\ \text{s}\).
Suppose the sums of positive cell depths are \(328.5000\ \text{m}\) and \(328.5900\ \text{m}\) at consecutive saved outputs.

The two stored volumes are

\[
V_0=(20\ \text{m})^2(328.5000\ \text{m})=131{,}400\ \text{m3}
\]

\[
V_1=(20\ \text{m})^2(328.5900\ \text{m})=131{,}436\ \text{m3}
\]

The absolute storage change is \(36\ \text{m3}\), and the interval inflow volume is

\[
Q_{in}\Delta t_s
=(50\ \text{m3/s})(900\ \text{s})
=45{,}000\ \text{m3}
\]

Therefore,

\[
C_V=\frac{36}{45{,}000}=0.0008
\]

This synthetic result is below \(10^{-3}\).
It establishes only the stated domain-storage comparison under the supplied values.

## Exact current implementation semantics

**Current implementation:** [`calculate_volume_convergence`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/hydraulic_solvers/run.py) calculates positive-depth storage from consecutive `.wd` grids using raster resolution squared.
The watcher supplies `save_interval_seconds`, not the separate LISFLOOD-FP `massint` output interval, as \(\Delta t_s\).
The first saved grid receives a sentinel ratio of `1` because no previous grid is available.
The first-grid return occurs before boundary checking, so it also contains no boundary-check result.
Later ratios are compared with the configured tolerance using strict less-than, so a value exactly equal to the tolerance does not trigger convergence.

For later saved grids, the current boundary check returns no result until the downstream centerline endpoint cell is wet.
Once active, it examines wet perimeter cells and flags only those whose WSE lies inclusively between the two centerline-endpoint WSE values.
An `edge_error` termination requires such a flagged result and `allow_water_on_edges` set to false.
When convergence and a disallowed edge violation are both true on the same saved grid, the current branch records `volume_convergence` because convergence has priority over `edge_error`.
Absence of `edge_error` does not establish that a completed check found clean edges because convergence may have suppressed the edge reason, the check may not have run, the downstream endpoint may still have been dry, wet edge WSE may have fallen outside the endpoint range, or edge water may have been allowed.

The current default tolerance is `0.001`.
DR-022 ALT-G selects volume convergence, and DR-028 ALT-A selects \(10^{-3}\), both with status Alternate Selected in the reviewed Decision Register.
EXP-014 and Case-018 are preliminary scoped evidence for that choice, not universal validation.
The standalone DR-022 file also marks ALT-J as `#current` while its history and the reviewed register identify ALT-G.
That competing file-local marker remains an Open question and does not replace the registered selection within its recorded status and scope.

When the ratio is below tolerance, the watcher requests process termination and records `volume_convergence` as the termination condition.
When the process ends by itself while the watcher still considers it running, a nonzero return code raises an error and a zero return code is mapped to `max_simulation_time`.
When elapsed wall time exceeds `max_simulation_wall_time_seconds` while the process is still running, the watcher requests termination and records `max_wall_time`.
For watcher-requested termination, the helper sends terminate, waits three seconds, and kills the process if it has not exited.
When the watcher terminates the process for convergence, an edge error, or maximum wall time, the stored result records the termination condition but not the raw process return code, termination-signal outcome, or whether termination escalated to kill.

The scenario manifest stores the final ratio, termination condition, wall time, nominal upstream WSE, upstream discharge, derived simulated time, maximum wet-cell depth, median wet-cell depth, and flooded area.
Its schema can name an optional time-series Zarr store, but neither public ND nor public KWSE job currently forwards `save_zarr` into `RunConfig`.
The generic true branch creates a directory-backed store and then applies file-only hashing, so current manifest construction cannot complete that branch.
The derived simulated time is the number of discovered `.wd` files multiplied by the saved-output interval.

The manifest does not store the complete convergence history, raw process return code, termination-signal outcome, whether termination escalated to kill, boundary-check details, outflow history, solver mass file, or a closed mass-balance residual.
A stored manifest must therefore not be read as evidence that those omitted checks passed.

## What the ratio measures

The current ratio measures one domain-integrated response over one saved-output interval:

- It detects the magnitude of net storage change represented by the two positive-depth rasters.
- It normalizes that change by the constant imposed inflow volume during the interval.
- It can support a bounded claim that domain storage is changing slowly relative to supplied inflow.
- It provides an automated stopping signal under the selected threshold.

The absolute value treats filling and draining as equally unconverged at the same magnitude.
Because the diagnostic compares totals, positive and negative local depth changes can offset before the absolute value is applied to the net domain storage change.

## What the ratio does not measure

The ratio does not calculate boundary outflow.
It does not account explicitly for rainfall, infiltration, evaporation, exchange, or other modeled sources and sinks.
It does not show whether inflow equals outflow, whether local depths or velocities stopped changing, or whether the wet extent stabilized.
It does not show whether a wet edge is intended, whether the boundary influences the interpretation area, or whether the domain is large enough.
It does not establish numerical independence from grid size, internal time-step controls, saved-output interval, or convergence threshold.
It does not establish physical agreement with observed WSE, depth, extent, velocity, or timing.
It does not prove that the result is independent of the initial condition or hot-start source.

The name `volume_convergence` is project terminology for this storage-change proxy.
It must not be expanded into a claim of complete water-volume or mass-balance closure.

## Complete balance evidence

For the same control volume and interval, a closed volume balance would compare

\[
R_V=
\Delta S-
\left(
V_{in}-V_{out}+V_{source}-V_{sink}
\right)
\]

where every term has units m3 under one sign convention.
A useful report would state both the signed residual \(R_V\) and a declared normalization with guarded behavior when its scale is zero or very small.

The current termination calculation supplies \(\Delta S\) and the normalization \(V_{in}=Q_{in}\Delta t_s\).
It does not supply \(V_{out}\), all internal source and sink volumes, or \(R_V\).
No outflow-closure conclusion follows from a solver exit or scenario manifest.

## Complementary evidence for scenario review

A defensible review should consider evidence in separate lanes:

1. Inspect numerical stability diagnostics and any limiting cells or oscillations.
2. Plot storage ratio through time instead of relying only on its final value.
3. Inspect local WSE, depth, velocity, flux, and wet-extent change where those quantities matter.
4. Close the interval water balance from boundary and source or sink fluxes when the solver and workflow expose them.
5. Inspect the termination reason and distinguish controlled termination, solver completion, wall-time limit, simulation-time limit, and failure.
6. Inspect boundary and edge behavior, including whether water reached unintended edges.
7. Compare cold and compatible hot starts for the target scenario.
8. Perform spatial, temporal, parameter, boundary, and threshold sensitivities appropriate to the decision.
9. Compare relevant outputs with observations or an accepted benchmark.
10. Apply explicit acceptance criteria for the intended library or FIM use.

![Convergence evidence diagram](../assets/convergence-evidence.svg)

**What to notice:** Numerical stability, process or exit status, storage convergence, true mass-balance evidence, and hydraulic adequacy remain separate inputs to acceptance.
An acceptance gate can require all five without treating one as proof of another.

## Hot starts are initial conditions

A hot start initializes a target simulation from a prior modeled state rather than a dry or generic state.
If the source state is close to the target solution, the target run can spend less time filling storage and propagating an initial wetting front.
The tactic changes the initial condition, not the target forcing, boundary contract, or acceptance criteria.

The source and target must be compatible in grid geometry, alignment, CRS, vertical datum, terrain meaning, wet-depth meaning, and solver input convention.
They also need hydraulic compatibility in discharge, downstream condition, and expected flow paths.
A technically readable raster can still be a poor hydraulic initial state.

### Current project behavior

**Current implementation:** The ND adaptive sequence passes the current adaptive search position's final `depth` asset as the next newly simulated trial's `hot_start`.
That search position can be a newly simulated, re-adopted, or reused scenario and can be an accepted reference or a trial retained as the position after a `reject_low` result.
The KWSE input can identify an ND or KWSE source scenario, and the job resolves that source manifest's final `depth` asset.
The LISFLOOD-FP writer materializes that raster, converts it to ARC ASCII, and writes it as `startfile`.

The current code therefore supplies prior water depth only.
It does not supply a velocity, momentum, face-flux, or solver checkpoint state through the hot-start path.
The cited official LISFLOOD-FP manual likewise defines `startfile` as an initial water-depth raster for its documented release.
The behavior of the exact deployed executable when depth is supplied without motion state still requires version-specific confirmation before a stronger claim about momentum initialization is made.

The source scenario remains part of provenance because its discharge, downstream condition, run identity, terrain, and final state affect the starting condition.
The source scenario's own convergence or adequacy does not automatically transfer to the target scenario.
[CONF-010](../reference/conflicts-and-open-questions.md#conf-010-depth-only-hot-starts-and-initial-condition-independence) records the unresolved initialization and sensitivity evidence.

## Initial-condition sensitivity

At least one target-scenario comparison should use materially different defensible initial states when initial-condition dependence could affect the product.
Useful comparisons include a cold start and a compatible hot start, or two compatible hot starts approaching the target from different neighboring scenarios.

Compare the target result after applying the same forcing, boundaries, duration limits, and acceptance criteria.
Inspect final depth and WSE, wet extent, local flux or velocity where available, convergence history, termination reason, and balance evidence.
Material disagreement means the target has not shown independence from the tested initial-condition pathway.

A faster hot-started run is an efficiency result.
Agreement among adequately run initial-condition pathways is the evidence needed for an independence claim.

## Common misconceptions

### A small storage ratio proves steady flow

It proves only small net domain storage change relative to inflow over one saved interval.
Local changes, outflow error, and boundary effects can remain.

### A mass file name proves mass balance

An available file or configured write interval is not a reviewed balance calculation.
The relevant terms, units, interval, residual, and normalization must be inspected.

### A zero process exit proves convergence

An exit status reports process behavior.
The current termination reason and hydraulic evidence answer different questions.

### A hot start continues the previous simulation

The current path reuses a final depth raster as a new initial condition under a target scenario.
It does not carry the complete prior dynamic state through a solver checkpoint.

### The shortest run is the best hot start

Short runtime can result from a nearby compatible state, an unsuitable state, premature termination, or missing work.
Runtime alone does not distinguish those explanations.

## Competency check

Explain why \(C_V=8\times10^{-4}\) can coexist with a poor inflow-outflow balance.
Name two local transient signals that can be hidden by a domain-total storage sum.
Then identify the exact state supplied by the current hot-start path and state one comparison needed before claiming initial-condition independence.

## Practice

Complete [Lab 7: Convergence and Solver Evidence](../labs/lab-07-convergence-and-solver-evidence.md) after reading the solver comparison that follows.

## Source notes

- **Scientific foundation:** Storage continuity and the distinction between storage change and full balance are supported by [SCI-016](../reference/bibliography.md#sci-016-hec-ras-continuity-equation).
- **Selected methodology and Evidence or experiment:** Project termination choices and preliminary evidence are recorded in [SDR-003](../reference/bibliography.md#sdr-003-domain-inflow-terrain-and-convergence-decisions) and [SDR-004](../reference/bibliography.md#sdr-004-cases-issues-and-experiments).
- **Current implementation:** The exact convergence, process, edge, hot-start, result, and artifact paths are mapped in [JOB-007](../reference/bibliography.md#job-007-convergence-hot-start-and-solver-execution-paths).
- **External solver documentation:** The historical official LISFLOOD-FP `startfile` contract is recorded in [SCI-033](../reference/bibliography.md#sci-033-lisflood-fp-user-manual).
- **Open question:** The storage-change and mass-balance boundary remains recorded in [CONF-002](../reference/conflicts-and-open-questions.md#conf-002-volume-convergence-and-mass-balance), and the depth-only initialization gap remains recorded in [CONF-010](../reference/conflicts-and-open-questions.md#conf-010-depth-only-hot-starts-and-initial-condition-independence).
