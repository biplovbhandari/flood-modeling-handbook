# What Is Flood Inundation Mapping?

Flood inundation mapping connects a water condition to an estimate of where water may go and what hydraulic conditions may occur there.
A map is a model result with stated inputs, assumptions, spatial support, units, and limitations.
It is not a direct observation of every flooded location, and it is not a complete prediction unless the forcing and scenario context are specified.

## Why this topic matters

A depth raster, inundation polygon, and water-surface raster can look similar on a screen while answering different questions.
Correct interpretation begins by naming the represented quantity and tracing the hydrologic forcing and hydraulic response that produced it.
That trace also separates model preparation, simulation, response selection, and product assembly.

## Prerequisites

The reader should be familiar with maps, raster and vector data, and the idea that elevations use horizontal and vertical references.
No knowledge of a particular modeling system or solver is required.

## Learning objectives

After this chapter, the reader should be able to:

- describe what a FIM product represents;
- distinguish depth, extent, WSE, and velocity products;
- distinguish hydrologic forcing from hydraulic response;
- distinguish one-dimensional and two-dimensional hydraulic representations without treating either as universally best; and
- trace a prepared discharge through a hydraulic response library to a composite product.

## The minimum FIM statement

**Scientific foundation:** A useful FIM statement identifies a water condition, a hydraulic or geospatial method, and a mapped result.

For example, a statement might say that a specified discharge was applied to a modeled reach, a hydraulic method calculated the response under stated downstream and terrain conditions, and the resulting water depth and extent were mapped.
Omitting the discharge, downstream condition, terrain realization, or vertical datum removes information needed to interpret the map.

The phrase flood inundation map is often used for both the mapped extent and a collection of related raster quantities.
This handbook uses FIM product when the exact mapped quantity could vary, and it names the quantity whenever interpretation depends on it.

## Four common mapped products

### Depth

Depth is the vertical distance from the local terrain or bed elevation to the water surface.
For compatible elevations, depth is calculated as

\[
h = WSE - z_b
\]

Depth supports questions such as how far water stands above the modeled ground at a location.

### Extent

Extent identifies the area classified as inundated under a stated wetting rule.
It may be represented as wet raster cells or an inundation polygon.
Extent does not preserve how deep or fast the water is within the mapped area.

### Water-surface elevation

Water-surface elevation, or WSE, is the elevation of the water surface relative to a stated vertical datum.
WSE supports comparison across locations and transfer of downstream water levels only when horizontal locations, units, and vertical references are compatible.
WSE is not interchangeable with depth because terrain elevation varies in space.

### Velocity

Velocity describes the rate and direction of water motion.
A velocity product may contain a magnitude, directional components, or both.
Velocity and depth together can support hazard interpretation, but the hazard meaning depends on the chosen method and use case.

## Hydrologic forcing and hydraulic response

**Scientific foundation:** Hydrology describes how water is generated, accumulated, and routed through a catchment and drainage network.
Its output at a hydraulic-model boundary is often a discharge value or hydrograph.

This handbook uses hydrologic forcing for water inputs to a hydraulic problem.
Examples include a constant upstream discharge, a time-varying discharge hydrograph, or rainfall applied directly to a domain.

A water level specified at a downstream boundary is a hydraulic boundary control rather than an imposed mass input.
The water level may come from an observation, another hydraulic model, or a synthetic scenario.
It constrains the hydraulic response and may influence conditions upstream.
Hydraulic boundary conditions are therefore broader than hydrologic forcing because they include both water inputs and controls such as specified water level.

Hydraulic response is what the modeled water does after the forcing meets terrain, channel geometry, roughness, initial conditions, and downstream controls.
Response quantities include WSE, depth, velocity, inundated area, and storage.

The same discharge can produce different hydraulic responses when terrain, roughness, downstream stage, domain extent, or initial conditions change.
Conversely, similar inundation extents can arise from different combinations of forcing and boundary control.
A map alone therefore does not identify its cause.

## One-dimensional and two-dimensional representations

The following comparison describes the represented dimensions and governing-equation context rather than ranking either method.
Scientific adequacy still depends on the question, data, geometry, boundaries, numerical method, and validation evidence.

