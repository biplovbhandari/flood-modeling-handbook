# LISFLOOD-FP and SFINCS

LISFLOOD-FP and SFINCS both solve depth-averaged flood-flow problems, but their files, boundary semantics, state representations, numerical controls, and outputs are not interchangeable by name alone.
The current project checkout executes LISFLOOD-FP and does not provide an executable SFINCS scenario path.

## Why this topic matters

A solver comparison should begin with the physical problem and evidence requirements rather than with filenames or runtime claims.
A candidate solver becomes supported only after its complete input, execution, output, diagnostic, identity, and validation contracts work for the intended scenarios.

## Prerequisites

Read the preceding four chapters in this section and [Backwater and Boundary Control](../02-open-channel-flow/04-backwater-and-boundary-control.md).
Retain the distinctions among a known-stage boundary, a slope-based normal-depth condition, freefall, an initial condition, and an active-domain mask.

## Learning objectives

After this chapter, the reader should be able to:

- compare common physical and evidence requirements before solver-specific differences;
- map terrain, roughness, inflow, known-stage, outflow, active-domain, and initial-state concepts to each solver's documented inputs;
- explain why identical labels do not guarantee identical boundary behavior;
- separate external solver capability, current checkout support, target methodology, and deployment implications; and
- state the evidence required before calling two solver paths equivalent.

## Start with the common hydraulic problem

Both solvers need a bounded problem definition that includes:

- a horizontal grid or mesh with a stated CRS, alignment, extent, and active domain;
- terrain or elevation information with compatible units and vertical reference;
- resistance information such as Manning's n at a defensible scale;
- inflow locations, values, units, and timing;
- downstream and lateral boundary locations with explicit mathematical meaning;
- an initial water state compatible with the target problem;
- simulation timing and numerical controls;
- outputs and diagnostics sufficient for stability, convergence, balance, edge, sensitivity, and physical review; and
- solver version, build, configuration, identity, and artifact provenance.

The common shallow-water vocabulary does not imply identical equations or discretizations.
The primary LISFLOOD-FP paper documents a local-inertial formulation in that model family.
Official SFINCS documentation exposes its own grid, mask, wetting, time-step, forcing, and restart contracts.
Each exact executable and configured equation set must be evaluated on its own evidence.

## Four source scopes

Keep these source scopes separate throughout the comparison.

| Scope | What it can establish | What it cannot establish |
| --- | --- | --- |
| External solver documentation or primary research | Capability and formulation for the documented version or study. | What the current project checkout executes or validates. |
| Current project code | The checked-out writer, runner, watcher, result, and artifact behavior. | General capability of every solver release or methodological approval. |
| Project comparison or target material | Candidate mappings, intended equivalence, and deployment concepts. | A working current path when code raises `NotImplementedError` or lacks execution and post-processing. |
| Runtime and validation evidence | Behavior of one exact build, configuration, dataset, and test. | Universal equivalence across reaches, scenarios, versions, and hardware. |

