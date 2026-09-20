# Roughness and Land Cover

Manning's n is an effective hydraulic parameter that represents resistance at the scale and formulation of a model.
A land-cover class can provide a reproducible starting value, but it is not a direct measurement of that effective resistance and does not prove calibration.

## Why this topic matters

Roughness changes velocity, conveyance, travel time, WSE, inundation extent, and the route taken through a heterogeneous floodplain.
A plausible-looking lookup table can still produce misleading results when source classes are wrong, channel roughness is inferred from upland cover, narrow features disappear during resampling, or one calibrated adjustment compensates for terrain or boundary error.

## Prerequisites

Read [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md), [Shallow-Water Models](../03-2d-hydraulics/01-shallow-water-models.md), and [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md).
Use [Equations and Units](../reference/equations-and-units.md) for Manning conveyance and the fixed-geometry sensitivity relation.

## Learning objectives

After this chapter, the reader should be able to:

- explain why Manning's n is an effective model parameter rather than a universal material constant;
- distinguish categorical land cover from hydraulic roughness;
- trace the current Annual NLCD source through class reprojection and lookup conversion;
- identify the authority and limits of the selected lookup table;
- explain how heterogeneity, grid scale, and aggregation affect represented resistance;
- distinguish calibration from transfer to another reach, event, season, or grid; and
- design a roughness sensitivity review that does not hide other uncertainty.

## Manning's n represents effective resistance

Manning's n summarizes resistance associated with bed material, vegetation, surface irregularity, channel form, obstructions, and unresolved geometry within the assumptions of the hydraulic formulation.
Its useful value depends on depth, season, vegetation state, model scale, grid orientation, and which losses the model represents elsewhere.

For steady uniform flow in SI units, the handbook uses:

\[
Q=\frac{1}{n}AR_h^{2/3}S_f^{1/2}
\]

At fixed area, hydraulic radius, and friction slope, discharge is inversely proportional to n.
In a simulation with fixed inflow, however, area, depth, velocity, WSE, wet extent, and flow route can all adjust, so the final response is not generally a simple inverse scaling.

Manning's n can also absorb error from unresolved vegetation, bathymetry, structures, grid scale, or boundary conditions during calibration.
That compensation can improve agreement at one site while reducing transferability.

## Land cover is a classified observation

Land use and land cover data divide the landscape into categorical classes such as open water, developed land, forest, crops, and wetlands.
The class describes a predominant surface state under the dataset's classification system and mapping year.
It does not directly measure stem density, crop condition, understory, channel substrate, debris, building blockage, or depth-dependent drag.

**Selected methodology:** DR-019 ALT-A has Alternate Selected status and selects National Land Cover Database land cover converted to Manning's n.

**Current implementation:** `DEFAULT_LULC_SOURCE` is an environment-dependent fallback evaluated when the process imports the settings module.
Its checked-in fallback value is `/vsis3/usgs-landcover/annual-nlcd/c1/v0/cu/mosaic/Annual_NLCD_LndCov_2023_CU_C1V0.tif` when `DEFAULT_LULC_SOURCE` is unset, and a caller-supplied `lulc_source` overrides that process value for the job.
The checked-in path identifies the conterminous United States Annual NLCD Collection 1.0 land-cover mosaic for mapping year 2023.
It is not an unconditional current source or a claim that 2023 land cover represents every modeled event or future condition.

Inspect `model_manifest.inputs.lulc_source` to identify the source connection string realized by a completed build.
Inspect `model_manifest.inputs.epsg_code`, `model_manifest.inputs.grid_resolution`, and the roughness raster CRS and transform to identify the realized destination grid rather than inferring it from checked-in fallbacks.

**Authoritative source:** The Annual NLCD guide describes land cover as a categorical sixteen-class product representing predominant surface state within the mapping year.
Its map and reference sample units use 30 m by 30 m spatial extents.
Classification uncertainty and documented artifacts therefore remain relevant after conversion to roughness.

## The selected and checked-in lookup fallback

