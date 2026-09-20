# Domain and Boundary Geometry

A hydraulic domain is the rectangular grid over which the current model is defined, while boundary geometries identify where water enters, leaves, or receives a transferred water-surface condition.
Choosing these geometries is a scientific decision because an artificial edge can behave like a wall, an outlet, or a stage control and can therefore change water-surface elevation, flow paths, storage, and mapped inundation.

## Why this topic matters

The solver cannot represent water outside its domain.
If hydraulically connected water reaches an unintended closed edge, it can pool and raise water-surface elevation inside the grid.
If water is allowed to leave through an unintended open edge, the model can lose discharge before the intended outlet.
If a floodplain is clipped at the rectangle, the resulting map can underestimate extent even when every generated file is readable.

## Prerequisites

Read [Network Preparation](01-network-preparation.md), [Terrain, Topobathymetry, and Structures](02-terrain-topobathymetry-and-structures.md), [Roughness and Land Cover](03-roughness-and-land-cover.md), [Backwater and Boundary Control](../02-open-channel-flow/04-backwater-and-boundary-control.md), and [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md).
Use [Source Authority](../reference/source-authority.md) to distinguish selected methodology from current implementation.

## Learning objectives

After this chapter, the reader should be able to:

- distinguish a rectangular numerical domain from the geometries used to construct or constrain it;
- compare a computed domain with an authored domain;
- explain centerline buffers, extra geometries, grid snapping, and the grid-snapped domain anchor;
- assess inflow lines, downstream outflow areas, edge cells, and stage-transfer-line overlap;
- explain why clipping can bias a hydraulic result even when the model completes;
- distinguish initial-domain construction from post-run domain expansion; and
- state the evidence needed before a domain and its boundary geometries can be accepted.

## The current numerical domain is a rectangle

**Current implementation:** The current `build_model` job represents the model domain with a bounding box ordered as west, south, east, and north.
The resulting DEM and roughness rasters contain every row-column cell in that rectangle.
The domain GeoJSON is the corresponding rectangular polygon.

The rectangle is not the same object as a reach divide, catchment, inundation polygon, centerline buffer, STL, or outflow area.
Those geometries can help determine an extent or select boundary cells, but the realized current grid remains rectangular.

A rectangle often includes dry upland cells that never become wet.
Those extra cells are not automatically a problem, but they increase computation and potential representable storage only if they later wet.
They can also hide the fact that the intended hydraulic corridor occupies only part of the grid.

The opposite error is more consequential.
A rectangle that excludes a connected floodplain, tributary route, backwater area, or downstream transfer geometry prevents the model from representing that pathway at all.

## Computed domains use geometry bounds and grid snapping

When `BuildModelInputs.domain` is null, the current job computes a domain from the bounds of several geometries.
The set includes the target reach, the inflow line, a buffered target centerline, any supplied extra geometries, and a clipped portion of the upstream mainstem when one is supplied.

The target centerline and retained upstream-mainstem segment are buffered by

\[
d_b=W_{bf}M_c
\]

where \(W_{bf}\) is the current bankfull-width estimate in m and \(M_c\) is `centerline_buffer_bankfull_multiplier`.
The checked-in code default for \(M_c\) is 15, which is a code default rather than a selected Decision Register value.
The current input model applies no bound to \(M_c\), so zero or negative values pass and can remove the intended buffered centerline corridor from the computed bounds.
The required input range is `centerline_buffer_bankfull_multiplier > 0`, while any finite upper limit requires authorization by the domain-method owner.
Using this metre-based estimate directly as geometry distance assumes that the model CRS is projected and uses metres unless an explicit conversion is applied.

