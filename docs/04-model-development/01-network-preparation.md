# Network Preparation

Network preparation turns a source hydrofabric into the directed, versioned reach graph that model-building and scenario-planning components can use without guessing about direction, connectivity, or identity.
The prepared network is a scientific input because every trim, merge, breakpoint, and identifier mapping changes where forcing enters, where downstream control is transferred, and which results can be assembled.

## Why this topic matters

A hydraulically plausible raster model can still represent the wrong river system if a reach is reversed, a confluence is merged away, a lake is treated as ordinary channel, or a source identifier loses its lineage.
The current modeling jobs consume network choices made elsewhere, so successful job completion is not evidence that the network was prepared according to selected methodology.

## Prerequisites

Read [Watersheds, Reaches, and Hydrofabrics](../01-hydrology-for-fim/01-watersheds-reaches-and-hydrofabrics.md), [Forcing Sources and Uncertainty](../01-hydrology-for-fim/05-forcing-sources-and-uncertainty.md), and [Source Authority](../reference/source-authority.md).
Use the [Glossary](../reference/glossary.md) for the stable meanings of reach, headwater, confluence, mainstem, hydrofabric, and hydraulic connectivity.

## Learning objectives

After this chapter, the reader should be able to:

- verify reach direction and upstream-downstream topology independently of map appearance;
- explain how a mainstem rule affects inflow placement at a confluence;
- distinguish headwater treatment from ordinary upstream-mainstem treatment;
- explain the selected short-reach, lake, and coastal network decisions without claiming that current modeling jobs implement them;
- define identifier and lineage requirements for a modified network; and
- state what evidence is required before a prepared network can be accepted as a modeling input.

## Direction is a contract, not a drawing convention

**Scientific foundation:** A reach network is a directed graph.
Each reach needs a stable identifier, an upstream end, a downstream end, and a downstream relationship that agrees with the surrounding graph.
The order of coordinates in a line can encode direction, but coordinate order is only trustworthy when the dataset contract says so and a topology check confirms it.

A useful direction review checks at least four relationships.

1. The reach start and end must follow the documented upstream-to-downstream convention.
2. The downstream identifier must name the reach reached from the downstream endpoint, or a documented terminal value.
3. Every listed immediate upstream reach must point to the reviewed reach under the same network version.
4. Drainage area, elevation, or another diagnostic may support the direction, but no one attribute should silently replace the topology contract.

Elevation does not resolve every direction question.
Flat reaches, hydroflattened water surfaces, tidal reaches, diversions, and noisy terrain can all defeat a simple rule that expects every downstream endpoint to be lower.

**Hydraulic consequence:** Reversing a reach can put inflow and outflow geometry at the wrong ends, reverse a slope estimate, misidentify downstream dependencies, and propagate stage information in the wrong direction.

## Mainstem selection is a stated rule

At a confluence, more than one reach is immediately upstream.
The network must preserve all tributary relationships even when one upstream reach is selected for a specific mainstem operation.

Possible mainstem rules include largest drainage area, largest discharge, highest stream order, longest upstream path, or a named-river convention.
These rules can select different reaches, so the chosen rule must travel with the prepared network or the consuming request.

**Intended field semantics:** The `BuildModelInputs.upstream_reach_ids` description says that the field contains the identifiers of reaches draining into the modeled reach.
The `BuildModelInputs.upstream_mainstem_reach_id` description says that the field contains the immediate upstream reach with the largest drainage area and uses null for a headwater.

**Current enforcement:** The current `build_model` job trusts both caller-supplied fields and does not traverse `reach_to_id` to derive or verify them.
It does not verify that `upstream_reach_ids` is complete or adjacent, that `upstream_mainstem_reach_id` belongs to that list, that the supplied mainstem has the largest drainage area, or that null identifies a true network headwater.
Geometry construction queries and uses the supplied mainstem identifier when it is non-null and follows the null branch directly when it is null.

**Hydraulic consequence:** The caller-supplied upstream-mainstem geometry controls where the current job places the inflow line when the identifier is non-null.
Choosing a different tributary can move the forcing line to a different channel and change the local route into the target reach even when total discharge is unchanged.

## Headwaters depend on network extent

A headwater has no upstream reach in the represented network.
It is not necessarily the physical source of a stream, because a coarser or clipped network can make an interior reach appear to be a headwater.

**Selected methodology:** DR-014 ALT-B has Alternate Selected status and places point inflows along a headwater reach.

**Current implementation:** When the caller supplies null for `upstream_mainstem_reach_id` and false for `ds_of_lake`, `make_inflow_line` creates one perpendicular line at the first coordinate of the target reach.
The current code does not verify that null represents a true network headwater.
It uses the null behavior directly, assumes that the target geometry is directed upstream to downstream, and implements a single line rather than the selected distributed points.

**Open question:** No handbook statement can reconcile this difference by renaming the current line as distributed inflow.
An authorized component must either supply and validate the selected geometry through a supported contract or the selected methodology must change.

## Confluences are protected topology

Confluences are locations where distinct upstream pathways combine.
They carry information about separate forcing, timing, and downstream influence even when one branch has much less drainage area than another.

