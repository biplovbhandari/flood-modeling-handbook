# Backwater and Boundary Control

Open-channel depth changes when the local flow cannot remain at one uniform depth.
A downstream stage, confluence, structure, lake, coast, or other control can alter the water surface upstream, especially during subcritical flow.

## Why this topic matters

Boundary conditions are part of the hydraulic problem, not administrative settings at the edge of a grid.
An unsuitable downstream condition can distort WSE, depth, velocity, storage, and inundation inside the area being interpreted.

## Prerequisites

Read [Energy, Momentum, and Flow Regimes](02-energy-momentum-and-flow-regimes.md) and [Manning Flow and Normal Depth](03-manning-flow-and-normal-depth.md).
Retain the distinction between geometric depth and WSE, and retain the stated downstream-positive sign convention.

## Learning objectives

After this chapter, the reader should be able to:

- define gradually varied flow, backwater, control section, and upstream or downstream influence;
- explain why a raised downstream WSE can affect an upstream subcritical profile;
- distinguish imposed discharge, imposed WSE, slope-based outflow, closed edges, transfer boundaries, and initial conditions;
- keep solver tokens separate from physical boundary meanings; and
- identify evidence needed before choosing a boundary near a confluence, lake, coast, or structure.

## Gradually varied flow

**Scientific foundation:** Gradually varied flow is steady open-channel flow whose depth changes along the channel over a distance long enough that pressure remains approximately hydrostatic and one-dimensional profile reasoning remains useful.
Its depth is not constant, so it is not uniform flow.
It is also different from rapidly varied flow at a hydraulic jump, abrupt structure, or similarly short transition.

The simplified profile intuition used here assumes steady flow, one-dimensional and approximately hydrostatic treatment, gradual variation, prismatic geometry, and constant discharge or no material lateral inflow over the reach being interpreted.
It does not apply unchanged at a confluence, a material lateral inflow, an abrupt geometry transition, a hydraulic jump, a structure with rapidly varied flow, or a strongly two-dimensional flow pattern.
Those locations require a control-volume, network, structure, or numerical-model treatment that represents the added flow and momentum exchanges.

For subcritical flow, \(Fr<1\), a downstream disturbance can propagate information upstream under the idealized shallow-water interpretation.
If a downstream control raises depth above the local normal-depth tendency, the water surface can adjust over an upstream reach until the control's effect decays or meets another control.
That upstream increase in WSE is a backwater effect.

Backwater is not limited to dams.
A high downstream river stage at a confluence, a lake or reservoir level, a tidal water level, a bridge constriction, debris, or reduced conveyance can all create downstream control under suitable conditions.

## Control sections and influence directions

A control section is a location where geometry, a structure, critical flow, known stage, or another hydraulic relationship constrains the local stage-discharge state strongly enough to organize the neighboring profile.
Examples can include a weir crest, a free overfall near critical control, a constriction, or a downstream water body with observed stage.
The label must follow evidence for the actual mechanism.

Upstream influence means that a disturbance or imposed condition at a downstream location can alter hydraulics at locations upstream.
Downstream influence means that an upstream disturbance or imposed condition affects locations downstream.

In idealized subcritical flow, one shallow-water disturbance direction can travel upstream and one can travel downstream.
Both upstream and downstream hydraulic information can therefore matter.
In idealized supercritical flow, both characteristic directions travel downstream relative to the ground, so downstream conditions do not ordinarily determine the upstream solution.
Near critical, mixed, multidirectional, or rapidly varied conditions require more complete analysis than a single section-wide Froude number.

## Two downstream controls, one reach

![Two schematic longitudinal profiles compare a slope-based downstream condition with a raised known downstream water-surface elevation.](../assets/open-channel-controls.svg)

