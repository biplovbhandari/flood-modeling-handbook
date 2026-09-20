# The Build-Model Job

The current `build_model` job turns one prepared-network reach and caller-authored modeling inputs into a rectangular terrain and roughness grid, vector geometry assets, a model manifest, and a result that names the intended output directory.
It constructs model inputs for later scenarios, but it does not run a hydraulic solver, prove that storage materialized, or establish that the resulting model is scientifically adequate.

## Why this topic matters

The job is where network, geometry, terrain, roughness, identity, storage, and warning contracts meet.
A request can pass type validation and produce a complete-looking artifact directory while still carrying the wrong topology, a mutable source, an unknown vertical datum, an unmapped roughness value, or an undersized domain.

Understanding the exact path makes it possible to separate software completion from hydraulic readiness and to place missing checks with the component that owns them.

## Prerequisites

Read [Network Preparation](01-network-preparation.md), [Terrain, Topobathymetry, and Structures](02-terrain-topobathymetry-and-structures.md), [Roughness and Land Cover](03-roughness-and-land-cover.md), and [Domain and Boundary Geometry](04-domain-and-boundary-geometry.md).
Use [Lab 8](../labs/lab-08-inspect-a-built-model.md) to inspect a checked-in fixture after completing this trace.

## Learning objectives

After this chapter, the reader should be able to:

- trace validated request data through every current `build_model` stage;
- distinguish checked-in environment fallbacks, process-realized fallbacks, caller overrides, and manifest-recorded inputs;
- distinguish code defaults from selected Decision Register values;
- explain reach lookup, bankfull-width estimation, inflow construction, domain construction, raster extraction, and roughness conversion;
- reconstruct model identity, domain code, model ID, output addressing, and manifest creation;
- identify implemented and documented-but-unimplemented warnings;
- explain existing-model short-circuit behavior and its evidence limits; and
- state what each result field proves and does not prove.

## The current execution boundary

The common `Job.run` method validates an input dictionary with the job's Pydantic model, creates a temporary directory, calls `BuildModelJob._run`, optionally prints the returned model as a SEPEX-style `plugin_results` object, and returns the result.
Temporary files disappear when the common job context exits.
Durable evidence therefore depends on successful copies to the configured output location or on already existing content that is independently observed.

The `build_model` request operates on one `reach_id` at a time.
It does not prepare the network, derive a rainfall-runoff hydrograph, choose scenario discharges, construct ND or KWSE libraries, execute a solver, derive an STL, expand a domain after a run, or composite reach results.

## Step 1: Validate and realize inputs

The input model rejects unknown top-level fields because it uses `extra="forbid"`.
It requires `reach_id`, `reach_network_path`, and `base_output_path`.
Other fields have typed defaults, bounds, or both.

The validation that matters for this chapter includes the following behavior.

- `grid_resolution`, `walk_us_dist_pct`, and `bankfull_width_multiplier` must be greater than zero.
- `walk_us_dist_pct` has no upper bound, so values greater than 1 pass even though the field is documented as a fraction of reach length.
- `centerline_buffer_bankfull_multiplier` has no bound, so zero and negative values pass validation.
- `domain_buffer` must be at least zero.
- `epsg_code` must be a positive integer.
- An authored `domain` must have positive x and y extent and must already lie on the `grid_resolution` lattice.
- Unknown request fields are rejected.
- Caller-supplied reach topology is typed but not checked against graph relationships.
- A `lulc_lookup` dictionary receives integer keys and numeric values, but completeness, positivity, representable code range, and physical plausibility are not enforced.
- `other_geometries` values are strings, but geometry parsing occurs after input validation.

Type validation therefore establishes request shape and selected scalar bounds.
It does not establish source availability, network correctness, datum compatibility, geometry intersection, source immutability, or hydraulic suitability.
It also does not establish that `epsg_code` identifies a projected CRS with metre linear units.
Current geometry lengths, bankfull width, buffers, later slope calculations, per-unit-width discharge, cell area, stored volume, and flooded area assume metre-based projected coordinates unless explicit conversions are applied.
The documented inflow-placement fraction requires `0 < walk_us_dist_pct <= 1`.
The computed-domain corridor requires `centerline_buffer_bankfull_multiplier > 0`, while any finite upper limit remains for the authorized domain-method owner to define.
The current input-contract owner for these scientific ranges is not identified by the reviewed sources.

