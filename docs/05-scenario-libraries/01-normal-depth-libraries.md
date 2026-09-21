# Discharge-Only Scenario Libraries

A discharge-only library represents one reach with a bounded set of steady upstream discharges and a slope-based downstream condition.
Each scenario must preserve its model realization, discharge, downstream slope, boundary geometry, initial state, run settings, termination evidence, artifacts, and membership decision.

## Why this topic matters

A scenario can finish and publish without proving that its downstream condition is physically adequate, its domain contains the relevant floodplain, or its result is independent of the initial state.
Published trials are also not necessarily selected library members.

## Prerequisites

Read [Flood Frequency, AEP, and Bounds](../01-hydrology-for-fim/04-flood-frequency-aep-and-bounds.md), [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md), [Backwater and Boundary Control](../02-open-channel-flow/04-backwater-and-boundary-control.md), and [The Model-Building Operation](../04-model-development/05-build-model-job.md).

## Learning objectives

After this chapter, the reader should be able to:

- explain what a discharge-only scenario represents;
- distinguish authored bounds, trial discharges, and selected members;
- describe slope-based downstream boundary construction;
- distinguish termination, convergence, mass balance, edge evidence, publication, and materialization;
- identify hot-start compatibility requirements;
- interpret scenario artifacts and warnings; and
- state what evidence is required before downstream-stage planning uses the library.

## What one scenario means

