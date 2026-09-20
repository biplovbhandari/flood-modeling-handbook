# Lab 8: Inspect a Built-Model Fixture

This lab uses a checked-in test fixture to practice model-manifest and geospatial-artifact inspection without running `build_model`.
The directory is test data in the reviewed checkout.
Its presence and timestamp do not establish that it came from a current production job, that it matches the current schema exactly, that its remote sources are still identical, or that it is hydraulically adequate.

## Prerequisites

Complete these chapters before starting:

- [Network Preparation](../04-model-development/01-network-preparation.md)
- [Terrain, Topobathymetry, and Structures](../04-model-development/02-terrain-topobathymetry-and-structures.md)
- [Roughness and Land Cover](../04-model-development/03-roughness-and-land-cover.md)
- [Domain and Boundary Geometry](../04-model-development/04-domain-and-boundary-geometry.md)
- [The Build-Model Job](../04-model-development/05-build-model-job.md)

Read [Source Authority](../reference/source-authority.md) and [Lab Conventions](README.md).

## Execution boundary

The required work is **Core inspection**.
Commands explicitly labeled below are **User-run optional** and inspect checked-in files only.
No build, solver, test, infrastructure, deployment, or production access is required.

## Learning objectives

After completing this lab, the learner should be able to:

- inspect a model manifest as an inventory and provenance record;
- compare manifest grid fields with raster dimensions, transform, bounds, resolution, and CRS;
- compare vector geometry roles, CRS, extents, and attributes;
- verify output checksums without confusing integrity with scientific validity;
- identify identity coverage and missing identity dimensions;
- identify the anchor versus `reach_centroid` mismatch and the downstream-reach assignment issue;
- separate implemented warnings from missing checks; and
- state what artifact presence cannot establish.

## Checked-in fixture

Use this directory relative to the `twod-fim-jobs` repository root:

```text
tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/
```

The expected files are:

```text
anchor.geojson
dem.tif
domain.geojson
inflow.geojson
model_manifest.json
outflow_area.geojson
reach.geojson
roughness.tif
```

Treat this list as a fixture inventory, not as a list of current `build_model` manifest assets.

## Part A: Inspect the manifest contract

**Core inspection:** Read `model_manifest.json` and record the following fields.

1. Record `type`, `twod_fim_version`, `created_at`, `reach_id`, `identity_hash`, `domain_code`, and `model_id`.
2. Record the requested network path, source strings, grid resolution, EPSG code, upstream IDs, inflow placement values, and output base path under `inputs`.
3. Record the bbox, anchor, offsets, rows, and columns.
4. Record all identity fields and state which request values are absent from identity.
5. Record every manifest asset role and filename.
6. Record the warning list.
7. Identify fixture files that are not named by the manifest.
8. Compare the serialized `inputs` keys with the current `BuildModelInputs` model and identify any current fields omitted by this fixture.

Do not fill an omitted serialized field with a current default without labeling that operation as schema reconstruction rather than an observed fixture value.

## Part B: Inspect raster alignment

**Core inspection:** Inspect `dem.tif` and `roughness.tif` and make one comparison table with these fields.

- Record driver, data type, nodata value, width, height, horizontal CRS, affine transform, and bounds.
- Calculate x and y cell size from bounds and dimensions.
- Compare dimensions with `properties.grid`.
- Compare bounds with `domain.bbox` and `domain.geojson`.
- Compare resolution and CRS with manifest inputs.

State whether the two rasters are horizontally aligned.
Then state whether the same evidence identifies a vertical datum or proves appropriate resampling, terrain quality, roughness calibration, or complete lookup coverage.

**User-run optional:** From the `twod-fim-jobs` repository root, the learner may run these read-only inspection commands.

```bash
gdalinfo -json tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/dem.tif
gdalinfo -json tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/roughness.tif
```

These commands inspect checked-in files and do not run a model.

## Part C: Inspect vector roles and geometry

**Core inspection:** For `anchor.geojson`, `domain.geojson`, `inflow.geojson`, `reach.geojson`, and `outflow_area.geojson`, record geometry type, feature count, CRS, extent, and attribute names.

Answer these questions.

