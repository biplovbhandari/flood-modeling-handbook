# End-to-End Mental Model

The system converts hydrologic conditions into reusable hydraulic responses and then assembles selected responses into flood inundation products.
Each transition changes either scientific meaning, software representation, or ownership.
Keeping those transitions visible prevents a file path, job result, or scheduler state from being mistaken for scientific evidence.

## Why this topic matters

Most difficult failures occur at boundaries rather than inside one calculation.
A valid discharge can be assigned to the wrong reach, a valid WSE can use an incompatible vertical datum, a completed scenario can fall outside the intended library, or a stored raster can be composited with an incompatible quantity.
An end-to-end mental model gives each check a place and an owner.

## Prerequisites

Read [What Is Flood Inundation Mapping?](01-what-is-fim.md) and [Source Authority](../reference/source-authority.md).
The later chapters define the detailed hydrology, boundaries, and scenario methods introduced here.

## Learning objectives

After this chapter, the reader should be able to:

- trace catchment conditions to a composite FIM product;
- identify where discharge becomes a hydraulic boundary condition;
- explain the separate roles of model building, scenario execution, scenario planning, network coupling, storage, and compositing;
- map the current jobs to the larger scientific chain; and
- distinguish a job response from a verified materialized artifact.

## The scientific chain

The chain below is intentionally solver-neutral.
It establishes the meaning of each stage before later chapters introduce the project's ND and KWSE scenario families.

| Stage | Scientific meaning | Representative record or artifact | Main responsibility boundary |
| --- | --- | --- | --- |
| 1. Catchment and network conditions | Precipitation, losses, storage, routing, and antecedent conditions influence flow through the drainage network. | Hydrologic observations, forcing datasets, hydrographs, frequency analysis, and prepared network topology. | Hydrologic analysis and network preparation are outside the three current modeling jobs. |
| 2. Hydraulic forcing | A discharge value or hydrograph is selected for a reach and time or probability context. | Discharge value with units, source, reach identity, time or probability basis, and uncertainty. | The forcing source is upstream of the jobs package. |
| 3. Reach-model realization | Terrain, roughness, domain, grid, centerline, and inflow geometry define the hydraulic problem in space. | Model manifest plus terrain, roughness, domain, centerline, inflow line, and a grid-snapped domain-anchor asset stored in the `reach_centroid` schema field. | `build_model` owns current reach-model construction from prepared inputs. |
| 4. Scenario definition | Forcing, downstream condition, initial state, solver settings, and model identity define one hydraulic simulation. | Typed scenario input and boundary-condition records. | The executing job validates and realizes supplied inputs, while planning may be external. |
| 5. Hydraulic response | The solver evolves water over the domain under the defined forcing and controls. | Depth outputs, WSE time series when saved, inundation polygon, storage and convergence metrics, and warnings. | The scenario runner and hydraulic solver own execution and post-processing. |
| 6. Reach library | Selected scenarios represent a bounded portion of a reach's response to discharge and downstream conditions. | Set of scenario manifests plus a durable plan or index that explains inclusion. | ND selection partly occurs inside the ND job, while network-wide KWSE planning is outside the current jobs. |
| 7. Network coupling | A downstream modeled state supplies a boundary influence to an upstream reach. | Downstream scenario manifest, depth raster, stage-transfer line, and selected stage value. | Planning chooses compatible dependencies, and the KWSE job realizes the supplied dependency. |
| 8. Composite product | Compatible reach or scenario results are combined under an explicit overlap and quantity rule. | Composite raster or polygon plus source-scenario and method provenance. | Compositing is outside the three current modeling jobs. |

## From catchment condition to discharge

**Scientific foundation:** Catchment conditions affect how water reaches the drainage network, while routing changes the timing and magnitude of flow along the network.
The resulting discharge is a flow rate through a section or boundary, usually expressed in m3/s.

The hydraulic model does not need to reproduce every upstream hydrologic process to consume its result.
It does need a discharge whose reach, units, temporal meaning, source, and uncertainty are known.
A value representing a steady library scenario has a different time interpretation from one point on a forecast hydrograph even if the numerical value is identical.

**Current implementation:** The current jobs accept discharge in whole cubic metres per second for scenario forcing.
They do not calculate rainfall-runoff response or establish the National Water Model or frequency-analysis meaning of that discharge.

## From prepared reach data to a hydraulic model

**Current implementation:** [`build_model`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/build_model.py) consumes a prepared reach identifier and network, upstream topology inputs, terrain and land-cover sources, grid settings, and either an authored or computed domain.
It produces a model manifest and geospatial assets for terrain, roughness, centerline, inflow line, domain, and a grid-snapped domain anchor.
The current schema stores that anchor under the field named `reach_centroid`, but the exported point is the reach centroid floored to the model grid rather than the exact geometric centroid.
The field name and realized geometry therefore have different meanings and must remain visible during artifact inspection.

