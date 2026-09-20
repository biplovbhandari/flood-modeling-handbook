# Lab 7: Convergence and Solver Evidence

This lab asks for a direct readiness verdict from a synthetic convergence history and a bounded current-code evidence packet.
All numerical values are synthetic instructional givens outside project authority.
They are not project observations, validation results, solver benchmarks, or selected thresholds except where the prompt separately cites current project sources.

## Prerequisites

Complete these chapters before starting:

- [Time Stepping and Stability](../03-2d-hydraulics/03-time-stepping-and-stability.md)
- [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md)
- [LISFLOOD-FP and SFINCS](../03-2d-hydraulics/05-lisflood-fp-and-sfincs.md)

Read [Source Authority](../reference/source-authority.md) and [Lab Conventions](README.md).
Use [Equations and Units](../reference/equations-and-units.md) for storage continuity and the project storage-change ratio.

## Execution boundary

All required steps are **Core inspection**.
No command or production access is required, and any independent tooling remains optional and user-run without changing the evidence boundary.

## Learning objectives

After completing this lab, the learner should be able to:

- calculate storage and convergence from saved depth-grid summaries;
- distinguish a convergence-triggered termination from raw solver success;
- identify missing mass-balance, edge, local-transient, and hydraulic evidence;
- assess a depth-only hot start without claiming complete state continuity;
- distinguish current LISFLOOD-FP support from candidate SFINCS material; and
- issue a direct readiness verdict with explicit acceptance conditions.

## Synthetic scenario givens

Use a square grid with cell width \(\Delta x=20.0\ \text{m}\).
Use constant inflow \(Q_{in}=50.0\ \text{m3/s}\), saved-output interval \(\Delta t_s=900\ \text{s}\), and convergence tolerance \(C_{tol}=0.001\).
Assume no distributed source or sink values are supplied in the evidence packet.

The saved positive-depth sums are:

| Saved model time | Sum of positive cell depths | Stored diagnostic |
| ---: | ---: | ---: |
| 900 s | 225.0000 m | 1.0 sentinel because no prior grid exists. |
| 1,800 s | 281.2500 m | To calculate. |
| 2,700 s | 315.0000 m | To calculate. |
| 3,600 s | 326.2500 m | To calculate. |
| 4,500 s | 328.5000 m | To calculate. |
| 5,400 s | 328.5900 m | To calculate. |

Use \(V_t=(\Delta x)^2\sum h_{i,t}\) and \(C_V=|V_t-V_{t-\Delta t_s}|/(Q_{in}\Delta t_s)\).

## Synthetic result packet

The supplied scenario record contains:

| Field or observation | Supplied value |
| --- | --- |
| `termination_condition` | `volume_convergence` |
| `volume_convergence` | 0.0008 |
| `wall_time` | 74.2 s |
| `sim_time` | 5,400 s |
| `max_depth` | 3.20 m |
| `median_depth` | 0.65 m |
| `flooded_area` | 1.84 km2 |
| Final assets | Depth raster, inundation polygon, and stage-transfer line. |
| Watcher observation | The watcher requested process termination after reading the 5,400 s depth grid. |
| Raw process return code | Not supplied. |
| Termination-signal details | No signal outcome or terminate-to-kill escalation record is supplied. |
| Configured wall-time limit | Not supplied, and the recorded termination is not `max_wall_time`. |
| Boundary details | No `edge_error` termination is recorded, but no saved boundary-check result, downstream-endpoint activation state, `allow_water_on_edges` value, or edge map is supplied. |
| Balance details | No outflow series, source or sink series, mass file, or balance residual is supplied. |
| Local transient details | No point WSE, velocity, flux, or cellwise depth-change history is supplied. |
| Observation evidence | None supplied. |

Treat the `sim_time` field as the current code's saved-grid count multiplied by the output interval.
Do not assume that this field or the termination condition is a raw executable exit status.

## Synthetic hot-start packet

The target scenario used a prior scenario's final depth raster as `startfile`.
The source scenario used the same grid dimensions, alignment, terrain asset, and vertical datum label.
Its inflow was \(45.0\ \text{m3/s}\), while the target inflow is \(50.0\ \text{m3/s}\).
The source downstream condition, run identity, convergence history, velocities, fluxes, and balance evidence are not supplied.
No cold-start target result is supplied.

