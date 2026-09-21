# Domain and Boundary Geometry

A model domain is the numerical area in which the solver can store and move water.
Boundary geometry states where discharge or stage enters, where water can leave, and where information transfers between neighbouring models.

## Why this topic matters

A domain can contain a reach centerline while clipping its connected floodplain, backwater region, or alternate flow path.
A boundary can touch the correct edge while selecting the wrong cells.
Geometry therefore belongs to the hydraulic contract rather than only the map layout.

## Prerequisites

Read [Backwater and Boundary Control](../02-open-channel-flow/04-backwater-and-boundary-control.md), [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md), and [Network Preparation](01-network-preparation.md).

## Learning objectives

After this chapter, the reader should be able to:

- distinguish computed and authored domains;
- explain how buffering and grid snapping determine a rectangular grid;
- interpret a grid anchor without confusing it with an exact centroid;
- evaluate inflow, outflow, and stage-transfer geometry;
- diagnose clipping and unintended edge water;
- distinguish initial construction from evidence-driven expansion; and
- specify geometry provenance and acceptance evidence.

## A numerical domain is a hydraulic choice

Many raster solvers use a rectangular grid even when the active hydraulic area is irregular.
The rectangle defines storage, possible pathways, and perimeter cells.
An active mask may further restrict computation, but the mask then becomes part of the model realization.

**Applied example:** The `R-200` model uses a 10 m square grid and a rectangular domain.
The inflow geometries for `R-100` and `R-300` enter near the upstream edge, and the terminal boundary lies on the downstream edge.
This arrangement is synthetic and does not define a universal confluence layout.

## Computed domains use bounds, buffers, and snapping

A computed domain can begin with the union of the target centerline, inflow geometry, a buffered approach segment, required transfer geometry, and any explicitly supplied extent geometry.
The method should then add an outer buffer and snap outward to the grid lattice.

A centerline buffer can be expressed as:

\[
d_b=W_{bf}M_c
\]

where $W_{bf}$ is an estimated bankfull width and $M_c$ is a stated multiplier.
Both values need units, bounds, and provenance.
The buffer is a construction aid rather than evidence that the connected floodplain is contained.

For resolution $r$, outward snapping can use:

\[
x_{min,s}=r\left\lfloor\frac{x_{min}}{r}\right\rfloor,
\quad
x_{max,s}=r\left\lceil\frac{x_{max}}{r}\right\rceil
\]

with the same operation for y coordinates.
This rule contains the unsnapped bounds and yields full cells when coordinates and resolution use compatible units.

## Authored domains are explicit instructions

An authored domain bypasses automatic extent construction when the operation treats it as final.
Its author must establish that the rectangle contains inflow geometry, required transfer regions, relevant floodplain storage, and space for the largest intended scenario.

The model-building operation should validate positive extent, grid alignment, horizontal-reference compatibility, and containment of required anchor and boundary features.
It should not silently enlarge an authored domain because doing so would create a different realization.

**Design principle:** Record whether a domain was computed or authored and preserve the controlling values either way.

## Grid alignment is exact

Terrain and roughness rasters should share the intended bounds, dimensions, horizontal reference, and affine transform.
The domain polygon should represent that raster extent in a compatible reference.
Vector vertices do not need to lie on raster nodes, but their rasterization or intersection result must be inspected.

Small coordinate changes can move a line to different cells even when a visual overlay looks unchanged.
Compare transforms, bounds, dimensions, references, and selected cells rather than only map appearance.

