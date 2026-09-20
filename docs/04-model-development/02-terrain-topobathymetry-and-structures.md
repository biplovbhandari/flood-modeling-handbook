# Terrain, Topobathymetry, and Structures

Terrain supplies the elevations that define storage, pathways, barriers, and depth, but a terrain raster is not a complete hydraulic representation of submerged channels, bridges, culverts, or drainage connections.
Every terrain choice therefore needs a horizontal grid contract, a vertical reference contract, source provenance, transformation history, and an explicit statement of what structures or below-water geometry are absent.

## Why this topic matters

A small elevation difference can open or close a flow path, move the wetting front, change storage, and alter upstream water-surface elevation.
A model can be numerically stable while still being hydraulically wrong because its terrain has the wrong datum, an unsuitable resampling method, a blocked culvert, an erased bridge contraction, or an unrepresented channel bed.

## Prerequisites

Read [Quantities, Units, and Datums](../00-orientation/03-quantities-units-and-datums.md), [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md), and [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md).
Use [Equations and Units](../reference/equations-and-units.md) for the depth and terrain-error relations.

## Learning objectives

After this chapter, the reader should be able to:

- distinguish source resolution, model-grid resolution, vertical precision, vertical accuracy, and vertical datum;
- explain why horizontal reprojection does not transform or document the vertical datum;
- evaluate raster resampling and alignment in terms of hydraulic consequences;
- explain the limits of bare-earth and hydroflattened DEMs;
- distinguish ordinary topographic lidar from topobathymetric measurement;
- identify how bridges, culverts, and drainage enforcement affect conveyance; and
- map selected terrain decisions and current code defaults without resolving their conflicts silently.

## Horizontal and vertical resolution answer different questions

Horizontal resolution describes the spacing or footprint of elevation samples or raster cells on the map.
It controls which channels, berms, roads, swales, and structure approaches can be represented spatially.

Vertical resolution describes the increment or precision with which elevation values are stored or reported.
Vertical accuracy describes agreement with the actual elevation under a stated reference and test method.
A raster can store millimeter-sized numeric increments without being accurate to a millimeter.

Source resolution and model-grid resolution are also different.
A source DEM at approximately 10 m spacing can be reprojected to a 10 m projected grid, but the source and destination cells still differ in origin, shape, orientation, and sampled support.
Matching the nominal number 10 does not make the cells coincident or create new information.

**Selected methodology:** DR-017 ALT-A has Alternate Selected status and selects a 10 m horizontal modeling resolution.
The decision record identifies compute cost and mapping detail as trade-offs, but it does not establish that 10 m is adequate for every controlling feature or reach class.

**Current implementation:** `BuildModelInputs.grid_resolution` defaults to 10 in the units of the destination horizontal CRS.
The same value defines the destination terrain and roughness grid.
`DEFAULT_EPSG_CODE` is an environment-dependent fallback evaluated when the process imports the settings module, with checked-in fallback value 5070 when the environment variable is unset.
The caller can override `BuildModelInputs.epsg_code`, so the realized `model_manifest.inputs.epsg_code` and output raster CRS must be inspected to know the CRS actually used.
Current input validation requires only a positive EPSG integer and does not verify that the selected CRS is projected or uses metres.

**Horizontal-unit contract:** Current geometry lengths, bankfull-width lines and buffers, slope lengths, per-unit-width discharge, square-cell area, stored water volume, and flooded-area calculations assume a projected horizontal CRS with metre linear units unless each affected operation applies an explicit conversion.
A geographic or foot-based caller override can pass current EPSG validation while making those calculations dimensionally wrong.
Scientific use therefore requires confirmation of a metre-based projected CRS or documented conversions before model construction or scenario interpretation.

## CRS and vertical datum form separate contracts

A horizontal CRS defines horizontal position, orientation, and units.
A vertical datum defines the reference surface from which an elevation is measured.
One EPSG code for a horizontal CRS does not automatically document the elevation datum of every raster value.

Before terrain enters a hydraulic model, record at least the following information.

- Record the source horizontal CRS and datum.
- Record the destination horizontal CRS, affine transform, extent, and resolution.
- Record the elevation units and vertical datum.
- Record any geoid, ellipsoid, tidal, or local-datum transformation and its grid or model version.
- Record whether the values represent bare earth, a water surface, a channel bed, or a blended product.

