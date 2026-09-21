# LISFLOOD-FP and SFINCS

LISFLOOD-FP and SFINCS both solve depth-averaged flood-flow problems, but their files, boundary semantics, state representations, numerical controls, and outputs are not interchangeable by name alone.
This chapter compares bounded public documentation and does not claim that either solver is implemented or supported by a particular workflow.

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
- separate external solver capability, implementation support, and runtime validation evidence; and
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
The primary LISFLOOD-FP study in SCI-031 documents a local-inertial formulation evaluated in that paper.
The SFINCS 2.0.6 documentation in SCI-032 and SCI-034 describes its grid, mask, wetting, time-step, forcing, and restart contracts.
Each exact executable and configured equation set must be evaluated on its own evidence.

## Evidence scopes

Keep these source scopes separate throughout the comparison.

| Scope | What it can establish | What it cannot establish |
| --- | --- | --- |
| External solver documentation or primary research | Capability and formulation for the documented version or study. | What a separate workflow implements, executes, or validates. |
| Implementation evidence | The behavior of an inspected writer, runner, monitor, result contract, and artifact path. | General capability of every solver release or scientific adequacy. |
| Design or mapping evidence | An intended translation, comparison method, or acceptance rule. | A working solver path or a validated hydraulic result. |
| Runtime and validation evidence | Behavior of one exact build, configuration, dataset, and test. | Universal equivalence across reaches, scenarios, versions, and hardware. |

**Design principle:** Do not turn a public capability comparison into a claim of implementation support.
A workflow supports a solver only when its complete translation, execution, diagnostic, artifact, and validation paths are evidenced separately.

## Bounded comparison

All LISFLOOD-FP capability statements in this table are scoped to the official release 5.9.6 manual recorded in SCI-033 unless the row names the primary research in SCI-031.
All SFINCS capability statements are scoped to official documentation release 2.0.6 recorded in SCI-032 and SCI-034.
The statements do not inherit claims from floating documentation or establish behavior of a separate executable or workflow.

| Concept | LISFLOOD-FP 5.9.6 documentation | SFINCS 2.0.6 documentation |
| --- | --- | --- |
| Grid and elevation | The manual documents raster terrain and raster-based floodplain calculation. | The documentation describes a staggered rectilinear grid, optional rotation, elevation input, and optional subgrid tables. |
| Active domain | The manual places documented boundary conditions on domain edges or at points through its boundary-input contract. | The documentation defines mask values 0 inactive, 1 active, 2 water-level boundary, and 3 outflow. |
| Roughness | The manual documents Manning resistance inputs, including spatial grids. | The documentation describes uniform, land-sea, spatially varying, and subgrid roughness paths. |
| Discharge forcing | The manual documents fixed and time-varying discharge boundary forms whose values depend on boundary geometry. | The forcing documentation describes discharge source points and time series rather than a boundary form. |
| Specified-stage boundary | The manual documents fixed and time-varying free-surface elevation forms. | The documentation describes water-level boundary cells, boundary locations, and water-level time series. |
| Outflow condition | The manual describes a uniform-flow boundary with an optional free-surface or valley slope. | The documentation describes mask value 3 as an outflow cell whose depth is held at zero. |
| Initial state | The manual defines an initial water-depth raster for the documented release. | The documentation describes spatial water-level initialization and a restart file carrying WSE, fluxes, and mean velocities. |
| Output timing | The manual distinguishes saved outputs from other diagnostic intervals. | The documentation distinguishes map-output timing from internal adaptive time-step diagnostics. |
| Documentation identity | The manual is explicitly bounded to LISFLOOD-FP code release 5.9.6. | The cited input and forcing pages are explicitly bounded to SFINCS documentation release 2.0.6. |

This table is intentionally bounded.
It does not claim that one solver is more accurate, faster, supported, or interchangeable because those conclusions require controlled implementation and validation evidence.

## From documented capability to implementation support

**Design principle:** Supporting a solver requires an end-to-end contract for input translation, executable identity, invocation, monitoring, failure handling, post-processing, artifacts, diagnostics, and validation.
A configuration value, partial writer, container image, or solver name does not establish that contract.

**Applied example:** A candidate workflow translates one synthetic model to each solver.
It records the exact solver release and build, materialized terrain and roughness, discharge forcing, specified-stage boundaries, initial state, numerical controls, output inventory, process result, and water-balance evidence.
The comparison is accepted only after both paths complete those contracts and reproduce stated reference quantities within declared tolerances.

**Evidence note:** Passing the same terrain resolution and nominal discharge to two solvers does not make the problems equivalent.
The realized active domain, boundary placement, state variables, wetting rules, time integration, and output interpretation must also be compared.

## Normal-depth semantics require exact behavior

Normal depth is a hydraulic state in which steady uniform-flow resistance balances the relevant slope under stated geometry and roughness assumptions.
A software token is meaningful only after its solver and version define the mathematical behavior.
Freefall is a different physical concept involving loss of downstream support.

The LISFLOOD-FP 5.9.6 manual describes a uniform-flow boundary with an optional slope.
That documented boundary should therefore be described as a normal-depth or uniform-flow outflow when teaching its physical meaning.
The manual does not make normal depth, steep-slope drainage, and physical freefall interchangeable.

The SFINCS 2.0.6 documentation cited here describes water-level and zero-depth outflow mask roles.
It does not document the LISFLOOD-FP token or establish an equivalent slope-based normal-depth relation.

**Open question:** If two solver paths are intended to represent the same downstream condition, which documented equations, state locations, flow directions, and wetting behavior make them hydraulically comparable?
File-name similarity or a shared label cannot answer that question.

