# What Is Flood Inundation Mapping?

Flood inundation mapping connects a water condition to an estimate of where water may go and what hydraulic conditions may occur there.
The map is a model result with stated inputs, assumptions, spatial support, units, and limitations.
It is not a direct observation of every flooded location and it is not a complete prediction unless the forcing and scenario context are also specified.

## Why this topic matters

A depth raster, inundation polygon, and water-surface raster can look similar on a screen while answering different questions.
Correct interpretation begins by naming the represented quantity and tracing the hydrologic forcing and hydraulic response that produced it.
That distinction also clarifies which work belongs to scientific modeling and which work belongs to orchestration, storage, and product assembly.

## Prerequisites

The reader should be familiar with maps, raster and vector data, and the idea that elevations use a coordinate reference and a vertical reference.
No knowledge of the project scenario families or hydraulic solver is required.

## Learning objectives

After this chapter, the reader should be able to:

- describe what a FIM product represents;
- distinguish depth, extent, WSE, and velocity products;
- distinguish hydrologic forcing from hydraulic response;
- compare GIS-based, one-dimensional, and two-dimensional inundation methods without treating one method as universally best; and
- identify the scientific and software boundaries of the current 2D FIM jobs.

## The minimum FIM statement

**Scientific foundation:** A useful FIM statement has three parts: a water condition, a hydraulic or geospatial method, and a mapped result.

For example, a result might state that a specified discharge was applied to a modeled reach, a hydraulic model calculated the response under stated downstream and terrain conditions, and the final water depth and extent were mapped.
Omitting the discharge, downstream condition, terrain realization, or vertical datum removes information needed to interpret the map.

The phrase flood inundation map is often used for both the mapped extent and a collection of related raster quantities.
The handbook uses FIM product when the exact mapped quantity could vary, and names the quantity whenever interpretation depends on it.

## Four common mapped products

### Depth

Depth is the vertical distance from the local terrain or bed elevation to the water surface.
For compatible elevations, depth is calculated as $h = WSE - z_b$.
Depth supports questions such as how far water stands above the modeled ground at a location.

### Extent

Extent identifies the area classified as inundated under a stated wetting rule.
It may be represented as wet raster cells or an inundation polygon.
Extent does not preserve how deep or fast the water is within the mapped area.

### Water-surface elevation

Water-surface elevation, or WSE, is the elevation of the water surface relative to a stated vertical datum.
WSE supports comparison across locations and transfer of downstream water levels only when the horizontal locations, units, and vertical references are compatible.
WSE is not interchangeable with depth because terrain elevation varies in space.

### Velocity

Velocity describes the rate and direction of water motion.
A velocity product may contain a magnitude, directional components, or both.
Velocity and depth together can support hazard interpretation, but the hazard meaning depends on the chosen method and use case.

**Current implementation:** The checked-out scenario manifest defines final depth, inundation polygon, and stage-transfer-line assets, with a nullable time-series Zarr field.
Neither public scenario job currently forwards `save_zarr` into `RunConfig`, and the generic true branch cannot complete manifest construction because it applies file-only hashing to a directory-backed store.
The current ND and KWSE input models reject `save_velocity=true` as not implemented.
General FIM science therefore includes velocity, but the current jobs checkout must not be described as producing a supported velocity raster through that option.
See the [current solver models](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/solvers.py), [ND input model](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_nd_scenarios.py), and [KWSE input model](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_kwse_scenarios.py).

## Hydrologic forcing and hydraulic response

**Scientific foundation:** Hydrology describes how water is generated, accumulated, and routed through a catchment and drainage network.
Its relevant output at the hydraulic-model boundary is often a discharge value or hydrograph.

Hydrologic forcing is the imposed water input to a hydraulic problem.
Examples include a constant upstream discharge, a time-varying discharge hydrograph, or rainfall applied directly to a domain.

A water level specified at a downstream boundary is a hydraulic boundary control rather than an imposed mass input.
The water level may come from an observation, another hydraulic model, or scenario planning.
It constrains the hydraulic response and may influence conditions upstream, while the upstream discharge or other water input remains the hydrologic forcing.

Hydraulic response is what the modeled water does after the forcing meets terrain, channel geometry, roughness, initial conditions, and downstream controls.
Response quantities include WSE, depth, velocity, inundated area, and storage.

The same discharge can produce different hydraulic responses when terrain, roughness, downstream stage, domain extent, or initial conditions change.
Conversely, similar inundation extents can arise from different combinations of forcing and boundary control.
A map alone therefore does not identify its cause.