Merging across a confluence can erase a forcing location and make it impossible to represent different tributary conditions.
It can also make stage-transfer and result-lineage relationships ambiguous because one merged object would span more than one independent upstream dependency.

**Selected methodology:** DR-023 ALT-B has Alternate Selected status and allows merging only through a continuous chain in which a merge candidate has exactly one immediate upstream reach.
The record explicitly excludes confluences from merging in either direction.

## Short-reach merging changes the modeling unit

A very short reach can be a poor independent modeling unit when its length is small relative to the hydraulic transition or floodplain width and when its incremental drainage-area change is negligible.
Merging can reduce artificial boundaries and excessive library fragmentation, but it also changes reach identity, forcing placement, and the length over which one model is interpreted.

**Selected methodology:** DR-024 ALT-B has Alternate Selected status.
It uses a 5 percent drainage-area criterion measured against the reach where the candidate chain started and a 5 km minimum merged length.
The 5 km value is a floor rather than a ceiling, so the selected text says to continue until the chain clears the threshold and then stop.
Both values and their interpretation come from DR-024 rather than from a universal hydraulic rule.

The selected method still requires implementation details that the decision record does not fully define.
Those details include deterministic traversal order, treatment of a chain that ends before 5 km, geometry concatenation, attribute aggregation, source-to-prepared identifier lineage, and handling of a network-version change.

**Hydraulic consequence:** Merging removes an internal model boundary and can provide more space for flow to adjust.
It can also smooth over a local geometry change or forcing increment that matters hydraulically.
The drainage-area threshold is therefore a network-selection rule, not proof that merged reaches have equivalent terrain, roughness, structure, or backwater behavior.

## Lakes create breakpoints and unresolved geometry choices

A lake can act as a level-pool control, a storage feature, and a break between riverine models.
Treating a centerline through a lake as ordinary channel can imply slope, conveyance, and one-dimensional direction that do not represent the waterbody.

**Selected methodology:** DR-007 ALT-A has Alternate Selected status and calls for removing lake reaches, trimming partially overlapping reaches at a dead-pool polygon, and tagging the resulting lake inlets and outlets.
The record leaves the overlap threshold unresolved and notes that a meeting used approximately 100 percent while Case-013 used greater than 75 percent.
Those numbers are competing project evidence within DR-007, not interchangeable defaults.

**Needs-Review methodology:** DR-034 ALT-A proposes shrinking an existing national waterbody layer to approximate a dead-pool polygon.
The Decision Register marks DR-034 Needs-Review, and the source dataset and shrink rule remain open.

**Selected methodology:** DR-037 ALT-B has Alternate Selected status and treats lakes as breakpoints outside Flows2FIM rather than ordinary reach objects.
This selection does not by itself define lake-stage products, handoff ownership, or identifier policy.

**Current implementation:** `BuildModelInputs.ds_of_lake` is a caller-supplied boolean.
When true, the current job places an inflow line inside the target reach at `walk_us_dist_pct` of target-reach length from its first coordinate.
The job does not derive the flag, trim the network, remove lake reaches, create dead-pool polygons, or validate lake topology.

## Coasts are terminal-policy boundaries

At a coast, riverine and coastal processes overlap.
A network cutoff is therefore a model-scope decision rather than a claim that river discharge or water level stops at that location.

**Selected methodology:** DR-038 ALT-A has Alternate Selected status and uses NOAA tidal-surface coverage as a proxy.
The record says to drop a fully covered reach, trim a partly covered reach to its upstream portion, mark the coastal network break, and cascade removal downstream so that no orphaned fragment remains.

The approved handbook design still identifies coastal handling as unresolved or weakly settled.
DR-038 was drafted from developer discussion, and its own raw notes describe the proxy as an expedient research approach rather than validation of an inland-coastal handoff.

**Current implementation:** No current `twod-fim-jobs` entry point intersects reaches with coastal coverage, trims a coastal reach, or cascades downstream removal.

**Hydraulic consequence:** Cutting the network too far inland omits riverine backwater and inundation that the river model could represent.
Cutting it too far seaward can apply a riverine boundary where tides, surge, waves, or coastal circulation control the water surface.

## Identifiers must survive modification through lineage

A prepared-network identifier must be stable within one network version and unambiguous across stored models, scenarios, and composites.
When source reaches are merged, trimmed, or removed, the preparation artifact should record at least the following facts.

- It should record the prepared-network version and the source-network version.
- It should map each prepared reach to every contributing source reach and record the order and direction of those source segments.
- It should record any new prepared identifier and the rule that generated it.
- It should record the prepared downstream identifier, all immediate upstream identifiers, and the mainstem-selection result.
- It should record lake, coastal, headwater, terminal, trim, and merge tags with the decision revision that produced them.
- It should record removed reaches and the reason they are absent rather than making their disappearance indistinguishable from missing data.

The reviewed network decisions do not select a complete merged-identifier or lineage schema.
That missing contract is an Open question, not permission to reuse one source identifier without recording the other contributors.

