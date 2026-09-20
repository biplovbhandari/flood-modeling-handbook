# KWSE and Stage Transfer

A known-water-surface-elevation scenario couples an upstream reach model to a completed downstream scenario.
The current job does this by applying upstream discharge, opening selected outer-edge spans, and converting downstream depth plus downstream terrain into cell-specific fixed WSE points along the downstream scenario's stage-transfer line.

## Why this topic matters

Backwater can make one upstream discharge produce different depths and extents under different downstream stages.
A KWSE library represents that dependence, but only when the downstream source scenario, transfer geometry, grids, datums, boundary handling, initial state, run identity, and artifacts remain traceable.

The scalar stage that names a scenario is not the complete transferred boundary.
Current code applies a spatial WSE field sampled from a downstream raster, and that distinction affects scientific interpretation, exact reuse, and failure diagnosis.

## Prerequisites

Read [Backwater and Boundary Control](../02-open-channel-flow/04-backwater-and-boundary-control.md), [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md), [Domain and Boundary Geometry](../04-model-development/04-domain-and-boundary-geometry.md), [Normal-Depth Libraries](01-normal-depth-libraries.md), and [Adaptive Discharge Selection](02-adaptive-discharge-selection.md).
Use [Source Authority](../reference/source-authority.md) to keep selected methodology, current implementation, target design, and open questions separate.

## Learning objectives

After this chapter, the reader should be able to:

- trace the current `run_kwse_scenarios` path from request validation through its job response;
- explain how a downstream scenario's depth, terrain, STL, domain, and grid become upstream `HFIX` points;
- explain why nominal `bc_value` names a scenario but does not set its per-cell transferred WSE values;
- distinguish the current run-specific STL product from DR-025 and DR-026 methodology;
- identify coverage, datum, smoothing, rasterization, and dry-cell limitations;
- distinguish a depth-only hot start from a complete solver checkpoint; and
- separate publication, exact reuse, materialization, and hydraulic adequacy.

## Authority boundary

**Selected methodology:** DR-001 ALT-A selects KWSE modeling for all reaches.
DR-003 ALT-D selects downstream-FIM-informed edge cells with freefall language.
DR-009 ALT-B places transfer at the reach outlet.
DR-025 ALT-B selects a downstream-FIM WSEL contour as the STL.
DR-026 ALT-B selects a separately derived STL for each run.
DR-031 ALT-B selects cell-by-cell stage transfer from a downstream scenario.
Each of these records has Alternate Selected status in the handbook-reviewed Decision Register.

**Current implementation:** The current `run_kwse_scenarios` job consumes an ordered caller-provided list and runs LISFLOOD-FP scenarios serially.
It does not decide which discharge-stage pairs belong in the list, discover network dependencies, or prove that the downstream scenario is the correct topological neighbor.

**Target design:** System-design material assigns scenario planning, dependency ordering, storage observation, and upstream propagation to a controller or reconciliation loop.
The checked-in reconciler contains prototype planning, submission, and observation code, but this is outside the three current modeling jobs and is not evidence of verified deployment.

