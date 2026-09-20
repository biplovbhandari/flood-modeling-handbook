# Lab 9 Solution: Diagnose Domain and Boundaries

This solution demonstrates evidence-disciplined diagnosis of the synthetic packet.
It does not validate a project model, establish an expansion threshold, or authorize a methodology change.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, and readiness stopping condition are defined in [Lab 9](../lab-09-diagnose-domain-and-boundaries.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and preserves competing diagnoses until a discriminating check resolves them.
Stop when the answer satisfies the prompt's competency criteria or retains `NOT READY` with the smallest evidence set that could change the verdict.

## 1. Domain-clipping diagnosis

The packet directly observes one connected wet region that reaches both the unintended west perimeter and the intended south-edge outlet span.
The west cells have no assigned outlet role in the packet.
This observation is material evidence that the rectangular boundary may truncate a connected hydraulic pathway.

The leading hypothesis is that the domain clips a laterally connected floodplain or backwater area on the west side.
If that edge is closed, water that should spread farther west can accumulate inside the domain and raise local or upstream WSE.
If that edge is incorrectly opened, the model can release water through a lateral boundary that is not a physical outlet and reduce flow or WSE elsewhere.

The evidence is not yet sufficient to choose an expansion distance or to declare clipping as the only cause.
Competing explanations include a shallow connected numerical fringe that is immaterial to the intended FIM, a terrain or datum error that makes the west corridor artificially low, a wrong inflow or outflow placement that redirects flow, an unintended opening in the boundary preprocessing, and residual transient behavior despite the stored domain-total convergence result.

`volume_convergence` says only that net domain storage change was small relative to interval inflow under the current diagnostic.
It does not show where water moved, whether west-edge cells continued changing, whether outflow balanced inflow, or whether the rectangle represented the connected floodplain.

The immediate domain status is therefore unresolved and not acceptable for hydraulic interpretation.

## 2. Edge-evidence assessment

The target endpoint WSE range is inclusive from 102.7 m through 103.8 m.
The wet west-edge values from 103.1 m through 103.4 m lie inside that range.
Under the supplied description of the current classification, those values could satisfy the WSE portion of a flagged-edge rule.

That arithmetic does not prove an `edge_error` should have been stored.
The packet does not say whether convergence and a disallowed edge violation were both true on the same saved grid, whether the relevant check ran after the first-grid sentinel, whether the downstream endpoint was wet enough to activate it, whether `allow_water_on_edges` was false, which cells the code evaluated, or whether a `BoundaryCheckResult` was persisted.
Current branch priority stores `volume_convergence` when convergence and an edge violation are simultaneous, so that persisted reason cannot exclude the violation.
The lack of `edge_error` therefore cannot be interpreted as a clean check.

At minimum, persisted boundary evidence should include:

1. The model time and saved grid used by the check.
2. A flag stating whether the check activated or why it was withheld.
3. `allow_water_on_edges` and every threshold or classification setting.
4. The upstream and downstream endpoint coordinates and sampled WSE values.
5. Every wet perimeter cell with edge side, row, column, terrain, depth, WSE, and intended role.
6. Connectivity from each wet component to inflow, target reach, intended outlet, and any transfer line.
7. The rule outcome for each cell and the resulting termination or warning decision.

These fields distinguish a completed clean check from a skipped, withheld, allowed, out-of-range, or incompletely persisted check.

## 3. Inflow and slope calculations

### Inflow

The target reach segment runs from `(100, 700)` to `(900, 100)`.
The inflow line lies at x=80 from y=660 through y=760.
The target segment has x coordinates from 100 through 900 and therefore does not intersect the line at x=80.

A zero target-reach intersection is not necessarily wrong because the request claims the line is on separate upstream mainstem `U-17`.
The current build warning only reports more than one intersection with the target reach.
An empty warning therefore establishes neither intersection with `U-17` nor hydraulic connectivity.

The next geometry evidence must include the exact versioned `U-17` row, its coordinate order, its downstream adjacency to the target, evidence that it is the selected mainstem, and its intersection with the inflow line.
The next numerical evidence must show which grid cells the inflow line rasterizes to, that those cells are active and hydraulically connected, and how the requested discharge is divided across them.

### Slope

Current code uses the absolute endpoint terrain difference divided by recorded length.
The calculated scalar is:

\[
S_{code}
=\frac{|100.0\ \text{m}-100.8\ \text{m}|}{1000\ \text{m}}
=\frac{0.8\ \text{m}}{1000\ \text{m}}
=0.0008
\]

This value exceeds the supplied \(10^{-4}\) minimum, so current code would use 0.0008.
The synthetic m/m interpretation is valid because the packet states metre elevations, metre reach length, and a metre-based projected grid.
For a real request, the current positive-EPSG validation alone would not prove projected metre units.

The directed signed bed change from the first endpoint to the last is \(100.0-100.8=-0.8\) m.
Under the supplied upstream-to-downstream direction, terrain rises rather than falls.
Taking the absolute value removes that sign and presents a positive magnitude as if it were an ordinary downhill slope.

The result can indicate reversed reach coordinates, a wrong topology assumption, endpoint sampling onto the wrong cells, terrain noise, a structure or embankment near an endpoint, a locally adverse bed, or an unsuitable use of endpoint terrain as water-surface slope.
The next evidence should include prepared-network direction, a longitudinal terrain and channel profile, endpoint cell locations, nearby terrain samples, structure information, and any independent water-surface or channel-slope data.

## 4. STL, datum, and identity assessment

The STL spans x=760 through x=1000, so its total length is (1000-760=240) m.
The downstream raster provides x coverage only from 820 through 980, which overlaps the STL for (980-820=160) m.
The uncovered parts total ((820-760)+(1000-980)=60+20=80) m.
Only two-thirds of the line has supplied x coverage.

Geometric overlap alone does not make the STL usable.
The covered cells must also exist on the downstream grid, carry valid terrain and depth values, represent the intended downstream wet state, and map to the upstream transfer cells under compatible horizontal and vertical references.

The downstream source labels WSE in meters and NAVD88.
The upstream model supplies no vertical datum.
A horizontal CRS match cannot show whether upstream terrain uses NAVD88, another orthometric datum, an ellipsoidal height, or an unknown offset.
The required evidence is an explicit upstream terrain vertical datum and unit, the downstream terrain and depth datum contract, any transformation used, and a verification that transferred WSE and upstream terrain share one reference before computing depth or applying stage.

The downstream manifest naming `D-09` is not enough to prove that it is the target's downstream neighbor.
The review needs the same prepared-network version that built the target, a verified `reach_to_id` relationship, any modified-network lineage, and the downstream scenario's reach, model, run identity, discharge, boundary type, convergence, edge, and hydraulic evidence.

The model identity hashes DEM and LULC source strings rather than immutable content.
The same URL or path can resolve to changed bytes while retaining the same identity hash.
Immutable object versions or content checksums, retrieval provenance, and comparison with realized asset checksums are needed to detect that change.

## 5. Selected next discriminating check

**B. Map the connected wet component, cell-by-cell boundary roles, terrain, WSE, and flow direction along the west edge and trace that component back to the reach and intended outlet.**

This check directly tests whether the west-edge water belongs to a connected floodplain or backwater pathway that the current rectangle truncates.
It also shows whether terrain error, an unintended open edge, redirected inflow, or isolated shallow cells better explain the observation.

Choice A commits to maximum expansion without identifying which edge or pathway needs space.
Choice C substitutes a storage-change termination for boundary adequacy.
Choice D changes resistance before resolving geometry, source, datum, and boundary causes, which can hide rather than discriminate among them.

If check B confirms a connected physically plausible floodplain beyond the unintended edge, the next step is a bounded outward domain revision followed by the same scenario and comparison criteria.
The expansion distance should follow an authorized method and the observed pathway rather than an arbitrary maximum.

## 6. Readiness verdict

**NOT READY.**

The packet contains material unresolved or contradictory evidence in every boundary category.
It shows connected water on an unintended edge without persisted check details, an inflow that does not intersect the target and lacks its claimed upstream geometry, a positive slope magnitude that hides an adverse directed terrain difference, partial STL source coverage, an unknown upstream vertical datum, unproved downstream topology and scenario suitability, and mutable source identity.

The smallest follow-up evidence set that could change the verdict includes:

1. The connected-component and cell-level west-edge diagnostic selected in part 5.
2. Persisted boundary-check activation, settings, endpoint values, cell classifications, and intended roles.
3. Versioned `U-17`, target, and `D-09` topology and geometry with direction and lineage checks.
4. Rasterized inflow cells, active-cell connectivity, and discharge allocation.
5. A reviewed longitudinal terrain and water-surface profile for the slope boundary.
6. Full STL coverage by valid downstream wet cells and exact downstream scenario provenance.
7. Compatible vertical-datum and unit contracts for upstream terrain and transferred WSE.
8. Immutable DEM and LULC source identities plus realized asset integrity.
9. A bounded domain revision or sensitivity comparison if the connected clipping hypothesis is confirmed.
10. Numerical, mass-balance, boundary, sensitivity, and hydraulic acceptance evidence for the intended scenario use.
