# Normal-Depth Libraries

A normal-depth library represents one reach with a bounded set of steady upstream discharges and a slope-based downstream boundary.
The current job builds that set by running LISFLOOD-FP scenarios, recording final-state metrics and artifacts, and using an adaptive algorithm to decide which simulated discharges count as selected library entries.

## Why this topic matters

An ND scenario is more than a solver call.
Its scientific meaning depends on the model realization, discharge, downstream slope and boundary geometry, initial depth state, run settings, termination evidence, final artifacts, and selection decision.

A scenario can complete and publish successfully without proving that the downstream boundary is physically adequate, the domain contains the relevant floodplain, the result is independent of its hot start, or the scenario belongs in the selected library.
Those boundaries matter when ND results later support KWSE planning and stage transfer.

## Prerequisites

Read [Flood Frequency, AEP, and Bounds](../01-hydrology-for-fim/04-flood-frequency-aep-and-bounds.md), [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md), [Backwater and Boundary Control](../02-open-channel-flow/04-backwater-and-boundary-control.md), [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md), [Domain and Boundary Geometry](../04-model-development/04-domain-and-boundary-geometry.md), and [The Build-Model Job](../04-model-development/05-build-model-job.md).
Use [Source Authority](../reference/source-authority.md) to keep selected methodology, current implementation, target design, and open questions separate.

## Learning objectives

After this chapter, the reader should be able to:

- trace the current ND workflow from a model-manifest path to published scenario artifacts and the job response;
- explain how current code constructs upstream and downstream boundary conditions;
- distinguish solver termination, convergence evidence, edge evidence, publication, materialization, and selected library membership;
- identify the exact provenance and limitation of an ND hot start;
- interpret the final depth, inundation, STL, metrics, manifest, warnings, and response fields; and
- state which ND claims require evidence beyond current outputs.

## Authority boundary

**Selected methodology:** DR-029 ALT-A selects lower and upper discharge bounds based on National Water Model retrospective flow, and DR-030 ALT-C selects adaptive discharge stepping based on hydraulic response.
Both records have Alternate Selected status in the handbook-reviewed Decision Register.

**Current implementation:** The current `run_nd_scenarios` job consumes caller-supplied bounds and does not derive them from National Water Model data.
The current job implements a particular adaptive algorithm whose metrics, defaults, reuse behavior, q-grid behavior, and publication semantics do not exactly match every statement in DR-030 or the generated job documentation.

**Target design:** The system-design guide treats `q_*_bound` and response-density settings as authored intent, while the adaptive ND `q_set` is emergent and must be observed in storage and checked against intent.
This target design does not establish that a deployed controller currently performs that observation or membership check.