## Checked-in fallbacks are not necessarily realized inputs

The settings module evaluates `DEFAULT_DEM_SOURCE`, `DEFAULT_LULC_SOURCE`, and `DEFAULT_EPSG_CODE` from environment variables when the process imports that module.
If the variables are unset, checked-in strings supply a 3DEP seamless VRT, an Annual NLCD 2023 Collection 1.0 mosaic, and EPSG:5070.

`BuildModelInputs` then uses those process values as field defaults unless the caller supplies overrides.
The checked-in constant is therefore only a fallback definition.
The process environment can change the field default, and the caller can change the realized request again.

Inspect `model_manifest.inputs` to identify the values recorded for one built artifact.
Do not infer a realized source or CRS from the repository constant alone.

`grid_resolution`, `walk_us_dist_pct`, `bankfull_width_multiplier`, `domain_buffer`, and `centerline_buffer_bankfull_multiplier` use checked-in code defaults rather than environment lookups.
They can still be overridden by the caller.

## Code defaults and selected values are separate authorities

The following comparison prevents a default from being described as methodology merely because the software supplies it.

| Topic | Current checked-in code default | Selected methodology at the reviewed Decision Register revision |
| --- | --- | --- |
| Model resolution | `grid_resolution=10`. | DR-017 ALT-A selects 10 m. |
| Ordinary upstream inflow offset | `walk_us_dist_pct=0.1`. | DR-016 ALT-A selects 0.25 of upstream-reach length. |
| Inflow width | `bankfull_width_multiplier=1.0`, applied to the estimated bankfull width. | DR-015 ALT-A selects a fixed 100 m line. |
| Headwater inflow | A single perpendicular line at the first target-reach coordinate when the upstream mainstem is null and `ds_of_lake=false`. | DR-014 ALT-B selects points distributed along the reach. |
| Initial domain | A computed bbox from available geometry or an exact authored bbox. | DR-011 ALT-D selects the bbox of inflow, downstream STL when available, and buffered centerline. |
| Domain expansion | No build-model expansion behavior exists. | DR-012 ALT-G selects WSE-informed expansion with a 50-bankfull-width limit. |

Agreement in the 10 m row does not make every realized model 10 m because a caller can override the input.
Disagreement in another row does not authorize the handbook to silently replace code behavior or the selected decision.
The reviewed Decision Register selects DR-011 ALT-D, while the standalone DR-011 file also marks ALT-E as `#current`.
That file-marker conflict remains an Open question and does not change the registered selection within its recorded status and scope.

## Step 2: Look up the target and optional mainstem reaches

The job calls `query_reach` for `inputs.reach_id` and requires the fields named by the current constants for reach ID, downstream reach ID, drainage area, stream order, and geometry.
The checked-in field names are `reach_id`, `reach_to_id`, `total_da_sqkm`, `stream_order`, and `geometry`.

The reader requests only the matching `reach_id` and rejects an unavailable dataset, a missing required field, an absent identifier, a duplicate identifier, or a horizontal CRS that does not match `epsg_code` when the source CRS is present.
It then merges a contiguous `MultiLineString` to a `LineString` or raises when the parts cannot be merged into one line.

The job copies `upstream_reach_ids` from the request for later properties.
If `upstream_mainstem_reach_id` is non-null, it independently looks up and normalizes that one reach.

The job does not derive or verify the upstream list from `reach_to_id`.
It does not establish completeness, adjacency, mainstem membership, largest drainage area, direction, or true headwater status.
Those facts remain caller and prepared-network responsibilities.

## Step 3: Realize the roughness lookup

When `lulc_lookup` is already a dictionary, the job uses that dictionary.
When the input is a path or URI string, the job reads JSON and converts every key to an integer.

The normalized dictionary later enters model identity.
Equivalent dictionary content and JSON-path content can therefore produce the same lookup hash even though the recorded input representation differs.

The source path itself is not included in the lookup hash.
This is useful for content equivalence, but reproduction still requires the manifest to preserve the original input and a consumer to retain or reconstruct the resolved dictionary.

## Step 4: Estimate bankfull width

Current code estimates bankfull width from target-reach drainage area as

\[
W_{bf}=2.7A_d^{0.352}
\]

where \(A_d\) is the current `total_da_sqkm` value in km2 and \(W_{bf}\) is in m under the code's documented relation.
The formula is recorded as current implementation, not as proof that the estimate represents every reach or flow state.

