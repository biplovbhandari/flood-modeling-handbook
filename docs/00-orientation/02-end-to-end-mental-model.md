# End-to-End Mental Model

A flood-mapping workflow converts hydrologic conditions into hydraulic responses and then assembles selected responses into flood inundation products.
Each transition changes scientific meaning, data representation, responsibility, or more than one of these.
Keeping the transitions visible prevents a file path, completed calculation, or storage record from being mistaken for scientific evidence.

## Why this topic matters

Many difficult failures occur at boundaries rather than inside one calculation.
A valid discharge can be assigned to the wrong reach, a valid WSE can use an incompatible vertical datum, a completed scenario can fall outside the intended library, or a stored raster can be composited with an incompatible quantity.
An end-to-end mental model gives each check a defined place.

## Prerequisites

Read [What Is Flood Inundation Mapping?](01-what-is-fim.md) and [Source Authority](../reference/source-authority.md).
Later chapters define the detailed hydrology, boundaries, and scenario methods introduced here.

## Learning objectives

After this chapter, the reader should be able to:

- trace catchment conditions to a composite FIM product;
- identify where discharge becomes a hydraulic boundary condition;
- explain the separate roles of model preparation, scenario definition, response calculation, library selection, network coupling, storage, and compositing; and
- distinguish a calculation result from a verified materialized artifact.

## The scientific chain

The chain below is solver-neutral.

| Stage | Scientific meaning | Representative record or artifact | Required check |
| --- | --- | --- | --- |
| Catchment and network conditions | Precipitation, losses, storage, routing, and antecedent conditions influence flow through the drainage network. | Hydrologic observations, forcing datasets, hydrographs, frequency analysis, and network topology. | Confirm source, location, period, units, and uncertainty. |
| Hydraulic forcing | A discharge value or hydrograph is associated with a reach and a time or probability context. | Forcing record with reach identity and provenance. | Confirm that the hydrologic quantity matches the hydraulic boundary. |
| Model realization | Terrain, roughness, domain, grid or mesh, channel geometry, and boundary locations define the problem in space. | Model record and prepared geospatial artifacts. | Confirm compatible spatial and vertical references. |
| Scenario definition | Forcing, downstream condition, initial state, numerical settings, and model identity define one calculation. | Scenario record. | Confirm completeness and stable identity. |
| Hydraulic response | The method calculates water state under the scenario conditions. | WSE, depth, velocity, extent, storage, convergence evidence, and warnings. | Confirm quantity meaning and numerical adequacy. |
| Response library | Selected scenarios cover a bounded part of the response to forcing and downstream conditions. | Library index with inclusion reasons and bounds. | Confirm coverage and distinguish attempted from selected scenarios. |
| Network coupling | A downstream state can supply boundary information to an upstream model. | Dependency record and transferred boundary values. | Confirm compatible location, datum, scenario meaning, and dependency order. |
| Composite product | Compatible results are combined under a stated rule. | Composite raster or polygon with source provenance. | Confirm overlap rule, quantity compatibility, and source completeness. |

## From catchment condition to discharge

**Scientific foundation:** Catchment conditions affect how water reaches the drainage network, while routing changes the timing and magnitude of flow along the network.
The resulting discharge is a flow rate through a section or boundary, usually expressed in m3/s.

A hydraulic model does not need to reproduce every upstream hydrologic process to consume their result.
It does need a discharge whose reach, units, temporal meaning, source, and uncertainty are known.
A value representing a steady library scenario has a different time interpretation from one point on a forecast hydrograph even if the numerical value is identical.

## From prepared data to a model realization

Model preparation changes network and source-data descriptions into one spatial realization of the hydraulic problem.
Typical inputs include terrain, roughness, reach geometry, the domain, grid or mesh settings, and inflow and downstream boundary locations.

The model record should preserve input identities, transformations, warnings, and produced artifacts.
That provenance supports reproducibility, but it does not prove that the domain, terrain, roughness, or boundary placement is scientifically adequate.

**Design principle:** Treat a model realization as an input to scenarios rather than as a flood scenario by itself.
The model has no scenario-specific discharge, downstream control, or computed response until those elements are added.

## From discharge to a boundary condition

**Scientific foundation:** Discharge becomes hydraulic forcing only when it is applied at a defined boundary or source location with a sign and unit convention.
The spatial boundary connects the hydrologic quantity to the hydraulic domain.

Downstream handling is a separate boundary choice and can change the upstream hydraulic response.
One discharge therefore does not define a unique response without the model realization, downstream condition, initial condition, and numerical settings.

## Scenario definition and hydraulic response

A scenario record should identify the model realization, forcing, downstream condition, initial state, numerical settings, and intended outputs.
The hydraulic method then calculates the response under that complete definition.

Response artifacts may include WSE, depth, velocity, extent, storage, and diagnostic time series.
A summary record may also contain convergence measures, warnings, and checksums.
Neither record completeness nor numerical completion alone establishes scientific validity.

## Response libraries