**Selected methodology:** DR-020 ALT-A has Alternate Selected status and selects the following project table, which the record attributes to USACE guidance.

**Current implementation:** `DEFAULT_LULC_LOOKUP` contains the same class-to-value pairs as the checked-in `BuildModelInputs.lulc_lookup` fallback, while a caller can supply a different dictionary or JSON path.
Each value is Manning's n in \(\mathrm{s}/\mathrm{m}^{1/3}\) under the handbook's SI convention.

| Annual NLCD code | General class | Checked-in fallback n |
| --- | --- | --- |
| 11 | Open Water. | 0.04. |
| 21 | Developed, Open Space. | 0.04. |
| 22 | Developed, Low Intensity. | 0.10. |
| 23 | Developed, Medium Intensity. | 0.08. |
| 24 | Developed, High Intensity. | 0.15. |
| 31 | Barren Land. | 0.025. |
| 41 | Deciduous Forest. | 0.16. |
| 42 | Evergreen Forest. | 0.16. |
| 43 | Mixed Forest. | 0.16. |
| 52 | Shrub or Scrub. | 0.10. |
| 71 | Grassland or Herbaceous. | 0.035. |
| 81 | Pasture or Hay. | 0.03. |
| 82 | Cultivated Crops. | 0.035. |
| 90 | Woody Wetlands. | 0.12. |
| 95 | Emergent Herbaceous Wetlands. | 0.07. |

The labels above follow the Annual NLCD class legend, while the numeric values come from DR-020 and current code.
The table is a selected project prior and checked-in input fallback, not a universal truth, a calibrated national surface, or proof that one value is valid at every depth and season.

Official USACE guidance supports using land-cover base values, but it also requires a value for every class and describes higher-priority classification polygons, channel-specific regions, base overrides, and calibration regions.
That guidance directly warns against assuming that a national land-cover raster adequately defines main-channel roughness.

## The current conversion path

The current code performs the following operations.

1. It opens the realized caller-supplied or fallback LULC raster recorded in the validated job inputs.
2. It creates the same destination bounds, rows, columns, horizontal CRS, and transform used by the model DEM.
3. It calls `rasterio.warp.reproject` without an explicit resampling argument, so Rasterio's documented nearest-neighbor default applies.
4. It casts the reprojected class values to `uint8`.
5. It indexes a 256-entry lookup array whose unspecified entries contain `-9999`.
6. It writes the resulting `float32` Manning raster.

This order preserves categorical class values under nearest-neighbor sampling before lookup conversion.
It does not perform majority aggregation, fractional cover, composite resistance, or subcell channel treatment.

The source Annual NLCD cells are 30 m by 30 m.
When no caller overrides the 10 m `grid_resolution` input default and the process uses the checked-in EPSG:5070 fallback, the destination cells are 10 m by 10 m in EPSG:5070.
The realized manifest inputs and raster metadata remain authoritative when the environment or caller supplies different values.
Resampling the class raster to a finer grid creates more output cells but no new finer-resolution land-cover observations.
It can repeat one source class across several model cells and shift a boundary according to grid alignment.

## Lookup completeness and warning behavior

Annual NLCD includes code 12 for Perennial Ice or Snow, but the checked-in lookup fallback has no code 12 entry.
The current conversion casts reprojected source values to `uint8` before indexing the lookup array.
Negative, fractional, or out-of-range source values can therefore truncate or wrap to a valid code from 0 through 255 before lookup.
Only a post-cast code that is absent from the populated lookup array reliably receives the `-9999` fill value.

`BuildModelInputs.lulc_lookup` validates only that dictionary keys are integers and values are floats, or that the input is a string path.
It does not require positive Manning values, complete post-cast class coverage, a mapping for nodata, or keys restricted to 0 through 255.
Because the source data are cast to `uint8`, lookup indices outside 0 through 255 cannot represent distinct reachable raster codes through the cast input.
Out-of-range lookup keys can instead fail or alias during lookup-array construction, depending on the Python or NumPy index involved, rather than define a distinct source class.