The estimated width has two current uses.
It sets inflow-line length after multiplication by `bankfull_width_multiplier`, and it sets computed-domain centerline buffer distance after multiplication by `centerline_buffer_bankfull_multiplier`.
The unmultiplied estimate is rounded to two decimals in manifest properties.

No validation compares the estimate with observed channel or floodplain width.

## Step 5: Construct and inspect the inflow line

`make_inflow_line` follows one of three branches.

1. A lake-outlet request places a perpendicular line within the target reach at `walk_us_dist_pct` of target length from the first coordinate.
2. A null upstream mainstem places a perpendicular line at the first target-reach coordinate.
3. A supplied upstream mainstem places a perpendicular line `walk_us_dist_pct` of mainstem length upstream from the mainstem's downstream end.

All three branches assume meaningful coordinate order and topology.
The line width is the estimated target-reach bankfull width multiplied by `bankfull_width_multiplier`.

A `walk_us_dist_pct` value greater than 1 can clamp or reinterpret interpolation rather than place the line at a documented fraction within the reach.
The request can still continue to artifact construction, so a valid manifest does not prove that the realized inflow occupies the intended fractional position.

The implemented check finds intersections between the target reach centerline and the generated line.
It emits `centerline_inflow_multi_intersection` only when there are more than one.
Zero and one target-reach intersections both produce no warning.

For an ordinary upstream-mainstem line, zero target-reach intersections can be expected because the line lies on the adjacent upstream reach.
The same outcome can also occur when a bad geometry misses the intended channel, so the absence of this warning is not an intersection acceptance test.

## Step 6: Construct the domain and grid

When an authored `domain` is present, the job uses that bbox exactly and derives its anchor and offsets.
When it is absent, the job assembles extra geometries, buffers centerlines, takes total bounds, applies `domain_buffer`, snaps outward, and derives the anchor and offsets.

A zero or negative `centerline_buffer_bankfull_multiplier` can remove the intended buffered target and approach corridor from the computed bounds while the inflow line and any other supplied geometries still allow the build to continue.
Current validation and warnings do not reject or identify that condition.

The number of columns is domain width divided by `grid_resolution`.
The number of rows is domain height divided by `grid_resolution`.
These integer dimensions become `properties.grid` and define both destination raster transforms.

The job emits `large_domain_area` when rectangular area is greater than \(10^9\) square CRS units under the checked-in threshold.
The threshold comment says it still needs tuning.
No current build warning reports a domain that is too small, clips a floodplain, excludes an STL, or needs DR-012 expansion.

## Step 7: Build model identity and output addressing

The current `Identity` object contains exactly these fields.

- `sdr_commit` records the jobs image's baked `SDR_COMMIT` value.
- `reach_geom_hash` records the first eight hexadecimal characters of SHA-256 over the target reach WKT.
- `grid_resolution` records the realized scalar.
- `epsg_code` records the realized horizontal CRS identifier.
- `dem_source_inputs_hash` hashes the DEM source string.
- `lulc_source_inputs_hash` hashes the LULC source string.
- `lulc_lookup_dict_hash` hashes canonical JSON for the realized lookup dictionary.

The job hashes the serialized identity object and truncates the SHA-256 hexadecimal digest to eight characters to create `identity_hash`.
It joins that value to the domain offset string to create `model_id`.

The domain offset string has the form `N...S...E...W...` and records integer cell offsets from the grid-snapped anchor.
It is a domain realization code rather than a content hash.

The job constructs `model_dir` as `base_output_path` with its trailing slash removed, followed by `/model_id/`.
The job itself does not add `reach=<reach_id>`.
A caller that requires reach partitioning must include the reach-specific segment in `base_output_path`.

The address is content-derived only within the fields that the identity and domain code actually cover.
It is not a complete content-addressed guarantee because source strings can name changing data and several output-affecting request values are absent from the identity object.

## Identity gaps that affect scientific reproduction

The current identity hashes DEM and LULC connection strings, not downloaded source bytes, version IDs, object ETags, or source checksums.
The same mutable URL or path can therefore produce different terrain or land-cover content without changing these identity fields.

The current identity object does not include `reach_network_path`, upstream IDs, upstream-mainstem ID, `ds_of_lake`, `walk_us_dist_pct`, `bankfull_width_multiplier`, `centerline_buffer_bankfull_multiplier`, `domain_buffer`, `other_geometries`, or authored `domain`.
Some of those inputs can change the domain code through changed bounds, but equal final offsets do not prove that the same boundary geometry or construction path was used.

