# Network Preparation

Network preparation turns hydrographic source data into a directed, versioned reach graph that model-building and scenario-planning operations can use without guessing about direction, connectivity, or identity.
Every trim, merge, breakpoint, and identifier mapping changes where forcing enters, where downstream control is transferred, and which results can be combined.

## Why this topic matters

A numerically stable model can represent the wrong river system when a reach is reversed, a confluence is merged away, a lake is treated as ordinary channel, or a modified reach loses its lineage.
Network preparation is therefore part of the scientific method, not a clerical preprocessing step.

## Prerequisites

Read [Watersheds, Reaches, and Hydrofabrics](../01-hydrology-for-fim/01-watersheds-reaches-and-hydrofabrics.md) and [Forcing Sources and Uncertainty](../01-hydrology-for-fim/05-forcing-sources-and-uncertainty.md).
Use the [Glossary](../reference/glossary.md) for the meanings of reach, headwater, confluence, mainstem, hydrofabric, and hydraulic connectivity.

## Learning objectives

After this chapter, the reader should be able to:

- verify reach direction and upstream-downstream topology independently of map appearance;
- explain how a mainstem rule affects inflow placement at a confluence;
- distinguish a network headwater from the physical source of a stream;
- evaluate short-reach merging, lake breakpoints, and coastal cutoffs;
- define identifier and lineage requirements for a modified network; and
- state the evidence required before a prepared network can support model construction.

## Direction is a contract

**Scientific foundation:** A reach network is a directed graph.
Each reach needs a stable identifier, an upstream end, a downstream end, and adjacency that agrees with the surrounding graph.
Coordinate order can encode direction, but it is trustworthy only when the data contract states the convention and topology checks confirm it.

A direction review should check at least four relationships.

1. The start and end coordinates must follow the documented upstream-to-downstream convention.
2. The downstream identifier must name the reach connected at the downstream endpoint, or a documented terminal value.
3. Each immediate upstream reach must point to the reviewed reach in the same network version.
4. Elevation, drainage area, or stream order may support the result, but no one diagnostic should replace the topology contract.

Flat reaches, hydroflattened water surfaces, diversions, tidal reaches, and noisy terrain can defeat a rule that expects every downstream endpoint to be lower.
Reversing a reach can move inflow and outflow geometry to the wrong ends, reverse a slope estimate, and propagate stage information in the wrong direction.

