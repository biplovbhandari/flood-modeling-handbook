# Terrain, Topobathymetry, and Structures

Terrain supplies elevations that define storage, pathways, barriers, and reported depth.
A terrain raster is not a complete hydraulic representation of submerged channels, bridges, culverts, or drainage connections.

## Why this topic matters

A small elevation difference can open or close a flow path, move a wetting front, change storage, and alter upstream water-surface elevation.
A model can be numerically stable while remaining hydraulically wrong because its terrain uses an incompatible datum, an unsuitable resampling rule, a blocked culvert, or an unrepresented channel bed.

## Prerequisites

Read [Quantities, Units, and Datums](../00-orientation/03-quantities-units-and-datums.md), [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md), and [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md).

## Learning objectives

After this chapter, the reader should be able to:

- distinguish source resolution, model resolution, numeric precision, vertical accuracy, and vertical datum;
- explain why horizontal reprojection does not transform the vertical datum;
- evaluate resampling and alignment by their hydraulic consequences;
- explain the limits of bare-earth and hydroflattened elevation products;
- distinguish topographic lidar from topobathymetric measurement;
- identify how bridges, culverts, and drainage enforcement affect conveyance; and
- specify a reproducible terrain record.

## Horizontal and vertical resolution answer different questions

Horizontal resolution describes the spacing or support of elevation samples on the map.
It controls which channels, berms, roads, swales, and structure approaches can be represented spatially.

Numeric precision describes how finely values are stored.
Vertical accuracy describes agreement with actual elevation under a stated reference and test method.
A raster can store millimetre increments without being accurate to a millimetre.

Source resolution and model-grid resolution are separate facts.
A source near 10 m spacing can be reprojected to a 10 m projected grid, but the grids can still differ in origin, orientation, cell footprint, and sampled support.

**Applied example:** The model record for `R-200` states a 10 m square grid in a projected coordinate system with metre units.
That value defines the hydraulic grid but does not claim that every terrain feature is observed at 10 m resolution or that 10 m is adequate for every structure.

## Horizontal and vertical references are separate contracts

A horizontal coordinate reference system defines planimetric position, orientation, and units.
A vertical datum defines the reference surface from which elevation is measured.
One horizontal identifier does not automatically document the vertical reference of raster values.

A terrain record should state:

- the source and destination horizontal references;
- the destination transform, extent, and resolution;
- elevation units and vertical datum;
- any vertical transformation and its grid or model version;
- whether values represent bare earth, a water surface, a channel bed, or a blend; and
- exact source release, tiles, object versions, or content digests.