This stage changes the representation from network and source-data descriptions to a particular hydraulic model realization.
The model is not yet a flood scenario because it has no scenario-specific discharge, downstream condition, or solver response.

The model manifest records identity, input, property, asset, warning, and provenance fields.
The manifest supports reproducibility and traceability, but it does not prove that the domain, terrain, roughness, or inflow placement is scientifically adequate.

## From discharge to a boundary condition

**Scientific foundation:** Discharge becomes hydraulic forcing only when it is applied at a defined boundary or source location with a sign and unit convention.
The spatial boundary connects the hydrologic quantity to the hydraulic domain.

**Current implementation:** Both current scenario jobs construct a `QFIX` boundary from a scenario discharge and the model manifest's inflow-line asset.
The boundary tells the solver where the specified volume rate enters the domain.
Downstream handling is a separate boundary choice and can change the upstream hydraulic response.

The later scenario-library chapters explain the project-specific downstream boundary families.
For orientation, remember only that one discharge does not define a unique response without the downstream condition, model realization, initial condition, and solver settings.

## The two current scenario-job roles

### Discharge-response library execution

**Current implementation:** [`run_nd_scenarios`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/run_nd_scenarios.py) consumes a model manifest, a caller-provided discharge range, adaptive-step settings, run settings, `existing_scenarios`, and `q_grid_resolution`.
It runs a sequence of discharge scenarios with a slope-based downstream condition.
It can re-adopt caller-supplied existing manifests that match the reach, model identity, and run identity and whose discharge falls within the supplied range, then re-evaluate them under the current response bands without simulating them again.
It evaluates changes in maximum depth, median depth, and flooded area to select hydraulically distinct library entries within the supplied range.
The current `_propose` calculations snap later adaptive proposals to `q_grid_resolution`.
Current code does not enforce that grid for re-adopted manifests, the minimum or maximum endpoints, or the initial `min_upstream_inflow + delta_upstream_inflow` trial.
The current library can therefore contain off-grid discharges even when `q_grid_resolution` is greater than one.
Every newly simulated trial is published immediately, including a trial later classified as rejected by the adaptive selection logic.
Published trial artifacts are therefore not the same thing as selected library membership.
The small job response reports scenario comparisons and warnings rather than a complete authoritative library index.

The job does not determine the hydrologic basis of the minimum and maximum discharge bounds.
It also does not aggregate results across reaches or create a composite product.

### Downstream-stage-aware execution

**Current implementation:** [`run_kwse_scenarios`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/run_kwse_scenarios.py) consumes a caller-provided ordered scenario list.
Each list entry carries an upstream discharge, a nominal downstream-stage or stage-grid planning coordinate in `bc_value`, a downstream scenario manifest, and an optional hot-start reference.
The achieved upstream-end `RunScenarioResults.nominal_wse` is computed later during post-processing, while transferred pointwise `HFIX` values come from the downstream source raster values.
The job processes every supplied combination and applies cell-specific transferred water information along a stage-transfer line.
It simulates and publishes a scenario when no matching manifest exists, or re-adopts an existing manifest when the manifest is present and its recorded inputs exactly match.
The manifest-only reuse check does not itself verify that every asset referenced by that manifest exists.
The job returns manifest paths and warnings for both newly simulated and re-adopted scenarios.

The job does not decide which discharge-stage points belong in the library.
It does not determine the dependency order, choose hot starts, or perform composite post-processing.

## Scenario planning and network coupling

**Scientific foundation:** A reach library is useful only if its scenarios cover the forcing and downstream-control conditions needed by its intended use.
At a confluence or along a connected network, a downstream water level can influence an upstream reach.
The scenario set therefore has both local hydraulic meaning and network dependency meaning.

**Target design:** The target orchestrator detects a gap, verifies model and scenario artifacts in storage, records materialized state in the `materialized_*` tables, plans downstream-stage-aware scenarios, supplies work to the modeling jobs, and propagates changed downstream dependencies upstream.
It treats the modeling jobs as tools with typed inputs and manifests.
See the [target orchestrator design](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/orchestrator-design.md) and [system design guide](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/guide.md).

Some older target-design text uses `current_state` for this responsibility.
The current reconciliation-loop design records the narrower materialized-state claim instead, so the older term is a target-design terminology transition rather than the current table contract.

This ownership boundary matters because the executing job can faithfully run an incomplete or scientifically unsuitable plan.
Execution success answers whether the supplied scenario ran under the implemented checks.
It does not answer whether the planner chose the right bounds, combinations, or network dependencies.

## Library, storage, and materialization

A library is a scientifically selected collection of scenarios.
Storage is where the scenario records and assets persist.
Materialization is the verified presence of the intended artifact at the identity-derived location.
These concepts overlap operationally but are not synonyms.

