# Lab 8 Solution: Inspect a Built-Model Fixture

This solution records one bounded inspection of the checked-in fixture.
It does not prove current production provenance, current-job execution, durable storage materialization, or hydraulic adequacy.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, optional user-run commands, and stopping criteria are defined in [Lab 8](../lab-08-inspect-a-built-model.md).
Use this solution only after attempting that prompt.
The reasoning uses only checked-in fixture evidence and cited local sources and requires no production access.
No command is required to use the solution, and any optional prompt command remains user-run with results bounded to the inspected checkout.
Stop when the answer satisfies the prompt's competency criteria or retains the bounded fixture verdict with the missing evidence stated.

## 1. Manifest and schema observations

The artifact is a checked-in test fixture at:

```text
twod-fim-jobs/tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/
```

The main top-level observations are:

| Field | Fixture value |
| --- | --- |
| `type` | `model`. |
| `twod_fim_version` | `0.1.0`. |
| `created_at` | `2026-09-04T13:59:29.233581`. |
| `reach_id` | `1257410937935512`. |
| `identity_hash` | `10850311`. |
| `domain_code` | `N48S45E47W42`. |
| `model_id` | `10850311_N48S45E47W42`. |

The serialized request names the checked-in reach-network fixture, one upstream reach, that same upstream reach as mainstem, a reach-partitioned output base path, the 3DEP seamless VRT string, a checked-in coarse LULC raster, an empty extra-geometry list, zero domain buffer, a 100 m grid, a 0.1 upstream-walk fraction, EPSG:5070, a bankfull-width multiplier of 1.0, and the complete lookup dictionary.

The serialized fixture does not contain current `BuildModelInputs` keys `domain`, `ds_of_lake`, or `centerline_buffer_bankfull_multiplier`.
A current schema parser can reconstruct omitted optional fields from current defaults, but those reconstructed values are not serialized fixture observations.
The fixture may therefore represent an earlier contract shape even when current default reconstruction makes it readable.

The domain block records bbox `[-2062400, 2804600, -2053500, 2813900]`, anchor `[-2058200, 2809100]`, and offsets `[48, 45, 47, 42]`.
The properties block records 93 rows and 89 columns.

The identity contains only the baked SDR revision, reach-geometry hash, grid resolution, EPSG code, DEM source-string hash, LULC source-string hash, and realized lookup hash.
It omits upstream IDs, inflow offset and width parameters, the network path, `other_geometries`, domain buffer, centerline buffer, lake-outlet flag, and authored domain.

The six manifest asset roles are `terrain`, `roughness`, `centerline`, `inflow_line`, `reach_centroid`, and `domain`.
`outflow_area.geojson` is present in the fixture directory but is not named by the model manifest.
The warning list is empty.

## 2. Raster-alignment table

| Property | `dem.tif` | `roughness.tif` | Comparison |
| --- | --- | --- | --- |
| Driver | GeoTIFF. | GeoTIFF. | Same. |
| Data type | Float32. | Float32. | Same. |
| Width by height | 89 by 93. | 89 by 93. | Both match 89 manifest columns and 93 rows. |
| Horizontal CRS | EPSG:5070, NAD83 / Conus Albers. | EPSG:5070, NAD83 / Conus Albers. | Both match the manifest input. |
| Transform | `[-2062400, 100, 0, 2813900, 0, -100]`. | `[-2062400, 100, 0, 2813900, 0, -100]`. | Exactly aligned. |
| Bounds | West -2062400, south 2804600, east -2053500, north 2813900 m. | Same. | Both match the manifest and domain polygon. |
| Cell size | 100 m by 100 m. | 100 m by 100 m. | Both match `grid_resolution=100`. |
| Nodata | -999999. | 250. | Different role-specific sentinels. |

The x calculation is \(({-2053500}-({-2062400}))/89=8900/89=100\) m.
The y calculation is \((2813900-2804600)/93=9300/93=100\) m.

This evidence establishes horizontal alignment for the two checked-in rasters and the rectangular domain.
It does not identify the DEM vertical datum, prove that no vertical transformation was needed, show which resampling operation produced this fixture, validate terrain or structures, prove all original classes were mapped correctly, or establish roughness calibration.

## 3. Vector-role and geometry table

| File | Geometry | Features | EPSG | Extent or point | Important fields or interpretation |
| --- | --- | ---: | ---: | --- | --- |
| `anchor.geojson` | Point. | 1 | 5070 | `(-2058200, 2809100)`. | Fields `x` and `y` reproduce `domain.anchor`. |
| `domain.geojson` | Polygon. | 1 | 5070 | `[-2062400, 2804600, -2053500, 2813900]`. | Fields reproduce bbox, offsets, and `N48S45E47W42`. |
| `inflow.geojson` | LineString. | 1 | 5070 | `[-2056111.157, 2806982.317, -2055874.146, 2807182.296]`. | Field `ind`; the line is inside the domain. |
| `reach.geojson` | LineString. | 1 | 5070 | `[-2059232.444, 2807794.481, -2056698.910, 2810758.509]`. | Fields include reach ID, downstream ID, drainage area, and stream order. |
| `outflow_area.geojson` | Polygon. | 1 | 5070 | `[-2062600, 2809873.145, -2057231.880, 2814100]`. | It is outside the current model-manifest asset contract. |

The domain polygon agrees with the manifest bbox and its fields agree with the offset code.
The anchor point agrees with `domain.anchor`.
It is a reach centroid floored to the 100 m grid, so the manifest role `reach_centroid` is incomplete and can be mistaken for the exact geometric centroid.