The current identity also has no vertical-datum field, terrain-resampling field, or explicit source-content identity.
The manifest retains complete inputs and output checksums, so it is necessary evidence, but its presence does not close these identity gaps.

The baked `SDR_COMMIT` is `826a602ddcaf58bf4081dc04b65ba15b82cc8c8a` in the reviewed jobs checkout.
It differs from the knowledge-base revision reviewed by this handbook, so the recorded identity proves only the revision it names.

## Step 8: Check for an existing manifest

Before extracting rasters, the job checks whether `model_manifest.json` exists at the computed destination.
If it exists, the job parses it with the current `ModelManifest` model and compares its reconstructed `inputs` object with the current validated inputs.

When both checks succeed, the job returns immediately.
It does not re-read asset bytes, verify asset existence, compare asset checksums, re-emit stored warnings, or assess scientific adequacy in this branch.
The returned warnings are only warnings recomputed before the existence check, which currently means inflow multi-intersection and large-domain checks.

When a present manifest is schema valid but its reconstructed inputs do not equal the current request, `_check_model_built` returns false and the job proceeds with a rebuild.
When the manifest is absent or fails current manifest validation, the job also proceeds with a build at the computed destination.
A validation failure is not returned as an existing-model warning.

Several output-affecting inputs are absent from the identity object.
If one of those inputs changes without changing the final domain code, the unequal-input request can rebuild into the same full `model_id` and `model_dir` as the existing manifest.
Examples include topology or boundary-geometry inputs whose changed realization produces the same bbox offsets.
The same-address outcome follows current address coverage and is not proof that the two requests are scientifically equivalent.

The generated job documentation says the existence check returns a warning that the model already exists.
Current code does not implement such a warning class or append one.
This documentation statement must remain visibly classified as unimplemented.

## Step 9: Extract the DEM

The job opens `dem_source`, creates a destination transform from the domain bbox and grid dimensions, reprojects band 1 into a `float32` array, and writes `dem.tif`.
The destination horizontal CRS is `EPSG:<epsg_code>`.

The current call omits an explicit resampling argument, so Rasterio's documented nearest-neighbor default applies.
No vertical datum is supplied, transformed, or recorded.
The terrain asset receives an output checksum, the source connection string, and a retrieval time.

Those fields identify one realized output and source address.
They do not establish source immutability, vertical compatibility, bathymetry, drainage enforcement, structure representation, or terrain adequacy.

## Step 10: Convert land cover to roughness

The job reprojects the realized LULC source to the same bbox, rows, columns, and horizontal CRS as the DEM.
It then casts the reprojected array to `uint8`, indexes a 256-entry lookup array, and writes `roughness.tif` as `float32`.

The lookup array uses `-9999` for unpopulated post-cast indices.
The current job does not reject that negative result and emits no unmapped-class warning.
Pre-cast negative, fractional, or out-of-range values can truncate or wrap into apparently valid `uint8` indices.

The generated documentation says the job checks for similar roughness values.
Current code contains only an unimplemented marker for that check.
It does not emit a similar-roughness warning.

## Step 11: Write vector assets

The job writes the following current files into the temporary directory.

| Filename | Manifest role | Current content |
| --- | --- | --- |
| `reach.geojson` | `centerline` | The target prepared-network reach and required attributes. |
| `inflow.geojson` | `inflow_line` | The constructed perpendicular line. |
| `anchor.geojson` | `reach_centroid` | The grid-snapped domain anchor, not necessarily the exact centroid. |
| `domain.geojson` | `domain` | The rectangular bbox polygon and offset fields. |

The vector assets use `epsg_code` as their output horizontal CRS.
The reach and inflow assets record `reach_network_path` as their source URL.
The anchor and domain assets also record that path even though they are computed geometries.

The current `Assets` schema does not include `outflow_area.geojson` or an STL.
Those can be present in a broader fixture or scenario workflow without being current `build_model` outputs.

## Step 12: Relocate asset hrefs and construct properties

For every asset, `_create_copy_job` keeps only the filename and replaces its temporary href with `model_dir/filename`.
It also builds a temporary-to-destination copy mapping.