**Authoritative source:** The USGS 1/3 arc-second 3DEP collection is a bare-earth elevation product at approximately 10 m spacing.
USGS states that its conterminous United States elevations are in meters referenced to NAVD88 and its horizontal coordinates use NAD83 geographic coordinates.
That statement is scoped to the conterminous United States and does not extend that vertical-reference claim to other regions.
Source metadata remains controlling for every retrieved tile or service result.

**Current implementation:** `build_model` reprojects the source raster horizontally into `EPSG:{epsg_code}`.
It has no vertical-datum input, no vertical-transformation step, and no manifest field that names the terrain vertical datum.
The output CRS therefore documents the horizontal reference only, while the elevation values retain whatever vertical meaning the source supplied.

**Hydraulic consequence:** A constant datum offset shifts both modeled WSE and reported terrain reference when consistently applied, but mixing incompatible terrain, stage, boundary, or observation datums changes computed depth and stage comparisons directly.

## The checked-in DEM fallback and provenance boundary

**Selected methodology:** DR-018 ALT-A has Alternate Selected status and selects USGS 3DEP as the terrain source.

**Current implementation:** `DEFAULT_DEM_SOURCE` is an environment-dependent fallback evaluated when the process imports the settings module.
Its checked-in fallback value is `https://prd-tnm.s3.amazonaws.com/StagedProducts/Elevation/13/TIFF/USGS_Seamless_DEM_13.vrt` when `DEFAULT_DEM_SOURCE` is unset, and a caller-supplied `dem_source` overrides that process value for the job.
The checked-in path points to the seamless 1/3 arc-second 3DEP product, which USGS describes as continually updated as source data improve.
It is therefore neither an unconditional current source nor a permanently pinned dataset version.

Inspect `model_manifest.inputs.dem_source` to identify the source connection string realized by a completed build.
Inspect `model_manifest.inputs.epsg_code` and the terrain raster CRS to identify the realized horizontal CRS rather than inferring either value from checked-in fallbacks.
The terrain asset's `source_url`, retrieval time, and checksum provide additional realized-artifact evidence.

The model identity hashes the source connection string, not the remote source content.
The realized terrain asset records the source string, retrieval time, and a truncated checksum of the output file.
Two downloads from an unchanged realized URL can therefore receive the same source-string identity input while producing different terrain content and output checksums.
ISU-011 records this project reproducibility concern as scoped issue evidence.

Reproducible preparation should pin a source release, tile set, object version, or content digest that can be retrieved again.
The realized output checksum is useful integrity evidence, but it does not by itself identify which upstream source objects produced the raster.

## Resampling and alignment are hydraulic operations

Reprojection maps destination cell locations back to a source grid and assigns destination values with a resampling rule.
The rule is part of the terrain method because it can preserve, smooth, displace, or erase controlling elevations.

Nearest-neighbor resampling selects a nearby source value without averaging.
It preserves existing values but can create blocky boundaries and alias a narrow feature when grids are shifted or rotated.
Bilinear and cubic methods interpolate continuous elevation but can lower a narrow ridge or raise a narrow channel.
Average, minimum, maximum, and other aggregation rules each preserve a different property and can create different conveyance.

**Current implementation:** `twod_fim_jobs/utils/geospatial.py` uses one `_extract_raster` routine for DEM and LULC reprojection.
The call to `rasterio.warp.reproject` does not pass a `resampling` argument.
Rasterio documents the default as `Resampling.nearest` and warns that nearest neighbor may not suit continuous data.
The current output array is `float32`, and the destination transform is created from the model bounding box and requested row and column counts.

The current implementation therefore uses nearest-neighbor reprojection for the continuous DEM as well as the categorical LULC raster.
That statement describes the checked-out code and does not endorse nearest neighbor as the selected terrain policy.

An alignment review should inspect source and destination transforms, not only cell size.
It should also sample the centerline, banks, levees, roads, structure openings, domain edges, and known low pathways after transformation.

## Bare-earth does not mean hydraulically complete

A bare-earth DEM attempts to represent ground after removing vegetation and many above-ground objects.
It is not a surveyed channel-and-structure model.

USGS 3DEP specifications require bridge decks to be removed from the bare-earth surface and require a logical interpolated surface below a bridge.
The same specifications leave the roadway intact over culverts and break the mapped stream at the culvert location.
Hydroflattening also replaces qualifying water surfaces with level or monotonically sloping cartographic surfaces.

These treatments have different hydraulic consequences.