The inflow line lies inside the bbox.
Its extent does not overlap the target reach extent, so it does not intersect `reach.geojson`.
That is consistent with an inflow located on a separate upstream mainstem, but the fixture does not include the upstream-mainstem geometry as a model asset.
The correct next check is to inspect reach `1257410962372414` in the exact prepared-network version and verify line intersection, coordinate direction, adjacency, and mainstem selection.

`reach.geojson` records `reach_to_id=1257412149032946`, while the manifest records `properties.downstream_reach_id=1257410937935512`, which is the modeled reach itself.
The prepared-network field is the relevant fixture evidence for downstream adjacency.
The manifest field demonstrates the current assignment issue and must not be used as authoritative topology evidence.

The outflow polygon extends 200 m west of the domain and 200 m north of the domain.
That can be consistent with a later two-cell square buffer around selected domain edges at 100 m resolution, but spatial plausibility does not prove production history.
The model manifest does not name the file.
A producing scenario request or manifest, current derivation code, file checksum, and lineage record are needed before assigning it a current scenario role.

## 4. Checksum and identity classification

The checked-in full SHA-256 values begin as follows.

| Manifest role | Full checksum prefix observed | Recorded first 16 characters | Match? |
| --- | --- | --- | --- |
| `terrain` | `37f0d79d0c8eb3df...` | `37f0d79d0c8eb3df`. | Yes. |
| `roughness` | `4492aa8baecc2cd6...` | `4492aa8baecc2cd6`. | Yes. |
| `centerline` | `5cbb7e8aa6a6621f...` | `5cbb7e8aa6a6621f`. | Yes. |
| `inflow_line` | `d7e12795c14a7ca0...` | `d7e12795c14a7ca0`. | Yes. |
| `reach_centroid` | `6c17f9231162eb97...` | `6c17f9231162eb97`. | Yes. |
| `domain` | `c8d6faba00c59582...` | `c8d6faba00c59582`. | Yes. |

Canonical JSON hashing of the recorded identity object begins with `10850311`, which agrees with `identity_hash` under the current recipe.

| Statement | Classification | Reason |
| --- | --- | --- |
| The checked-in bytes of every named asset match the fixture checksum. | **Supported by fixture.** | Fresh SHA-256 inspection agrees for all six named files. |
| The identity hash can be reconstructed from the recorded identity fields and hash recipe. | **Requires current code.** | The fixture supplies fields, while the code supplies canonical serialization, SHA-256, and eight-character truncation behavior. |
| Identity pins exact DEM and LULC source bytes. | **Unsupported conclusion.** | It hashes source strings, not source bytes or immutable object versions. |
| Identity includes inflow, topology, extra-geometry, and authored-domain inputs. | **Unsupported conclusion.** | Those fields are absent from the identity object. |
| Output checksums can detect a later byte change to checked-in assets. | **Supported by fixture.** | Recalculation can compare current bytes with the recorded digest prefix. |
| Matching checksums prove scientific adequacy. | **Unsupported conclusion.** | Integrity proves byte agreement, not topology, datum, calibration, boundaries, clipping, or validation. |

## 5. Warning and missing-evidence table

| Condition | Proved by empty warnings? | Next required evidence |
| --- | --- | --- |
| Inflow intersects intended channel exactly once. | No. | Inspect the exact upstream-mainstem geometry, rasterized forcing cells, and connected conveyance. |
| Domain contains the largest relevant floodplain. | No. | Review the largest intended scenario's wet extent, WSE, terrain, edge roles, and domain sensitivity. |
| Domain does not require expansion. | No. | Apply an authorized expansion diagnostic and stop rule to persisted scenario evidence. |
| DEM has a known compatible vertical datum. | No. | Obtain source metadata and a model-level vertical-datum contract, then compare all WSE inputs and observations. |
| Every roughness value is positive and mapped from a valid class. | No. | Validate pre-cast source values, nodata, lookup coverage, post-conversion values, and transformation provenance. |
| Roughness variability is plausible. | No. | Review land-cover context, channel treatment, calibration, sensitivity, and scale. |
| Prepared topology and downstream assignment are correct. | No. | Validate the versioned prepared network and resolve the manifest downstream assignment mismatch. |
| Every named asset remains in durable storage. | No. | Observe the configured storage location and verify every asset against its manifest checksum. |
| Model is ready for ND and KWSE use. | No. | Complete data, geometry, boundary, sensitivity, operational, and intended-use acceptance reviews. |

The empty list establishes only that no implemented build warning was recorded in this fixture.
It does not show which current checks ran under a current job, and several required checks do not exist in current build code.

## 6. Evidence verdict

**FIXTURE INSPECTED, MODEL ADEQUACY NOT ESTABLISHED.**

The fixture supports a bounded conclusion that its six manifest-named files are present in the checkout, match recorded checksum prefixes, use horizontally aligned EPSG:5070 geometry and rasters, and agree with the recorded 100 m, 89 by 93 rectangular grid.
It also exposes a stale serialized input shape, the anchor role mismatch, the downstream assignment issue, an unmanifested outflow-area file, mutable source identity, absent vertical-datum evidence, and an empty warning list with limited meaning.

The smallest next evidence set includes:

1. A versioned prepared-network record that validates direction, adjacency, mainstem selection, and reach lineage.
2. Immutable terrain and LULC source identities plus source metadata, including vertical datum and units.
3. Complete roughness source-value, nodata, lookup, output-value, calibration, and sensitivity checks.
4. Inflow, outflow, STL, edge-cell, and largest-scenario domain-overlap review.
5. Domain-expansion or domain-sensitivity evidence under authorized criteria.
6. Independent observation of manifest and asset materialization at the intended storage address.
7. Hydraulic scenario evidence, numerical and mass-balance review, comparison evidence, uncertainty, and intended-use acceptance criteria.
