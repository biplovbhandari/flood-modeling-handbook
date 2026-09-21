# Glossary

These definitions establish the handbook's stable terminology.
Where two uses of a term can imply different physical or software behavior, the distinction is stated rather than hidden.

## Source mapping

The definitions below synthesize the source families listed in the [Bibliography and Source Map](bibliography.md).
The table supplies a compact attribution map, and applied-example qualifications remain linked from the relevant definition or chapter.

| Terminology family | Primary source records |
| --- | --- |
| Watersheds, networks, runoff, hydrographs, routing, frequency, and forcing | [SCI-003](bibliography.md#sci-003-watersheds-and-drainage-basins) through [SCI-014](bibliography.md#sci-014-nonstationary-flood-frequency-analysis) |
| Conservation, energy, momentum, Froude number, Manning flow, and boundaries | [SCI-016](bibliography.md#sci-016-hec-ras-continuity-equation) through [SCI-029](bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance) |
| Two-dimensional solvers, wetting, forcing, and numerical behavior | [SCI-027](bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics) and [SCI-031](bibliography.md#sci-031-lisflood-fp-local-inertial-formulation) through [SCI-034](bibliography.md#sci-034-sfincs-forcing-documentation) |
| Raster geometry, terrain, topobathymetry, land cover, structures, and resampling | [SCI-030](bibliography.md#sci-030-gdal-geotransform) and [SCI-035](bibliography.md#sci-035-usgs-3dep-one-third-arc-second-dem) through [SCI-042](bibliography.md#sci-042-rasterio-reprojection-and-resampling) |
| Verification, validation, calibration, uncertainty, and intended use | [SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) |
| Scientific software, evidence, uncertainty, and acceptance | [SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance), with generic conflicts preserved in [Conflicts and Open Questions](conflicts-and-open-questions.md) |

## Hydrology and network terms

### Annual exceedance probability (AEP)

The probability that a specified magnitude will be equaled or exceeded in any one year under the probability model and assumptions being used.
AEP is dimensionless and is often expressed as a fraction or percentage.

### Recurrence interval

The reciprocal of annual exceedance probability under the usual stationary annual-maximum interpretation, written as \(T = 1/p\).
A recurrence interval is an average frequency description, not a schedule or guarantee.

### Catchment

The land area that drains water toward a specified outlet or network element.
A hydrologic catchment is not necessarily identical to a hydraulic model domain.

### Drainage divide

The boundary separating neighboring drainage areas.
Water on opposite sides of a divide drains toward different outlets under the represented drainage model.

### Drainage area

The horizontal area enclosed by a drainage divide for a specified outlet.
The outlet and the source dataset or delineation method are part of the definition.

### Flow direction

The represented upstream-to-downstream direction along a reach or drainage path.
Flow direction is a topological relationship and must not be inferred only from how a line appears on a map.

### Headwater

A reach or stream at the upstream edge of the represented network with no upstream reach in that network.
Whether a reach is a headwater depends on network extent and resolution.

### Confluence

A location where two or more upstream streams or reaches join one downstream stream or reach.

### Tributary

A stream or reach that enters another stream or selected mainstem.

### Mainstem

The principal path through a drainage network under a stated naming or selection rule.
An applied method can select a mainstem by drainage area, stream order, name, or another documented rule, but the selected rule does not redefine the general term.

### Strahler stream order

A branching-network index in which headwater reaches are order 1, equal-order joins increase the downstream order by one, and unequal-order joins retain the larger order.
The index depends on the represented network and does not directly measure discharge.

### Reach

A directed segment of the drainage network used as a unit of topology, forcing, modeling, or artifact organization.
The precise geometry and segmentation come from the hydrofabric and any approved network preprocessing.

### Hydrofabric

A geospatial representation of hydrologic features and their connectivity, commonly including reaches, catchments, identifiers, and upstream-downstream relationships.

### Water balance

An accounting of water inputs, outputs, and storage change for a defined control volume and time interval.
Every included flux, store, sign convention, unit, and boundary must be stated.

### Infiltration

The movement of water from the land surface into soil or rock.

### Evapotranspiration

The combined transfer of water to the atmosphere through evaporation and plant transpiration.

### Baseflow

The relatively sustained component of streamflow supplied by delayed catchment storage, often including groundwater discharge.
Its separation from event runoff depends on the selected method and assumptions.

### Event runoff

The portion of streamflow response associated with a specified precipitation or melt event and analysis window.
The term can include rapid and delayed pathways depending on the selected separation method.

### Hydrograph

A graph or time series of a hydrologic quantity at a stated location.
In these chapters, discharge hydrograph means discharge plotted against time.

### Rising limb

The portion of a hydrograph during which discharge increases toward a local peak.

### Peak discharge

The maximum discharge within a stated event window or other defined interval.
The time, location, sampling interval, and units are part of the value's meaning.

### Recession limb

The portion of a hydrograph during which discharge decreases as direct inputs weaken and catchment or channel storage drains.

### Hydrograph volume

The time integral of discharge over a stated interval.
Its SI unit is cubic metres when discharge is in m3/s and time is in seconds.

### Hydrologic routing

The prediction or calculation of changes in a hydrograph's timing, shape, and magnitude as the flow response moves through a reach, reservoir, or network.

### Travel time

The elapsed time associated with movement of a flow feature or water response between stated locations under a stated definition.
Peak-to-peak lag is not automatically the travel time of an individual water parcel.

### Attenuation

The reduction and spreading of a hydrograph feature, commonly its peak, as routing and storage redistribute flow over time.
A lower peak does not by itself prove a loss of event volume.

### Lateral inflow

Water entering a routed reach between its upstream and downstream endpoints from tributaries, local runoff, groundwater, drainage systems, or other represented sources.

## Hydraulic quantities

### Control volume

A defined region in space used to account for water storage, boundary fluxes, sources, sinks, momentum, or energy.
Its boundary and sign convention must be stated before applying a conservation balance.

### Hydraulic area

The area of a channel cross-section occupied by water at a stated time and water level, also called wetted cross-sectional area in this handbook.
The SI unit is square metres.

### Water-surface top width

The width of the water surface across a section, denoted \(T_w\) in this handbook.
The SI unit is metres.

### Wetted perimeter

The length of the solid cross-section boundary in contact with water.
The free water surface is not included, and the SI unit is metres.

### Hydraulic radius

Hydraulic area divided by wetted perimeter, \(R_h=A/P\).
It measures area relative to the resisting boundary and is not generally a geometric radius.

### Conveyance

The geometry-and-resistance term \(K=(1/n)AR_h^{2/3}\) in the SI Manning relation \(Q=KS_f^{1/2}\).
Its value normally changes with water level because area and hydraulic radius change.

### Discharge

The volumetric flow rate through a section or boundary.
The SI unit is cubic metres per second, written as m3/s.

### Area-averaged velocity

The signed velocity normal to a cross-section averaged over its wetted area, defined as \(\bar{V}=A^{-1}\int_A u_n\,dA\).
It reproduces total discharge through \(Q=A\bar{V}\) but does not preserve the local velocity distribution.

### Stage

Water level measured relative to a stated reference or datum.
Stage is incomplete without its reference elevation or datum.

### Stage-discharge rating curve

A site-specific relationship used to estimate discharge from stage, or stage from discharge when that direction is scientifically supported, under stated channel and control conditions.
The relationship can change when geometry, roughness, vegetation, debris, ice, or downstream control changes.

### Water-surface elevation (WSE)

The elevation of the water surface relative to a stated vertical datum.
The SI unit is metres.

### Depth

The vertical distance from the terrain or bed elevation to the water surface at a location.
Depth is computed as \(h = WSE - z_b\) when both elevations use the same vertical datum.
A depth value retains units, sign convention, location, and surface definition, but it does not itself carry a vertical datum.

### Velocity

The rate and direction of water motion.
The SI unit is metres per second.

### Depth-averaged velocity

The horizontal velocity averaged through the represented water depth.
It does not resolve vertical shear, vertical circulation, or a vertical velocity profile.

### Hydraulic depth

Wetted cross-sectional area divided by water-surface top width, \(D_h=A/T_w\).
Hydraulic depth is used in the general-section Froude number and equals geometric water depth only for a rectangular section.

### Velocity head

The kinetic-energy contribution expressed as a length, \(\alpha\bar{V}^2/(2g)\), where \(\alpha\) accounts for a nonuniform velocity distribution.
The SI unit is metres.

### Energy head

The sum of bed elevation, hydrostatic depth, and corrected velocity head for the stated open-channel section and datum.
Energy-head loss represents mechanical energy dissipated between sections by the processes included in the selected method.

### Kinetic-energy correction coefficient (\(\alpha\))

A dimensionless factor that corrects energy flux for a nonuniform cross-section velocity distribution.
It weights local normal velocity with the third power and equals 1 for a perfectly uniform unidirectional distribution.

### Momentum correction coefficient (\(\beta\))

A dimensionless factor that corrects momentum flux for a nonuniform cross-section velocity distribution.
It weights local normal velocity with the second power and equals 1 for a perfectly uniform unidirectional distribution.

### Momentum flux

The rate at which streamwise momentum crosses a control surface.
For a one-dimensional section it has the form \(\rho\beta Q\bar{V}\) and the unit N.

### Water-surface gradient

The change in WSE per unit horizontal or streamwise distance under a stated positive direction.
It is dimensionless when both elevation and distance use the same length unit, and its sign determines the direction of the gravity-pressure contribution in a momentum balance.

### Friction slope

The dimensionless rate of mechanical energy-head loss with distance used to represent resistance to flow.
Its sign convention or positive-flow magnitude must be stated when it appears in a momentum equation.

### Froude number

The dimensionless ratio \(Fr=|\bar{V}|/\sqrt{gD_h}\) that compares section-averaged flow speed with shallow-water wave celerity under the stated assumptions.
A section-wide value is a diagnostic abstraction and does not establish one regime for every subsection or two-dimensional cell.

### Subcritical flow

Open-channel flow with \(Fr<1\) in the stated section-scale approximation.
One shallow-water disturbance direction can propagate upstream, so downstream hydraulic conditions can influence upstream water levels.

### Critical flow

The idealized transition condition \(Fr=1\) for the stated section-scale approximation.
Values near one require care because geometry, losses, nonuniform velocity, and numerical representation can alter the diagnosed regime.

### Supercritical flow

Open-channel flow with \(Fr>1\) in the stated section-scale approximation.
Both idealized shallow-water disturbance directions travel downstream relative to the ground when the mean flow is positive downstream.

### Manning's n

An empirical roughness coefficient used in Manning's equation to represent resistance to flow.
In SI form its unit is \(\mathrm{s}/\mathrm{m}^{1/3}\), although it is often treated informally as a tabulated coefficient.
It is not a universal material constant because effective resistance can vary with vegetation, irregularity, obstructions, stage, discharge, season, grid scale, and model representation.
Calibration can also make Manning's n compensate for error in terrain, structures, boundaries, or other inputs.

### Uniform flow

An idealized open-channel condition in which depth, hydraulic area, and mean velocity do not change along the channel.
For steady uniform flow in a prismatic channel, the bed, water surface, and energy grade line are parallel under the stated slope convention.

### Normal depth

The depth associated with steady, uniform open-channel flow for a specified discharge, geometry, roughness, and energy slope.
Normal depth is a hydraulic state, not a synonym for unrestricted freefall.

### Backwater

An upstream change in water-surface elevation caused by a downstream control, reduced conveyance, confluence, structure, or other condition that influences the flow profile.

### Gradually varied flow

Steady open-channel flow whose depth changes along the channel over a distance long enough that hydrostatic and one-dimensional profile reasoning remains useful.
It is nonuniform flow and is distinct from an abrupt hydraulic jump or structure transition.

### Control section

A location where geometry, a structure, critical flow, known stage, or another hydraulic relation strongly constrains the local stage-discharge state and organizes the neighboring profile.
The controlling mechanism must be supported by evidence for the location and flow range.

### Upstream influence

The ability of a downstream disturbance or imposed condition to alter hydraulic state at locations upstream.
Idealized subcritical flow permits this influence, but its distance and magnitude depend on the complete hydraulic setting.

### Downstream influence

The effect of an upstream disturbance, forcing, or control on hydraulic state at locations downstream.
It can occur in both subcritical and supercritical flow even though the information directions differ.

### Free outflow

An incomplete general label for a boundary intended to let water leave a model.
The label must be replaced by the actual mathematical or solver behavior, such as normal depth, critical depth, rating relation, or another defined condition.

## Model conditions and geometry

### Boundary condition

A prescribed relationship or value at the edge of a modeled domain, such as inflow discharge, fixed WSE, transferred WSE, or a slope-based outflow condition.

### Inflow line

A line geometry used to select one or more grid locations where discharge enters a hydraulic model.
Geometric presence inside a domain does not prove that the line intersects active, connected conveyance or that discharge is distributed appropriately.

### Initial condition

The model state at the start of a simulation, including the initial distribution of water depth or WSE.

### Discharge-only scenario

A hydraulic scenario in which an upstream discharge is paired with a stated downstream condition, such as a normal-depth outflow, without a transferred water-surface field from a downstream scenario.
The label describes the scenario family and does not imply uniform flow throughout the domain.

### Downstream-stage-aware scenario

A hydraulic scenario in which an upstream model uses water-surface information derived from one or more compatible downstream scenarios.
The record must distinguish the nominal stage used for planning from the spatial values applied at the transfer boundary.

### Stage-transfer geometry

A line, point set, or other interface geometry used to transfer water-surface information from a downstream simulation to an upstream simulation.
The geometry, selected cells, sampling rule, wet-support rule, and datum compatibility are part of the transfer contract.

### Domain

The spatial extent over which the hydraulic model grid and boundary conditions are defined.
A domain is a model construct and is not interchangeable with a catchment or reach divide.

### Computed domain

A domain whose extent is derived from supplied geometries, buffers, and grid-snapping rules rather than supplied directly as the final bounding box.
The realized extent, grid, and construction inputs must be recorded.

### Authored domain

A final domain extent supplied as an explicit modeling instruction.
An authored domain should be validated for grid alignment, reference compatibility, required geometry containment, and hydraulic adequacy without being silently changed.

### Domain realization identifier

An identifier for one realized domain extent, grid, and active-mask configuration.
It can support comparison or addressing but does not by itself prove content integrity or domain adequacy.

### Centerline buffer

A polygon formed by expanding a reach centerline laterally by a stated distance.
A computed-domain method can use the buffer to influence rectangular bounds, but the buffer is not itself a floodplain observation or a hydraulic boundary.

### Edge cell

A grid cell on the external perimeter of the numerical domain.
An edge cell needs an intended role because it can be closed, carry a boundary condition, or reveal that hydraulically connected water has reached an artificial limit.

### Grid resolution

The horizontal cell size used to represent terrain, roughness, water state, and fluxes in a raster hydraulic model.
Smaller cells provide finer spatial representation but generally increase computational and storage cost.

### Structured grid

An ordered row-column arrangement of computational cells.
A structured grid can be rectilinear, rotated, or otherwise mapped, so array order alone does not establish map coordinates.

### Cell center

A representative location associated with one computational cell where a numerical method stores or evaluates one or more state variables.
The computational center does not have to be the exact geometric centroid.

### Cell face

The shared boundary between neighboring computational cells or a segment of the external domain boundary.
Normal flux across a face transfers water and momentum between cells or through a configured boundary.

### Active cell

A cell included in the numerical hydraulic domain and eligible to participate in the configured calculation.
An active cell can be wet or dry.

### Inactive cell

A cell excluded from ordinary hydraulic updates by the domain mask or solver representation.
An inactive cell is not the same as a dry active cell.

### Hydraulic connectivity

The represented ability of water to move between locations through open cell faces, channels, structures, or boundary connections under the current state.
Map adjacency or a crossing centerline does not by itself prove hydraulic connectivity.

### Grid anchor

A snapped reference point used to align a domain, express offsets, or compare model realizations.
If the anchor is derived from a centroid, snapping can move it away from the exact geometric centroid, so the record should preserve both the source point and the snapping rule when that distinction matters.

## Terrain and resistance terms

### Digital elevation model (DEM)

A raster or other gridded representation of elevation relative to a stated vertical datum and in stated units.
The term alone does not identify source resolution, vertical accuracy, surface treatment, bathymetry, or hydraulic connectivity.

### Bare-earth DEM

A DEM intended to represent the ground surface after vegetation and specified above-ground objects have been removed.
Bare-earth processing does not guarantee a surveyed submerged channel bed or complete representation of bridges, culverts, levees, walls, and drainage connections.

### Hydroflattening

A cartographic DEM treatment that makes qualifying waterbody surfaces level or monotonically sloping according to the applicable specification.
Hydroflattening does not recover below-water bathymetry and is not equivalent to hydraulic drainage enforcement.

### Drainage enforcement

A terrain or structure treatment intended to preserve a drainage path through a represented obstruction.
Burning, breaching, inserting a culvert relation, and explicit structure modeling are different methods with different assumptions about capacity, blockage, loss, and overtopping.

### Topobathymetry

An elevation surface that combines land topography and submerged bathymetry across a shoreline or channel margin.
A valid merge requires compatible horizontal and vertical references, units, times, uncertainty, and overlap treatment.

### Land use and land cover (LULC)

A categorical representation of predominant surface use or cover under a defined classification system, mapping period, resolution, and accuracy assessment.
A LULC class is not a direct measurement of hydraulic roughness.

### Resampling

The assignment or aggregation of source raster values onto a different grid.
The resampling rule can preserve, smooth, displace, or erase hydraulically controlling terrain and categorical boundaries.

### Prepared reach network

A versioned directed network produced from a source hydrofabric after authorized trimming, merging, tagging, or other preprocessing.
Its contract should preserve source-to-prepared identifier lineage, direction, adjacency, selection rules, and removal reasons.

## Numerical and diagnostic terms

### Conservative variables

State variables whose cell-integrated conserved quantities are updated through cell-face fluxes and source terms.
For the basic constant-density shallow-water equations after division by density, a common vector is \([h,hu,hv]^T\).
The variable \(h\) is water storage per unit plan area, while \(hu\) and \(hv\) are depth-integrated horizontal momentum components per unit density and per unit plan area.
The momentum components have units m2/s, which are also the dimensions of unit-width volume flux in their respective horizontal directions.

### Wetting front

The moving boundary between represented wet and dry areas.
Its motion depends on fluxes, terrain connectivity, grid representation, time stepping, and solver-specific wetting logic.

### Wetting and drying threshold

A solver-specific numerical depth or related state criterion used to activate or deactivate wet-cell calculations near zero water depth.
No one threshold is a universal physical constant.

### Thin water

A small positive represented depth near the wet-dry transition.
It can describe physical sheet flow, residual storage, an initial layer, or a numerical treatment, so its meaning requires solver and terrain context.

### Gravity-wave speed

The hydrostatic shallow-water long-wave speed relative to the water, \(c=\sqrt{gh}\), for local depth \(h\).
It supplies a characteristic speed for CFL reasoning under the stated shallow-water assumptions.

### Courant number

A dimensionless comparison between distance traveled by a represented signal during one numerical time step and a characteristic grid distance.
Its exact definition and acceptable range depend on the numerical method.

### Adaptive time step

An internal numerical time step that changes in response to the current state and configured solver limits.
Adaptive stepping can enforce a numerical criterion without proving physical correctness.

### Numerical stability

The property that computational errors remain controlled rather than growing without bound under the tested method, grid, state, and settings.
Numerical stability does not establish numerical accuracy or physical adequacy.

### Numerical accuracy

The closeness of a discrete numerical solution to the solution of the selected mathematical model for the quantities and tolerance being assessed.
It requires stated space-time and diagnostic evidence and does not validate the selected physics or inputs.

### Software verification

Assessment of whether implemented software satisfies its specified computational behavior.
Software verification can include unit, integration, regression, failure-path, conservation, analytical-solution, or manufactured-solution tests, but it does not establish that the selected model represents a real river adequately.

### Numerical verification

Assessment of whether a discrete numerical result adequately represents the selected mathematical model for stated quantities and tolerances.
It can include grid and time-step studies, numerical error estimates, convergence, conservation, and comparisons with known mathematical solutions.

### Scientific validation

Assessment of how well a model represents relevant real-world behavior for a stated intended use.
Validation is bounded by the locations, conditions, quantities, referents, uncertainty, and use that were assessed.

### Calibration

Adjustment of numerical or modeling parameters within physically defensible bounds to improve agreement with a referent.
Calibration data influence parameter selection and therefore do not independently validate performance on other data or conditions.

### Benchmark comparison

Comparison of stated model quantities with a defined referent under controlled and documented conditions.
A benchmark can be an observation, analytical result, accepted test case, or another model, and its scope and uncertainty limit the conclusion.

### Plausibility review

Qualitative or quantitative inspection for physical coherence and obvious contradiction.
Plausibility supports triage but does not independently establish accuracy, provenance, numerical verification, uncertainty, or validation.

### Acceptance criteria

Recorded qualitative or quantitative conditions used by an authorized reviewer to judge fitness for a stated intended use.
Criteria should define the quantity, support, range, uncertainty treatment, threshold or rule, authority, and response to failure.

### Operational monitoring

Repeated observation of accepted production behavior for input drift, source changes, identity and materialization failures, unusual run outcomes, and reassessment triggers.
Monitoring remains bounded by the accepted use and does not replace missing verification or validation.

### Post-run diagnosis

Investigation of a realized result by comparing competing explanations and selecting observations that distinguish them.
Diagnosis can identify a bounded cause or evidence gap without granting scientific acceptance.

### Physical correctness

The adequacy of the equations, inputs, parameters, boundaries, structures, and resulting behavior for the stated real-world use.
It requires physical evidence and uncertainty bounds rather than solver completion alone.

### Quasi-steady

A state in which the modeled quantities relevant to a stated criterion change slowly enough for the intended use, even though the solver is time-dependent and exact steady state has not been proven.
The criterion, interval, and tolerance must be stated.

### Residual transient behavior

The time variation that remains in modeled depth, WSE, velocity, flux, extent, storage, or another stated quantity after the largest initial adjustments have passed.
Its acceptability depends on the quantity, spatial support, interval, tolerance, and intended use.

### Mass balance

An accounting of all inflows, outflows, storage changes, and modeled sources or sinks over a defined control volume and time interval.
A complete mass-balance assessment requires more than storage change alone.

### Volume convergence

A dimensionless comparison between modeled storage change over an interval and a stated reference volume, such as inflow volume over that interval.
It can support a quasi-steady diagnostic or stopping rule, but it does not by itself close the full mass balance.

### Solver exit status

Evidence about how an executable process ended, such as normal completion, failure, timeout, or requested termination.
Exit status does not establish numerical stability, hydraulic convergence, artifact completeness, or physical adequacy.
The evidence record should preserve the raw outcome and any requested or escalated termination when those details affect diagnosis.

### Maximum wall-time termination

A termination condition reached when elapsed wall time exceeds a configured limit while execution is still active.
It is distinct from a simulated-time limit, convergence termination, boundary-triggered termination, and solver-declared failure or success.
The record should preserve whether a graceful stop succeeded or required escalation.

### Hydraulic adequacy

The degree to which the equations, terrain, roughness, boundaries, forcing, initial state, numerical behavior, sensitivities, outputs, and validation evidence support a stated hydraulic use.
Hydraulic adequacy is use-specific and is not established by solver completion or one convergence metric.

### Uncertainty

A lack of complete knowledge about an input, parameter, method, numerical result, artifact, operation, or real-world referent that limits a stated decision.
An uncertainty statement should name its source, scope, representation, interactions, and evidence limits rather than use one undifferentiated error bar.

### Sensitivity analysis

A controlled assessment of how a stated output or decision metric changes when a factor, method, or interaction changes over a defensible range or set of alternatives.
Sensitivity does not by itself define how probable an alternative is, identify which alternative is more accurate, or establish acceptance.

### Structural-model uncertainty

Uncertainty about whether the selected equations, represented processes, dimensional assumptions, and omitted processes are adequate for the intended use.
Changing a parameter within one model structure does not test structural-model uncertainty completely.

### Operational uncertainty

Uncertainty introduced by scheduling, retries, resource limits, source drift, partial failure, publication, observation, monitoring, or other execution-system behavior.
Operational uncertainty can invalidate artifact interpretation even when the hydraulic calculation itself is unchanged.

### Hot start

Initialization of a simulation from a prior modeled state rather than from a dry or generic initial condition.
The source scenario and compatibility assumptions are part of the run provenance.

## Scenario, artifact, and identity terms

### Scenario

One hydraulic simulation defined by a model realization, forcing, boundary conditions, initial condition, solver environment, and run settings.

### Library

A selected collection of scenarios intended to represent a reach's hydraulic response over a bounded range of discharge and, where applicable, downstream stage.

### Adaptive discharge selection

A method that chooses a later discharge from hydraulic responses measured at completed, scientifically accepted scenarios.
The handbook's applied example refines adjacent intervals on a fixed candidate grid using maximum depth and flooded area.

### Scenario point

One completed scenario represented by its discharge, complete identity, acceptance state, and response metrics in an adaptive selector.
A failed or scientifically rejected point can remain in an execution inventory without dividing an interval or becoming a selected member.

### Candidate discharge grid

A finite, explicitly authored set of discharge values from which an adaptive method can select scenarios.
The grid, endpoints, units, rounding, candidate ordering, and tie rules belong to the method record.

### Published trial

A scenario whose producer has exposed its record and artifacts at an intended generation or address.
Publication does not establish materialization, scientific acceptance, or selected library membership.

### Selected library member

A scenario the selection method chooses to represent the library, distinct from every simulated or published trial.
Selected membership should be explicit, durable, and traceable to the method and evidence that produced it.

### Model identity

The stable identity of the scientific, data, and method inputs that define a model realization.
It should cover the prepared network, source content, terrain, roughness, references, grid, domain, boundary geometry, structures, and method or producer versions needed for reproduction.

### Model identifier

A readable label or digest derived from model identity for indexing or addressing.
It does not independently prove artifact presence, source immutability, storage materialization, or scientific adequacy.

### Model reuse

Use of an existing model realization instead of producing a new one after complete identity, required artifacts, integrity metadata, compatibility, and applicable acceptance state have been checked.
A present address or schema-valid record alone is insufficient reuse evidence.

### Run identity

The stable identity of the output-affecting model, forcing, boundary, initial-state, numerical, and producer settings used for one simulation.
Run identity supports provenance and comparison but does not by itself prove execution, materialization, scientific compatibility, or adequacy.

### Manifest

A structured record that names inputs, identities, settings, artifacts, metrics, warnings, and provenance for a model or scenario.
Its schema and producing operation define the exact contract.

### Composite flood inundation map (composite FIM)

A flood inundation product assembled from multiple reach or scenario results according to a stated compositing method.
Its record should preserve selected source membership, compatibility evidence, transformation and overlap rules, identity, provenance, diagnostics, and acceptance state.

### Nominal downstream stage

The scalar WSE target used to plan, index, or describe a downstream-stage-aware scenario.
It does not replace the spatial water-surface values derived from the bound downstream source scenario or bracketing sources.

### Nominal stage grid

A discrete set of downstream-stage targets used to plan downstream-stage-aware scenarios.
The datum, units, bounds, increments or explicit members, binding rule, and rounding policy belong to the planning record.

### Planned scenario

One intended discharge-stage target together with its bound downstream source scenario and hot-start source.
A planned scenario is planning evidence and does not become materialized merely because execution was requested or an address was returned.

### Transfer point

One grid location where a transferred downstream condition is written into an upstream solver boundary.
A defensible rule checks terrain-elevation datum compatibility, depth units and surface definition, valid nodata masks, strictly positive wet support when required, and coordinate mapping before emitting a value.

### Publication

The state in which one or more producer outputs have become externally observable at their intended addresses.
A failed write attempt that makes no intended output externally observable is not publication.
Partial publication exposes some but not all required outputs.
Complete publication exposes all outputs required by the producer write contract, but it does not prove integrity, one-generation consistency, compatibility, materialization, selected membership, validity, or acceptance.

### Materialization

An observer conclusion that the object required by current intent exists at the expected address and satisfies the applicable manifest, asset, identity, realization, and completeness checks.
Materialization is distinct from job success, publication, storage presence, and scientific acceptance.

### Reconciliation

Repeated comparison of desired state with observed materialized state followed by bounded action to close a detected gap.
Reconciliation observes storage rather than treating a synchronous operation response as completion evidence.

### Asset provenance

The trace from an artifact to its source inputs, producer, identity, realization, transformations, retrieval or creation time, address, and integrity metadata.
An asset href alone is not complete provenance.

### Immutable generation

A scientifically distinct object version that remains available without in-place mutation after publication.
Generation-specific addressing or an atomic pointer to immutable content can preserve prior complete generations for recovery and comparison.

### Scientific software contract

A recorded agreement that connects scientific meaning and assumptions to typed inputs, validation rules, identities, artifacts, operations, and intended-use acceptance.
It is broader than a schema because it also governs compatibility, provenance, publication, materialization, reuse, migration, and rollback.

### Scientific compatibility

The supported claim that a producer and consumer assign sufficiently equivalent meaning to quantities, references, methods, identities, and artifacts for a stated use.
Schema compatibility or parsing success does not by itself establish scientific compatibility.

### Methodology identity

The versioned set of scientific decisions and settings that defines the recipe used to produce an object.
A methodology change that invalidates reuse should create a distinct identity or another immutable generation boundary rather than silently replacing content at one address.

### Same-address collision

A condition in which two scientifically unequal requests derive the same storage address.
Exact record comparison can reject reuse while still allowing a later operation to replace or mix artifacts at that shared address.

### Atomic publication

Publication in which consumers observe either the complete prior generation or the complete new generation, but not a partial mixture.
This can use atomic promotion of a staged generation or an atomic pointer update to immutable content.

### Idempotent retry

A repeated equivalent request that produces or returns the same externally visible scientific generation without duplicate, mixed, or overwritten output.
Idempotency requires stable request identity and recovery from partial publication, not only a manifest-exists check.

### Readiness verdict

An authorized conclusion about a named change and intended use under stated revisions, evidence, restrictions, rollout stage, and unresolved risk.
The methodology-change verdicts are `READY`, `READY WITH CONDITIONS`, and `NOT READY`.
They do not replace the diagnostic verdict vocabulary used by the validation framework.

## Boundary terminology discipline

### Free overfall

A physical condition in which flow loses downstream support near a drop or control.
It is not a synonym for a slope-based normal-depth outflow.

### Normal-depth outflow

A downstream boundary that uses a stated slope and a normal-depth relation under its solver-specific formulation.
The slope, direction, cells, units, and solver semantics must be recorded.

### Specified-stage boundary

A boundary that prescribes water-surface elevation or a stage time series relative to a stated reference.
It can exchange water with the domain according to the modeled solution and boundary formulation.

### Transferred-stage boundary

A boundary whose spatial water-surface values are derived from one or more compatible source scenarios.
The source identities, transfer geometry, wet-support rule, interpolation, coverage, and datum compatibility belong to the boundary contract.

An interface label never proves its physical or numerical behavior.
See [CQ-001](conflicts-and-open-questions.md#cq-001-terminology-behavior-mismatch).