**Open question:** The relationship among selected freefall language, the current slope-based `FREE` interface at 0.5 m/m, and contiguous spans across dry gaps remains unresolved in [CONF-001](../reference/conflicts-and-open-questions.md#conf-001-boundary-condition-terminology-and-behavior).
The request-boundary and empty-list behavior remains unresolved in [CONF-020](../reference/conflicts-and-open-questions.md#conf-020-scenario-request-boundary-validation).

## The scientific transfer relationship

At a compatible horizontal location, current transfer preprocessing calculates:

\[
WSE_{ds,i}=h_{ds,i}+z_{ds,i}
\]

- \(WSE_{ds,i}\) is downstream water-surface elevation at source cell \(i\), in m relative to the downstream terrain datum.
- \(h_{ds,i}\) is final downstream depth at source cell \(i\), in m.
- \(z_{ds,i}\) is downstream terrain elevation at source cell \(i\), in m relative to the same datum.

The calculation is valid only when depth and terrain use the same grid, units, horizontal support, and vertical reference.
Applying the value to the upstream model also requires compatibility between downstream WSE and upstream terrain datum.

A line crossing on a map is therefore necessary but not sufficient.
The transfer needs source-raster coverage, valid array indices, compatible datums, intended scenario provenance, and a scientifically defensible hydraulic source.

## Current workflow from payload to response

The following trace describes the reviewed current jobs checkout.
It does not describe target planning or verified deployed orchestration.

### 1. Validate the request and create temporary workspace

`Job.run` validates the payload against `RunKWSEScenariosInputs`, creates a temporary directory, delegates to the job, and removes the directory after return or failure.
Only the top-level `RunKWSEScenariosInputs` model sets `extra="forbid"`, so an unknown top-level key is rejected.
Nested `KWSEScenario` and `HotStart` use Pydantic's default extra handling, so unknown keys inside those objects are ignored rather than rejected.

Required job fields are `model_manifest_path`, `model_results_base_path`, and `scenarios`.
Each `KWSEScenario` requires positive whole-m3/s `upstream_discharge`, scalar `bc_value`, and the case-sensitive field `downstream_Scenario` containing a downstream scenario-manifest path.
The `scenarios` field has no minimum length, so an empty list is schema valid.
The optional `hotstart` records source discharge, `ND` or `KWSE` boundary type, source boundary value, and an eight-character run identity hash.
Missing required nested fields, wrong types that cannot be validated, nonpositive scenario discharge, an unsupported hot-start `bc_type`, and an invalid identity-hash pattern still fail validation.
Using `downstream_scenario` instead of exact required `downstream_Scenario` leaves the required field missing while the wrongly cased key is ignored.
The schema also accepts an explicit `identity_hash: null` because the field type permits null, even though a non-null string must match the eight-character pattern.
The shared `volume_convergence_tolerance`, `max_simulation_length_seconds`, `save_interval_seconds`, and `max_simulation_wall_time_seconds` fields have no positive or finite bounds.
At minimum, the request boundary must require one or more scenarios and finite positive values for each convergence or time control.
Any narrower scientific ranges and their owner remain an authorized project decision.

The job validates the upstream model manifest first.
It creates one immutable `RunConfig` shared by every supplied scenario and calculates the current run identity hash from the solver enum and baked Decision Register commit.
The current constructor does not pass `inputs.save_zarr` into `RunConfig`, so the accepted KWSE `save_zarr` input does not enable Zarr creation in this path and the run-config default remains false.
Setting `save_velocity=true` fails input validation with `NotImplementedError`.

When `scenarios` is empty, the runner executes no scenario and returns `manifests=[]` with `warnings=[]` without warning that no scenario or artifact was produced.
A nonpositive convergence tolerance can prevent the strict-less-than convergence branch from triggering, while nonpositive time or save controls can create an immediate termination or invalid solver-output schedule.
These are consequences permitted by the current request contract, not evidence that they occurred operationally.

### 2. Read one downstream scenario manifest

For each scenario in list order, the job reads `downstream_Scenario` and validates it as a `RunScenarioManifest`.
The current job does not compare the downstream manifest's reach ID with an authoritative prepared-network adjacency record.
It also does not perform an independent scientific-adequacy review of that downstream run.

The downstream manifest supplies these transfer inputs:

- `assets.inundation_polygon` for current outer-edge treatment;
- `assets.stage_transfer_line` for transfer geometry;
- `assets.depth` for downstream final depth;
- `inputs.terrain` for downstream terrain elevation;
- `inputs.grid_properties` for downstream row-column mapping; and
- `inputs.domain` for the downstream raster transform.

### 3. Build the upstream discharge boundary

The job creates a `QFixBC` on the upstream model manifest's `assets.inflow_line` with the scenario's `upstream_discharge`.
During preprocessing, the line is rasterized with `all_touched=True` on the upstream grid.

If the line selects \(n\) points at cell resolution \(r\), the current LISFLOOD-FP writer assigns each point:

\[
q_{point}=\frac{Q}{rn}
\]

The resulting `QFIX` value is a per-unit-width value whose total across selected cells represents the requested discharge under the writer's convention.
Its m2/s interpretation assumes that upstream cell resolution is in metres.
Current code validates only a positive EPSG integer and does not enforce a projected horizontal CRS with metre linear units.

### 4. Build the current outer-edge boundary

The job creates a `FreeBC` whose vector is the downstream scenario's inundation polygon and whose value is exactly 0.5 m/m.
The schema and writer call this a slope-based `FREE` condition.

Preprocessing intersects the polygon with the north, south, east, and west edges of the upstream rectangular domain.
For each touched cardinal edge, it writes one span from the minimum through maximum intersection coordinate.

Disjoint polygon intersections on the same edge therefore become one contiguous span.
That span includes intervening dry gaps and can open cells not individually intersected by the downstream inundation polygon.

The current 0.5 m/m slope is not derived from `bc_value` and is not a per-cell transferred stage.
Calling it selected freefall without qualification would overstate the relationship between DR-003 and current solver input.

### 5. Build the transfer boundary object

The job creates one `TransferBC` using the downstream STL, downstream depth, downstream terrain, downstream grid, and downstream domain.
The object also carries the scenario's scalar `bc_value`.

The scalar is used by `RunScenarioInputs` to construct the upstream scenario directory and code, such as `kwse=11.5/q=18500` and `KWSE11.5Q18500` under the current formatting precision.
It does not set every `HFIX` point to 11.5 m.
Current KWSE directory names format the raw boundary value to one decimal place.
Distinct raw values that round to the same one-decimal text therefore target the same address even though exact input comparison prevents an unequal stored manifest from being reused.
A later new run can still sequentially overwrite or mix files at that shared address.

### 6. Rasterize the downstream STL on the upstream grid

The transfer geometry must be a `Point`, `LineString`, or `MultiLineString`.
A polygon transfer geometry raises `ValueError`.

A line or merged multiline is rasterized on the upstream model grid with `all_touched=True`.
The output coordinates are upstream cell centers touched by the line.

If the STL lies wholly outside the upstream domain, rasterization can return no points and the current boundary-processing path returns an empty list without reaching the later `NonIntersectingKWSELine` check.
If points exist but every sampled downstream WSE is omitted, `NonIntersectingKWSELine` is raised.

### 7. Sample downstream WSE at upstream-selected coordinates

For every selected upstream coordinate, current code maps the same x-y coordinate into a downstream row and column using the downstream domain and grid.
It reads the sum of the downstream depth and terrain arrays at that index.

The lookup performs no explicit row-column bounds check.
A positive out-of-range row or column raises during NumPy indexing, while a negative index can wrap silently to the opposite raster edge and return a plausible value from the wrong cell.

Current code also does not validate that localized downstream depth and terrain match each other or the manifest declaration in shape, affine transform, bounds, CRS, horizontal units, vertical datum, nodata mask, or cell registration before raw array addition and sampling.
Equal declared domain and grid values do not prove raster congruence, and equal array shapes alone do not prove aligned cells.

Only values strictly greater than zero become point `HFIX` entries.
Zero and negative WSE values are omitted.

Current code tests positive WSE rather than positive depth.
A dry source cell represented by depth zero and positive terrain can therefore produce an `HFIX` point when its array values are otherwise readable.
The transfer calculation performs raw array addition without an explicit nodata mask, so omission of a common negative nodata sentinel can occur only as a side effect of the later positive-WSE test rather than as validated nodata handling.

No interpolation between downstream cell centers is performed in this transfer step.
The combination of line rasterization, cell-center coordinate mapping, array indexing, source-grid resolution, and grid alignment determines which downstream cells are sampled.

The current preprocessing path does not explicitly validate matching CRS, horizontal units, or vertical datum here.
Compatibility must be established by upstream contracts and evidence.

### 8. Resolve an optional depth-only hot start

The job first constructs the target scenario inputs with no hot start.
If a `hotstart` is present, it creates a proxy source scenario address from the target inputs.

For an `ND` seed, the proxy uses the seed discharge and a `FREE` value equal to the seed `bc_value`.
For a `KWSE` seed, the proxy uses the seed discharge and a copied transfer boundary whose scalar value equals the seed `bc_value`.
The proxy also uses the requested hot-start run identity hash.

When a payload supplies explicit `identity_hash: null`, current job code passes that null into proxy address construction instead of replacing it with the target run identity.
The resulting lookup does not implement the schema description's current-identity fallback and fails to locate the intended hot-start manifest.
The production contract must either reject explicit null or resolve it to the current run identity before address construction through validated model construction rather than an unchecked update.

The job reads the source manifest at the proxy's predicted `manifest_href` and assigns the source manifest's `assets.depth` to target `inputs.hot_start`.
It does not accept an arbitrary hot-start path.

Preprocessing localizes that depth asset and writes it as LISFLOOD-FP `startfile`.
The current interface supplies no velocity, momentum, face flux, or complete solver checkpoint.
The source address is durable provenance, but it does not prove target independence from initial conditions.

### 9. Derive the target address and check exact reuse

The target address has this current shape:

```text
<model_results_base_path>/reach=<reach_id>/<model_identity_hash>/<run_identity_hash>/kwse=<bc_value>/q=<upstream_discharge>/scenario_manifest.json
```

The results path uses the identity portion of `model_id` and omits its domain-code portion.
The full current `model_id`, including domain realization, remains inside the manifest inputs and top-level fields.

Before preprocessing or solver execution, `run_scenario` checks whether the predicted manifest exists.
It reuses the scenario only when the stored manifest validates and its complete stored `RunScenarioInputs` equal the newly constructed inputs.

This is exact input equality at the manifest level.
The check does not independently verify that every referenced artifact still exists or that its bytes match the stored checksum.
It also does not prevent two unequal raw boundary values that share one rounded directory name from publishing sequentially to that same address.

An exact reuse returns `CompletedScenario.processed=None`.
The KWSE job counts that scenario as re-adopted, calls `publish_scenario`, and performs no upload because there are no local processed outputs.

### 10. Localize inputs and write LISFLOOD-FP files

For a new run, a cache whose paths are keyed by each asset's declared 16-character checksum localizes remote terrain, roughness, boundary geometry, downstream depth, downstream terrain, and hot-start depth as each is needed.
Current cache code returns an existing cached file or copies a remote file without hashing those bytes and comparing them with the declared checksum.
The cache key therefore does not establish that the localized input matches the manifest's declared integrity value.
Terrain and roughness are converted to Arc ASCII rasters.

The writer creates `lisflood.bci` with upstream `QFIX`, cardinal-edge `FREE`, and pointwise `HFIX` elements.
It creates `lisflood.par` with DEM, Manning raster, boundary file, output interval, mass interval, simulation time, initial time step, acceleration formulation, CUDA and elevation-offset settings, and optional `startfile`.

### 11. Execute and monitor the solver

The current execution path starts `lisflood` as a subprocess and streams standard output and standard error.
A watcher reads stable saved `lisflood_result-????.wd` files while the process runs and once more after exit.

The first readable grid receives a sentinel convergence value of 1 and no edge check.
Later grids can trigger strict-less-than volume convergence, `edge_error` when water is flagged and not allowed, or maximum wall-time termination.
When convergence and a disallowed edge violation are both true on the same saved grid, current branch order records `volume_convergence` and suppresses `edge_error` as the persisted reason.
Persisted convergence therefore does not prove that no implemented edge violation occurred on that grid.
The KWSE job can publish and return that scenario with its current empty job-warning list.
An ordinary zero process exit without an earlier watcher termination is recorded as `max_simulation_time`.
A nonzero exit raises `CalledProcessError`.

The current edge diagnostic is the same narrow endpoint-WSE-range check described in [Normal-Depth Libraries](01-normal-depth-libraries.md).
Its detailed `BoundaryCheckResult` is not persisted in the scenario manifest.

### 12. Post-process the final saved state

Post-processing sorts saved `.wd` files and requires at least one.
It converts the last file to `depth.tif`, masks zero depth as nodata, polygonizes wet cells into `inundated_area.geojson`, derives `stl.geojson`, and creates `depths.zarr` only when `RunConfig.save_zarr` is true.
The reviewed KWSE job does not forward its `save_zarr` input into that run config, so current KWSE execution does not reach the optional Zarr branch through its public input.
If another caller supplies `RunConfig.save_zarr=true`, the generic branch creates a directory-backed Zarr store and then passes that directory to the file-only `hash_file` helper, so scenario-manifest construction cannot complete.

The `.wd` conversion assigns literal EPSG:5070 when the solver output profile has no CRS.
It does not recover the realized model CRS from the target model manifest, scenario inputs, or terrain asset.
For a non-5070 model, this fallback can mislabel or spatially misassociate depth, the derived inundation polygon, STL geometry, later transfer inputs, and compositing outputs.
The immediate inconsistency is that inundation inherits the depth label while STL construction uses the DEM transform and CRS.

The final `sim_time` is calculated as the saved-output interval multiplied by the number of saved `.wd` files.
It is not read from an exact final solver clock.

### 13. Build the run-specific STL

Current STL production is the same for ND and KWSE runs.
It adds the current run's final depth to its own terrain only where depth is positive, leaving dry cells as NaN.

The WSE field is padded and repeatedly Gaussian-smoothed with NaN-aware weighting.
The code then samples the smoothed WSE at the upstream endpoint of the run's centerline and extracts every contour at that level using `skimage.measure.find_contours`.
The contour vertices are mapped to raster cell centers, combined into a multiline geometry, and clipped by the final inundation polygon.

This is a post-processed, smoothed, run-specific contour product.
It can differ among runs because depth, wet extent, sampled upstream WSE, and contour topology can differ even when terrain is unchanged.

The procedure introduces representational uncertainty through raster resolution, wet-cell masking, NaN extrapolation, multiple smoothing scales, contour interpolation, coordinate mapping to cell centers, multipart contours, and clipping.
The manifest records the resulting STL asset and checksum but does not record every smoothing parameter as an explicit method identity.

### 14. Build and publish the scenario manifest

The scenario manifest records the run identity, scenario code, full model ID, self address, complete scenario inputs, final properties, output assets, and warnings.
Its asset schema names `depth.tif`, `inundated_area.geojson`, `stl.geojson`, and a nullable Zarr asset, each represented by an `Asset` with a 16-character checksum prefix when present.
The current public KWSE path leaves the Zarr field null, and the generic true branch cannot complete manifest construction until directory-store hashing is defined.

Publishing a current public KWSE scenario copies depth, inundation polygon, STL, and then writes `scenario_manifest.json` last.
The sequence is not one atomic directory transaction.
A failure after an earlier copy can leave partial or mixed addressed content.

The manifest-last convention makes the manifest a useful completion marker, but the current job does not roll back already copied files.
Independent storage observation and asset verification remain necessary for target materialization.

### 15. Return the job response

The job appends each new or exactly reused manifest's `self_href` to `RunKWSEScenariosResult.manifests`.
It returns `warnings=[]` under the reviewed current code.

When SEPEX-style printing is enabled, `Job.run` prints the model-dumped result inside `{"plugin_results": ...}`.
The response proves only what the job returned under that invocation.
It does not independently observe storage, establish library completeness, or prove hydraulic adequacy.

## Why domains must overlap

The downstream STL is created from a downstream run, but the transfer points are selected on the upstream grid.
The same map coordinates must therefore lie inside both domains and within valid source raster coverage.

Overlap serves three distinct purposes:

1. The downstream model supplies a physically represented WSE field at the interface.
2. The upstream model contains cells where that condition can be applied.
3. The transition region allows comparison of upstream and downstream results around the handoff.

An STL present in both bounding boxes can still fail these purposes when the source cells are nodata, the line misses active upstream cells, the datums differ, the selected source scenario is unsuitable, or smoothing moves the contour away from a defensible hydraulic interface.

The checked-in reconciler prototype currently supplies a downstream maximum-ND-discharge STL through `build_model.other_geometries` when constructing an upstream computed domain.
Its own comments state that this geometry is not part of current model identity and that changing it alone does not necessarily trigger a rebuild when the domain is unauthored.
This prototype behavior is not proof that overlap is implemented or verified in deployment.

## Current STL output versus selected methodology

DR-025 ALT-B selects a WSEL contour from downstream FIM, and DR-026 ALT-B selects a separate STL for each run.
Current post-processing does create one `stl.geojson` for every realized scenario by contouring that run's smoothed WSE field.

This resemblance does not collapse the authority layers.
The Decision Register defines intended methodology within its selected status.
Current code defines the algorithm actually used by this checkout.

The current algorithm additionally chooses the contour level at the run's upstream centerline endpoint, exports every contour at that smoothed level, clips the result to inundation, and stores one nominal WSE rounded to 0.1 m.
Those detailed choices need direct validation against the intended reach-outlet location and transfer quality.

## Figure: downstream-to-upstream transfer

![Two overlapping reach domains with downstream WSE samples transferred upstream along a run-specific STL](../assets/kwse-stage-transfer.svg)

**What to notice:** The hydraulic information moves from the completed downstream scenario toward the upstream scenario even though river flow points downstream.
The figure uses visibly different upstream and downstream grid origins and resolutions.
One shared map query coordinate lies inside an upstream transfer cell and a different downstream containing cell; the downstream cell's sampled value supplies the upstream `HFIX` value.
A shared coordinate does not imply coincident cell centers, footprints, resolution, affine transform, or raster registration.
The downstream job owns the source depth, terrain, inundation, and run-specific STL artifacts.
Planning owns the choice of discharge-stage pair and source scenario.
The upstream KWSE job owns the exact transfer preprocessing and solver execution.
Storage observation and reconciliation own materialization evidence rather than the job response.

## Failure modes and diagnostic signals

### Missing or invalid downstream manifest

The job fails while reading or validating `downstream_Scenario` before a target run is created.
Check the exact case-sensitive payload field, address, schema, identity, and source scenario provenance.

### Transfer line outside the upstream domain

Rasterization can yield no points.
The current helper can return an empty boundary-element list in that case rather than always raising the custom nonintersection error.
Inspect both bounding boxes, CRS, actual line coordinates, and upstream cell coverage.

### No positive transferred WSE

When points exist but all sampled `depth + terrain` values are nonpositive, transfer preprocessing raises `NonIntersectingKWSELine`.
Inspect nodata handling, source indices, units, datum, terrain sign, and whether source cells should be wet.

### Dry cells become transfer points

Positive terrain can make a point pass the current `WSE > 0` condition even when source depth is zero.
Inspect the source depth separately rather than treating every written `HFIX` point as downstream inundation evidence.

### Partial source coverage or index failure

An STL can extend outside the downstream raster even while overlapping the upstream grid.
A positive out-of-range index raises, while a negative NumPy index can wrap to the opposite raster edge and silently sample the wrong cell.
Inspect explicit index bounds and compare depth, terrain, and manifest shape, transform, bounds, CRS, horizontal units, vertical datum, nodata masks, and cell registration before trusting the transfer.

### Explicit-null hot-start identity

An explicit `identity_hash: null` passes the current schema but is sent into address construction instead of resolving to the current run identity.
Reject null or resolve it through validated model construction before attempting source-manifest lookup.

### Rounded boundary address collision

KWSE names use one decimal place, and ND slope names use one decimal place in scientific notation.
Distinct raw values can therefore share an address even though exact input comparison refuses unequal reuse.
Prevent same-address overwrite by validating values onto the naming grid, increasing address precision, or using a content-complete scenario identity.

### Wrong nominal label

Changing `bc_value` changes the target address and scenario identity label, but not the sampled downstream per-cell WSE field when the downstream source remains unchanged.
Compare the scalar target, source manifest, achieved upstream nominal WSE, and actual `HFIX` values as separate quantities.

### Hot-start source missing

The job reconstructs the source address from source coordinates and fails when that manifest is absent or invalid.
Check seed ordering, run identity, source boundary type, formatting precision, and publication state.

### Partial publication

An interrupted upload can leave one or more assets without the final manifest, or can replace earlier files before a later copy fails.
Treat storage observation and checksum verification as separate from process success and returned paths.

## Competency check

1. Why can a dry cell with positive terrain produce a current transfer point?
2. Which value determines the folder name, and which values determine pointwise `HFIX`?
3. Why must an STL overlap two domains and one valid source raster?
4. What uncertainty does current STL smoothing introduce?
5. How does exact scenario reuse differ from proof of materialization?
6. Which component chooses the current KWSE scenario list?
7. Why can a negative source index silently return the wrong downstream value while a positive out-of-range index raises?
8. What happens when a hot-start payload supplies explicit `identity_hash: null`?
9. Why can two unequal raw boundary values target one scenario address?
10. What does an empty `scenarios` list return, and which validation is required before that response can be interpreted as requested work?
11. Why does a checksum-derived cache path not prove input integrity in the current implementation?

## Further reading and source notes

- [Scenario Planning and Propagation](04-scenario-planning-and-propagation.md) explains who should choose the ordered list and why dependencies run downstream to upstream.
- [Compositing, Identity, and Provenance](05-compositing-identity-and-provenance.md) explains addressed paths, manifests, publication, materialization, and composite-product boundaries.
- [Decision-Code-Artifact Crosswalk XW-009](../reference/decision-code-artifact-crosswalk.md#xw-009-stage-transfer-geometry-and-values) maps transfer intent to current code and artifacts.
- [Conflicts and Open Questions](../reference/conflicts-and-open-questions.md) records unresolved request validation, input integrity, boundary, transfer, identity, and orchestration gaps.
- Bibliography entries SDR-001, SDR-002, SDR-010, SDR-011, JOB-002, JOB-003, JOB-007, JOB-010, SYS-001, SYS-002, and SYS-006 identify the reviewed local sources.

No external source was required for this chapter.