## Known-stage boundaries are not interchangeable files

A known-stage boundary supplies WSE relative to a stated vertical datum.
Its value, timing, location, interpolation, and wet or dry behavior must remain compatible with terrain and the target scenario.

The LISFLOOD-FP 5.9.6 manual documents a fixed free-surface-elevation boundary.
The SFINCS 2.0.6 documentation describes water-level boundary cells, boundary points, and a water-level time series.
Those two contracts can represent the same intended physical category while still differing in spatial interpolation, state placement, wetting behavior, and file structure.

**Applied example:** A transferred-stage boundary first aligns the source depth raster with source terrain and confirms compatible linear units.
Because depth has no vertical datum, source WSE is derived by adding depth to terrain expressed in the terrain's vertical datum.
The derived WSE is then confirmed or transformed to the target vertical datum and aligned with the target grid, interface geometry, and time support before sampling wet source cells.
The procedure preserves zero or negative datum-referenced WSE values when they are valid instead of treating sign as a wetness test.
The transfer record identifies the source terrain datum, target datum, grids, transformation, sampling geometry, time support, and every realized boundary point.

**Evidence note:** Equivalent intent does not establish equivalent realized stage transfer.
The spatial interpolation, time interpolation, cell classification, wetting behavior, and solver version must be verified for each path.

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

Implementation support requires more than a solver image or configuration value.
The executable must be identifiable, runnable, observable, and tied to inputs and artifacts through a stable identity.
Failures and controlled termination must be distinguishable.
Outputs must be converted into the required depth, inundation, metric, warning, and result-record contracts.

**Design principle:** Run identity should record the executable version and build, immutable image or package identity, run configuration, numerical mode, and relevant hardware information.
A solver name alone does not prove version-pinned or reproducible execution.

For either solver, gaps in reachable preprocessing, executable invocation, version discovery, monitoring, convergence and edge diagnostics, post-processing, result construction, artifacts, tests, or validation prevent a support claim.
Container availability or accelerator capability does not close those software and scientific gaps by itself.

## Solver-adoption evidence

Before a candidate solver is called supported or equivalent, require at least:

1. A version-pinned executable and reproducible build or image identity.
2. Validated translations for grid, terrain, roughness, inflow, known stage, slope-based outflow, active domain, and initial state.
3. Defined behavior for normal-depth outflow, freefall, and every solver-specific boundary form.
4. Equivalent or deliberately different output and diagnostic contracts with documented consequences.
5. Process-failure, timeout, controlled-termination, restart, and partial-artifact handling.
6. Storage-change, full-balance, edge, local-transient, and stability evidence appropriate to that solver.
7. Paired cold-start and hot-start or restart sensitivity where initialization matters.
8. Spatial, temporal, parameter, and boundary sensitivity comparisons.
9. Benchmarks against observations or accepted reference cases across representative reach classes.
10. Stated acceptance thresholds and assigned responsibility for deployment and scientific approval.

Agreement in final maximum depth alone is not enough to establish equivalence.
An implementation that omits the executable version, build, immutable package identity, configuration, or relevant hardware identity does not satisfy item 1.

## Common misconceptions

### Both solvers accept a slope, so the boundary is equivalent

The solver can use that slope in a different equation, state location, direction, and wetting context.
The exact input and hydraulic response must be compared.

### A partial preprocessing path proves solver support

An unexercised preprocessing path is candidate implementation material, not evidence of a complete executable solver path.

### Official capability proves implementation support

Official documentation establishes capability for its documented version.
Implementation support requires a working translation, execution, artifact, and validation contract.

### A faster image is the better solver

Runtime matters after the physical problem, numerical behavior, artifacts, diagnostics, reliability, and acceptance evidence satisfy the intended use.

### A solver token defines a universal physical condition

The LISFLOOD-FP 5.9.6 manual associates its documented open-boundary form with slope-based uniform-flow behavior.
Another solver or version can use a different token and governing relation, so descriptive physical meaning and versioned documentation must accompany the label.

## Competency check

Describe one common physical requirement shared by both solvers and one solver-specific implementation difference for each of grid, inflow, known stage, outflow, and initial state.
Then explain why a configuration value and partial writer do not establish implementation support.
Finally, state which evidence would control a claim about implementation execution and which source would control a claim about external SFINCS 2.0.6 capability.

## Practice

Complete [Lab 7: Convergence and Solver Evidence](../labs/lab-07-convergence-and-solver-evidence.md).
After Lab 7, review the chapters on normal depth, backwater, grids, stability, convergence, and solver behavior before beginning model-development material.

## Source notes

- **Scientific foundation:** The LISFLOOD-FP local-inertial formulation is supported by [SCI-031](../reference/bibliography.md#sci-031-lisflood-fp-local-inertial-formulation).
- **Scientific foundation:** Historical LISFLOOD-FP grid, boundary, output, and initial-depth contracts are bounded to release 5.9.6 in [SCI-033](../reference/bibliography.md#sci-033-lisflood-fp-user-manual).
- **Scientific foundation:** SFINCS grid, masks, terrain, roughness, initialization, restart, outputs, numerical controls, and forcing files are bounded to documentation release 2.0.6 in [SCI-032](../reference/bibliography.md#sci-032-sfincs-user-manual) and [SCI-034](../reference/bibliography.md#sci-034-sfincs-forcing-documentation).
- **Design principle:** Public capability, implementation support, and runtime validation are separate evidence scopes.
- **Open question:** Hydraulic equivalence remains unestablished until both solver paths are version-pinned, fully implemented, and compared with declared acceptance criteria.