**Current implementation concern:** `build_model` requires the configured `reach_to_id` field in a reach-network row, but the current manifest assignment populates `properties.downstream_reach_id` from `reach_id` rather than from `reach_to_id`.
The checked-in example manifests therefore repeat the modeled reach identifier in `downstream_reach_id`.
A current model manifest must not be used as proof of downstream topology until that field behavior is corrected or explicitly redefined.

## The boundary between network preparation and current jobs

The current `build_model` job consumes one prepared reach row plus caller-authored topology inputs.
It uses those inputs to build model geometry and metadata, but it does not implement the upstream network-preparation workflow.

The following responsibility boundary prevents overclaiming.

| Responsibility | Required authority and evidence |
| --- | --- |
| Direct reach geometry and topology. | A versioned prepared-network artifact must define direction, identifiers, adjacency, and lineage. |
| Mainstem selection. | The caller or an upstream preparation component must apply a stated rule and supply `upstream_mainstem_reach_id`. |
| Short-reach merging. | An upstream component must implement DR-023 and DR-024 and preserve a source-to-prepared lineage record. |
| Lake trimming and tagging. | An upstream component must implement the authorized parts of DR-007 and must not treat unresolved DR-034 details as settled. |
| Coastal trimming and removal. | An upstream component must implement the authorized parts of DR-038 and retain the proxy and handoff limitations. |
| Model artifact creation. | The current `build_model` job can consume the prepared reach and caller inputs to create model geometry and raster artifacts. |

## Worked network review

Suppose reaches H1 and H2 join at confluence C, followed downstream by S1, S2, and S3 in that order.
For this illustration, start at downstream reach S3 and walk upstream through its single-upstream chain to S2 and then S1.
Assume the cumulative length of S3, S2, and S1 first exceeds 5 km when S1 is added, and assume the drainage-area difference between the starting reach S3 and upstream endpoint S1 is 4 percent of S3's drainage area.
Also assume that the producer stops immediately when the selected length floor is first cleared and does not attempt to include confluence reach C.

Under DR-023 and DR-024, H1, H2, and C cannot be merged across their confluence.
Under the stated traversal and stopping assumptions, the S3-S2-S1 chain is consistent with the recorded direction and reference rule because the walk begins at S3, each continuation follows one upstream reach, the cumulative length first clears the selected 5 km floor at S1, and the drainage-area difference is measured against starting reach S3 rather than against each immediate neighbor.

This illustration does not resolve the decision record's unspecified production details, establish that the merge is implemented, prove that local terrain and structures are negligible, or define which identifier the merged reach receives.
Those require a prepared-network producer, its lineage artifact, and hydraulic review.

## Failure modes and diagnostic signals

### A reach start is treated as upstream without verification

The current headwater and lake-outlet placement logic uses distance from the first coordinate.
Inspect coordinate order and topology together before accepting the boundary geometry.

### Mainstem means the whole named river

The current field description defines the value as the immediate upstream reach with the largest drainage area, which is narrower than a basin-wide named-river mainstem.
The current job does not enforce that description and uses the caller-supplied identifier directly.

### A successful model build proves selected network preparation

The job accepts an already prepared network and caller-supplied topology.
It does not prove that merges, lake treatment, coastal treatment, or identifier lineage were performed correctly.

### A merged network can discard old identifiers

Downstream plans, forcing, results, and validation data can still refer to source identifiers.
Discarding the source mapping makes reproducible joins and change analysis impossible.

### The manifest proves the downstream neighbor

The current `downstream_reach_id` assignment repeats the modeled reach identifier.
Use the authoritative prepared network until that contract is resolved.

## Competency check

1. Why can a descending terrain profile support but not define reach direction?
2. Which current input distinguishes all upstream neighbors from the one upstream mainstem?
3. Why does DR-023 prohibit merging across a confluence?
4. What do the 5 percent and 5 km values in DR-024 mean, and what do they not prove?
5. Which lake and coastal steps occur outside the current modeling jobs?
6. What lineage must be retained when three source reaches become one prepared reach?
7. Why is the current manifest's `downstream_reach_id` not reliable topology evidence?

## Source notes

- **Scientific foundation:** Directed network, watershed, and stream-order concepts are supported by [SCI-003](../reference/bibliography.md#sci-003-watersheds-and-drainage-basins) and [SCI-004](../reference/bibliography.md#sci-004-strahler-stream-order).
- **Selected methodology:** Network modification records and their exact statuses are indexed under [SDR-008](../reference/bibliography.md#sdr-008-network-preparation-decisions).
- **Selected methodology:** DR-014 headwater inflow placement is indexed under [SDR-005](../reference/bibliography.md#sdr-005-reach-inflow-placement-decisions).
- **Current implementation:** Network inputs, inflow construction, manifest assignment, and job scope are indexed under [JOB-004](../reference/bibliography.md#job-004-reach-topology-and-steady-forcing-contracts) and [JOB-008](../reference/bibliography.md#job-008-model-development-input-transformations-and-warnings).
- **Open questions:** Prepared-network ownership, identifier lineage, and current job boundaries are preserved in [CONF-006](../reference/conflicts-and-open-questions.md#conf-006-work-outside-current-modeling-jobs).