## Part A: Calculate the convergence history

**Core inspection:** For every saved time:

1. Calculate stored volume in m3.
2. Calculate absolute storage change from the preceding saved grid where available.
3. Calculate interval inflow volume.
4. Calculate \(C_V\).
5. Apply the strict current comparison \(C_V<C_{tol}\).

Identify the first saved output that meets the stated current threshold.
Explain why the first output's stored value of `1.0` is not a calculated ratio from two grids.

## Part B: Separate the evidence

**Core inspection:** Create a table with one row for each of these categories:

- numerical stability;
- residual transient behavior;
- quasi-steady behavior;
- storage change;
- inflow-outflow mass-balance closure;
- solver exit status;
- wall-time and controlled-termination handling;
- boundary or edge behavior;
- hydraulic adequacy; and
- acceptance.

For each category, state what the packet supports, what remains missing, and whether a pass can be issued.
Do not use `volume_convergence` as evidence that outflow closure, stable internal stepping, intended edge behavior, or observed agreement passed.
For boundary evidence, distinguish a completed check with no flagged cells from the first-grid sentinel, a check withheld while the downstream endpoint is dry, an allowed-edge configuration, and a wet perimeter cell outside the inclusive endpoint-WSE range.
For process evidence, distinguish `max_wall_time` from `max_simulation_time`, solver-declared exit, convergence termination, and edge termination.

## Part C: Assess the hot start

**Core inspection:** State exactly which target initial state is supported by the packet.
List the compatibility evidence that is present.
List the source and target evidence that remains missing.

Explain why the source depth field may reduce initial filling time.
Then explain why the absent motion state and cold-start comparison prevent a claim of complete-state continuation or initial-condition independence.

Propose one target-scenario sensitivity comparison that would discriminate between an efficient compatible hot start and a path-dependent result.

## Part D: Assess solver support

**Core inspection:** Classify each statement as **External solver capability**, **Current implementation**, **Target or candidate material**, or **Unsupported conclusion**:

1. The official LISFLOOD-FP manual for its documented release defines `startfile` as initial water depth.
2. The reviewed checkout launches `lisflood` for scenario execution.
3. The local project solver-comparison document describes both solvers as interchangeable.
4. The current SFINCS writer raises `NotImplementedError` before export.
5. An enum value and unreachable writer prove current SFINCS support.
6. Official SFINCS documentation describes water-level boundaries, discharge sources, masks, initial water levels, and restart state.

Use [JOB-007](../reference/bibliography.md#job-007-convergence-hot-start-and-solver-execution-paths), [SYS-004](../reference/bibliography.md#sys-004-solver-comparison-document), and [CONF-003](../reference/conflicts-and-open-questions.md#conf-003-sfincs-documentation-and-current-support).

## Part E: Issue a readiness verdict

**Core inspection:** Choose exactly one verdict:

- `READY` means the supplied evidence establishes numerical, balance, boundary, sensitivity, and hydraulic acceptance for the stated scenario use.
- `NOT READY` means one or more required evidence categories are absent or fail.

State the verdict first.
Then list the smallest additional evidence set that could change it.
Keep synthetic arithmetic separate from project authority.

## Deliverable

Submit a short answer with these sections:

1. Convergence calculation table.
2. Evidence-separation table.
3. Hot-start assessment and sensitivity comparison.
4. Solver-support classification.
5. Direct readiness verdict and missing evidence.

## Competency criteria

The lab is complete when the answer:

- calculates interval inflow volume as \(45{,}000\ \text{m3}\);
- calculates ratios 0.5, 0.3, 0.1, 0.02, and 0.0008 after the sentinel;
- identifies 5,400 s as the first output satisfying the strict threshold;
- states that the ratio is a storage-change proxy rather than full mass-balance closure;
- does not infer a raw successful exit, termination-signal outcome, terminate-to-kill outcome, or completed clean edge check from the manifest fields;
- identifies depth as the only supplied hot-start state in the current path;
- preserves LISFLOOD-FP as current execution and SFINCS as candidate or target support; and
- issues `NOT READY` because the supplied packet lacks required numerical, balance, edge, sensitivity, and physical evidence.

After completing the lab, compare the reasoning with [Lab 7 Solution](solutions/lab-07-convergence-and-solver-evidence-solution.md).