**Scientific foundation:** A normal-depth condition applies a friction-slope relation at a boundary and does not imply uniform flow throughout the model.
Downstream boundary concepts are supported by [SCI-023](../reference/bibliography.md#sci-023-hec-ras-uniform-flow-computations) and [SCI-024](../reference/bibliography.md#sci-024-hec-ras-downstream-boundary-conditions).

A discharge-only scenario contains:

- one model identity and realization;
- one upstream discharge;
- one downstream slope value and selected edge geometry;
- one initial-state record;
- one run-settings record;
- termination and diagnostic evidence;
- final-state artifacts; and
- a scenario identity that covers every output-affecting value.

The slope is a boundary approximation, not proof that downstream backwater is absent.

## Synthetic library definition

**Applied example:** The terminal reach `R-200` uses authored discharge bounds of 100 to 250 cubic metres per second.
The candidate grid is 25 cubic metres per second.
The selected discharge set after interval refinement contains 100, 125, 175, 200, 225, and 250 cubic metres per second.

The downstream slope is 0.0015 m/m for every member.
The model uses 10 m cells, metre units, vertical datum `VD-1`, and the realized model record defined in the preceding chapter.

| Discharge | Role | Initial state | Membership |
| ---: | --- | --- | --- |
| 100 m3/s | Lower endpoint | Dry start | Selected |
| 125 m3/s | Interval-refinement member | Depth from 100 m3/s | Selected |
| 175 m3/s | Interval-refinement member | Depth from 100 m3/s | Selected |
| 200 m3/s | Interval-refinement member | Depth from 175 m3/s | Selected |
| 225 m3/s | Interval-refinement member | Depth from 200 m3/s | Selected |
| 250 m3/s | Upper endpoint | Depth from 100 m3/s | Selected |

The 150 m3/s grid candidate remains untried under the interval-refinement stopping rule.
The table states every discharge needed to interpret this example.
It does not claim that these values are suitable for another reach.

## `R-200` forcing allocation

Each `R-200` discharge is the total flow applied across two named upstream boundary lines plus one local incremental contribution.
The prepared drainage areas are 84 square kilometres for `R-100`, 51 square kilometres for `R-300`, and 7 square kilometres for the local area between the upstream boundaries and the `R-200` outlet.

The synthetic allocation begins with drainage-area proportions of 84:51:7 over the total `R-200` area of 142 square kilometres.
The two named boundary values are rounded to the nearest whole cubic metre per second, and the local contribution is the residual required to preserve the exact total.

| Total `R-200` discharge | `R-100` boundary | `R-300` boundary | Local incremental contribution |
| ---: | ---: | ---: | ---: |
| 100 m3/s | 59 m3/s | 36 m3/s | 5 m3/s |
| 125 m3/s | 74 m3/s | 45 m3/s | 6 m3/s |
| 175 m3/s | 104 m3/s | 63 m3/s | 8 m3/s |
| 200 m3/s | 118 m3/s | 72 m3/s | 10 m3/s |
| 225 m3/s | 133 m3/s | 81 m3/s | 11 m3/s |
| 250 m3/s | 148 m3/s | 90 m3/s | 12 m3/s |

The local contribution can be represented by a separate named source region or distributed over a documented interior area.
Its geometry and allocation method must not be hidden inside one of the tributary inflows.

**Design principle:** Scenario identity must include the total discharge, both named boundary allocations, the local contribution and its geometry, the 84:51:7 allocation rule, and the rounding-and-residual convention.

## Discharge bounds need provenance

Bounds may come from flood-frequency analysis, retrospective flows, design requirements, rating information, or another stated method.
The scenario record should preserve source version, period, fit, uncertainty, units, and rounding.

**Design principle:** A scenario runner should consume validated bounds and preserve their provenance, but it should not silently invent the hydrologic method that produced them.

At the request boundary, require finite positive values, an upper bound not below the lower bound, a positive candidate step, and settings that use compatible units.

## Constructing the downstream slope

One simple terrain-based diagnostic is:

\[
S_b=\max\left(\frac{|z_u-z_d|}{L},S_{min}\right)
\]

$z_u$ and $z_d$ are endpoint terrain elevations, $L$ is reach length, and $S_{min}$ is a documented positive floor.
The absolute value prevents a negative input but can hide a direction or terrain problem, so direction must be checked separately.

For the applied example, the authored slope is 0.0015 m/m and does not use this diagnostic formula.
The model record preserves the boundary geometry and value directly.

## Scenario workflow

### 1. Validate the scenario record

Validate model identity, discharge, boundary type and value, boundary geometry, initial state, run settings, units, and artifact requirements.
Unknown fields should fail when silent acceptance could hide a misspelling.

### 2. Observe the model artifacts

Read the model record from storage and verify terrain, roughness, domain, grid, centerline, and boundary-support geometry before running a scenario.
A model address returned by an earlier operation is not enough.

### 3. Construct boundary inputs

Rasterize the inflow geometry and distribute total discharge according to the documented solver convention.
Intersect the downstream boundary geometry with perimeter cells while preserving disjoint segments.

### 4. Resolve the initial state

A dry start supplies no prior water depth.
A warm start can supply depth or a complete solver state, depending on the method.
The record must state exactly what was transferred.

Depth alone does not preserve velocity, momentum, face flux, turbulence variables, or a complete checkpoint.
Warm-start compatibility therefore requires the same grid, terrain, vertical reference, wetting convention, solver family, and compatible boundary context.

### 5. Compute scenario identity and check reuse

Identity should cover the complete model identity, total discharge, each named inflow allocation, local incremental contribution and geometry, allocation and rounding rules, downstream condition and geometry, run settings, initial state, producer version, and required artifacts.
Reuse also requires current observation of every referenced asset.

### 6. Execute and monitor

Monitor solver exit status, simulated time, wall time, convergence indicators, mass balance, and edge conditions.
Persist the sequence or sufficient summaries to explain why the run stopped.

Convergence and edge checks can be true at the same saved state.
The record should preserve both rather than allowing branch order to hide one condition.

### 7. Post-process the final state

Create the final depth raster, inundation geometry, water-surface or transfer support, metrics, and scenario record.
Recover spatial reference and units from the realized model record rather than assigning a fallback that can mislabel the output.

### 8. Publish and observe

Publish one complete generation and then read it from storage.
Verify the scenario record and every required asset before recording materialization.

### 9. Record library membership

Store the selected member list separately from the inventory of finished trials.
Membership should name the selection method, comparison evidence, endpoints, exceptions, and revision.

## Final-state metrics

For wet cells $W$, square-cell resolution $r$, and wet-cell count $N_w$:

\[
h_{max}=\max_{i\in W}(h_i)
\]

\[
h_{med}=\operatorname{median}_{i\in W}(h_i)
\]

\[
A_f=\frac{N_wr^2}{10^6}
\]

Maximum and median depth are in metres when depth is in metres.
Flooded area is in square kilometres only when $r$ is in metres.
The wet threshold and nodata rule must be stated.

Median depth can fall when new shallow cells become wet because the population changes.
That behaviour is not necessarily a numerical error.

## Artifact roles

| Artifact | What it supports | What it does not prove |
| --- | --- | --- |
| Final depth | Spatial depth for the final saved state | Correct datum, convergence, or scientific acceptance |
| Inundation geometry | Wet-area footprint under the stated threshold | Boundary adequacy or observational agreement |
| Transfer support | Candidate geometry or values for upstream coupling | Compatibility with every upstream model |
| Scenario record | Identity, inputs, outputs, and diagnostics | Current asset existence unless observed |
| Library index | Selected membership and selection rationale | Hydraulic validity of each member |

## Edge handling and partial completion

An edge diagnostic should distinguish intended boundary cells from unintended perimeter wetting.
It should record activation state, wet sides, cell counts, water-surface range, connectivity, and margin to the domain edge.

**Design principle:** Do not interpret the absence of an edge warning as proof that the domain is adequate.
The check may not have activated, water may be allowed on an intended boundary, or another stopping condition may have occurred first.

If a scenario fails, retain a clear partial-result record without adding the scenario to the selected library.
Previously published trials can remain valid only if their own records and assets are complete and observed.

## Synthetic failure examples

### A warm start is adopted by filename alone

The source depth uses another grid and vertical reference.
The run starts without an explicit error but the initial state is spatially wrong.

### An obsolete trial is mistaken for a member

A complete 150 m3/s result from an earlier plan remains in storage, so a consumer includes it even though the current library index excludes it.

### Convergence hides an edge condition

The convergence threshold and unintended lateral-edge wetting occur at the same saved state.
Persisting only the convergence reason removes material diagnostic evidence.

### Publication stops before the scenario record

Depth is visible, but the generation lacks the record needed to prove identity and completeness.
The observer must reject the partial publication.

## Readiness boundary

A discharge-only library is ready for downstream scientific use only when:

1. discharge bounds and units are traceable;
2. each selected scenario has complete identity and observed artifacts;
3. membership is explicit and distinct from trial inventory;
4. convergence, mass balance, edge, and domain evidence are adequate;
5. warm-start sensitivity is understood; and
6. downstream-boundary limitations are carried forward.

## Common misconceptions

### Normal-depth control means uniform flow everywhere

It defines one downstream condition and does not remove local acceleration, storage, or backwater within the model.

### Termination proves convergence

A run can stop because of wall time, simulated time, failure, edge policy, or another condition.

### No edge warning proves containment

The inference exceeds the implemented and persisted evidence.

### Every published scenario is a library member

Obsolete trials and scientifically rejected candidates can be complete artifacts without belonging to the selected set.

## Competency check

1. What does the 0.0015 m/m value mean in the applied example?
2. Which applied discharges are selected, and which candidate remains untried?
3. Why is depth-only initialization not a complete checkpoint?
4. Which evidence should remain separate from solver termination?
5. Why must a library index remain distinct from storage inventory?
6. What must an observer verify before recording a scenario as materialized?
7. How are total `R-200` discharge, the two tributary boundaries, and the local incremental contribution related?

## Further reading and source notes

- **Scientific foundation:** Uniform-flow and normal-depth calculations are supported by [SCI-023](../reference/bibliography.md#sci-023-hec-ras-uniform-flow-computations).
- **Scientific foundation:** Downstream boundary-condition concepts are supported by [SCI-024](../reference/bibliography.md#sci-024-hec-ras-downstream-boundary-conditions) and [SCI-026](../reference/bibliography.md#sci-026-hec-ras-2d-external-boundary-conditions).
- **Scientific foundation:** Model sensitivity and credibility boundaries are supported by [SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity) and [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations).