A response library is a scientifically selected collection of scenarios.
It represents a bounded region of forcing and boundary-condition space for a stated intended use.

**Design principle:** Keep attempted calculations separate from selected library membership.
A calculation can finish successfully and still be redundant, outside the authored bounds, numerically unsuitable, or unsupported by the intended method.

Library documentation should state the lower and upper bounds, sampling method, inclusion criteria, gaps, and uncertainty.
It should also preserve enough identity to distinguish models, scenarios, and artifacts without relying on filenames alone.

## Network coupling

Connected reaches can exchange more than discharge information.
A modeled downstream water level can influence an upstream reach through a downstream boundary.
The transferred values must preserve location, units, vertical datum, source-scenario identity, and the dependency relation.

**Design principle:** Scenario-dependency propagation is not hydrologic routing.
Hydrologic routing changes a time-varying flow response through a river network.
Scenario-dependency propagation carries selected boundary information between hydraulic calculations.

## Library, storage, and materialization

A library is a selected scientific collection.
Storage is where records and artifacts persist.
Materialization is the verified presence of the intended artifact at its identity-derived location.

These concepts overlap operationally but are not synonyms.
A returned path is a pointer to inspect, not proof that the artifact exists, is complete, belongs to the intended scenario, or makes the library scientifically adequate.

**Design principle:** Verify important artifacts from observed storage and content rather than from a task return value alone.

## From reach libraries to a composite product

**Scientific foundation:** Compositing combines compatible mapped quantities under a stated overlap rule.
Source scenarios must use compatible units, spatial support, horizontal references, vertical references when elevations are combined, and scenario meaning.

A pixelwise maximum is one possible overlap rule for compatible depth rasters, but it is not universally correct for every quantity or purpose.
The product record should name the rule and retain the contributing scenario identities.

## Applied trace for reach R-200

**Applied example:** This synthetic trace uses reach `R-200` and does not claim external operational behavior.

1. Catchment analysis supplies \(Q=250\ \text{m3/s}\) with source, time basis, units, reach identity, and uncertainty.
2. Model preparation creates a model record for `R-200` from terrain, roughness, domain, channel, and boundary geometry.
3. A scenario record combines that model with \(Q=250\ \text{m3/s}\), a stated downstream condition, an initial state, and numerical settings.
4. The hydraulic method calculates WSE, depth, velocity, and extent where supported.
5. Inspection verifies the response artifacts, their identities, and their diagnostic evidence.
6. Library selection decides whether this response belongs in the bounded collection for `R-200`.
7. A network plan checks whether boundary dependencies with neighboring reaches are compatible.
8. Product assembly combines compatible selected responses under a documented overlap rule.

The number 250 changes role as it moves from sourced hydrologic information to scenario input, boundary forcing, library coordinate, and product-selection key.
The hydraulic response is the calculated water state produced under the complete scenario.

## Responsibility map

| Responsibility | Scientific or information role |
| --- | --- |
| Establish the source and meaning of discharge | Hydrologic analysis and provenance. |
| Prepare terrain, roughness, domain, and boundary geometry | Model development. |
| Select discharge bounds | Scientific methodology and planning. |
| Define and calculate scenarios | Hydraulic analysis. |
| Select a response library | Sampling methodology and scientific acceptance. |
| Plan network dependencies | Hydraulic planning and dependency management. |
| Persist and verify artifacts | Storage, identity, and materialization. |
| Combine compatible results | Product methodology and geospatial processing. |

## Common failure patterns

### The correct number is attached to the wrong reach

Units can be correct while reach identity or time context is wrong.
Check the forcing source, reach identifier, and scenario provenance together.

### A path was returned, so the library is complete

A path identifies a candidate artifact.
Verify the record, assets, identities, intended bounds, selected scenario set, and storage state.

### The scenario completed, so the plan was correct

Completion answers whether the supplied scenario was processed under the implemented checks.
Planning adequacy requires separate evidence about coverage and dependencies.

### Any two depth rasters can be combined

Both rasters must share compatible spatial support, terrain meaning, units, scenario context, and compositing rules.
The same quantity name is insufficient.

## Competency check

Draw or describe eight boxes from catchment conditions through composite product.
For each arrow, state whether the main change is scientific meaning, data representation, responsibility, or a combination of those changes.
Then explain why a verified response artifact does not by itself prove that a response library is complete.

## Source notes

- **Scientific foundation:** Watershed and runoff concepts are supported by [SCI-003](../reference/bibliography.md#sci-003-watersheds-and-drainage-basins) and [SCI-006](../reference/bibliography.md#sci-006-surface-runoff-and-catchment-response).
- **Scientific foundation:** Stable terms and equations are mapped in [Glossary](../reference/glossary.md) and [Equations and Units](../reference/equations-and-units.md).
- **Design principle:** The chain deliberately separates scientific selection, calculation, storage, verification, and compositing.
- **Evidence note:** The `R-200` trace is synthetic and cannot establish external deployment, accepted methodology, or current behavior.