Directed-network and stream-order concepts are supported by [SCI-003](../reference/bibliography.md#sci-003-watersheds-and-drainage-basins) and [SCI-004](../reference/bibliography.md#sci-004-strahler-stream-order).

## Mainstem selection must be explicit

At a confluence, several reaches may be immediately upstream.
The graph must preserve every tributary relationship even when one upstream reach is selected for a mainstem operation.

Possible rules include largest drainage area, largest design discharge, highest stream order, longest upstream path, or a named-river convention.
These rules can select different reaches, so the prepared-network record must state the chosen rule and its input values.

**Applied example:** Reaches `R-100` and `R-300` join at the upstream end of `R-200`.
`R-100` has a drainage area of 84 square kilometres, and `R-300` has a drainage area of 51 square kilometres.
If the stated rule is largest immediate-upstream drainage area, `R-100` is the mainstem for inflow-placement purposes while both reaches remain upstream neighbours of `R-200`.

Choosing `R-300` instead would move the forcing geometry to a different channel even if the total discharge were unchanged.

## Headwaters depend on network extent

A headwater has no upstream reach in the represented graph.
It is not necessarily the physical source of a stream because clipping or a coarse hydrofabric can make an interior reach appear to be a headwater.

Forcing at a headwater may use a cross-channel line, distributed points, a lateral source region, or another documented geometry.
The chosen treatment must state its spatial support, orientation, and relationship to the first modeled cells.

**Design principle:** Never infer a true headwater from a missing upstream identifier alone.
Confirm that the network extent, topology, and missing-value convention support the classification.

## Confluences are protected topology

Confluences preserve separate forcing pathways, timing, and downstream influence.
Merging across a confluence can erase an independent tributary and make stage-transfer or result-lineage relationships ambiguous.

**Design principle:** A merge chain may continue only through reaches that have exactly one immediate upstream continuation and one immediate downstream continuation within the chain.
The merge must stop before a confluence unless a separate method explicitly preserves each tributary's forcing and lineage.

## Short-reach merging changes the modeling unit

A short reach can be a poor independent modeling unit when its length is small relative to a hydraulic transition or floodplain width.
Merging can reduce artificial model boundaries and scenario-library fragmentation, but it also changes reach identity and the length over which one model is interpreted.

**Applied example:** A synthetic preparation rule considers merging a single-thread chain until its cumulative length reaches at least 4.0 km, provided the drainage-area change from the downstream starting reach remains below 6 percent.
The values are example method settings, not universal hydraulic thresholds.
The producer must also define traversal order, geometry concatenation, attribute aggregation, treatment of a chain that ends early, and identifier generation.

Merging removes an internal boundary and can give flow more distance to adjust.
It can also hide a local structure, terrain transition, or forcing increment that matters hydraulically.
The merge criteria therefore screen network geometry but do not prove hydraulic equivalence.

## Lakes and coasts create breakpoints

A lake can act as a level-pool control, a storage feature, and a break between riverine models.
Treating a centerline through a lake as ordinary channel can imply a slope and conveyance that do not represent the waterbody.

A lake method should state how it identifies the waterbody polygon, classifies full and partial overlap, trims inlet and outlet reaches, represents storage or stage, and records removed reaches.
An overlap percentage is not self-explanatory without the denominator, geometry repair rules, and treatment of shoreline uncertainty.

At a coast, a cutoff is a model-scope choice rather than a claim that river discharge or water level stops.
Cutting too far inland can omit riverine backwater, while cutting too far seaward can apply a river boundary where tides, surge, or waves control the water surface.

**Open question:** Which lake-stage and coastal-stage products are sufficiently compatible with the river-network geometry, datum, and intended scenario range?
The answer requires a stated handoff method and validation evidence, not only spatial overlap.

## Identifiers must preserve lineage

A prepared identifier must be stable within one network version and unambiguous across models, scenarios, and composites.
When reaches are merged, trimmed, or removed, the preparation record should include:

- the prepared-network version and source-network version;
- every contributing source reach in upstream-to-downstream order;
- the identifier-generation rule;
- all immediate upstream identifiers and the downstream identifier;
- the mainstem rule and its result;
- headwater, terminal, lake, coast, trim, and merge classifications; and
- removed reaches with explicit reasons for removal.

**Design principle:** A model record must not repeat its own `reach_id` where a downstream neighbour is required.
Topology claims must come from the prepared network or another record that has been checked against it.

## Synthetic preparation record

The following compact record is sufficient to interpret the applied network used in later chapters.

| Field | `R-100` | `R-300` | `R-200` |
| --- | --- | --- | --- |
| Immediate upstream reaches | None | None | `R-100`, `R-300` |
| Downstream reach | `R-200` | `R-200` | Terminal |
| Direction | Upstream to downstream | Upstream to downstream | Upstream to downstream |
| Drainage area | 84 km2 | 51 km2 | 142 km2 |
| Mainstem role at confluence | Selected by largest area | Tributary | Not applicable |
| Breakpoint class | None | None | Terminal river boundary |

The two upstream drainage areas total 135 square kilometres.
The remaining 7 square kilometres between the upstream boundaries and the `R-200` outlet form the local incremental drainage area.

**Evidence note:** The table establishes the synthetic topology and the rule used to select the mainstem.
It does not establish terrain adequacy, boundary placement, hydraulic calibration, or the validity of any external network dataset.

## Acceptance review

Before accepting a prepared network, answer the following questions.

1. Are direction, adjacency, and terminal values internally consistent?
2. Does every confluence preserve each immediate upstream reach?
3. Is the mainstem rule stated with the values used to apply it?
4. Are headwaters classified from the represented network extent rather than a missing value alone?
5. Are merge, trim, lake, and coastal methods reproducible?
6. Can every prepared reach be traced to its source reaches?
7. Are removed reaches recorded with reasons?
8. Do model and scenario records use the same network version?

## Common misconceptions

### A descending terrain profile defines direction

Elevation supports a direction review but cannot resolve flat, tidal, hydroflattened, or noisy reaches by itself.

### Mainstem means the whole named river

A mainstem field may mean only the selected immediate upstream neighbour at one confluence.
The network contract must define the scope.

### A successful model build proves network preparation

A model-building operation can consume a prepared reach without proving that upstream merges, lake treatment, coastal treatment, or lineage were correct.

### A merged network can discard old identifiers

Forcing, observations, and historical results can still refer to source identifiers.
Discarding the mapping makes joins and change analysis irreproducible.

## Competency check

1. Why can elevation support but not define reach direction?
2. Which reaches are immediate upstream neighbours of `R-200`, and which one is the applied-example mainstem?
3. Why should a merge stop before the `R-100`, `R-300`, and `R-200` confluence?
4. What does the synthetic 4.0 km and 6 percent merge rule establish, and what does it not establish?
5. Which additional contracts are needed at lake and coastal breakpoints?
6. What lineage must remain when several source reaches become one prepared reach?

## Further reading and source notes

- **Scientific foundation:** Watershed and directed-network concepts are supported by [SCI-003](../reference/bibliography.md#sci-003-watersheds-and-drainage-basins).
- **Scientific foundation:** Stream-order concepts are supported by [SCI-004](../reference/bibliography.md#sci-004-strahler-stream-order).
