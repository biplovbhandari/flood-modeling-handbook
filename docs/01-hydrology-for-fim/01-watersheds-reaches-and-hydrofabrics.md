# Watersheds, Reaches, and Hydrofabrics

A watershed organizes land by where water drains, while a reach network organizes channels by how flow moves from one modeled segment to the next.
The 2D FIM workflow needs both ideas because a discharge originates from an upstream drainage area but is applied to a particular directed reach model.

## Why this topic matters

A catchment boundary, stream centerline, reach identifier, and hydraulic model domain can overlap on a map without representing the same object.
Confusing them can attach the right discharge to the wrong reach, reverse the upstream-downstream relationship, or treat a model boundary as a physical drainage divide.

## Prerequisites

Read [What Is Flood Inundation Mapping?](../00-orientation/01-what-is-fim.md), [End-to-End Mental Model](../00-orientation/02-end-to-end-mental-model.md), and [Source Authority](../reference/source-authority.md).
Use [Glossary](../reference/glossary.md) for the stable network terms introduced here.

## Learning objectives

After this chapter, the reader should be able to:

- identify a watershed outlet, drainage divide, and drainage area;
- follow flow direction through headwaters, tributaries, confluences, and a mainstem;
- explain what Strahler stream order does and does not mean;
- distinguish a hydrologic catchment, hydrofabric reach, and hydraulic model domain; and
- map the current `build_model` network inputs to their exact code and decision sources.

## Watersheds depend on an outlet

**Scientific foundation:** A watershed, drainage basin, or catchment is the land area that drains toward a specified outlet.
The outlet can be a streamgage, confluence, lake outlet, reach endpoint, or another defined location.
Moving the outlet changes the drainage area, so a watershed is incomplete without its outlet.

A drainage divide is the boundary separating neighboring drainage areas.
On a simple terrain surface, water on opposite sides of a divide moves toward different outlets.
Real drainage can be altered by depressions, karst, diversions, storm drains, canals, and other features, so a divide is a modeled or mapped interpretation of how water drains at the chosen scale.