- Removing a bridge deck can keep the terrain from blocking the channel, but the interpolated surface does not represent piers, abutments, deck low chord, contraction, pressure flow, or overtopping losses.
- Leaving a road over a culvert can make the road a continuous barrier if the culvert opening is not represented separately.
- Hydroflattening improves cartographic water surfaces but does not recover the submerged bed.
- A bare-earth label does not prove that small berms, levees, walls, ditches, or urban drainage connections are represented at model scale.

## Below-water terrain is a separate evidence problem

Ordinary topographic lidar generally uses infrared wavelengths that do not penetrate the water surface.
USGS distinguishes that limitation from bathymetric lidar, which uses green wavelengths and whose penetration depends on water clarity and other survey conditions.

Topobathymetry combines topographic and bathymetric elevation into a continuous surface across land, shoreline, and submerged areas.
The merge requires compatible datums, units, timestamps, resolutions, uncertainty, and overlap treatment.
Availability alone does not prove that a topobathymetric source is current, complete, or accurate enough for a particular river.

Missing below-water geometry can reduce represented channel area and hydraulic radius.
For a given discharge, the model may then require a higher WSE to create enough conveyance, while the reported depth remains referenced to an artificial water-surface-like terrain rather than the true bed.
The magnitude and sign of the full result error still depend on boundary conditions, roughness, geometry, and flow regime.

## DR-021 contains an unresolved authority conflict

**Registered methodology:** The reviewed Decision Register lists DR-021 ALT-A, no handling, with Alternate Selected status and a last-update date of 2026-02-02.

**Standalone record:** The DR-021 file marks both ALT-A and ALT-E with `#current`.
Its decision history says that ALT-G, use topobathymetric DEM where available, was selected after a team meeting on 2026-05-08, but ALT-G itself has no `#current` marker and the controlling register still lists ALT-A.

**Evidence or experiment:** The ALT-A section records Case-015 as a rejected outcome associated with WSE higher than benchmark FIM.
EXP-012 proposes a with-bathymetry versus without-bathymetry comparison, but a proposed experiment is not a completed policy change.

**Current implementation:** `build_model` clips and reprojects the supplied DEM and has no topobathymetry-policy input or merge operation.

**Open question:** The handbook cannot select ALT-A, ALT-E, or ALT-G as the unambiguous current policy.
Authorized project governance must reconcile the Decision Register, the file-local markers, and the decision history before implementation can be assessed against one selected alternative.

## Culverts and drainage enforcement

Drainage enforcement modifies terrain or adds structure behavior so water can pass through a represented obstruction.
Common approaches include burning a channel, breaching an obstruction, inserting a culvert relation, or modeling the structure explicitly.
Each approach makes a different statement about opening size, invert elevation, capacity, blockage, head loss, and overtopping.

**Registered methodology:** DR-010 ALT-A, do nothing, has Alternate Selected status.
The same decision record marks Case-003 and Case-011 as rejected outcomes associated with divergent flow paths and WSE higher than benchmark FIM.
That evidence makes the selected policy contestable, but it does not authorize another alternative automatically.

**Current implementation:** The `build_model` input contract has no culvert inventory, bridge inventory, terrain-burning rule, breach rule, opening geometry, blockage state, or structure coefficient.
The current job does not modify the DEM for culverts.

Doing nothing preserves the source surface and avoids inventing an opening where a culvert may be blocked during a flood.
It can also create a false dam where the source DEM leaves the road intact.
Burning every crossing can create false connectivity or too much conveyance.
A production policy therefore needs a structure inventory, geometry and capacity rules, event-state assumptions, provenance, and validation evidence.

## Bridges require more than deck removal

A bridge changes channel opening, contraction and expansion losses, pier drag, pressure-flow transition, roadway overtopping, and possible flow around the approaches.
An interpolated bare-earth surface below a removed deck cannot encode all of those mechanisms.

Official USACE bridge guidance requires explicit deck or roadway, pier, abutment, cross-section, roughness, and modeling-approach data for its documented bridge methods.
That guidance is a scientific example of the information structures can require, not a claim that the current project uses HEC-RAS.

**Current implementation:** The current project passes a terrain raster and roughness raster to LISFLOOD-FP and has no bridge structure object in the reviewed `build_model` or scenario input contracts.
A bridge location can therefore be represented only through the supplied raster values and any effects already encoded in them.
Current job completion does not establish that bridge head loss, pressure flow, piers, abutments, or roadway overtopping were represented.

## Terrain error affects WSE and depth through two paths

Depth is computed from WSE and terrain or bed elevation:

\[
h=\eta-z_b
\]

For comparable results at one location, the change relation is:

\[
\Delta h=\Delta\eta-\Delta z_b
\]

The direct reporting path is visible if WSE is held fixed.
If the represented bed is 0.40 m too high, computed depth is 0.40 m too low at that fixed WSE.

The hydraulic-response path is more complicated because terrain also controls storage, slope, wetted area, face openings, and connectivity.
An artificial obstruction can raise upstream WSE, while an artificially low pathway can drain water and lower WSE elsewhere.
The total depth error is therefore not generally the negative of terrain error because both terms in the change relation can move.

The 0.40 m value in this example is an illustrative calculation rather than a project tolerance.
An acceptance threshold requires stated product needs, source accuracy, structure uncertainty, and validation evidence.

## A terrain acceptance review

Before accepting a model DEM, answer the following questions.

1. Which exact source release, tiles, object versions, or checksums produced it?
2. What horizontal CRS, transform, resolution, and alignment does it use?
3. What elevation units and vertical datum apply, and where is that contract stored?
4. Which resampling rule produced the model grid, and what features could that rule erase or create?
5. Does the source represent bare earth, hydroflattened water, submerged bed, or a blend?
6. Which bridges, culverts, roads, levees, walls, and drainage connections are represented or absent?
7. Which topobathymetry and structure decisions apply, and are their statuses internally consistent?
8. Do cross-sections, longitudinal profiles, structure checks, and benchmark comparisons support use at the intended WSE and depth ranges?

## Common misconceptions

### A 10 m source and a 10 m model grid are the same raster

Reprojection changes the grid origin, geometry, and sampled support even when nominal spacing is similar.

### EPSG:5070 proves NAVD88 elevations

EPSG:5070 is the checked-in environment fallback for the horizontal CRS, not an unconditional realized value.
The manifest input and terrain raster CRS identify the realized value.
The current code does not record or transform the vertical datum.

### Bare earth includes a surveyed channel bed

Hydroflattened water surfaces and infrared-lidar limitations can leave the submerged bed absent.

### Bridge removal means bridge hydraulics are represented

Deck removal prevents one type of false blockage.
It does not represent piers, abutments, contraction, pressure flow, head loss, or overtopping.

### A rejected case changes the selected alternative

Case evidence can challenge a decision, but only an authorized methodology update changes the selected status.

## Competency check

1. Why are source resolution and model-grid resolution separate facts?
2. What vertical information is missing from the current `BuildModelInputs` and model manifest contracts?
3. Which resampling method does the current DEM path use, and why is that not automatically a recommended policy?
4. What does USGS bridge and culvert surface treatment imply for hydraulic connectivity?
5. Why is DR-021 not internally unambiguous at the reviewed revision?
6. Under what restricted condition does a 0.40 m positive terrain error create a 0.40 m negative depth error?
7. What additional evidence is needed when terrain itself changes the solved WSE?

## Source notes

- **Selected methodology and evidence:** Terrain, culvert, topobathymetry, and experiment records are indexed under [SDR-009](../reference/bibliography.md#sdr-009-terrain-structures-and-roughness-decisions).
- **Current implementation:** Source defaults, identity inputs, raster reprojection, artifacts, and warnings are indexed under [JOB-008](../reference/bibliography.md#job-008-model-development-input-transformations-and-warnings).
- **Scientific foundation:** 3DEP source characteristics are supported by [SCI-035](../reference/bibliography.md#sci-035-usgs-3dep-one-third-arc-second-dem), and surface treatments are supported by [SCI-036](../reference/bibliography.md#sci-036-usgs-dem-surface-treatments).
- **Scientific foundation:** Topographic-lidar limits and topobathymetric scope are supported by [SCI-037](../reference/bibliography.md#sci-037-usgs-lidar-survey-types) and [SCI-038](../reference/bibliography.md#sci-038-noaa-topobathy-lidar).
- **Scientific foundation:** Structure requirements are illustrated by official USACE guidance in [SCI-041](../reference/bibliography.md#sci-041-usace-bridges-in-2d-flow-areas).
- **Transformation behavior:** Rasterio's documented resampling contract is recorded under [SCI-042](../reference/bibliography.md#sci-042-rasterio-reprojection-and-resampling).
- **Open questions:** Topobathymetry authority and terrain transformation gaps are preserved in [CONF-005](../reference/conflicts-and-open-questions.md#conf-005-topobathymetry-policy) and [CONF-011](../reference/conflicts-and-open-questions.md#conf-011-terrain-source-datum-resampling-and-structures).