**What to notice:** Both panels use the same falling bed and upstream discharge direction.
The left panel shows the uniform-flow reference implied by a slope-based normal-depth condition.
The right panel shows a raised downstream WSE, a deeper downstream section, and an influence arrow pointing upstream.
The dashed line in the right panel is the normal-depth reference, not a second simulated water surface.
The separation between the raised profile and the dashed reference grows toward the downstream control.
The drawing is qualitative and does not assert a distance of influence for a project reach.

The figure does not claim that known stage is always preferable.
It shows that the two boundary choices encode different hydraulic information and can produce different profiles.

## Boundary and initial conditions answer different questions

| Concept | What is prescribed | Typical hydraulic role | Main evidence need |
| --- | --- | --- | --- |
| Imposed discharge | Flow rate \(Q\), steady or time-varying. | Adds or removes a specified flux through a boundary. | Discharge magnitude or hydrograph, units, timing, geometry, direction, and provenance. |
| Imposed WSE or known stage | WSE relative to a stated vertical datum, steady or time-varying. | Constrains water level and allows flux response according to the solver and local state. | Datum-compatible stage evidence, time support, boundary location, and physical control. |
| Slope-based or normal-depth outflow | Friction slope used with geometry and roughness to form a depth-discharge relation. | Allows outflow according to a uniform-flow approximation at the edge. | Defensible friction-slope estimate, roughness, geometry, flow regime, and distance from interpreted results. |
| Closed edge | Zero normal flux through the modeled edge. | Retains water inside that edge and can create pooling or reflection if placed across a real flow path. | Evidence that the edge is a divide, wall, symmetry boundary, or otherwise impermeable at modeled conditions. |
| Transfer boundary | WSE information derived from another modeled scenario and mapped to a shared or intersecting geometry. | Carries downstream hydraulic control into an upstream model. | Compatible scenario identity, terrain, grid, datum, geometry, stage extraction, and provenance. |
| Initial condition | Water depth, WSE, or other state at simulation start. | Sets the starting state and can reduce or increase adjustment time. | Compatible state, datum, time, geometry, and justification for initialization. |

An initial condition does not remain a boundary constraint merely because the initial state touches an edge.
A stage can be used both to initialize and to constrain a boundary only when both roles are explicitly configured and supported.