1. Does `domain.geojson` match the manifest bbox and domain code fields?
2. Does `anchor.geojson` match `domain.anchor`, and why is the manifest role `reach_centroid` incomplete as a description?
3. Does the inflow line lie inside the domain?
4. Does the inflow line intersect the target `reach.geojson`, and is that enough to decide whether it intersects the intended upstream mainstem?
5. What does `reach.geojson.properties.reach_to_id` say, and how does it compare with `properties.downstream_reach_id` in the manifest?
6. Is `outflow_area.geojson` named by the current model manifest?
7. Does the outflow-area extent remain inside the domain, and what would be needed to establish its production history and scenario role?

**User-run optional:** The learner may inspect vector summaries with:

```bash
ogrinfo -ro -so -al tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/domain.geojson
ogrinfo -ro -so -al tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/reach.geojson
ogrinfo -ro -so -al tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/inflow.geojson
ogrinfo -ro -so -al tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/anchor.geojson
ogrinfo -ro -so -al tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/outflow_area.geojson
```

## Part D: Verify integrity and assess identity

**Core inspection:** Compare the first 16 hexadecimal characters of SHA-256 for every manifest asset with its recorded checksum.
Classify each conclusion below as **Supported by fixture**, **Requires current code**, **Missing evidence**, or **Unsupported conclusion**.

1. The checked-in bytes of every named asset match the checksum recorded by the fixture manifest.
2. `identity_hash=10850311` can be understood only from the fields recorded under `identity` and the current canonical hash recipe.
3. The identity pins the exact DEM and LULC source bytes used in 2026.
4. The identity includes inflow offset, inflow width, upstream-mainstem ID, `other_geometries`, and authored domain.
5. The output checksums can detect a later byte change to the checked-in assets.
6. Matching checksums prove the DEM, roughness, domain, and boundaries are scientifically adequate.

**User-run optional:** The learner may calculate full checksums with:

```bash
shasum -a 256 tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/dem.tif tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/roughness.tif tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/reach.geojson tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/inflow.geojson tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/anchor.geojson tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/domain.geojson
```

## Part E: Interpret warnings and missing evidence

**Core inspection:** The fixture warning list is empty.
Create a table with one row for each condition below and state whether an empty current build-warning list proves that condition passed.

- The inflow intersects the intended channel exactly once.
- The domain contains the largest relevant floodplain.
- The domain does not require expansion.
- The DEM has a known and compatible vertical datum.
- Every roughness value is positive and mapped from a valid source class.
- Roughness variability is hydraulically plausible.
- The prepared topology and downstream assignment are correct.
- Every named asset remains present in durable storage.
- The model is ready for ND and KWSE scenario use.

For each row, identify the next evidence needed.

## Part F: Issue an evidence verdict

**Core inspection:** Choose exactly one verdict.

- `FIXTURE INSPECTED, MODEL ADEQUACY NOT ESTABLISHED` means the checked-in files support a bounded artifact and contract inspection, but missing scientific and operational evidence prevents acceptance.
- `MODEL ADEQUATE` means the supplied fixture alone establishes topology, data quality, datum compatibility, domain and boundary adequacy, source immutability, storage materialization, and intended-use validation.

State the verdict first.
Then list the smallest evidence set that would move the fixture from artifact inspection toward a scientifically reviewable model.

## Deliverable

Submit a short answer with these sections:

1. Manifest and schema observations.
2. Raster-alignment table.
3. Vector-role and geometry table.
4. Checksum and identity classification.
5. Warning and missing-evidence table.
6. Direct evidence verdict.

## Competency criteria

The lab is complete when the answer:

- labels the directory as a checked-in fixture rather than current-job production evidence;
- identifies EPSG:5070, a 100 m square grid, 89 columns, 93 rows, and matching raster and domain bounds;
- identifies `anchor.geojson` as the grid-snapped anchor exposed under `reach_centroid`;
- identifies the fixture manifest downstream ID as the modeled reach while `reach.geojson` records a different `reach_to_id`;
- identifies `outflow_area.geojson` as present but absent from the model manifest;
- distinguishes source-string identity from source-content identity;
- does not interpret empty warnings or matching checksums as scientific adequacy; and
- issues `FIXTURE INSPECTED, MODEL ADEQUACY NOT ESTABLISHED`.

After completing the lab, compare the reasoning with [Lab 8 Solution](solutions/lab-08-inspect-a-built-model-solution.md).