The generated build-model documentation says that the job checks whether all roughness values are similar.
The current job code instead contains an unimplemented marker for that warning and does not emit it.
The current build warnings cover a centerline that intersects its inflow more than once and a domain larger than the configured threshold.
They do not report an unmapped land-cover class, a `-9999` roughness value, an unmapped nodata value after conversion, or an unusually uniform roughness surface.

**Hydraulic consequence:** A negative fill value is not a physical Manning coefficient.
Allowing it into solver input can corrupt friction behavior or trigger solver-dependent failure, while an absent warning can make the invalid region hard to distinguish from a valid completed build.

## Spatial heterogeneity affects pathways

A spatially varying roughness field can redirect water toward a smoother path, delay spreading through dense vegetation, or create different velocities on a road, channel, field, and wooded floodplain at the same WSE.
The effect depends on connectivity and water depth as well as the local value.

Narrow features are especially sensitive to grid placement.
A narrow road, levee-side strip, channel, hedgerow, or riparian corridor can disappear when one nearest class is selected for a coarser or shifted cell.
Conversely, one sampled high-resistance class can occupy an entire hydraulic cell even if the source feature covers only a small fraction of it.

The hydraulically appropriate aggregate is not necessarily the arithmetic mean of n.
Parallel flow paths and series resistance have different effects, and the numerical method may place friction at cells, faces, or subgrid elements.
Any aggregation rule must match the solver representation and preserve the controlling pathways for the intended result.

## Resolution and roughness interact with terrain

Terrain defines which areas become wet and how much water each cell stores.
Roughness affects how quickly and by which route water moves through those wet areas.
Changing resolution can therefore change both the roughness pattern and the area over which each value acts.

A finer grid does not automatically make the roughness more accurate when the source classification remains 30 m.
It can make class edges appear more detailed without adding observation detail.
A coarser grid can reduce computation but must define whether roughness is sampled, aggregated, or represented through a solver-supported composite relation.

Terrain, roughness, and grid sensitivity should be reviewed together where a result changes near a controlling road, channel, wetland, or vegetation boundary.

## Calibration and transfer limits

Calibration adjusts uncertain model inputs so selected outputs agree better with observations under defined conditions.
For roughness, the observations might include WSE, stage, velocity, timing, extent, or a rating relation.

A defensible calibration records the following elements.

- It records the observation source, datum, uncertainty, event, and spatial support.
- It records which roughness regions or class values were adjustable and their physically plausible bounds.
- It records terrain, boundary, forcing, structure, and solver versions held fixed.
- It separates calibration data from validation data when enough evidence exists.
- It tests whether several parameter sets can produce similar agreement through compensation.

A calibrated value is not automatically transferable to another reach, season, event, grid, vegetation state, or solver.
Transfer is more credible when the hydraulic setting and representation are comparable and independent evidence shows that the parameter retains meaning.

**Current implementation:** The checked-in lookup fallback contains no calibration metadata or uncertainty range.
No current build-model behavior proves that its values were calibrated for a target reach.

## Roughness sensitivity

Under the restricted Manning relation with geometry and slope fixed, changing roughness from \(n_1\) to \(n_2\) gives:

\[
\frac{Q_2}{Q_1}=\frac{n_1}{n_2}
\]

For example, increasing \(n\) from 0.040 to 0.044 is a 10 percent increase.
With area, hydraulic radius, and slope fixed, the discharge ratio is \(0.040/0.044\approx0.909\), which is about a 9.1 percent decrease.

This worked calculation is an equation consequence, not a project sensitivity result.
In a fixed-discharge model, depth and WSE can rise until conveyance is restored, and two-dimensional flow can redistribute among pathways.

A useful sensitivity review varies roughness within documented plausible ranges and compares at least WSE, depth, extent, velocity, flow split, convergence behavior, and edge response.
It should include coherent regional or class-based changes as well as any local channel or floodplain region whose value controls the interpretation.
It should not vary roughness alone when terrain, bathymetry, structures, or boundaries are plausible competing causes of the same mismatch.