The properties block records grid rows and columns, drainage area, estimated bankfull width, caller-supplied upstream IDs, stream order, reach length, and caller-supplied upstream-mainstem ID.

The current assignment to `properties.downstream_reach_id` uses the target `reach_id` field rather than the required network `reach_to_id` field.
Checked-in manifests therefore can repeat the modeled reach identifier in the downstream field even when `reach.geojson` records a different `reach_to_id`.
Use the authoritative prepared-network field for downstream adjacency until this contract is corrected or redefined.

## Step 13: Create the manifest

The job creates `model_manifest.json` with the following major blocks.

- Top-level producer fields record type, hash algorithm, package version, creation time, reach ID, identity hash, domain code, and model ID.
- `inputs` records the validated request, including defaults that Pydantic realized for the current request.
- `domain` records bbox, anchor, and offsets.
- `identity` records the seven identity inputs.
- `properties` records grid dimensions and reach attributes.
- `assets` records six role-keyed artifact hrefs and integrity metadata.
- `warnings` records the implemented non-fatal build warnings emitted before manifest creation.

The code writes the manifest to the temporary directory after all other temporary assets exist.
It then adds the manifest to the copy mapping after the asset mappings, so Python's insertion-preserving dictionary iteration currently copies the six assets before the manifest.

Publication is sequential rather than transactional.
Each destination file is replaced through its own `fsspec` write, and the code provides no directory-level staging, atomic promotion, rollback, or cleanup of already copied files.
If an asset copy fails, earlier assets can remain at the destination while later assets and the new manifest are absent.
If a same-address rebuild replaces one or more assets before the final manifest copy, readers can temporarily encounter the older manifest paired with newly replaced assets.
If the final manifest copy fails, that mixed state can remain after the job raises.

The manifest is an inventory and provenance record.
It is not an acceptance certificate, hydraulic result, domain-expansion result, validation report, or proof that every named remote destination remains available later.

## Step 14: Copy to the output address

The job iterates over the copy mapping and uses `fsspec` streams to copy each file to its destination.
Any copy failure raises `WriteFailureError` and prevents the normal result return.

A normal new-build return therefore follows completion of the copy loop in this process.
The return object itself still does not independently observe the destination after copying, and the existing-manifest branch does not verify assets.
The manifest-last order narrows the normal new-address visibility window because a first build has no manifest until all six assets have copied.
It does not make publication atomic, repair partial files, prevent mixed generations at a reused address, or guarantee that a storage observer cannot read intermediate objects.

In the target reconciliation pattern, observed storage is the materialization authority.
A job result naming `model_dir` must not be treated as independent proof that a complete, current, scientifically adequate model is materialized.

## Step 15: Interpret the result fields

`BuildModelResult` contains four fields.

| Field | What it supports | What it does not prove |
| --- | --- | --- |
| `identity_hash` | It identifies the current seven-field identity object under the current hash recipe. | It does not identify mutable source bytes, every output-affecting input, or hydraulic adequacy. |
| `model_id` | It combines the identity hash with the domain offset code used for the intended folder. | It does not prove that the folder exists, is complete, or contains matching assets. |
| `model_dir` | It names the destination address the job used or found. | It does not independently observe storage materialization. |
| `warnings` | It carries warnings emitted by implemented checks during this call. | An empty list does not prove valid roughness, vertical datum, unclipped domain, correct topology, or current asset integrity. |

## Model-development chain

![Model-development chain from prepared inputs to an addressed model directory](../assets/model-development-chain.svg)

**What to notice:** Prepared network and source rasters enter different transformation paths.
Geometry determines the rectangular domain and boundary artifacts, while the same domain grid controls terrain and roughness extraction.
The figure groups the six assets and manifest as one logical publication set, but current code copies that set sequentially and does not publish it atomically.
The manifest records inputs, identity, properties, warnings, and output checksums, but a separate observation and scientific review remain necessary before treating the directory as materialized and adequate.