**Scientific foundation:** The USGS 3DEP one-third arc-second product is described as a bare-earth elevation product near 10 m spacing, with scope-specific horizontal and vertical metadata documented by the provider in [SCI-035](../reference/bibliography.md#sci-035-usgs-3dep-one-third-arc-second-dem).
The public product name remains meaningful only when the exact release and retrieved source objects are recorded.

## Resampling and alignment are hydraulic operations

Reprojection maps destination cells to a source grid and assigns values through a resampling rule.
The rule can preserve, smooth, displace, or erase controlling elevations.

Nearest-neighbour resampling preserves sampled values but can alias a narrow feature.
Bilinear and cubic interpolation can smooth a continuous surface but can lower a narrow ridge or raise a narrow channel.
Average, minimum, and maximum aggregation each preserve different properties.

Rasterio documents available reprojection and resampling behaviour in [SCI-042](../reference/bibliography.md#sci-042-rasterio-reprojection-and-resampling).
Naming the software is not enough because the realized record must also state the chosen method, nodata treatment, source transform, and destination transform.

**Design principle:** Inspect the transformed centerline, banks, levees, roads, structure openings, low pathways, and domain edges rather than accepting a matching nominal cell size.

## Bare earth is not hydraulically complete

A bare-earth elevation model attempts to represent ground after removing vegetation and many above-ground objects.
It is not a surveyed channel-and-structure model.

USGS surface-treatment specifications describe bridge removal, interpolation below bridges, hydroflattening, and road surfaces retained at culverts in [SCI-036](../reference/bibliography.md#sci-036-usgs-dem-surface-treatments).
These treatments have different hydraulic consequences.

- Removing a bridge deck can prevent a false blockage, but the interpolated surface does not represent piers, abutments, low chord, contraction, pressure flow, or overtopping.
- Retaining a roadway over a culvert can create a false dam when the opening is not represented separately.
- Hydroflattening improves cartographic water surfaces but does not recover the submerged bed.
- A bare-earth label does not prove that small levees, walls, ditches, or urban drainage connections survive at model scale.

## Below-water terrain is a separate evidence problem

Ordinary topographic lidar generally does not penetrate the water surface.
USGS distinguishes topographic and bathymetric survey types in [SCI-037](../reference/bibliography.md#sci-037-usgs-lidar-survey-types), while NOAA describes topobathymetric lidar and its limitations in [SCI-038](../reference/bibliography.md#sci-038-noaa-topobathy-lidar).

Topobathymetry combines topographic and bathymetric elevation across land, shoreline, and submerged areas.
The merge requires compatible datums, units, timestamps, resolutions, uncertainty descriptions, and overlap rules.
Availability alone does not prove that a source is complete or accurate enough for a reach.

Missing channel geometry can reduce represented flow area and hydraulic radius.
For a fixed discharge, the model may require a higher water surface to provide conveyance, while reported depth remains referenced to an artificial surface instead of the bed.

**Open question:** Which topobathymetric sources and merge rules are suitable for the modeled event, reach type, and validation evidence?
The answer must be recorded per model rather than assumed from a product category.

## Culverts and drainage enforcement

Drainage enforcement changes terrain or adds structure behaviour so water can pass through a represented obstruction.
Common approaches include burning a channel, breaching an obstruction, inserting a culvert relation, or modeling the structure explicitly.

Each approach makes a different statement about opening size, invert elevation, capacity, blockage, head loss, and overtopping.
Doing nothing avoids inventing an opening but can preserve a false dam.
Burning every crossing can create false connectivity or too much conveyance.

**Design principle:** A structure method needs an inventory, geometry and capacity rules, event-state assumptions, provenance, and validation evidence.

## Bridges require more than deck removal

A bridge changes channel opening, contraction and expansion losses, pier drag, pressure-flow transition, roadway overtopping, and possible flow around approaches.
An interpolated surface below a removed deck cannot encode all of those mechanisms.

Official USACE bridge guidance illustrates the geometric and hydraulic information that explicit bridge methods can require in [SCI-041](../reference/bibliography.md#sci-041-usace-bridges-in-2d-flow-areas).
The citation supports the general requirement and does not imply that a particular solver or bridge method is used in the applied example.

## Terrain error affects water surface and depth

Depth is computed from water-surface elevation and terrain or bed elevation:

\[
h=\eta-z_b
\]

For comparable results at one location, the change relation is:

\[
\Delta h=\Delta\eta-\Delta z_b
\]

If water-surface elevation is held fixed and the represented bed is 0.35 m too high, computed depth is 0.35 m too low.
The full model response is more complicated because terrain also controls storage, slope, wetted area, face openings, and connectivity.
Both terms in the change relation can move.

The 0.35 m value is an illustrative calculation, not an acceptance tolerance.

## Synthetic terrain record

**Applied example:** The `R-200` model record states the following realized settings.

| Property | Realized value |
| --- | --- |
| Horizontal grid | Projected, metre units, 10 m cells |
| Vertical units | Metres |
| Vertical datum | Example datum `VD-1` |
| Terrain source | Synthetic bare-earth raster dated 2024-06-15 |
| Bathymetry | Synthetic surveyed channel blend dated 2024-05-20 |
| Resampling | Bilinear for continuous elevation |
| Nodata treatment | Nodata excluded from interpolation and retained in output mask |
| Structures | One synthetic culvert and no explicit bridge object |

**Evidence note:** The record makes the realization interpretable and distinguishes source classes.
It does not prove the terrain is accurate, the culvert is hydraulically adequate, or the datum transformation is correct.

## Terrain acceptance review

Before accepting a model terrain, answer the following questions.

1. Which exact source objects and versions produced it?
2. What horizontal reference, transform, resolution, and alignment does it use?
3. What elevation units and vertical datum apply?
4. Which resampling and nodata rules produced the model grid?
5. Does the surface represent bare earth, hydroflattened water, submerged bed, or a blend?
6. Which bridges, culverts, roads, levees, walls, and drainage connections are represented or absent?
7. Do profiles, cross-sections, structure checks, and benchmark comparisons support the intended use?

## Common misconceptions

### Equal nominal resolution means equal grids

Reprojection changes grid origin, geometry, and sampled support even when nominal spacing matches.

### A horizontal reference proves the vertical datum

Horizontal and vertical references are separate contracts.
Both must be recorded and checked.

### Bare earth includes a surveyed channel bed

Hydroflattened water surfaces and topographic-lidar limitations can leave the submerged bed absent.

### Bridge removal means bridge hydraulics are represented

Deck removal addresses one obstruction but does not represent piers, abutments, pressure flow, head loss, or overtopping.

## Competency check

1. Why are source resolution and model-grid resolution separate facts?
2. Which vertical information belongs in a terrain record?
3. How can resampling change a levee or narrow channel?
4. What do public bare-earth surface treatments imply for culverts and bridges?
5. Under what restricted condition does a 0.35 m positive terrain error create a 0.35 m negative depth error?
6. Which additional evidence is needed when terrain changes the solved water surface?

## Further reading and source notes

- **Scientific foundation:** USGS 3DEP source characteristics are supported by [SCI-035](../reference/bibliography.md#sci-035-usgs-3dep-one-third-arc-second-dem).
- **Scientific foundation:** Surface treatments are supported by [SCI-036](../reference/bibliography.md#sci-036-usgs-dem-surface-treatments).
- **Scientific foundation:** Lidar and topobathymetric scope are supported by [SCI-037](../reference/bibliography.md#sci-037-usgs-lidar-survey-types) and [SCI-038](../reference/bibliography.md#sci-038-noaa-topobathy-lidar).
- **Scientific foundation:** Structure requirements are illustrated by [SCI-041](../reference/bibliography.md#sci-041-usace-bridges-in-2d-flow-areas).
- **Scientific foundation:** Raster transformation behaviour is documented by [SCI-042](../reference/bibliography.md#sci-042-rasterio-reprojection-and-resampling).