The official HEC-RAS [2D external-boundary documentation](https://www.hec.usace.army.mil/confluence/rasdocs/r2dum/latest/boundary-and-initial-conditions-for-2d-flow-areas/external-boundary-conditions) distinguishes flow, stage, rating-curve, and normal-depth boundary types and separately describes optional stage-based initialization.
That solver documentation supports the conceptual distinctions but does not define the project's solver tokens or selected methodology.

## Free outflow is not one universal condition

Terms such as free outflow, free boundary, open boundary, freefall, and normal depth are sometimes used loosely.
They are not interchangeable physical specifications.

A normal-depth boundary uses a friction slope, geometry, and roughness to form a stage-discharge relationship under a uniform-flow approximation.
A free overfall involves a physical control near critical flow and a drop in downstream support.
A steep slope supplied to a normal-depth boundary can lower the computed boundary depth and increase its ability to pass water, but it does not by naming alone reproduce all physics of a free overfall.
A closed edge imposes no normal flux and is the opposite of an unrestricted outlet.

Always record the actual mathematical or solver behavior, the geometry where it is applied, the numerical value and units, and the physical evidence that motivates it.

## Boundary placement and sensitivity

The [HEC-RAS downstream-boundary documentation](https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.0/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-unsteady-flow-hydrodynamics/implicit-finite-difference-scheme/downstream-boundary-conditions) states that a normal-depth condition represents the stage that would occur under uniform flow and advises placing it far enough downstream that boundary error does not affect the study area.
The [steady-flow boundary guidance](https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.0/basic-data-requirements/steady-flow-data/boundary-conditions0) recommends varying the starting boundary elevation to test whether profiles converge before reaching the interpreted area.

A practical boundary-sensitivity review therefore asks:

1. Which physical quantity or relationship is being prescribed?
2. Is its location outside or inside the area where results will be interpreted?
3. Does the diagnosed flow regime permit upstream influence?
4. How far do plausible boundary alternatives change WSE, depth, velocity, storage, and inundation?
5. Do those changes decay before the decision-relevant area?
6. Is the apparent decay physical, or is it caused by domain limits, numerical diffusion, dry cells, or another modeled control?

Moving a boundary or changing its value is a sensitivity experiment.
It does not validate the preferred choice unless the choice also has physical and project evidence.

## Confluences, lakes, coasts, and structures require evidence

### Confluence

A tributary entering a higher-stage mainstem can experience backwater.
A local normal-depth outlet on the tributary can miss that downstream control.
The relevant evidence can include simultaneous flows, mainstem WSE, geometry, timing, and a compatible downstream scenario.
No universal rule says every confluence must use one boundary type.

### Lake or reservoir

A lake can impose a level-pool or time-varying stage, but outlet structures, wind setup, operations, bathymetry, and drawdown can complicate that picture.
A nearly zero normal-depth slope is not automatically equivalent to a known lake stage.
It can instead restrict outflow and cause model-dependent pooling.

### Coast or estuary

Tides, surge, waves, river flow, salinity effects, timing, and the selected vertical datum can matter.
A fixed stage can be unsuitable for a time-varying event, while a normal-depth outlet can omit coastal stage control entirely.
Coastal boundary selection requires event and datum evidence rather than a generic label.

### Structure

A bridge, culvert, weir, dam, gate, or other structure can create a stage-discharge control, energy loss, pressurized flow, overtopping, or rapidly varied transition.
Replacing the structure with a generic Manning slope or fixed stage can hide the controlling mechanism.
The required evidence depends on geometry, operations, submergence, blockage, and the flow range.

These contexts identify questions to investigate.
They are not universal project prescriptions.

## Current project boundary semantics

**Current implementation:** The current solver input model exposes `QFIX`, `HFIX`, `FREE`, and `TRANSFER` boundary types in [`solvers.py`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/solvers.py).
`QFIX` carries an imposed discharge, `HFIX` carries a fixed WSE, `FREE` carries a value described as normal-depth slope in m/m, and `TRANSFER` carries cell-related WSE information reconstructed from a downstream scenario's depth and terrain assets.
The existence of an interface type does not prove that every current job constructs or validates every type for every context.

**Current implementation:** The transfer writer calculates each candidate point's WSE as downstream depth plus downstream terrain and writes an `HFIX` point only when that WSE is greater than zero.
It does not require positive depth, so a dry downstream cell with positive terrain can produce an `HFIX` point equal to its terrain elevation, while a zero or negative WSE is omitted.
The nominal transfer `bc_value` participates in scenario labeling or identity but does not set the current writer's per-cell `HFIX` values.

**Open question:** The current transfer contract does not establish whether points should require wet downstream cells, how zero or negative WSE should be represented, whether the source and target vertical datums are compatible, or whether the realized `HFIX` points match the intended transfer geometry and hydraulics.

**Current implementation:** The ND job constructs `QFIX` at the inflow and `FREE` at a derived or caller-supplied outflow area.
Its `FREE` value is the centerline-endpoint terrain slope estimate subject to a configured minimum.

**Current implementation:** The KWSE job constructs `QFIX`, `TRANSFER`, and an additional `FREE` condition.
The transfer comes from the named downstream scenario.
The additional `FREE` condition intersects the downstream scenario's inundation polygon with each cardinal domain edge and uses a hard-coded slope value of 0.5 m/m in the reviewed code.
For each touched edge, the writer reduces every intersection to its overall bounds and emits one contiguous cardinal-edge span, including dry gaps between disjoint intersections.

**Selected methodology:** DR-003 has Alternate Selected status in the reviewed Decision Register and selects freefall at downstream-FIM-informed KWSE edge cells.
The decision explicitly distinguishes that choice from its normal-depth alternatives.

**Open question:** Current code represents the relevant edge condition with the `FREE` token whose schema meaning is normal-depth slope, and it supplies 0.5 m/m for KWSE edge handling.
Neither the token nor the steep numeric slope proves physical freefall.
The implemented contiguous per-edge span is also broader than DR-003's downstream-FIM-informed edge-cell wording when disjoint intersections leave dry gaps.
The geometry and hydraulic response of that difference remain unvalidated.
[CONF-001](../reference/conflicts-and-open-questions.md#conf-001-boundary-condition-terminology-and-behavior) keeps this semantics conflict visible.

**Open question:** The standalone DR-039 file marks an ND edge-handling alternative `#current`, but the reviewed Decision Register contains no DR-039 row or registered status.
[CONF-008](../reference/conflicts-and-open-questions.md#conf-008-unregistered-dr-039-selection) therefore prevents the handbook from presenting DR-039 as selected methodology.
Current ND code remains authoritative for checkout behavior.

**Selected methodology and open question:** Registered decisions DR-005, DR-006, and DR-008 address lake and coastal contexts, but the approved handbook design records lake and coastal policy as unresolved or weakly settled.
Those records do not justify a universal boundary prescription, and this chapter did not establish current end-to-end implementation or validation for every lake or coastal case.

## Common misconceptions

### Downstream control means water flows upstream

Backwater describes upstream influence on water level and related hydraulics.
The mean discharge can remain downstream while a shallow-water disturbance propagates upstream.

### A known downstream stage is always more accurate

It is more informative only when its location, time, datum, geometry, and physical control are compatible with the modeled event.
An incompatible stage can be a precise but wrong boundary.

### Closed edges are neutral

A closed edge enforces zero normal flux.
It can be appropriate at a real no-flow boundary and damaging across a real drainage path.

### The `FREE` token defines physical freefall

In the reviewed current schema, `FREE` carries a normal-depth slope.
Project terminology and selected-methodology wording conflict with that implementation label.

### A hot start or wet initial grid supplies downstream control

An initial state affects the transient adjustment.
It does not replace a boundary condition that continues to act during the simulation.

## Competency check

For a subcritical reach, compare a slope-based downstream condition with a known downstream WSE that is 0.8 m above the calculated normal-depth WSE.
Predict the direction of downstream depth change and possible upstream influence.
Then list the geometry, datum, timing, roughness, slope, flow-regime, and sensitivity evidence needed before deciding which boundary better represents the physical system.

Finally, explain why the same answer cannot be applied automatically to a confluence, lake, coast, or structure.

## Practice

Complete [Lab 5: Backwater and Boundaries](../labs/lab-05-backwater-and-boundaries.md) to compare boundary choices across synthetic river, confluence, lake, coastal, and structure contexts while preserving project evidence scopes.

## Source notes

- **Scientific foundation:** Boundary direction, normal-depth limitations, and boundary sensitivity are supported by [SCI-022](../reference/bibliography.md#sci-022-hec-ras-flow-regime-boundary-guidance), [SCI-024](../reference/bibliography.md#sci-024-hec-ras-downstream-boundary-conditions), and [SCI-026](../reference/bibliography.md#sci-026-hec-ras-2d-external-boundary-conditions).
- **Selected methodology and Open question:** Project boundary records and their authority limits are mapped under [SDR-002](../reference/bibliography.md#sdr-002-boundary-condition-decisions) and [SDR-007](../reference/bibliography.md#sdr-007-lake-and-coastal-boundary-decisions).
- **Current implementation:** Current boundary classes and ND or KWSE construction are mapped under [JOB-003](../reference/bibliography.md#job-003-current-implementation-locations).
- **Supporting reference:** *Open-Channel Hydraulics* remains supporting reading under SCI-001, but it was not directly inspected and no chapter or page citation is asserted.
