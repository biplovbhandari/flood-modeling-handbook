# Roughness and Land Cover

Manning's n is an effective hydraulic parameter that represents resistance at the scale and formulation of a model.
A land-cover class can provide a reproducible starting value, but it is not a direct measurement of effective resistance and does not prove calibration.

## Why this topic matters

Roughness changes velocity, conveyance, travel time, water-surface elevation, inundation extent, and routing through a heterogeneous floodplain.
A plausible lookup can still mislead when source classes are wrong, channel roughness is inferred from upland cover, narrow features disappear during resampling, or calibration compensates for terrain or boundary error.

## Prerequisites

Read [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md), [Shallow-Water Models](../03-2d-hydraulics/01-shallow-water-models.md), and [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md).

## Learning objectives

After this chapter, the reader should be able to:

- explain why Manning's n is an effective parameter rather than a universal material constant;
- distinguish categorical land cover from hydraulic roughness;
- describe a reproducible class-to-roughness conversion;
- identify incomplete, invalid, or poorly aligned lookup results;
- explain how heterogeneity and grid scale affect represented resistance;
- distinguish calibration from transfer to another event or model; and
- design a sensitivity review that does not hide competing uncertainty.

## Manning's n represents effective resistance

Manning's n summarizes resistance associated with bed material, vegetation, surface irregularity, channel form, obstructions, and unresolved geometry.
Its useful value depends on depth, season, vegetation state, model scale, grid orientation, and losses represented elsewhere.

For steady uniform flow in SI units:

\[
Q=\frac{1}{n}AR_h^{2/3}S_f^{1/2}
\]

At fixed area, hydraulic radius, and friction slope, discharge is inversely proportional to n.
In a fixed-discharge simulation, depth, velocity, water-surface elevation, wet extent, and routing can all adjust, so the final response is not generally a simple inverse scaling.

