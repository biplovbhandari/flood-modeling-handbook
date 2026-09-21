# Watersheds, Reaches, and Hydrofabrics

A watershed organizes land by where water drains, while a reach network organizes channels by how flow moves from one segment to the next.
Flood modeling needs both ideas because a discharge originates from an upstream drainage area but is applied to a particular directed reach model.

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
- identify the minimum topology needed to prepare a reach-scale hydraulic model.

## Watersheds depend on an outlet

**Scientific foundation:** A watershed, drainage basin, or catchment is the land area that drains toward a specified outlet.
The outlet can be a streamgage, confluence, lake outlet, reach endpoint, or another defined location.
Moving the outlet changes the drainage area, so a watershed is incomplete without its outlet.

A drainage divide is the boundary separating neighboring drainage areas.
On a simple terrain surface, water on opposite sides of a divide moves toward different outlets.
Real drainage can be altered by depressions, karst, diversions, storm drains, canals, and other features, so a divide is a modeled or mapped interpretation at a chosen scale.

Drainage area is the horizontal area enclosed by the divide for the selected outlet.
An outlet farther downstream usually has a larger contributing area because it accumulates upstream catchments.
The [USGS watershed overview](https://www.usgs.gov/water-science-school/science/watersheds-and-drainage-basins) explains the outlet-dependent definition and the role of drainage divides.

## Flow direction makes the network directed

**Scientific foundation:** A drainage network is directed from upstream to downstream.
Each directed reach has an upstream end, a downstream end, and zero or more reaches that drain into it.
Direction is a topological relationship rather than the left-to-right appearance of a line on a map.

A headwater reach has no upstream reach in the represented network.
A tributary joins another stream at a confluence.
The mainstem is the principal path named or selected under a stated rule, while a tributary is a stream that enters that path.
Those labels depend on network definition and selection rule rather than visual width alone.

At a confluence, multiple upstream flows contribute to one downstream reach.
Their hydrograph peaks need not arrive together.
The downstream peak therefore depends on timing, routing, storage, and local inflow as well as the arithmetic sum of simultaneous flows.

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

A hydraulic model domain is the area over which a numerical model represents terrain, roughness, water state, and boundary conditions.
The domain may extend across part of one catchment, cross a mapped divide to provide numerical space, include floodplain areas associated with several catchments, or omit upstream land that generated the applied discharge.
The model domain is therefore not a substitute for the catchment.

| Object | Geometry | Primary question | Typical attributes |
| --- | --- | --- | --- |
| Hydrologic catchment | Polygon | What land contributes water to this outlet? | Drainage area, outlet, land cover, soils, and storage characteristics. |
| Hydrofabric reach | Directed line or network segment | How is this channel segment connected upstream and downstream? | Reach identifier, downstream identifier, stream order, drainage area, and geometry. |
| Hydraulic model domain | Polygon with a grid or mesh | Where will hydraulic equations and boundaries be applied? | CRS, resolution, terrain, roughness, inflow geometry, and downstream boundaries. |

## Reading a small network

**Applied example:** The following network is synthetic teaching material.

Consider two order-1 headwaters, `R-100` and `R-110`, that join to form `R-200`.
Reach `R-200` then joins tributary `R-300` and continues as `R-400`.

```text
R-100 --\
         >-- R-200 --\
R-110 --/             >-- R-400
R-300 ----------------/
```

`R-100` and `R-110` are immediate upstream reaches of `R-200`.
`R-200` and `R-300` are immediate upstream reaches of `R-400`.
`R-100`, `R-110`, and `R-300` are headwaters in this represented network if they have no other upstream connections.
Under the Strahler rules, `R-200` is order 2 because two order-1 reaches join there.
If `R-300` is order 1, `R-400` remains order 2 because unequal orders join.

The cumulative drainage area at `R-400` includes areas contributing through `R-200` and `R-300`, subject to how the hydrofabric delineates and reports those areas.
The local catchment paired with `R-400` is not necessarily the full cumulative watershed above `R-400`.
Hydrofabrics may distinguish local catchment area from total upstream drainage area, so the attribute definition must be checked before use.

## Preparing topology for a reach model

At minimum, reach preparation needs a stable target reach identity, directed geometry, immediate upstream and downstream relationships, and the meaning of every drainage-area attribute.
A boundary placement method may also need the upstream geometry that best represents the principal inflow path.

**Design principle:** Derive topology from one declared network version and preserve the derivation with the model record.
Supplying a reach identifier without its network version can silently change connectivity when the hydrofabric is revised.

**Design principle:** Define the selection rule whenever one upstream reach is treated as the main inflow path.
Largest drainage area, longest path, stream name, stream order, and modeled discharge can identify different reaches at a confluence.

Headwater inflow placement needs separate treatment because no upstream reach geometry exists in the represented network.
Possible methods include a line across the channel near the upstream end, distributed source points, or an upstream extension supported by additional data.
The method should match the numerical representation and be tested for unintended local artifacts.

## Applied example for reach R-200

**Applied example:** The following values are synthetic teaching material.

Reach `R-200` receives flow from `R-100` and `R-110`.
The prepared network reports cumulative drainage areas of 82 km2 for `R-100` and 54 km2 for `R-110`.
A model-building operation selects `R-100` as the principal inflow path because the declared rule chooses the immediate upstream reach with the larger cumulative drainage area.
The operation places the inflow boundary on geometry derived from `R-100`, records both immediate upstream reaches, and associates \(Q=250\ \text{m3/s}\) only later when a scenario is defined.

The larger-area rule is a choice for this example rather than a universal definition of mainstem.
The example does not establish that the inflow placement is suitable for another network, reach, grid, or intended use.

## Common misconceptions

### A reach and its catchment have the same geometry

A reach is a directed channel segment.
A catchment is an area that drains toward an outlet or network element.
Their identifiers may be paired, but their geometries and scientific meanings differ.

### Larger stream order gives an exact discharge

Stream order summarizes branching position.
Discharge also depends on drainage area, precipitation, losses, storage, regulation, and time.

### A line dataset is enough to establish topology

Geometry alone does not reliably identify direction, immediate neighbors, or network version.
The directed relationships and attribute definitions must be explicit.

### The hydraulic domain is the watershed

The domain is a numerical modeling extent.
The watershed is the contributing land area for an outlet.
One cannot be used as evidence for the other without an explicit spatial relationship.

## Competency check

For the synthetic network above, answer the following questions:

1. Which reaches are immediate upstream neighbors of `R-200` and `R-400`?
2. Which reaches are headwaters in the represented network?
3. What are the Strahler orders of `R-200` and `R-400` if `R-100`, `R-110`, and `R-300` are order 1?
4. Why is `R-100` selected as the principal inflow path in the applied example?
5. What evidence would be needed before reusing that selection and placement method elsewhere?

## Source notes

- **Scientific foundation:** Watershed, drainage divide, and water-budget context come from [SCI-003](../reference/bibliography.md#sci-003-watersheds-and-drainage-basins).
- **Scientific foundation:** Stream-order rules come from [SCI-004](../reference/bibliography.md#sci-004-strahler-stream-order).
- **Design principle:** Reach topology, network version, and upstream-selection rules should be explicit model inputs with preserved provenance.
- **Evidence note:** The network and drainage areas in the `R-200` example are synthetic and do not describe a real hydrofabric.