**Current implementation:** The three current modeling jobs do not simulate rainfall-runoff generation.
`build_model` consumes a prepared reach network and source data to create a hydraulic model realization.
The scenario jobs consume that built model plus hydraulic inputs such as upstream discharge and downstream conditions.
The hydrologic process that selected or forecast that discharge is upstream of the jobs package.

## Three method families

The following comparison describes representation and use, not a quality ranking.
Scientific adequacy depends on the question, data, scale, controls, and validation evidence.

| Method family | How inundation is represented | Typical strengths | Important limitations and checks |
| --- | --- | --- | --- |
| GIS-based inundation method | Relates elevations, drainage connectivity, HAND-like surfaces, rating information, or other geospatial rules to a mapped water condition. | Can support broad coverage, rapid evaluation, and consistent geospatial processing. | Hydraulic interactions may be simplified or inherited from external relationships, so backwater, momentum, structures, and local flow paths require explicit evaluation. |
| One-dimensional hydraulic library | Solves flow along a channel network or cross-section sequence and maps section water levels across terrain. | Represents longitudinal controls and channel geometry efficiently when a dominant flow direction is a suitable abstraction. | Lateral exchange and multidirectional floodplain flow depend on the chosen cross-sections, connections, and mapping method. |
| Two-dimensional hydraulic library | Solves depth and horizontal flow over a grid or mesh and stores responses for selected forcing and boundary combinations. | Represents multiple horizontal flow paths, floodplain storage, lateral exchange, and spatially varying terrain and roughness. | Results depend on grid, terrain, roughness, boundary conditions, numerical method, runtime, and scenario coverage, and computational cost can be substantial. |

A GIS-based method can be the appropriate choice for one decision and a hydraulic model can be necessary for another.
A one-dimensional model can represent a confined river efficiently, while a two-dimensional model can better expose flow splits across a complex floodplain.
Method selection should follow the intended decision and evidence requirements rather than the visual detail of the output.

## The 2D FIM product boundary

The project's scientific chain starts before a hydraulic job and ends after an individual scenario run.
Hydrologic analysis supplies discharge bounds or values.
Reach and terrain preparation define a hydraulic model.
Scenario execution calculates reach responses.
Network planning selects compatible reach and downstream-condition combinations.
Product assembly combines selected reach results into a composite FIM product.

**Current implementation:** `build_model`, `run_nd_scenarios`, and `run_kwse_scenarios` implement the current modeling-job boundary.
They build reach artifacts and execute scenario families, but no current `twod-fim-jobs` entry point prepares the hydrologic forcing source, modifies the full network, plans the network-wide KWSE scenario set, or creates a cross-reach composite product.

**Target design:** The system design assigns gap detection, scenario planning, network propagation, artifact verification, and current-state updates to orchestration around the modeling tools.
This is intended ownership, not proof that the full target system is deployed.
See the [target orchestrator design](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/orchestrator-design.md).

## Common misconceptions

### A plausible map proves the forcing was correct

A plausible spatial pattern shows only that the output looks plausible under an informal visual check.
It does not prove that the discharge, downstream condition, datum, scenario identity, or model inputs were correct.

### Extent and depth are interchangeable

Extent answers where the wetting rule was met.
Depth answers how far the water surface lies above modeled terrain.
Discarding depth to make a polygon removes information.

### WSE is depth above the local ground

WSE is an elevation relative to a vertical datum.
Depth is a local vertical difference between compatible WSE and terrain elevation.

### A two-dimensional model is automatically more accurate

Additional spatial degrees of freedom do not correct poor terrain, wrong forcing, incompatible datums, unsuitable boundaries, or inadequate validation.
Representation and evidence determine fitness for the intended use.

### A completed job is a completed FIM product

A successful scenario job can produce a reach-level manifest and artifacts.
Library selection, network consistency, cross-reach compositing, provenance, and product validation remain separate responsibilities.

## Competency check

Without using project-specific scenario names, explain the following chain in three sentences:

1. What enters the hydraulic problem?
2. What physical response does the hydraulic method estimate?
3. What must happen before multiple reach responses become a composite FIM product?

Your answer should use the terms forcing, hydraulic response, and composite product correctly.

## Source notes

- **Scientific foundation:** Stable quantity definitions and equations are in [Glossary](../reference/glossary.md) and [Equations and Units](../reference/equations-and-units.md).
- **Current implementation:** Job behavior and supported artifacts are mapped in bibliography records JOB-002 and JOB-003 in [Bibliography and Source Map](../reference/bibliography.md).
- **Target design:** System ownership and data flow are mapped in bibliography record SYS-001.
- **Open question:** Current jobs, selected methodology, and target ownership do not yet form one verified deployed end-to-end product path.