## Provenance and identity

The current model manifest records `lulc_source` and the full `lulc_lookup` input.
Model identity includes a hash of the source connection string and a hash of the realized lookup dictionary.
The roughness asset records its output checksum, source string, and retrieval time.

These fields support tracing one realized output, but the source-string hash does not pin changing remote content.
A versioned land-cover object or content identity remains necessary when exact reproduction matters.
The lookup hash proves which dictionary was used only when the manifest and hash algorithm remain available and consistent.

## A roughness acceptance review

Before accepting a roughness raster, answer the following questions.

1. Which land-cover collection, release, mapping year, geographic extent, and exact source object were used?
2. Are all post-cast class and nodata codes mapped to positive, physically reviewable values, and were pre-cast values checked for truncation or wrap collisions?
3. Which resampling and alignment rules produced the hydraulic grid?
4. Does the raster preserve main-channel, roadway, wetland, vegetation, and urban pathways that matter at the intended depths?
5. Which values come from selected methodology, checked-in fallbacks, realized caller inputs, local overrides, or calibration?
6. What observation evidence supports calibration, and what conditions limit transfer?
7. Which sensitivity range changes WSE, depth, extent, velocity, or flow routing materially?
8. Do persisted artifacts and warnings make invalid or suspicious values visible?

## Common misconceptions

### NLCD class is Manning's n

Land cover is a categorical observation.
The lookup is a modeling choice that translates a class to an effective resistance parameter.

### A 10 m roughness raster contains 10 m land-cover information

The checked-in source fallback uses 30 m Annual NLCD cells.
Nearest-neighbor reprojection to a 10 m model grid increases output cell count without increasing source information.

### The selected lookup proves calibration

DR-020 and current code establish the selected and default values.
They do not supply reach-specific calibration evidence.

### Uniform values trigger a warning

The generated documentation describes such a check, but current code leaves it unimplemented.

### One calibrated n value can transfer everywhere

The value can compensate for other errors and can depend on depth, season, grid, structures, and solver formulation.
Transfer requires independent justification.

## Competency check

1. Why is Manning's n an effective parameter rather than a material constant?
2. Which parts of the checked-in LULC source fallback identify collection, release, region, and year, and where is the realized source recorded?
3. What happens when the current conversion sees Annual NLCD code 12?
4. Why does nearest-neighbor conversion to a 10 m grid not create 10 m source information?
5. Which warning described by generated documentation is not implemented in current code?
6. Under what assumptions does a 10 percent increase in n produce about a 9.1 percent decrease in Q?
7. What evidence is required before a calibrated roughness adjustment can be transferred?

## Source notes

- **Selected methodology:** LULC source and lookup decisions are indexed under [SDR-009](../reference/bibliography.md#sdr-009-terrain-structures-and-roughness-decisions).
- **Current implementation:** Default source, exact lookup, conversion order, provenance, and warning behavior are indexed under [JOB-008](../reference/bibliography.md#job-008-model-development-input-transformations-and-warnings).
- **Scientific foundation:** Annual NLCD classification, resolution, release, and caveats are supported by [SCI-039](../reference/bibliography.md#sci-039-annual-nlcd-collection-1-user-guide).
- **Scientific foundation:** Land-cover conversion, channel-specific refinement, overrides, and calibration regions are supported by [SCI-040](../reference/bibliography.md#sci-040-usace-land-cover-and-mannings-n-guidance).
- **Scientific foundation:** Roughness variability and sensitivity are also supported by [SCI-025](../reference/bibliography.md#sci-025-hec-ras-energy-loss-coefficients) and [SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity).
- **Transformation behavior:** Rasterio's documented nearest-neighbor default is recorded under [SCI-042](../reference/bibliography.md#sci-042-rasterio-reprojection-and-resampling).
- **Open questions:** Lookup completeness, validation, warnings, and source pinning are preserved in [CONF-012](../reference/conflicts-and-open-questions.md#conf-012-roughness-lookup-validation-and-warning-gaps).