The figure is registered as [VIS-006](../assets/source-register.md#vis-006-model-development-chain).

## Current warning matrix

| Condition | Current outcome |
| --- | --- |
| The inflow line intersects the target reach more than once. | The build can continue with `centerline_inflow_multi_intersection`. |
| The domain area exceeds the checked-in threshold. | The build can continue with `large_domain_area`. |
| The inflow line intersects the target reach zero times. | No build warning is emitted. |
| A resolved roughness value is `-9999`. | No dedicated build warning or error is emitted. |
| Roughness values are unusually similar. | Generated documentation describes a warning, but current code leaves the check unimplemented. |
| The destination manifest already exists with equal inputs. | The job returns early without an existing-model warning. |
| The destination manifest is schema valid but has unequal inputs. | The job rebuilds, potentially at the same full address when omitted identity inputs change without changing the domain code. |
| An asset or manifest copy fails. | The job raises, but earlier destination writes are not rolled back and can leave a partial or mixed-generation directory. |
| The domain clips a connected floodplain. | No build warning or expansion loop is implemented. |
| A terrain or WSE vertical datum is missing or incompatible. | No build validation or warning is implemented. |
| Source content changes behind the same DEM or LULC string. | No source-content identity warning is implemented. |

## Scientific readiness after a successful build

A model build is ready for scenario preparation only after additional evidence addresses the following questions.

1. Does the prepared network prove reach direction, topology, mainstem selection, and source lineage?
2. Is the realized horizontal CRS projected with metre linear units, or are explicit conversions documented for every affected geometric and raster calculation?
3. Are DEM, roughness, vector, STL, and boundary data horizontally aligned and vertically compatible?
4. Are terrain, bathymetry, structures, and drainage connections adequate for the intended reach and result?
5. Are all roughness values positive, mapped, reviewable, and supported by calibration or sensitivity evidence where needed?
6. Does the inflow intersect connected conveyance at the intended location and width?
7. Does the domain include the relevant floodplain and transfer geometry for the largest intended scenario?
8. Are outflow and stage-transfer cells constructed and reviewed later under the correct scenario contracts?
9. Does independent storage observation verify the manifest and every required asset at the predicted address?
10. Does the recorded identity cover immutable source content and the methodology revision needed for reproduction?

## Common misconceptions

### A validated request is a validated model

Pydantic validation establishes a bounded software contract.
It does not validate graph topology, source content, datums, or hydraulic sufficiency.

### A source-string hash pins the source

The hash pins the string.
It does not pin content that can change behind that string.

### Empty warnings mean the model passed QC

Only two build warnings are currently implemented, and neither is a complete model-quality review.

### The manifest downstream ID is authoritative

The current assignment repeats the modeled reach ID instead of using `reach_to_id`.

### `reach_centroid` is the exact centroid

The asset contains the centroid floored to the grid.

### A returned directory is observed materialization

The result names the intended address.
Storage observation, asset integrity, and scientific review are separate evidence gates.

## Competency check

1. Which input values come from environment-evaluated fallbacks, and where are realized values recorded?
2. Which topology claims does the job trust rather than derive?
3. Which selected inflow values differ from code defaults?
4. Which seven fields currently define `identity_hash`?
5. Why can mutable DEM content change without changing model identity?
6. What does the existing-model branch verify, and what does it skip?
7. Which current artifact role points to `anchor.geojson`, and why is that name misleading?
8. Why is `properties.downstream_reach_id` not current authoritative topology evidence?
9. Which warning statements in generated documentation are not implemented?
10. Why does a normal result not establish scientific adequacy or independent storage materialization?
11. Which scalar bounds must be enforced before inflow placement and computed-domain construction can satisfy their documented geometry contracts?

## Source notes

- **Current implementation:** The complete job trace, input model, geometry transformations, identity, warnings, copy behavior, result fields, and fixture tests are indexed under [JOB-009](../reference/bibliography.md#job-009-build-model-lifecycle-artifacts-and-existing-model-behavior).
- **Current transformation details:** Terrain and roughness behavior is also indexed under [JOB-008](../reference/bibliography.md#job-008-model-development-input-transformations-and-warnings).
- **Selected methodology:** Domain, inflow, grid, expansion, and STL decisions are indexed under [SDR-010](../reference/bibliography.md#sdr-010-domain-boundary-and-expansion-decisions).
- **Checked-in fixture:** Lab 8 evidence is indexed under [ART-001](../reference/bibliography.md#art-001-checked-in-build-model-test-fixture).
- **Target design and observed-state boundary:** The checked-in reconciler consumer is indexed under [SYS-005](../reference/bibliography.md#sys-005-checked-in-reconciler-build-model-caller).
- **Open questions:** Scalar bounds, identity, existing-model, warning, clipping, datum, downstream assignment, and roughness gaps are preserved in [Conflicts and Open Questions](../reference/conflicts-and-open-questions.md).