GDAL geotransform concepts are documented in [SCI-030](../reference/bibliography.md#sci-030-gdal-geotransform).

## The anchor is a grid reference

A model can use a snapped reference point to express the domain's north, south, east, and west offsets.
The reference may be derived from a reach centroid, but snapping means the stored point is not necessarily the exact centroid.

**Design principle:** Name the artifact by its actual role, such as `grid_anchor`, and record both the unsnapped reference and the snapping rule when the distinction matters.

## Inflow geometry must intersect intended conveyance

An inflow line or source region should cross connected channel or floodplain cells where imposed discharge can enter without immediately encountering an artificial wall.
Its record should state the source reach, placement rule, width, orientation, selected cells, and total discharge allocation.

At the synthetic confluence, `R-100` and `R-300` use separate inflow lines within the `R-200` model.
The lines remain distinct so their contributions and diagnostics can be traced separately.

For a line crossing $n$ equal-width cells of width $r$, a method that distributes total discharge $Q$ uniformly as discharge per unit width would use:

\[
q_{point}=\frac{Q}{rn}
\]

The units are square metres per second only when $Q$ is cubic metres per second and $r$ is metres.
The equation describes one allocation convention and does not require every solver to use it.

## Outflow geometry identifies intended edge cells

An outflow polygon or line can identify perimeter cells that receive a fixed-stage, transferred-stage, normal-depth, or other documented condition.
The method must preserve disjoint edge segments instead of filling the dry gap between them unless continuous opening is intended.

Water on an intended downstream edge can be expected.
Water on an unintended lateral or upstream edge can indicate clipping, backwater outside the rectangle, a disconnected path, or an incorrectly open boundary.

**Design principle:** Classify edge cells by intended role, connectivity, water surface, and scenario before deciding whether wetting is acceptable.

## Stage-transfer overlap is spatial and evidential

A transfer line should lie within both the upstream and downstream hydraulic contexts so a downstream scenario can provide stage values to an upstream scenario.
Spatial containment alone is not enough.

The review must establish that the geometry came from the intended downstream reach and scenario, uses compatible horizontal and vertical references, intersects valid wet source cells, and spans the relevant lateral connection.
A line can lie inside both rectangles while missing the wet floodplain or extending beyond the valid source raster.

## Clipping changes the hydraulic problem

Removing a connected part of the floodplain changes storage and conveyance and replaces the missing area with an artificial boundary condition.
If the new edge is closed, water can accumulate and raise upstream stage.
If the edge is opened, water can leave through a location that may not represent an actual outlet.

A sharp inundation cutoff at the domain rectangle is strong evidence of clipping, but a smooth edge is not proof of adequacy.
Backwater, low-relief floodplains, and alternate pathways can require space beyond the initially wet area.

## Initial construction and expansion are separate methods

Initial construction uses available geometry and configured buffers before scenario evidence exists.
Expansion uses later evidence, such as connected wet cells approaching an unintended boundary, to revise the domain.

An expansion method needs:

- a trigger based on persisted edge evidence;
- deterministic directions and distances;
- a maximum extent or other stop rule;
- a new model identity or realization record; and
- explicit rerun and comparison criteria.

An area warning can identify an unusually large rectangle but cannot prove that a smaller rectangle is adequate.

## Worked domain review

**Applied example:** Suppose the unsnapped union of required `R-200` geometries has bounds $(21043, 48127, 22618, 49594)$ m.
Assume an outer buffer of 20 m and a grid resolution of 10 m.

After buffering, the bounds are $(21023, 48107, 22638, 49614)$ m.
Outward snapping produces $(21020, 48100, 22640, 49620)$ m.
The realized width is 1,620 m and height is 1,520 m, so the grid has 162 columns and 152 rows.

**Evidence note:** The arithmetic proves grid alignment and containment of the supplied construction geometries.
It does not prove floodplain adequacy, transfer coverage, correct boundary cells, or acceptable edge behaviour.

If a connected wet band later reaches the west edge outside an intended boundary span, inspect water surface, terrain, connectivity, and boundary roles along that edge before expanding.

## Acceptance review

1. Was the domain computed or authored, and which values controlled it?
2. Does the horizontal reference use known linear units?
3. Do terrain and roughness share the intended bounds, dimensions, reference, and transform?
4. Do inflow, outflow, and transfer geometries select the intended cells?
5. Are terrain and stage sources vertically compatible?
6. Does each inflow intersect connected conveyance?
7. Do outflow conditions cover intended outlet cells without opening unrelated spans?
8. Does the transfer line overlap valid downstream wet cells and the intended upstream region?
9. Are wet perimeter cells classified by role and connectivity?
10. Does the largest relevant scenario remain hydraulically contained?
11. Can every geometry be traced to a versioned source and scenario identity?

## Common misconceptions

### A rectangle containing the centerline contains the floodplain

The centerline does not define lateral storage, backwater paths, or alternate conveyance.

### Grid snapping validates the domain

Snapping establishes alignment and full cells, not hydraulic sufficiency.

### Every wet edge is wrong

Wet cells can be expected at intended outlets or transfer boundaries.

### No warning means no clipping

Warning coverage is limited to implemented checks and persisted evidence.

### A transfer line inside the bounds is usable

The line also needs compatible source grids, datums, scenario provenance, wet-cell support, and adequate lateral coverage.

## Competency check

1. Which geometries should control a computed domain?
2. What changes when an authored domain is supplied?
3. Why is a snapped anchor not necessarily the exact centroid?
4. Which units are required by the discharge-allocation equation?
5. How can closed-edge and open-edge clipping bias a result differently?
6. What evidence distinguishes a connected clipped floodplain from isolated wet edge cells?

## Further reading and source notes

- **Scientific foundation:** Boundary-condition concepts are supported by [SCI-024](../reference/bibliography.md#sci-024-hec-ras-downstream-boundary-conditions) and [SCI-026](../reference/bibliography.md#sci-026-hec-ras-2d-external-boundary-conditions).
- **Scientific foundation:** Grid-size and mesh considerations are supported by [SCI-028](../reference/bibliography.md#sci-028-hec-ras-2d-computational-mesh) and [SCI-029](../reference/bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance).
- **Scientific foundation:** Raster geotransform concepts are documented by [SCI-030](../reference/bibliography.md#sci-030-gdal-geotransform).