| Representation | Scientific description | Evidence needed for use |
| --- | --- | --- |
| One-dimensional hydraulics | Represents flow primarily along a longitudinal coordinate and uses section or control-volume properties in mass and momentum equations. | Geometry, boundary conditions, parameter support, numerical checks, and validation for the intended use. |
| Two-dimensional hydraulics | Represents depth-averaged horizontal flow over an area using terrain, friction, mass conservation, and momentum equations on a grid or mesh. | Mesh or grid support, terrain, friction, boundary conditions, space-time sensitivity, numerical checks, and validation for the intended use. |

The one-dimensional description is supported by [SCI-020](../reference/bibliography.md#sci-020-hec-ras-one-dimensional-hydraulic-equations).
The two-dimensional description is supported by [SCI-027](../reference/bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics) and [SCI-028](../reference/bibliography.md#sci-028-hec-ras-2d-computational-mesh).
Method selection should follow the intended decision and evidence requirements rather than the number of represented dimensions or the visual detail of the output.

## A complete product chain

**Design principle:** Keep each change in scientific meaning explicit from hydrologic source through final product.

The chain begins with catchment and network conditions that support a discharge estimate or hydrograph.
Prepared terrain, roughness, channel geometry, and boundary locations define a model realization for a reach.
A scenario combines that model realization with forcing, downstream controls, initial conditions, and numerical settings.
The hydraulic calculation produces a spatial response.
A response library organizes selected scenarios over a stated range of forcing and boundary conditions.
A composite product combines compatible responses under an explicit overlap rule and preserves the provenance of every contributing scenario.

## Applied trace for reach R-200

**Applied example:** The following trace is synthetic and demonstrates the meaning of each transition without describing an external system.

1. Catchment evidence supports a discharge of \(Q=250\ \text{m3/s}\) for reach `R-200` under a stated event context.
2. Model preparation aligns terrain, roughness, the reach centerline, a model domain, and inflow and downstream boundary locations.
3. A scenario record associates the prepared model with \(Q=250\ \text{m3/s}\), a downstream condition, an initial state, and numerical settings.
4. The hydraulic method calculates WSE, depth, velocity, and extent where those outputs are supported.
5. A response-library record states whether the scenario is included, which conditions it represents, and which artifacts belong to it.
6. A composite-product operation combines compatible reach responses and records the overlap rule, source scenarios, units, and spatial references.

The value 250 remains the imposed discharge through the scenario trace, but the hydraulic response is the calculated water state rather than the number 250.
The trace makes no claim that the example has been deployed, accepted, or validated for a real location.

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

### A completed simulation is a completed FIM product

A successful simulation can produce reach-level response artifacts.
Library selection, network consistency, cross-reach compositing, provenance, and product validation remain separate responsibilities.

## Competency check

Explain the following chain in three sentences:

1. What enters the hydraulic problem?
2. What physical response does the hydraulic method estimate?
3. What must happen before multiple reach responses become a composite FIM product?

Your answer should use the terms forcing, hydraulic response, and composite product correctly.

## Source notes

- **Scientific foundation:** Stable quantity definitions and equations are in [Glossary](../reference/glossary.md) and [Equations and Units](../reference/equations-and-units.md).
- **Scientific foundation:** Watershed and runoff context is supported by [SCI-003](../reference/bibliography.md#sci-003-watersheds-and-drainage-basins) and [SCI-006](../reference/bibliography.md#sci-006-surface-runoff-and-catchment-response).
- **Scientific foundation:** One-dimensional and two-dimensional hydraulic representations are supported by [SCI-020](../reference/bibliography.md#sci-020-hec-ras-one-dimensional-hydraulic-equations), [SCI-027](../reference/bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics), and [SCI-028](../reference/bibliography.md#sci-028-hec-ras-2d-computational-mesh).
- **Design principle:** The staged trace separates forcing, model preparation, response, selection, and compositing so evidence for one stage is not treated as evidence for another.
- **Evidence note:** The `R-200` trace is constructed teaching material and supplies no evidence about an external implementation or location.