Drainage area is the horizontal area enclosed by the divide for the selected outlet.
An outlet farther downstream usually has a larger contributing area because it accumulates the catchments of upstream tributaries.
The [USGS watershed overview](https://www.usgs.gov/water-science-school/science/watersheds-and-drainage-basins) explains the outlet-dependent definition and the role of drainage divides.

## Flow direction makes the network directed

**Scientific foundation:** A drainage network is directed from upstream to downstream.
Each directed reach has an upstream end, a downstream end, and zero or more reaches that drain into it.
Direction is a topological relationship, not merely the left-to-right appearance of a line on a map.

A headwater reach has no upstream reach in the represented network.
A tributary joins another stream at a confluence.
The mainstem is the principal path named or selected under a stated rule, while a tributary is a stream that enters that path.
Those labels depend on network definition and selection rule rather than visual width alone.

At a confluence, multiple upstream flows contribute to one downstream reach.
Their hydrograph peaks need not arrive together.
The downstream peak therefore depends on timing, routing, storage, and local inflow as well as the arithmetic sum of upstream peak values.

## Stream order is a network index

**Scientific foundation:** Strahler stream order ranks a reach by its position in a branching network.
Headwater reaches are order 1.
When two reaches of the same order join, the downstream reach has an order one greater.
When reaches of different orders join, the downstream reach retains the larger order.

Stream order is useful for describing relative network position, but it is not a direct measurement of discharge, channel width, or flood magnitude.
Its value also depends on the mapped network density and ordering method.
The [USGS stream-order description](https://www.usgs.gov/media/images/streamorder) gives the Strahler rules and notes the effect of cartographic source scale.

## Catchments, reaches, and model domains are different objects

**Scientific foundation:** A hydrologic catchment is an area that contributes water to an outlet.
A reach is a directed segment of the drainage network between defined nodes or breakpoints.
A hydrofabric connects reaches, catchments, identifiers, attributes, and upstream-downstream relationships in a geospatial data model.

A hydraulic model domain is the area over which the numerical model represents terrain, roughness, water state, and boundary conditions.
The domain may extend across part of one catchment, cross a divide to provide numerical space, include floodplain areas associated with several catchments, or omit upstream land that generated the applied discharge.
The model domain is therefore not a substitute for the catchment.

The following comparison keeps the roles separate.

| Object | Geometry | Primary question | Typical attributes |
| --- | --- | --- | --- |
| Hydrologic catchment | Polygon | What land contributes water to this outlet? | Drainage area, outlet, land cover, soils, and storage characteristics. |
| Hydrofabric reach | Directed line or network segment | How is this channel segment connected upstream and downstream? | Reach identifier, downstream identifier, stream order, drainage area, and geometry. |
| Hydraulic model domain | Polygon with a grid or mesh | Where will the hydraulic equations and boundaries be applied? | CRS, grid resolution, terrain, roughness, inflow geometry, and downstream boundaries. |

## Reading a small network

Consider two order-1 headwaters, A and B, that join to form reach C.
Reach C then joins tributary D at another confluence and continues as reach E.

```text
A --\
     >-- C --\
B --/         >-- E
D ------------/
```

A and B are immediate upstream reaches of C.
C and D are immediate upstream reaches of E.
A, B, and D are headwaters in this represented network if they have no other upstream connections.
Under the Strahler rules, C is order 2 because two order-1 reaches join there.
If D is order 1, E remains order 2 because unequal orders join.

The cumulative drainage area at E includes the areas contributing through C and D, subject to how the hydrofabric delineates and reports the areas.
The catchment associated with reach E is not necessarily the full cumulative watershed above E.
Hydrofabrics may distinguish local catchment area from total upstream drainage area, so the field definition must be checked before use.

## Project connection: the current reach contract

**Current implementation:** The checked-out job code configures default reach-network fields named `reach_id`, `reach_to_id`, `total_da_sqkm`, `stream_order`, and `geometry` in [`consts.py`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/consts.py).
[`read_reaches`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/utils/storage.py) requires those fields and queries rows by `reach_id`.
These names are current implementation defaults, not universal hydrofabric field names.

**Current implementation:** [`BuildModelInputs`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/build_model.py) receives `reach_id`, `reach_network_path`, `upstream_reach_ids`, and `upstream_mainstem_reach_id` from its caller.
The contract defines `upstream_reach_ids` as the reaches draining into the modeled reach.
It defines `upstream_mainstem_reach_id` as the immediate upstream reach with the largest drainage area and uses null for a headwater.
The current job does not derive either upstream input by traversing `reach_to_id`.

**Current implementation:** [`BuildModelJob`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/build_model.py) queries the target reach and, when supplied, the upstream-mainstem reach.
The job uses the upstream-mainstem geometry when it constructs the inflow line and when it computes an unauthored model domain.
The resulting model manifest records drainage area, stream order, reach length, upstream reach identifiers, and upstream-mainstem identifier as properties.

The project term `upstream mainstem` is therefore narrower than every general use of mainstem.
In this current contract, it means the immediate upstream reach selected by largest drainage area, not the entire named river or the longest path through the basin.

## Selected methodology and implementation differences

**Selected methodology:** DR-013 selects ALT-A with status Alternate Selected.
That alternative places a line inflow on the highest-drainage upstream reach some distance from the target reach start.
This agrees with the current non-headwater choice of an upstream-mainstem geometry, but a decision status does not prove every placement detail is implemented.

**Selected methodology:** DR-014 selects ALT-B with status Alternate Selected for headwaters.
That alternative distributes point inflows along the headwater reach.

**Current implementation:** When `upstream_mainstem_reach_id` is null and the reach is not marked downstream of a lake, [`make_inflow_line`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/utils/geospatial.py) creates one perpendicular line at the reach start.

**Open question:** The selected distributed-point headwater method and the current single-line headwater implementation differ.
This chapter does not choose between them.

**Selected methodology:** DR-016 selects an upstream offset of 0.25 of upstream-reach length with status Alternate Selected.

**Current implementation:** `BuildModelInputs.walk_us_dist_pct` currently defaults to 0.1.

**Open question:** The selected offset and current default differ.
The difference must be resolved through an authorized methodology or implementation change rather than rewritten as if the values agree.

## Common misconceptions

### A reach and its catchment have the same geometry

A reach is a directed channel segment.
A catchment is an area that drains toward an outlet or network element.
Their identifiers may be paired, but their geometries and scientific meanings differ.

### Larger stream order gives an exact discharge

Stream order summarizes branching position.
Discharge also depends on drainage area, precipitation, losses, storage, regulation, and time.

### The job finds its upstream network automatically

The current `build_model` contract receives immediate upstream identifiers and the selected upstream-mainstem identifier from its caller.
The presence of `reach_to_id` in the network file does not mean this job traverses that field to prepare topology.

### The hydraulic domain is the watershed

The domain is a numerical modeling extent.
The watershed is the contributing land area for an outlet.
One cannot be used as evidence for the other without an explicit spatial relationship.

## Competency check

For the A-B-C-D-E network above, answer the following questions:

1. Which reaches are immediate upstream neighbors of C and E?
2. Which reaches are headwaters in the represented network?
3. What are the Strahler orders of C and E if A, B, and D are order 1?
4. Which current `build_model` input identifies the project-specific upstream mainstem for E?
5. Why does that field not prove that the selected headwater inflow methodology is implemented?

## Source notes

- **Scientific foundation:** Watershed, drainage divide, and water-budget context come from [SCI-003](../reference/bibliography.md#sci-003-watersheds-and-drainage-basins), and stream-order rules come from [SCI-004](../reference/bibliography.md#sci-004-strahler-stream-order).
- **Current implementation:** Reach fields, topology inputs, inflow geometry, and manifest properties are recorded under [JOB-004](../reference/bibliography.md#job-004-reach-topology-and-steady-forcing-contracts).
- **Selected methodology:** Inflow placement and offset decisions are recorded under [SDR-005](../reference/bibliography.md#sdr-005-reach-inflow-placement-decisions).
- **Open question:** The headwater geometry and upstream-offset differences are preserved here and not treated as resolved.