The job takes the total geometry bounds, expands all four sides by `domain_buffer`, and snaps the minimum coordinates downward and maximum coordinates upward to the `grid_resolution` lattice.
This outward snapping ensures that the snapped rectangle contains the unsnapped bounds.
The formulas and units are recorded in [Equations and Units](../reference/equations-and-units.md#computed-domain-grid-and-snapping).

**Current implementation detail:** For a non-null upstream mainstem, the code splits that line with the inflow line and retains the split piece nearest the target reach.
It then buffers that retained piece with the same distance used for the target centerline.
This step is intended to include the hydraulic approach between the inflow and the target reach, but its success depends on valid upstream geometry and a useful split.

## Extra geometries extend a computed domain

`other_geometries` accepts WKT geometry strings or paths to GeoJSON content.
The current loader assigns the target reach CRS to these geometries rather than reprojecting them.
The caller must therefore supply coordinates that already use the expected `epsg_code` CRS.

Extra geometries influence only the bounding rectangle.
They do not become hydraulic barriers, inflows, outflows, terrain modifications, or active-cell masks merely because they were included in domain construction.

**Selected methodology:** DR-011 ALT-D has Alternate Selected status and defines the initial domain as the bounding box of the inflow boundary, downstream STL when available, and buffered centerline.
The selected record does not itself transport the downstream STL into the job request.
The current job includes only the extra geometries supplied by its caller.

**Open question:** The standalone DR-011 file also marks materially different ALT-E as `#current` while the reviewed Decision Register selects ALT-D.
The register selection remains the methodology statement within its recorded status and scope, but authorized governance must reconcile the competing file-local marker before the source can be treated as internally consistent.

A caller can use this interface to include an STL or another required extent geometry, but the artifact must have compatible coordinates and relevant provenance.
Supplying a path is not evidence that the geometry overlaps the intended reach, belongs to the correct downstream scenario, or covers the largest relevant floodplain.

## An authored domain bypasses computed extent logic

When `BuildModelInputs.domain` is supplied, it is an authored bounding box.
The current input model requires four values with positive extent and requires every coordinate to be a multiple of `grid_resolution` within floating-point tolerance.
The job then uses the authored rectangle exactly as supplied.

The authored path does not apply `domain_buffer`, centerline buffers, extra geometries, or additional snapping.
It still computes the grid-snapped anchor from the target reach centroid and rejects a rectangle that does not contain that anchor.

An authored domain is therefore an explicit spatial instruction, not a request for the job to enlarge or repair a candidate polygon.
Its author must establish that the rectangle contains the inflow geometry, hydraulic interpretation area, required downstream transfer region, and enough surrounding terrain for the intended scenarios.

**Identity consequence:** The authored bbox does not enter the current identity object directly.
It changes the domain code and full model identifier through the offsets from the grid-snapped anchor.
Other parameters that alter boundary geometry can also be absent from the identity object, so the complete manifest inputs remain necessary for provenance review.

## Grid snapping controls exact cell alignment

Grid snapping makes the domain width and height divisible by `grid_resolution`.
The current job calculates columns and rows from those dimensions and uses the same bbox and dimensions to construct both raster transforms.

For an unrotated square grid, the west and north coordinates define the raster origin.
Columns advance eastward, while rows advance southward because the raster y pixel size is negative.
The DEM and roughness rasters must share the applicable bbox, dimensions, horizontal CRS, and affine transform for exact cell alignment.
The domain polygon must represent the same raster extent in a compatible CRS.
Reach, inflow, STL, and outflow vectors do not need vertices on raster grid nodes.
They need a compatible horizontal CRS plus explicit intersection, rasterization, or cell-mapping checks against the realized grid.

Small coordinate changes can move a line from one set of cells to another even when the visible map difference is modest.
The correct raster review therefore compares exact transforms, bounds, dimensions, and CRS rather than only visual overlays.
The correct vector review transforms coordinates when required and inspects the actual cells or intersections produced by the consumer.

## The anchor is a grid reference, not the exact centroid

The current job calculates the geometric centroid of the target reach and floors both coordinates to the resolution grid.
This grid-snapped point anchors north, south, east, and west offsets used in `domain_code`.

The exported file is named `anchor.geojson`, but the model-manifest asset role is `reach_centroid`.
That field name is a known mismatch because the stored point can differ from the exact geometric centroid by almost one grid cell in each coordinate.

Do not use the asset role as proof that the point is the exact centroid.
Use the `domain.anchor`, domain offsets, and exported point together as grid-address evidence.

## Inflow lines must intersect the intended conveyance

An inflow line is rasterized later into one or more forcing locations.
The line should cross the intended channel or connected floodplain in a location where imposed discharge can enter the model without immediately encountering an artificial wall or contaminating the interpreted reach response.

**Selected methodology:** DR-013 ALT-A selects a perpendicular line on the highest-drainage-area immediate upstream reach.
DR-016 ALT-A selects an offset of 0.25 of the upstream reach length.
DR-015 ALT-A selects a 100 m line width.

**Current implementation:** The current defaults use `walk_us_dist_pct=0.1` and `bankfull_width_multiplier=1.0`.
The line width is therefore the code-estimated bankfull width multiplied by the caller value rather than a fixed 100 m default.
The job trusts the caller-supplied upstream mainstem and does not prove that it is adjacent or has the largest drainage area.
The schema requires `walk_us_dist_pct > 0` but does not enforce the documented fraction range at or below 1.
The required placement range is `0 < walk_us_dist_pct <= 1`.

For a supplied upstream mainstem, the line is centered at a point located `walk_us_dist_pct` of that reach length upstream from its downstream end, assuming the line coordinates run upstream to downstream.
Values greater than 1 can clamp or reinterpret interpolation rather than select an interior point at the documented fraction.
For a null upstream mainstem and `ds_of_lake=false`, the current code instead places one perpendicular line at the first target-reach coordinate.
That headwater behavior differs from DR-014 ALT-B, which selects points distributed along the reach.

For `ds_of_lake=true`, the current code places a perpendicular line within the target reach at the configured fraction of target-reach length from its first coordinate.
The caller supplies the lake-outlet flag, and the job does not derive or validate that classification.

The implemented warning reports only when the generated inflow line intersects the target reach centerline more than once.
It does not warn when the line intersects the target reach zero times, which is normal for a line placed on a separate upstream mainstem but can also conceal a disconnected or misidentified geometry.
It does not test intersection with the supplied upstream mainstem after construction.

## Outflow areas identify edge spans for scenario setup

The current `build_model` job does not create an outflow-area asset and does not choose solver boundary cells.
Current ND scenario code accepts an optional outflow-area polygon or derives one later from the model domain and centerline.

The derived ND polygon uses the downstream half of the target centerline, a strip offset laterally by ten estimated bankfull widths, and a square buffer of two grid cells around the selected domain edge.
The boundary preprocessor intersects a polygon with each cardinal domain edge.
For each touched edge, it creates one continuous cardinal span from the minimum to maximum intersection coordinate, even when the polygon touches that edge in disjoint segments.

This behavior is current code, not automatically selected methodology.
The standalone DR-039 marks an alternative as current locally, but DR-039 is absent from the reviewed Decision Register and therefore remains an Open question in this handbook.

An outflow area outside the model manifest can still be a valid scenario input, but it must not be described as a `build_model` artifact.
Its presence beside a checked-in model fixture does not prove when, how, or by which current job it was produced.

## Edge cells need an intended role

An edge cell lies on the perimeter of the rectangular grid.
Depending on solver configuration, an edge can be closed, receive a slope-based outflow, receive a fixed or transferred WSE, or participate in another solver-specific boundary treatment.

Water on an intended downstream outlet edge can be expected.
Water on an unintended lateral or upstream edge can indicate clipping, backwater outside the rectangle, a disconnected flow path, or an incorrectly open boundary.

The absence of an edge warning is not proof that all wet edges are acceptable.
A check can be skipped, withheld until another condition is met, configured to allow edge water, limited by its classification rule, or not persisted in enough detail to support later review.

## STL overlap is both a domain and provenance question

A stage-transfer line is intended to lie within the upstream and downstream model contexts so that a downstream scenario can provide WSE values to an upstream scenario.
DR-009 ALT-B selects transfer at the reach outlet, and DR-025 ALT-B selects an STL derived from a downstream FIM WSE contour.

The initial upstream model domain must contain the relevant STL geometry when that dependency is available.
Spatial containment alone is not enough.
The review must also establish that the line came from the intended downstream reach and scenario, uses a compatible horizontal CRS and vertical datum, intersects valid source cells, and covers the lateral hydraulic connection that needs stage transfer.

An STL can overlap the domain polygon while missing the wet floodplain.
It can overlap the target reach while extending beyond the downstream raster.
It can also use elevations from an incompatible vertical datum even when its planimetric coordinates align perfectly.

## Clipping changes the hydraulic problem

Clipping is not only a cartographic omission.
Removing a hydraulically connected part of the floodplain changes the available storage and conveyance and replaces the missing area with an artificial boundary condition.

If the new edge is closed, water that should spread outside can accumulate inside and raise local or upstream WSE.
If the new edge is opened to drain freely, water can leave through a location that may not represent an actual outlet.
Either choice can change flow splits, velocities, arrival at the intended outlet, and the mapped wet extent.

A sharp inundation cutoff at the domain rectangle is strong diagnostic evidence of clipping, but a smooth-looking edge is not proof of adequacy.
Backwater, low-relief floodplains, alternate flow paths, and scenario-dependent expansion can require space beyond the initially wet edge.

## Initial construction and domain expansion are separate methods

**Selected methodology:** DR-012 ALT-G has Alternate Selected status.
It calls for water-surface-elevation-informed expansion and a maximum expansion distance of 50 estimated bankfull widths from the initial domain.

**Current implementation:** The current `build_model` job constructs one domain and does not inspect scenario output, classify wetted edges, expand the rectangle, or iterate the build.
The generated build-model documentation also lists model expansion as out of scope.

The implemented large-domain warning triggers only when rectangular area exceeds `LARGE_DOMAIN_AREA_THRESHOLD`, whose checked-in value is \(10^9\) square CRS units and whose code comment says it needs tuning.
That warning can identify a very large rectangle, but it cannot show that a small rectangle is adequate.

A domain-expansion workflow therefore requires an owner outside the current job, explicit trigger evidence, deterministic expansion, a stop rule, a revised model address, and rerun criteria.
No artifact-presence check can substitute for that scientific loop.

## Worked domain review

Suppose a target reach, upstream inflow line, and buffered centerlines have unsnapped total bounds of \((11073, 758847, 12654, 760313)\) m in a projected CRS.
Assume `domain_buffer=0` and `grid_resolution=10` m.

The current computed-domain rule floors the west and south coordinates and ceils the east and north coordinates.
The realized bbox is therefore \((11070, 758840, 12660, 760320)\) m.
Its width is 1,590 m and its height is 1,480 m, so the grid has 159 columns and 148 rows.

This arithmetic proves bbox grid alignment and containment of the supplied construction geometries.
It does not prove that vector vertices coincide with grid nodes or that later rasterization selects the intended cells.
It does not prove that the largest relevant floodplain, downstream STL, outflow area, or scenario-specific wetting remains inside the rectangle.

If later depth evidence shows an hydraulically connected wet band touching the west edge outside an intended boundary span, the next discriminating check is a map of WSE, terrain, connectivity, and intended boundary roles along that edge.
That check can distinguish a clipped connected floodplain from isolated shallow cells or an intended outlet before choosing whether and where to expand.

## A domain and boundary acceptance review

Before accepting one realization, answer the following questions.

1. Does the manifest record a computed or authored domain, and which inputs controlled it?
2. Is the realized horizontal CRS projected with metre linear units, or are explicit conversions documented for geometry, buffers, slope, discharge density, cell area, storage, and flooded area?
3. Do the DEM and roughness rasters share the intended extent, dimensions, horizontal CRS, and affine transform?
4. Does the domain polygon represent that raster extent in a compatible CRS?
5. Do the reach, inflow, STL, and outflow vectors use compatible horizontal coordinates, and do explicit intersection, rasterization, or cell-mapping checks select the intended cells?
6. Does the terrain and every WSE source state a compatible vertical datum and unit?
7. Does the inflow line intersect the intended connected conveyance rather than merely lie within the bbox?
8. Does the downstream boundary cover intended outlet cells without opening unrelated edge spans?
9. Does the STL overlap valid downstream wet cells and the intended upstream transfer region?
10. Are wet perimeter cells classified by role, connectivity, WSE, and scenario rather than treated as one generic error?
11. Does clipping or domain expansion evidence exist for the largest relevant scenario?
12. Can every geometry be traced to an immutable or versioned source and the correct reach or scenario identity?
13. Are warning absences interpreted only within checks that current code actually implements?

## Common misconceptions

### A rectangle containing the centerline contains the floodplain

The centerline is one-dimensional and does not define lateral storage, backwater paths, or alternate conveyance.

### Grid snapping validates the domain

Snapping establishes alignment and full cells.
It does not establish hydraulic sufficiency.

### Every wet edge is wrong

Water can be expected on intended outflow or transfer edges.
The diagnostic question is whether each wet edge cell has the intended role and hydraulic behavior.

### No warning means no clipping

The current build warnings do not perform a floodplain-clipping or domain-expansion assessment.

### An STL inside the bbox is usable

The STL also needs compatible source rasters, datum, scenario provenance, wet-cell support, and adequate lateral coverage.

## Competency check

1. Which geometries control the current computed domain, and which of those are caller supplied?
2. What changes when an authored `domain` is present?
3. Why is `reach_centroid` an imprecise name for the current anchor asset?
4. Which selected inflow values differ from current code defaults, and which current scalar bounds fail to enforce the documented fraction and buffer contracts?
5. Why can a zero target-reach intersection be normal for an upstream-mainstem inflow and still require review?
6. Why is an outflow-area file beside a model not necessarily a `build_model` artifact?
7. How can closed-edge and open-edge clipping bias a result in different ways?
8. What does DR-012 select, and which part is absent from the current job?
9. Which next check best distinguishes connected floodplain clipping from isolated edge cells?

## Source notes

- **Selected methodology:** Domain, inflow, expansion, and stage-transfer decisions are indexed under [SDR-010](../reference/bibliography.md#sdr-010-domain-boundary-and-expansion-decisions).
- **Current implementation:** Domain construction, geometry parsing, grid snapping, warnings, and later boundary-cell construction are indexed under [JOB-008](../reference/bibliography.md#job-008-model-development-input-transformations-and-warnings) and [JOB-009](../reference/bibliography.md#job-009-build-model-lifecycle-artifacts-and-existing-model-behavior).
- **Checked-in caller context:** The current reconciler payload and downstream-STL handoff are indexed under [SYS-005](../reference/bibliography.md#sys-005-checked-in-reconciler-build-model-caller).
- **Evidence or experiment:** Domain clipping and edge-pooling material is indexed under [SDR-004](../reference/bibliography.md#sdr-004-cases-issues-and-experiments).
- **Open questions:** Default and scalar-bound differences, expansion ownership, identity coverage, and warning gaps are preserved in [CONF-004](../reference/conflicts-and-open-questions.md#conf-004-selected-methodology-and-code-defaults), [CONF-013](../reference/conflicts-and-open-questions.md#conf-013-model-identity-existing-model-and-warning-gaps), and [CONF-014](../reference/conflicts-and-open-questions.md#conf-014-domain-expansion-and-clipping-evidence).