**Open question:** The reviewed Decision Register does not define the scientific meaning of DR-029's $Q_{HFT}$, and it contains no DR-041 row even though current code cites DR-041 for the discharge grid.
See [CONF-009](../reference/conflicts-and-open-questions.md#conf-009-scenario-publication-membership-reuse-and-discharge-grid-authority), [CONF-015](../reference/conflicts-and-open-questions.md#conf-015-adaptive-nd-criteria-and-generated-documentation), and [CONF-020](../reference/conflicts-and-open-questions.md#conf-020-scenario-request-boundary-validation).

## What one ND scenario means

The current job pairs one whole-number upstream discharge with a slope-based `FREE` boundary.
The `FREE` token is the current code interface for a slope value in m/m.
It must not be interpreted as proof of a physical free overfall or as proof that the entire reach is at normal depth.

The scenario also names one model ID, one run identity hash, terrain and roughness assets, domain and grid properties, an inflow line, a downstream boundary geometry, run settings, and an optional hot-start depth raster.
Together, those values define the scenario input contract.

## Inputs and discharge bounds

The required ND job inputs are a model-manifest path, a result base path, `min_upstream_inflow`, `max_upstream_inflow`, and `delta_upstream_inflow`.
The three discharge values are integers interpreted as m3/s by the current schema and code.
The minimum and opening increment must be positive.
`max_upstream_inflow` has no positivity or finite-range bound and is not required to be greater than or equal to the minimum.
The current input model also does not establish the provenance of the bounds or derive the DR-029 formula.

The selected DR-029 rule is:

\[
Q_{min}=0.9Q_{HFT}, \qquad Q_{max}=1.5Q_{100}
\]

The current job receives the resulting integer values after any source selection, frequency analysis, uncertainty treatment, and rounding have already occurred elsewhere.
That upstream work needs a durable provenance record because the ND manifest records the supplied numbers, not how they were derived.

Important optional inputs include the outflow-area polygon, convergence tolerance, edge-water policy, simulation and wall-time limits, saved-output interval, existing scenario manifests, discharge-grid resolution, `save_zarr`, and three adaptive-response bands.
The current schema applies no positive or finite bounds to `volume_convergence_tolerance`, `max_simulation_length_seconds`, `save_interval_seconds`, or `max_simulation_wall_time_seconds`.
The three response-band validators require only a minimum span, so negative band endpoints can pass when their difference is large enough.
At minimum, the request boundary must reject a nonpositive maximum, a maximum below the minimum, nonpositive or nonfinite convergence and time controls, and nonfinite or negative response-band endpoints in addition to retaining the existing span checks.
Any narrower scientific ranges and their owner remain an authorized project decision.
The public ND input accepts `save_zarr`, but the current ND job does not forward it into `RunConfig`, so the request is ignored and the run-config default remains false.

## Current workflow from request to response

The following trace describes the reviewed current checkout.
It does not describe a generated-document summary when that summary differs from code.

### 1. Validate the request and create temporary work space

`Job.run` validates the payload against `RunNDScenariosInputs`, creates a temporary directory, calls the ND job, and deletes the temporary directory after the job returns or raises.
Unknown input fields are forbidden.

A schema-valid nonpositive or below-minimum maximum can enter adaptive selection and eventually be used to construct a `QFIX` request.
A nonpositive convergence tolerance can prevent the strict-less-than convergence branch from triggering, while nonpositive time or save controls can create an immediate termination or invalid solver-output schedule.
Negative response bands can drive adaptive comparisons against scientifically invalid targets.
These are consequences permitted by the current request contract, not evidence that they occurred operationally.

The job reads the model manifest directly from its local or remote path and validates it as a `ModelManifest`.
Assets named by that manifest are localized later through the shared asset cache when geometry, raster, or hot-start content is needed.

### 2. Construct the downstream slope boundary

If `outflow_area_polygon_path` is supplied, the job uses that polygon.
Otherwise, it derives and immediately publishes `outflow_area.geojson` beside the model centerline asset.

The derived geometry uses the downstream half of the centerline as a direction.
It creates a strip extending ten estimated bankfull widths to each side, intersects that strip with the downstream half of the rectangular domain, intersects the result with the domain edge, and square-buffers the selected edge by two grid cells.

The derived outflow polygon is not a `build_model` manifest asset.
Its adjacent location is not enough to prove its producer, compatibility, or hydraulic adequacy.

The current slope calculation is:

\[
S_{code}=\max\left(\frac{|z_1-z_2|}{L},10^{-4}\right)
\]

The endpoint terrain values come from the model DEM at the two centerline endpoints, and (L) is the manifest reach length.
The absolute value removes flow direction, and the floor is the checked-in fallback rather than a reach-specific validation result.
The m/m interpretation assumes that horizontal reach length is measured in metres and terrain elevation is measured in metres.
Current code does not enforce a metre-based projected horizontal CRS before using the manifest length.

The job wraps the polygon and slope in a `FreeBC` whose token is `FREE`.

### 3. Establish run identity and adopt supplied finished scenarios

The run identity hash contains the configured solver enum and the jobs image's baked Decision Register commit.
It does not include the executable version, image digest, run settings, hardware, or boundary details.

For each path in `existing_scenarios`, the job reads and validates a scenario manifest.
It adopts the manifest only if its reach ID, full model ID, run identity hash, and discharge within the inclusive requested range match.

The adoption predicate does not compare the full scenario inputs, downstream geometry or slope, run configuration, convergence setting, hot start, saved-output interval, wall-time setting, termination reason, or referenced-asset existence.
It also does not require alignment to `q_grid_resolution`.
When multiple supplied manifests have the same discharge, the last adopted manifest for that discharge replaces the earlier dictionary entry.

Adoption makes a finished scenario available for arithmetic comparison and hot-start provenance without rerunning it.
It does not independently observe that every referenced asset still exists or is compatible with current intent.

### 4. Obtain the minimum-discharge baseline

If an adopted scenario exists exactly at `min_upstream_inflow`, it becomes the initial reference.
Otherwise, the job requests the minimum through `run_scenario`.
That helper can either simulate it or return an existing manifest whose stored `RunScenarioInputs` equal the requested inputs exactly.
A newly simulated baseline is published immediately, while exact reuse reads a matching manifest at the predicted address and performs no upload.

The baseline comparison has no reference manifest.
The response records zero changes and an `accept` result for that baseline.

The adaptive edge-abort branch is applied only to requested scenarios inside the later loop.
Current code does not run that branch against the baseline, so a loose adopted, newly simulated, or exactly reused baseline whose manifest says `edge_error` can still become the initial reference.
A newly simulated edge-error baseline is published, while loose adoption and exact reuse read existing manifests without verifying all referenced assets.

### 5. Build one scenario input

For every newly requested discharge, the job creates one immutable `RunConfig` from the ND job settings.
It creates a `QFIX` inflow on the model's inflow-line asset and pairs it with the `FREE` outflow boundary.

The scenario input records the model domain and grid, terrain, roughness, centerline, boundary conditions, optional hot-start asset, output base, reach ID, model ID, and run identity hash.
The output directory is derived from reach ID, the model identity portion of the model ID, run identity hash, downstream slope, and discharge.

Before executing the solver, `run_scenario` checks the predicted manifest address for both baseline and later requests.
It reuses that manifest only when the stored `RunScenarioInputs` equal the newly constructed inputs exactly.
This exact-scenario reuse path is separate from the looser `existing_scenarios` adoption predicate.

### 6. Localize assets and write LISFLOOD-FP inputs

The preprocessor localizes remote terrain, roughness, boundary geometry, and hot-start assets through a cache whose paths are keyed by each asset's declared 16-character checksum.
Current cache code returns an existing cached file or copies a remote file without hashing those bytes and comparing them with the declared checksum.
The cache key therefore does not enforce input integrity.
It converts terrain and roughness GeoTIFF files to ASCII rasters.

The inflow line is rasterized to boundary points.
The current LISFLOOD-FP writer divides total discharge by cell resolution and the number of selected inflow points before writing the per-unit-width `QFIX` value.
The resulting m2/s interpretation assumes that cell resolution is in metres.
A geographic or foot-based horizontal CRS can pass current EPSG validation while giving this calculation the wrong units.

The outflow polygon is intersected with each north, south, east, and west domain edge.
For each touched edge, one cardinal boundary span is written from the minimum through maximum intersection coordinate.
Disjoint polygon intersections on one edge therefore become one continuous span that includes the gaps between them.

The preprocessor writes `lisflood.bci` and `lisflood.par` in the scenario working directory.
The parameter file identifies the DEM, Manning raster, boundary file, saved-output interval, mass interval, simulation time, initial time step, acceleration formulation, and CUDA and elevation-offset settings.

When a hot start is present, the preprocessor converts only the referenced final depth raster and writes it as LISFLOOD-FP `startfile`.
The current interface does not supply velocity, momentum, face flux, or a complete solver checkpoint.

### 7. Execute and monitor the solver process

The current path starts `lisflood` as a subprocess and streams standard output and standard error through logging threads.
A watcher in a one-worker thread pool inspects stable saved `lisflood_result-????.wd` grids in sorted order while the process runs and after it exits.

The first readable grid establishes the previous array and returns a sentinel convergence value of 1 without a boundary check.
Each later grid can produce a storage-change convergence ratio and an edge check.

If the ratio is strictly less than the configured tolerance, the watcher terminates the process and records `volume_convergence`.
Equality to the tolerance is not sufficient.

If an edge violation is present and `allow_water_on_edges` is false, the watcher terminates the process and records `edge_error`.
If edge water is allowed, the same detected condition does not terminate the process.
If convergence and a disallowed edge violation are both true on the same saved grid, the current branch records `volume_convergence` because convergence is tested first.
The persisted convergence reason therefore does not prove that no implemented edge violation occurred on that grid.
The adaptive ND loop sees `volume_convergence`, does not take its manifest-level `edge_error` abort, and can publish, compare, and select that scenario without a `WaterOnEdgeWarning` for the simultaneous violation.

If the process exits nonzero, the watcher raises `CalledProcessError`.
If the configured wall-time limit is exceeded, the watcher terminates the process and records `max_wall_time`.
An ordinary zero exit without earlier convergence or edge termination is recorded as `max_simulation_time`.
Termination first sends `terminate`, waits up to three seconds, and then sends `kill` if needed.

### 8. Interpret the edge check narrowly

The current edge check adds depth to terrain to form WSE.
It withholds judgment until the downstream endpoint cell is wet.
It then flags perimeter cells whose WSE lies inclusively between the two endpoint WSE values.

The detailed `BoundaryCheckResult` exists only while watching a saved grid.
The scenario manifest persists the final termination reason but not check activation, endpoint values, wet-edge cell counts, sides, margins, or the complete sequence of boundary results.

No `edge_error` can therefore mean that convergence suppressed a simultaneous edge reason, the check was not yet available, the downstream endpoint was dry, edge water was allowed, wet edge cells fell outside the implemented WSE interval, or a completed check found no flagged cells.
It cannot by itself prove domain adequacy.

### 9. Post-process the final saved state

After monitoring returns, the postprocessor selects the last sorted `.wd` file.
It converts that file to `depth.tif`, retains positive depth values, converts exact zeros to nodata before current COG writing, polygonizes wet cells as `inundated_area.geojson`, and derives `stl.geojson` from the upstream-point WSE contour clipped by the inundation polygon.

**Current scenario-output CRS fallback:** The `.wd` conversion reads CRS from the solver output profile and assigns literal EPSG:5070 when that CRS is absent.
It does not recover the realized model CRS from the scenario inputs, model manifest, or terrain asset.
For a non-5070 model, this can mislabel or spatially misassociate `depth.tif`, the derived inundation polygon, STL geometry, downstream transfer inputs, and later compositing outputs.
The immediate cause is that inundation inherits the depth label while STL construction uses the DEM transform and CRS, so objects can appear to share coordinates without sharing the same realized spatial reference.

The generic postprocessor creates `depths.zarr` only when `RunConfig.save_zarr` is true.
The public ND job does not forward its accepted `save_zarr` input, so this path does not currently produce a Zarr artifact.
If another caller supplies `RunConfig.save_zarr=true`, the generic branch creates a directory-backed Zarr store and then passes that directory to the file-only `hash_file` helper, so manifest construction cannot complete that advertised branch.

If no `.wd` file exists, post-processing raises `FileNotFoundError`, so no scenario manifest is completed even though the watcher has a zero-metric fallback.

The manifest's `sim_time` is the saved-output interval multiplied by the number of `.wd` files.
That value is derived from file count and interval rather than read as an exact solver clock from the final file.

### 10. Calculate final-state inundation metrics

The watcher calculates metrics from positive-depth cells in the final processed saved grid.
Maximum depth is the largest positive depth in m.
Median depth is the median of positive cell depths in m.
Flooded area is the count of positive-depth cells multiplied by squared raster resolution and divided by (10^6), reported as km2 under the assumed metre-based projected grid.

If the processed grid has no positive-depth cells, all three metrics are zero.
Cells with zero or negative depth are outside the metric population.
See [Adaptive Discharge Selection](02-adaptive-discharge-selection.md#the-three-current-response-metrics) for the equations and comparison behavior.

### 11. Build the scenario manifest

The manifest records creation time, reach ID, run identity hash and object, scenario code, full model ID, self address, complete scenario inputs, final properties, asset records, and a scenario warning list.

The final properties include volume convergence, termination condition, wall time, nominal upstream WSE, upstream discharge, derived simulation time, maximum depth, median depth, and flooded area.
The asset schema can name the final depth COG, inundation polygon, STL, and a nullable Zarr store.
In the current public ND path the Zarr field remains null, and the generic true branch cannot construct a manifest until directory-store hashing is defined.

The scenario warning list remains separate from the ND job's returned warning list.
The current edge-abort path adds `WaterOnEdgeWarning` to the job result rather than adding detailed edge evidence to the scenario manifest.

### 12. Publish the scenario and distinguish membership

Publishing a current public ND scenario copies the depth raster, inundation polygon, STL, and then the scenario manifest to their final addresses.
The manifest is written last, but the file sequence is not one atomic directory transaction.

Current ND code publishes a newly simulated baseline immediately.
An exactly reused baseline has a matching manifest at the predicted address, and its publication call performs no asset upload because it has no local processed outputs.

For a later discharge not already in `done`, `run_scenario` returns either a newly simulated scenario or an exactly reused full-input manifest.
The ND loop checks the returned manifest's termination condition before its publication call and before calculating the adaptive verdict.
A newly simulated non-edge-error trial is then published even when its later verdict is `reject_low` or `reject_high`.
An exactly reused non-edge-error trial has a matching manifest at the predicted address, and the later publication call performs no upload.

A newly simulated later trial that terminates on `edge_error` is not published.
Its temporary local outputs are removed when the job's temporary directory closes.
An exactly reused later manifest with `edge_error` can trigger the same adaptive abort even though the matching manifest remains at the predicted address; referenced assets are not re-observed.
In both cases, the job returns its earlier comparison records plus a `WaterOnEdgeWarning` and does not append a comparison for the edge-error trial.

Adopted or exactly reused scenarios have manifests read from supplied or predicted addresses and have no local processed assets to upload again.
The ND job's in-memory set prevents repeated publication calls for the same discharge during one invocation.

For a new scenario, publication writes files sequentially before the manifest-last completion marker.
For exact reuse, current code proves only that it read a schema-valid manifest with equal complete inputs at the predicted address.
Exact reuse does not prove that referenced assets still exist or match their recorded checksums.
Publication does not prove selected library membership, storage observation by a controller, complete materialization, or scientific adequacy.

### 13. Return the job result

`RunNDScenariosResult` contains `scenario_comparison_results` and job-level `warnings`.
The comparison list begins with the accepted zero-change baseline and then includes each ordinary trial comparison appended by the main loop.

The result does not return an explicit selected-member list or a manifest list.
It also omits free-pass re-judgments, and a measured `reject_high` that current code later treats as accepted because no finer grid step exists remains recorded as `reject_high` in its comparison object.
Maximum-discharge inclusion is likewise a control-flow decision rather than a dedicated membership field.

When configured for SEPEX-style output, `Job.run` prints the model-dumped result inside `{"plugin_results": ...}` after the ND job returns.
A returned path or comparison record is not target materialization evidence because the target reconciler must observe required storage objects independently.

## Scenario artifacts and what each proves

| Artifact or record | Current meaning | What it does not prove |
| --- | --- | --- |
| `depth.tif` | Final saved positive depth values converted to a COG, with exact zeros converted to nodata. | Correct CRS labeling when the solver output omits CRS, convergence history, complete mass balance, initial-condition independence, or hydraulic adequacy. |
| `inundated_area.geojson` | Polygonized final depth result. | Selected membership, absence of clipping, or validated FIM accuracy. |
| `stl.geojson` | Upstream-point WSE contour clipped to the inundation polygon. | Suitability as a downstream-to-upstream transfer boundary in another reach. |
| `depths.zarr` | Not produced by the current public ND path because `save_zarr` is not forwarded; the generic true branch creates a directory store but fails when file-only hashing is attempted. | A completed manifest, published history artifact, complete solver checkpoint state, or validation. |
| `scenario_manifest.json` | Inputs, bounded identity, final metrics, termination reason, assets, and hrefs for one scenario. | Full solver environment, complete boundary-check history, referenced-asset existence, or selected library membership. |
| ND job result | Baseline and main-loop measured comparisons plus job warnings. | Free-pass re-judgments, explicit selected members, complete publication inventory, or materialization. |

## Sequential warm starts and path dependence

Every newly simulated trial after the baseline receives the current adaptive position's final depth asset.
The source may be accepted, `reject_low`, adopted, or exactly reused.
A `reject_high` trial does not become the position, so the next trial does not use that trial's depth merely because it ran most recently.

This sequencing prevents ordinary parallel execution of the adaptive search because the next hot-start source and the next discharge depend on the current result.
It can reduce spinup, but it can also preserve path dependence when the target run ends before omitted dynamic state and the supplied depth adjust to the target forcing and boundaries.

The manifest records the hot-start depth asset.
It does not show that a cold start, a different defensible warm start, and the selected path converge to equivalent target results.

## Edge abort and partial completion

For a later requested discharge that was not already in `done`, an `edge_error` manifest aborts the entire adaptive loop immediately whether the manifest was newly simulated or exactly reused by full-input equality.
The job does not continue at a smaller discharge, classify the affected edge, expand the domain, or rerun the model.

If the proposed discharge is already in the loose adopted or finished `done` mapping, the loop bypasses `run_scenario` and the associated termination check.
An adopted `edge_error` manifest in `done` can therefore proceed to comparison instead of triggering this abort branch.

Everything published before the abort remains in storage.
An exactly reused edge-error scenario also remains in storage because the abort does not remove pre-existing artifacts.
The returned comparison list remains partial and excludes the edge-error trial because the function returns before comparing it.
The warning therefore means that the current attempt stopped, not that prior publications were rolled back or that the reach now has a scientifically adequate partial library.

## Search overhead and storage implications

Adaptive search can simulate points that never become selected members.
Those trials improve the response curves, can become hot-start sources, and can be re-judged after the reference changes.

In the reviewed implementation, non-edge-error trials are also published before judgment.
Search overhead therefore consumes persistent asset storage, not only temporary compute and scratch space.
The generated job documentation's statements that rejected trials stay only in the working directory and that only accepted points plus endpoints are uploaded are stale relative to current code.

No current ND result enumerates which stored scenarios are search overhead and which are selected members.
A durable membership record and storage-level materialization check are required before downstream planning treats the published folder as the selected library.

## Worked trace of one trial

Assume the selected reference is 100 m3/s and the current position is 140 m3/s because the 140 m3/s trial was below every response floor.
The next newly simulated trial at 170 m3/s uses the published 140 m3/s depth raster as `startfile`, not the 100 m3/s reference depth and not a previously rejected-high 190 m3/s depth.

Suppose the 170 m3/s solver run ends on `volume_convergence` and produces valid final files.
Current code post-processes the result, builds its manifest, publishes all scenario assets, and only then compares the three metrics against the 100 m3/s reference.

If one metric exceeds its ceiling, the measured verdict is `reject_high`.
The already published 170 m3/s scenario remains stored as search evidence, but it is not a selected member under the ordinary verdict branch.
The next proposal can still use that measured point in its response curves.

## Common misconceptions

### ND means uniform flow throughout the model

ND names the downstream slope boundary family.
The two-dimensional interior can remain nonuniform, unsteady, backwater affected, or controlled by terrain and structures.

### `volume_convergence` proves mass balance

The implemented ratio measures change in stored positive-depth volume between saved grids relative to inflow volume.
It does not measure outflow or every source and sink.

### No `edge_error` proves the domain is large enough

The current check can be withheld, allowed, or fail to classify wet cells that do not fall inside its endpoint-WSE interval.
It can also find a violation on the same saved grid whose persisted reason becomes `volume_convergence` because convergence has branch priority.
Its detailed evidence is not persisted in the manifest.

### The most recently simulated trial is always the hot-start source

A `reject_high` trial does not advance the position.
The next simulation starts from the accepted or `reject_low` position retained by the search.

### Every published scenario is a library entry

Current code publishes ordinary non-edge-error trials before measuring the verdict.
Selected membership needs a separate record.

## Competency check

1. Which predicate allows an `existing_scenarios` manifest to be adopted, and which important compatibility dimensions does it omit?
2. How are the current outflow polygon and slope derived when no polygon is supplied?
3. Which files and settings are written before LISFLOOD-FP starts?
4. Under what exact conditions does the watcher record convergence, edge error, wall-time termination, or maximum simulation time, and which reason wins when convergence and an edge violation are simultaneous?
5. How do newly simulated and exactly reused baseline, ordinary, and edge-error scenarios differ in upload and abort behavior?
6. Why can the job response not serve as an explicit selected-library index?
7. What evidence would be required to show that sequential depth-only warm starts do not materially bias the intended outputs?
8. Why does the public ND `save_zarr` input produce no Zarr artifact, and why can the generic true branch not complete manifest construction?
9. Which checks are required before accepting the horizontal units and CRS labels of scenario artifacts?
10. Which currently accepted request values can defeat the discharge, convergence, time, response-band, or input-integrity contract before a scenario is scientifically interpretable?

## Further reading and source notes

- [Adaptive Discharge Selection](02-adaptive-discharge-selection.md) explains the selection state, response curves, acceptance window, proposal fallbacks, measured verdicts, and maximum behavior.
- [Decision-Code-Artifact Crosswalk XW-006](../reference/decision-code-artifact-crosswalk.md#xw-006-scenario-library-bounds-and-sampling) maps bounds and sampling across authority layers.
- [Crosswalk XW-012](../reference/decision-code-artifact-crosswalk.md#xw-012-hot-start-state-and-sensitivity) maps depth-only hot-start provenance and the unresolved sensitivity question.
- [Conflicts and Open Questions](../reference/conflicts-and-open-questions.md) records request validation, input integrity, publication, q-grid, metric, documentation, hot-start, edge, and identity gaps.
- Local sources JOB-005 and JOB-007 in [Bibliography](../reference/bibliography.md) identify the reviewed current-code paths and revision.

No external source was required for this current-workflow trace.