USACE guidance discusses roughness variability, influencing factors, and calibration limits in [SCI-025](../reference/bibliography.md#sci-025-hec-ras-energy-loss-coefficients).

## Land cover is a classified observation

Land-cover data divide the landscape into classes such as open water, developed land, forest, crops, and wetlands.
The class describes a predominant mapped surface state under a particular classification system and mapping year.
It does not directly measure stem density, crop condition, understory, channel substrate, debris, building blockage, or depth-dependent drag.

**Scientific foundation:** The Annual NLCD Collection 1 user guide describes a public categorical product, its class system, spatial support, and uncertainty in [SCI-039](../reference/bibliography.md#sci-039-annual-nlcd-collection-1-user-guide).
The public product name is meaningful only when the collection, release, mapping year, extent, and exact source object are recorded.

## A lookup is a modeling choice

A class-to-roughness table translates an observed category into a model parameter.
The table is a prior assumption until calibration or independent evidence supports it for the modeled setting.

**Applied example:** The synthetic `R-200` model uses the following illustrative lookup in SI units.

| Synthetic class | Description | Manning's n in s/m^(1/3) |
| --- | --- | ---: |
| 1 | Open water | 0.030 |
| 2 | Short grass | 0.040 |
| 3 | Cropland | 0.050 |
| 4 | Shrub | 0.090 |
| 5 | Forest | 0.140 |
| 6 | Dense developed surface | 0.110 |

The values are fully stated for the applied example and are not a national recommendation.
Public guidance on using land cover, overrides, channel-specific regions, and calibration is available in [SCI-040](../reference/bibliography.md#sci-040-usace-land-cover-and-mannings-n-guidance).

## Conversion must preserve categorical meaning

A reproducible conversion should perform the following operations explicitly.

1. Open the stated land-cover source and validate its class and nodata values.
2. Define the model bounds, horizontal reference, rows, columns, and transform.
3. Reproject categorical classes with an appropriate categorical resampling rule.
4. Reject or flag every class that lacks a valid positive roughness value.
5. Apply the lookup without numeric truncation or wraparound.
6. Write the roughness raster with units, nodata, transform, and source provenance.

Nearest-neighbour resampling commonly preserves integer classes, but it can shift or erase narrow features when grids are rotated or offset.
Majority aggregation, fractional cover, or solver-supported subgrid methods answer different questions and must be named when used.
Raster transformation behaviour is documented by [SCI-042](../reference/bibliography.md#sci-042-rasterio-reprojection-and-resampling).

## Validate the lookup at the boundary

The conversion boundary should reject:

- missing mappings for any realized class or nodata code;
- nonpositive, nonfinite, or sentinel roughness values;
- class values changed by an unintended cast;
- lookup keys outside the source classification contract; and
- rasters whose transform, extent, or reference does not match the model grid.

**Design principle:** An empty warning list proves only that implemented warning rules did not fire.
It does not prove complete class coverage, physical plausibility, channel representation, or calibration.

Useful warnings include unexpected classes, excessive uniformity, abrupt unsupported transitions, large nodata regions, and loss of narrow high-conveyance or high-resistance features.
Warnings should identify locations and counts rather than only provide a generic label.

## Spatial heterogeneity affects pathways

A spatially varying roughness field can redirect water toward a smoother path, delay spreading through vegetation, or change velocities on roads, channels, fields, and wooded floodplains at the same water surface.
The effect depends on connectivity and water depth as well as the local value.

Narrow features are sensitive to grid placement.
A road, channel, hedgerow, or riparian strip can disappear when one class is selected for a coarser cell.
One sampled high-resistance class can also occupy an entire hydraulic cell even if the feature covers only a small fraction.

The hydraulically appropriate aggregate is not necessarily the arithmetic mean of n.
Parallel pathways and series resistance behave differently, and numerical methods can place friction at cells, faces, or subgrid elements.

## Resolution and roughness interact with terrain

Terrain defines which areas become wet and how much water each cell stores.
Roughness affects how quickly and by which route water moves through those areas.
Changing resolution can alter both the roughness pattern and the area over which each value acts.

A finer hydraulic grid does not create finer land-cover observations when the source remains coarse.
A coarser grid can reduce computation but must define how roughness is sampled or aggregated.
Terrain, roughness, and grid sensitivity should be reviewed together near controlling roads, channels, wetlands, structures, and vegetation boundaries.

## Calibration and transfer limits

Calibration adjusts uncertain inputs so selected outputs agree better with observations under defined conditions.
For roughness, observations may include water-surface elevation, stage, velocity, timing, extent, or rating behaviour.

A calibration record should include:

- observation source, datum, uncertainty, event, and spatial support;
- adjustable regions or classes and their plausible bounds;
- terrain, boundary, forcing, structure, and solver versions held fixed;
- separation of calibration and validation evidence when possible; and
- checks for parameter compensation and non-uniqueness.

A calibrated value is not automatically transferable to another reach, season, event, grid, vegetation state, or solver.
Transfer requires comparable hydraulic representation and independent evidence.

## Roughness sensitivity

Under the restricted Manning relation with geometry and slope fixed:

\[
\frac{Q_2}{Q_1}=\frac{n_1}{n_2}
\]

Increasing n from 0.040 to 0.044 is a 10 percent increase.
With area, hydraulic radius, and slope fixed, the discharge ratio is $0.040/0.044\approx0.909$, or about a 9.1 percent decrease.

This calculation is an equation consequence, not a model sensitivity result.
In a fixed-discharge model, depth and water-surface elevation can rise until conveyance is restored, and two-dimensional flow can redistribute.

A useful sensitivity review compares water-surface elevation, depth, extent, velocity, flow split, convergence, and edge response across documented plausible ranges.
It should not vary roughness alone when terrain, bathymetry, structures, or boundaries are plausible competing causes.

## Roughness identity and provenance

The model record should preserve:

- the exact source object and content identity;
- the mapping year and classification version;
- the complete realized lookup;
- the resampling, alignment, and nodata rules;
- any local override polygons and their priority;
- output raster integrity information; and
- calibration or sensitivity evidence linked to the realization.

**Evidence note:** A source name or source-address hash does not pin mutable content.
Exact reuse requires an immutable version or content digest as well as the realized output record.

## Acceptance review

1. Which collection, release, mapping year, extent, and source object were used?
2. Are all realized classes and nodata values mapped to positive, finite values?
3. Which resampling and alignment rules produced the hydraulic grid?
4. Does the raster preserve important channel, roadway, wetland, vegetation, and urban pathways?
5. Which values are priors, local overrides, or calibrated parameters?
6. What evidence supports calibration, and what limits transfer?
7. Which sensitivity changes affect water surface, extent, velocity, or routing materially?

## Common misconceptions

### A land-cover class is Manning's n

Land cover is a categorical observation.
The lookup is a modeling choice.

### A fine roughness raster contains equally fine source information

Resampling can increase output cell count without increasing observational detail.

### A published lookup proves calibration

A public or documented lookup supplies a reproducible prior, not reach-specific validation.

### One calibrated value transfers everywhere

Roughness can compensate for other errors and can depend on depth, season, grid, structures, and solver formulation.

## Competency check

1. Why is Manning's n an effective parameter rather than a material constant?
2. What distinguishes the land-cover observation from the roughness lookup?
3. Why must class validation occur before any narrowing numeric cast?
4. Why does a finer hydraulic grid not create finer land-cover observations?
5. Under what assumptions does a 10 percent increase in n produce about a 9.1 percent decrease in discharge?
6. What evidence is needed before a calibrated adjustment can be transferred?

## Further reading and source notes

- **Scientific foundation:** Annual NLCD classification and caveats are supported by [SCI-039](../reference/bibliography.md#sci-039-annual-nlcd-collection-1-user-guide).
- **Scientific foundation:** Land-cover conversion, channel refinement, overrides, and calibration regions are supported by [SCI-040](../reference/bibliography.md#sci-040-usace-land-cover-and-mannings-n-guidance).
- **Scientific foundation:** Roughness variability and sensitivity are supported by [SCI-025](../reference/bibliography.md#sci-025-hec-ras-energy-loss-coefficients) and [SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity).
- **Scientific foundation:** Raster transformation behaviour is documented by [SCI-042](../reference/bibliography.md#sci-042-rasterio-reprojection-and-resampling).