The local solver-comparison document says both solvers are approved and interchangeable in the automation framework.
That statement conflicts with the current checkout, which cannot generate or execute a SFINCS scenario through the selected path.
[CONF-003](../reference/conflicts-and-open-questions.md#conf-003-sfincs-documentation-and-current-support) preserves the conflict instead of selecting the more convenient description.

## Bounded comparison

All SFINCS capability statements in this table are scoped to the official documentation release 2.0.6 recorded in SCI-032 and SCI-034.
They do not inherit claims from the floating `latest` documentation or establish behavior of a current project executable.

| Concept | LISFLOOD-FP documented capability | SFINCS documented capability | Current project checkout |
| --- | --- | --- | --- |
| Grid and elevation | The official manual documents raster terrain and raster-based floodplain calculation. | The official manual documents a staggered rectilinear grid, optional rotation, elevation input, and optional subgrid tables. | The LISFLOOD-FP writer converts aligned terrain and roughness rasters to ARC ASCII. The unreachable SFINCS writer contains binary-grid methods but is not current execution support. |
| Active domain | Boundary conditions can be placed on domain edges or at points in the documented `.bci` contract. | Official documentation release 2.0.6 defines mask values 0 inactive, 1 active, 2 water-level boundary, and 3 outflow. | Current LISFLOOD-FP execution uses the raster domain. Candidate SFINCS code initializes a full active mask and assigns other values, but the writer is blocked before files are produced. |
| Roughness | The manual documents Manning resistance inputs, including spatial grids. | The official manual documents uniform, land-sea, spatially varying, and subgrid roughness paths. | Current LISFLOOD-FP execution writes `manningfile` from the project roughness raster. |
| Inflow | `QFIX` and `QVAR` are documented, with `.bci` values interpreted according to boundary geometry. | Source coordinates and discharge time series are documented in `sfincs.src` and `sfincs.dis`. | Current code distributes steady total inflow across rasterized points, writes LISFLOOD-FP `QFIX`, and does not expose a current hydrograph path. |
| Known stage | `HFIX` is a fixed free-surface elevation, and `HVAR` supplies a time-varying elevation. | Mask value 2, boundary locations, and `bzs` time series prescribe water levels. | Current transfer boundaries become cell-specific LISFLOOD-FP `HFIX` points. Candidate SFINCS `.bnd` and `.bzs` methods are unreachable. |
| Slope-based or open outflow | The official manual describes `FREE` as uniform flow with an optional free-surface or valley slope. | Official documentation release 2.0.6 describes mask value 3 as an outflow cell whose depth is held at zero. It does not establish the local project's proposed mask 5 and `bdr` gradient contract. | Current `FreeBC` values are documented in code as normal-depth slopes and are written to LISFLOOD-FP. Candidate SFINCS mask 5 and `bdr` methods are unreachable and require exact-version verification. |
| Initial state | The official manual defines `startfile` as an initial water-depth raster for the documented release. | Official documentation release 2.0.6 describes spatial water-level initialization and a restart file carrying WSE, fluxes, and mean velocities. | The current hot-start path supplies only a prior depth raster to LISFLOOD-FP `startfile`. It does not build a SFINCS initial or restart state. |
| Output timing | The official manual distinguishes saved outputs and other diagnostic intervals. | The official manual distinguishes map-output timing and internal adaptive time-step diagnostics. | Current convergence watches LISFLOOD-FP saved depth grids at `save_interval_seconds`, not internal time steps or `mass_interval_seconds`. |
| Execution | The model runs from an executable and parameter file. | SFINCS has its own executable and input contract outside this project path. | Current code launches `lisflood <parfile>`. It contains no corresponding SFINCS launch, watch, post-process, or result path. |

This table is intentionally bounded.
It does not claim that one solver is more accurate or faster for the project because no controlled project benchmark supporting that conclusion was inspected for this chapter.

## LISFLOOD-FP in the current path

**Current implementation:** `SCENARIO_SOLVER` defaults to `lisflood`.
`write_model_files` selects `LisfloodWriter`, and `solve_scenario` launches the `lisflood` executable with the generated parameter file.
The parameter writer selects the acceleration formulation, can enable CUDA, and supplies terrain, roughness, boundary, output, simulation-time, initial-step, and optional depth-start inputs.

The current boundary writer rasterizes project geometries into `.bci` elements.
It divides total inflow by cell width and the number of inflow points so that LISFLOOD-FP can convert per-unit-width `QFIX` values to volumetric input.
It writes slope-based `FREE` conditions and converts transfer depths plus terrain to cell-specific `HFIX` elevations.

The watcher inspects saved `.wd` depth grids for storage change and a narrowly defined edge-WSE condition.
Post-processing selects the last saved grid, writes the final depth raster and inundation polygon, and derives an STL and nominal upstream WSE.
The generic code can assemble saved depths into a directory-backed Zarr store when `RunConfig.save_zarr` is true, but the public jobs do not forward that setting and the generic branch then fails when file-only hashing is applied to the directory.
These current artifacts do not include a complete outflow balance or velocity product.

The current path is evidence of implemented LISFLOOD-FP execution for the checked-out revision.
It is not evidence that every documented LISFLOOD-FP feature is enabled, that the exact executable matches the old official manual in every detail, or that hydraulic adequacy has been established for every reach.
The reviewed code implements ND scenario execution, but this code inspection does not supply representative-reach validation showing that the current slope, edge placement, convergence threshold, or final products are hydraulically adequate.

## Normal-depth semantics require exact behavior

Normal depth is a hydraulic state in which steady uniform-flow resistance balances the relevant slope under stated geometry and roughness assumptions.
The word `FREE` is only a token until its solver and configuration define the mathematical behavior.
Freefall is a different physical concept involving loss of downstream support.

The official LISFLOOD-FP manual for release 5.9.6 describes `FREE` as a uniform-flow boundary with an optional slope.
The current project schema describes `FreeBC.value` as a normal-depth slope in m/m and passes it to LISFLOOD-FP.
DR-003 nevertheless uses freefall language for selected KWSE edge treatment, while the current KWSE job supplies `FREE 0.5` in addition to its transfer boundary.
DR-039 has a file-local selection marker for ND edge behavior but no registered status in the reviewed Decision Register.

These sources do not make `FREE`, normal depth, steep-slope drainage, and freefall interchangeable.
[CONF-001](../reference/conflicts-and-open-questions.md#conf-001-boundary-condition-terminology-and-behavior) and [CONF-008](../reference/conflicts-and-open-questions.md#conf-008-unregistered-dr-039-selection) preserve the unresolved authority and behavior.

Official SFINCS documentation release 2.0.6 cited here documents water-level and zero-depth outflow mask roles.
The local project comparison and unreachable writer instead refer to a proposed mask 5 and `sfincs.bdr` gradient construction.
That candidate mapping needs documentation for the exact intended SFINCS version, an executable writer and runner, and hydraulic comparison evidence before it can be called supported normal-depth behavior.

## Known-stage boundaries are not interchangeable files

A known-stage boundary supplies WSE relative to a stated vertical datum.
Its value, timing, location, interpolation, and wet or dry behavior must remain compatible with terrain and the target scenario.

LISFLOOD-FP `HFIX` is documented as fixed free-surface elevation.
SFINCS documents water-level boundary cells, boundary points, and a water-level time series.
Those two contracts can represent the same intended physical category while still differing in spatial interpolation, state placement, wetting behavior, and file structure.

The current project transfer path samples a downstream scenario's depth plus terrain along an STL and writes a cell-specific LISFLOOD-FP `HFIX` point only when the resulting WSE is greater than zero.
The writer does not require positive downstream depth, so a dry cell with positive terrain can produce an `HFIX` point, while zero or negative WSE is omitted.
The nominal transfer `bc_value` participates in scenario labeling or identity but does not set those per-cell `HFIX` values.
Wet-cell filtering, zero or negative WSE support, source-target datum compatibility, and validation of the realized points remain Open questions.
The presence of candidate SFINCS `.bnd` and `.bzs` writer methods does not prove equivalent stage transfer because those methods do not execute in the current checkout.

## Active-domain behavior affects boundaries

An active-domain definition decides where equations are updated and where external conditions attach.
A full rectangular raster with overlaid boundary elements is not automatically equivalent to a mask that classifies inactive, active, stage-boundary, or outflow cells.

Before comparing results, verify:

- the same physical area and intended openings are represented;
- cell centers and faces align with the same terrain and boundary geometry;
- inactive regions do not create different barriers or shortcuts;
- inflow and stage values enter at hydraulically comparable locations;
- outflow cells have comparable one-way or two-way behavior; and
- wetting, drying, and boundary-adjacent storage are treated compatibly.

Matching grid resolution and bounding box is not enough.

## Deployment is part of support, not proof of hydraulics

Current support requires more than a solver image or enum value.
The executable must be identifiable, runnable, observable, and tied to inputs and artifacts through a stable identity.
Failures and controlled termination must be distinguishable.
Outputs must be converted into the project's required depth, inundation, STL, metric, warning, and manifest contracts.

**Current implementation:** The current run identity and its hash contain only the `SupportedSolver` enum value and the jobs image's baked `SDR_COMMIT`.
They do not record the executable version or build, image digest, run configuration, or hardware identity.
A current manifest therefore does not prove version-pinned execution or a complete solver-environment identity.

The checked-out code provides those paths only for LISFLOOD-FP.
For SFINCS, current gaps include reachable preprocessing, executable invocation, version discovery, output watching, convergence and edge diagnostics, post-processing, result construction, artifacts, tests, and validation evidence.
Container availability or external GPU capability would not close those software and scientific gaps by itself.

## Solver-adoption evidence

Before a candidate solver is called supported or equivalent, require at least:

1. A version-pinned executable and reproducible build or image identity.
2. Validated translations for grid, terrain, roughness, inflow, known stage, slope-based outflow, active domain, and initial state.
3. Defined behavior for `FREE`, normal depth, freefall, and every solver-specific boundary token.
4. Equivalent or deliberately different output and diagnostic contracts with documented consequences.
5. Process-failure, timeout, controlled-termination, restart, and partial-artifact handling.
6. Storage-change, full-balance, edge, local-transient, and stability evidence appropriate to that solver.
7. Paired cold-start and hot-start or restart sensitivity where initialization matters.
8. Spatial, temporal, parameter, and boundary sensitivity comparisons.
9. Benchmarks against observations or accepted reference cases across representative reach classes.
10. Stated acceptance thresholds and authorized ownership for deployment and scientific approval.

Agreement in final maximum depth alone is not enough to establish equivalence.
The current manifest identity does not satisfy item 1 because it omits the executable version, build, image digest, configuration, and hardware identity.

## Common misconceptions

### Both solvers accept a slope, so the boundary is equivalent

The solver can use that slope in a different equation, state location, direction, and wetting context.
The exact input and hydraulic response must be compared.

### A writer class proves solver support

Unreachable code that raises `NotImplementedError` before writing files is candidate implementation material, not an executable path.

### Official capability proves project support

Official documentation establishes capability for its documented version.
Project support requires a working current translation, execution, artifact, and validation contract.

### A faster image is the better solver

Runtime matters after the physical problem, numerical behavior, artifacts, diagnostics, reliability, and acceptance evidence satisfy the intended use.

### `FREE` means freefall

The current schema and official LISFLOOD-FP manual associate `FREE` with slope-based uniform or normal-flow behavior.
The selected-project freefall wording remains a recorded conflict.

## Competency check

Describe one common physical requirement shared by both solvers and one solver-specific implementation difference for each of grid, inflow, known stage, outflow, and initial state.
Then explain why the current SFINCS enum and writer methods do not establish support.
Finally, state which source would control a claim about current project execution and which source would control a claim about external SFINCS capability.

## Practice

Complete [Lab 7: Convergence and Solver Evidence](../labs/lab-07-convergence-and-solver-evidence.md).
After Lab 7, review the Pass 2 chapters on normal depth, backwater, grids, stability, convergence, and solver behavior before beginning project-methodology material.

## Source notes

- **Primary research:** The LISFLOOD-FP local-inertial formulation is supported by [SCI-031](../reference/bibliography.md#sci-031-lisflood-fp-local-inertial-formulation).
- **Official solver documentation:** Historical LISFLOOD-FP grid, boundary, output, and initial-depth contracts are recorded in [SCI-033](../reference/bibliography.md#sci-033-lisflood-fp-user-manual).
- **Official solver documentation:** SFINCS grid, masks, terrain, roughness, initialization, restart, outputs, numerical controls, and forcing files are scoped to documentation release 2.0.6 in [SCI-032](../reference/bibliography.md#sci-032-sfincs-user-manual) and [SCI-034](../reference/bibliography.md#sci-034-sfincs-forcing-documentation).
- **Current implementation:** Checked-out support and execution behavior are recorded in [JOB-003](../reference/bibliography.md#job-003-current-implementation-locations) and [JOB-007](../reference/bibliography.md#job-007-convergence-hot-start-and-solver-execution-paths).
- **Target or candidate comparison:** The local comparison is recorded with bounded authority in [SYS-004](../reference/bibliography.md#sys-004-solver-comparison-document).
- **Open questions:** Boundary terms and solver support remain governed by [CONF-001](../reference/conflicts-and-open-questions.md#conf-001-boundary-condition-terminology-and-behavior), [CONF-003](../reference/conflicts-and-open-questions.md#conf-003-sfincs-documentation-and-current-support), and [CONF-008](../reference/conflicts-and-open-questions.md#conf-008-unregistered-dr-039-selection).