**Current implementation:** Scenario manifests record model and run identity, scenario code, inputs, computed properties, assets, and warnings.
The public jobs publish depth, inundation polygon, and stage-transfer-line assets for a scenario.
Their accepted `save_zarr` inputs are not forwarded into `RunConfig`, and the generic true branch cannot complete manifest construction with the current file-only hash helper.

**Target design:** Orchestration observes storage and updates the applicable `materialized_*` state after a job completes.
The job's returned manifest path is therefore a pointer to inspect, not sufficient proof by itself that the full intended library exists and is scientifically complete.

## From reach libraries to a composite FIM product

**Scientific foundation:** Compositing combines compatible mapped quantities under a stated rule.
The source scenarios must use compatible units, grids or an explicit resampling method, horizontal references, vertical references when elevations are combined, and scenario meaning.

**Selected methodology:** Decision record DR-004 selects pixelwise maximum for composite pixel values with status Alternate Selected.
That selection applies within the decision's scope and does not identify a current producing job.

**Current implementation:** No current `twod-fim-jobs` entry point performs cross-reach compositing.
The final composite therefore begins after the three modeling jobs' current responsibility boundary.
See [XW-008](../reference/decision-code-artifact-crosswalk.md#xw-008-composite-fim-pixel-calculation).

## One illustrative trace

Suppose an upstream hydrologic source supplies $Q = 250$ m3/s for reach R under a stated event or library context.
The number first means a volumetric flow rate associated with a hydrologic source and reach.
A planner then selects a model and downstream condition and places 250 in a typed scenario input.
The scenario job binds 250 to the reach inflow line as a `QFIX` boundary.
The solver calculates a spatial response under the complete scenario definition.
Post-processing records the realized discharge, response metrics, and output assets in a scenario manifest.
Library logic decides whether that manifest belongs in the intended response collection.
Compositing later selects compatible scenario results and applies its documented overlap rule.

The numerical value remains 250 through this trace, but its role changes from sourced hydrologic information to job input, boundary forcing, provenance, library coordinate, and product-selection key.
The hydraulic response is not the number 250.
The response is the calculated water state produced under the complete scenario.

## Responsibility map

| Responsibility | Science, orchestration, or storage? | Current or target location |
| --- | --- | --- |
| Establish the source and meaning of discharge | Hydrologic science and data provenance | Outside the three current jobs. |
| Build terrain, roughness, domain, and boundary geometry | Scientific model preparation implemented as tooling | Current `build_model`. |
| Select discharge bounds from hydrologic evidence | Scientific methodology and planning | Caller or orchestration responsibility outside current jobs. |
| Adaptively select discharge-response entries within supplied bounds | Scientific sampling logic implemented as tooling | Current `run_nd_scenarios`. |
| Plan network-wide downstream-stage scenarios and dependency order | Scientific planning plus orchestration | Target orchestrator ownership, outside current jobs. |
| Execute supplied downstream-stage-aware scenarios | Hydraulic tooling | Current `run_kwse_scenarios`. |
| Persist scenario artifacts and manifests | Tool publishing plus storage service | Current jobs publish, while target orchestration verifies materialization. |
| Decide whether the intended library is complete | Scientific acceptance plus orchestration state | Outside a single scenario execution. |
| Combine compatible reach results into a composite | Product science and processing | Outside current jobs. |

## Common failure patterns

### The correct number is attached to the wrong reach

Units can be correct while reach identity or time context is wrong.
Check the forcing source, reach identifier, and scenario provenance together.

### The job returned a path, so the library is complete

A path identifies a candidate artifact.
Verify the manifest, assets, identities, intended bounds, scenario set, and storage state.

### The scenario executed, so the plan was correct

Execution validates only the constraints implemented by the job.
Planning adequacy requires separate evidence about coverage and network dependencies.

### A depth raster can always be combined with another depth raster

Both rasters must also share compatible spatial support, terrain meaning, units, scenario context, and compositing rules.
The same filename or quantity name is insufficient.

## Competency check

Draw or describe eight boxes from catchment conditions through composite product.
For each arrow, state whether the main change is scientific meaning, software representation, ownership, or a combination of those changes.
Then identify which boxes the three current modeling jobs do not own.

## Source notes

- **Current implementation:** The job and manifest claims come from local checkout paths recorded under JOB-002 and JOB-003 in [Bibliography and Source Map](../reference/bibliography.md).
- **Target design:** Orchestration ownership comes from SYS-001 and the linked target design files.
- **Selected methodology:** The composite rule is mapped in [XW-008](../reference/decision-code-artifact-crosswalk.md#xw-008-composite-fim-pixel-calculation).
- **Open question:** A durable current implementation for all work outside the three modeling jobs is not established by this chapter.
