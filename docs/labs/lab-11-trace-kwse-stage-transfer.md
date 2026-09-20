# Lab 11: Trace KWSE Stage Transfer

This lab uses a synthetic two-reach packet to trace planning, current KWSE execution, stage transfer, identity, publication, and materialization.
All reach identifiers, coordinates, elevations, checksums, and observations are instructional givens outside project authority.
They are not project measurements, approved thresholds, production evidence, or proof of deployed orchestration.

## Prerequisites

Complete [KWSE and Stage Transfer](../05-scenario-libraries/03-kwse-and-stage-transfer.md), [Scenario Planning and Propagation](../05-scenario-libraries/04-scenario-planning-and-propagation.md), and [Compositing, Identity, and Provenance](../05-scenario-libraries/05-compositing-identity-and-provenance.md).
Review [Lab Conventions](README.md) and [Source Authority](../reference/source-authority.md).

## Learning objectives

After completing this lab, the learner should be able to:

- trace a downstream scenario manifest into an upstream KWSE payload and manifest;
- distinguish nominal stage from cell-specific transferred WSE;
- identify exact required source and target artifacts;
- diagnose missing-input, coverage, datum, dry-cell, and publication failures;
- explain downstream-to-upstream dependency and serial hot-start order;
- evaluate model and run identity limits;
- distinguish publication from observed materialization; and
- classify responsibility across science, planning, execution, storage, and reconciliation.

## Execution label

**Core inspection:** Complete the lab from the supplied packet and current-code facts.
Do not run a project command, test, build, solver, container, infrastructure command, or deployment command.

## Synthetic network

River flow runs from upstream reach `U-100` into downstream reach `D-200` and then to a terminal outlet.
The KWSE library dependency runs in the opposite direction because `U-100` needs completed hydraulic artifacts from `D-200`.

```text
physical flow:        U-100  ------>  D-200  ------>  outlet
library dependency:  U-100  <------  D-200  <------  terminal start
```

Assume the prepared-network record establishes `U-100.reach_to_id = D-200` and records source-to-prepared lineage.
Assume no lake or coastal breakpoint lies between the reaches.

## Synthetic planned scenario

The planner has selected this target for `U-100`.

| Field | Synthetic value |
| --- | --- |
| Upstream target discharge | 400 m3/s |
| Stage-grid target and upstream `bc_value` | 103.0 m |
| Stage increment | 0.5 m |
| Bound downstream source | `D-200`, ND scenario at 450 m3/s |
| Downstream achieved upstream-end nominal WSE | 103.1 m |
| Downstream imposed boundary value | ND slope 0.0002 m/m |
| Target hot start | `U-100`, ND scenario at 400 m3/s and slope 0.0003 m/m |
| Target run identity hash | `a1b2c3d4` |

The 0.1 m distance between target and achieved downstream WSE is within the synthetic half-increment tolerance of 0.25 m.
The target stage is a scenario label and planning coordinate.
It is not a statement that every transferred point equals 103.0 m.

## Synthetic source artifacts

### Upstream model manifest for `U-100`

| Required field or asset | Synthetic observation |
| --- | --- |
| `model_id` | `11111111_N30S20E25W25` |
| Domain bbox | `[0, 0, 1000, 1000]` m |
| Grid | 100 rows by 100 columns |
| Horizontal CRS | EPSG:26918, projected with metre linear units. |
| Terrain | `U-100/.../dem.tif`, checksum `1111111111111111` |
| Roughness | `U-100/.../roughness.tif`, checksum `2222222222222222` |
| Inflow line | `U-100/.../inflow.geojson`, checksum `3333333333333333` |
| Centerline | `U-100/.../reach.geojson`, checksum `4444444444444444` |
| Terrain vertical datum | NAVD88 metres, supplied by separate synthetic metadata |

### Downstream source scenario manifest for `D-200`

| Required field or asset | Synthetic observation |
| --- | --- |
| `model_id` | `22222222_N25S30E30W20` |
| `scenario_code` | `ND2.0E04Q450` |
| `identity_hash` | `a1b2c3d4` |
| Domain bbox | `[700, -100, 1700, 900]` m |
| Grid | 100 rows by 100 columns |
| Final depth | `D-200/.../depth.tif`, checksum `5555555555555555` |
| Terrain | `D-200/.../dem.tif`, checksum `6666666666666666` |
| Inundation polygon | `D-200/.../inundated_area.geojson`, checksum `7777777777777777` |
| Stage-transfer line | `D-200/.../stl.geojson`, checksum `8888888888888888` |
| Nominal upstream WSE | 103.1 m |
| Termination | `volume_convergence` |
| Same saved grid | The implemented edge check also found a disallowed edge violation. |
| Datum | NAVD88 metres for terrain and resulting WSE |

### Upstream hot-start source manifest

| Required field or asset | Synthetic observation |
| --- | --- |
| Reach | `U-100` |
| `scenario_code` | `ND3.0E04Q400` |
| `identity_hash` | `a1b2c3d4` |
| Final depth | `U-100/.../nd=3.0E04/q=400/depth.tif`, checksum `9999999999999999` |

## Synthetic transfer samples

The downstream STL touches five upstream grid-cell centers.
Map each coordinate through the downstream grid and use the supplied values.

| Point | Downstream depth | Downstream terrain | Calculated WSE | Source state |
| --- | ---: | ---: | ---: | --- |
| P1 | 1.2 m | 101.6 m | Calculate | Wet and valid. |
| P2 | 0.8 m | 102.1 m | Calculate | Wet and valid. |
| P3 | 0.0 m | 102.7 m | Calculate | Dry positive-terrain cell. |
| P4 | 0.4 m | -0.4 m | Calculate | Wet with zero calculated WSE. |
| P5 | -9999 nodata | 102.4 m | Do not calculate as ordinary data | Depth nodata handling is not established by the packet. |

Use the reviewed current-code rule that a point is written as `HFIX` when calculated WSE is strictly greater than zero.
Current code does not separately require positive depth.

### Synthetic indexing and congruence hazard

One additional upstream-selected query coordinate maps through the declared downstream transform to row `-1`, column `10`.
The localized downstream depth and terrain arrays have equal shape, but the terrain raster's affine transform is shifted one-half cell east from the depth raster and neither raster metadata record is compared with the manifest declaration before sampling.

## Part A: Reconstruct the exact payload

**Core inspection:** Write the `KWSEScenario` portion of the target payload using the exact current field names.

Include:

- `upstream_discharge`;
- `bc_value`;
- case-sensitive `downstream_Scenario`; and
- `hotstart` with `upstream_discharge`, `bc_type`, `bc_value`, and `identity_hash`.

State which component authored the list and which component executes it.
Do not claim that `run_kwse_scenarios` discovers `D-200` or calculates the stage grid.
Also distinguish top-level unknown-key rejection from nested unknown-key ignoring, and explain why exact `downstream_Scenario` capitalization remains required.

## Part B: Inventory required artifacts

**Core inspection:** Create a dependency table with one row for each artifact or record required before the target can run.

At minimum include:

1. Prepared-network adjacency and lineage.
2. Upstream model manifest.
3. Upstream terrain, roughness, inflow line, and centerline.
4. Downstream scenario manifest.
5. Downstream depth, terrain, inundation polygon, STL, domain, and grid.
6. Upstream hot-start source manifest and final depth.
7. Target run settings and expected output address.
8. Target final depth, inundation polygon, STL, nullable Zarr field with current public-job absence, and scenario manifest.
9. Stored plan or equivalent selection provenance.
10. Storage-observation or materialization record.

For each row, state the responsible category: `science`, `planning`, `execution`, `storage`, or `reconciliation`.
Use more than one category only when the boundary genuinely crosses responsibilities.

## Part C: Calculate transfer points

**Core inspection:** Calculate WSE for P1 through P4.
Classify which points current code writes, omits, or cannot safely evaluate from the packet.

Explain:

- why P3 can become an `HFIX` point despite dry source depth;
- why P4 is omitted despite positive depth;
- why P5 needs explicit nodata behavior rather than ordinary addition; and
- why `bc_value=103.0` does not overwrite the retained point values.

Then identify the datum, units, CRS, transform, bounds, and valid-mask checks required before accepting the calculations.
Explain the different current outcomes for a positive out-of-range index and the supplied negative index.
Explain why equal array shape does not resolve the mismatched-transform case.

## Part D: Trace current execution and failure points

**Core inspection:** Put these steps in exact current order and name one material failure at each applicable step.

- validate target payload and upstream model manifest;
- read downstream scenario manifest;
- create `QFIX`, `FREE`, and `TRANSFER` objects;
- reconstruct and read hot-start manifest;
- predict target address and check exact input reuse;
- localize assets and create boundary elements;
- write `lisflood.bci` and `lisflood.par`;
- execute and monitor LISFLOOD-FP;
- post-process final saved state;
- build output manifest;
- publish assets and manifest;
- return manifest paths and warnings.

Your failure analysis must include these cases:

1. Missing downstream scenario manifest.
2. STL wholly outside the upstream domain.
3. Transfer coordinates outside valid downstream raster coverage.
4. No retained positive WSE points.
5. Missing hot-start source manifest.
6. Solver produces no `.wd` file.
7. Publication fails after `depth.tif` is copied but before the manifest is written.
8. Convergence and a disallowed edge violation occur on the same saved grid.
9. The solver `.wd` profile has no CRS for this EPSG:26918 model.

Also state why the reviewed KWSE `save_zarr` input does not enable Zarr creation even though the input field exists, and why a generic `RunConfig.save_zarr=true` call cannot complete current manifest construction.

Evaluate a second hot-start payload that is identical except for explicit `"identity_hash": null`.
State whether schema validation accepts it, whether current job code resolves it to `a1b2c3d4`, and which production contract choice is required.

## Part E: Reconstruct identity and address

**Core inspection:** Use the current path pattern to write the target manifest address beneath the synthetic results root `s3://synthetic/results`.

Use:

- reach ID `U-100`;
- model identity half `11111111`;
- run identity hash `a1b2c3d4`;
- nominal stage 103.0; and
- discharge 400 m3/s.

Then list at least four scientifically material inputs not included in the current run identity.
Explain why an eight-character folder hash and exact manifest-input reuse still do not prove asset presence, plan validity, materialization, or hydraulic adequacy.
State the current one-decimal KWSE naming precision and explain how two distinct raw stage values can target one address even when exact input comparison refuses reuse.

## Part F: Distinguish publication from materialization

**Core inspection:** Evaluate this synthetic outcome.

The job returned the target manifest path.
Storage contains `depth.tif` and `inundated_area.geojson`.
The target `stl.geojson` is absent.
The target `scenario_manifest.json` is present and still references the absent STL.

Answer:

1. Did the job report success under the given observation?
2. Is the scenario fully published?
3. Is the scenario materialized for a contract requiring all manifest assets?
4. Can it be a valid source for the next upstream reach?
5. Which component should detect and record the gap?

State the exact additional observations needed before materialization.

## Part G: Classify responsibilities

**Core inspection:** Assign one primary category to each responsibility.

| Responsibility | Category to supply |
| --- | --- |
| Select DR-032 and DR-033 methodology and acceptance evidence | Your answer |
| Calculate target stage grid and bind a downstream source run | Your answer |
| Convert source depth plus terrain into point `HFIX` entries | Your answer |
| Persist bytes at addressed object paths | Your answer |
| Observe manifests and assets and record current materialized state | Your answer |
| Define vertical-datum compatibility and dry-cell transfer policy | Your answer |
| Produce current run-specific STL during post-processing | Your answer |
| Trigger affected upstream work after downstream source changes | Your answer |

Defend any boundary where another category supplies required input or review.

## Part H: Issue a readiness verdict

**Core inspection:** Choose exactly one verdict.

- `READY FOR UPSTREAM LIBRARY USE` means the packet proves plan authority, topology, source suitability, transfer coverage, datum compatibility, intended dry-cell policy, exact artifacts, identity, materialization, convergence, edge adequacy, hot-start independence, and hydraulic acceptance.
- `NOT READY` means one or more conditions remain unsupported or contradictory.

State the verdict first.
Then list the smallest follow-up evidence set that could change it.

## Deliverable

Submit a short answer with these sections:

1. Exact target payload.
2. Required artifact and responsibility table.
3. Transfer-point calculations and limitations.
4. Ordered execution and failure trace.
5. Identity and expected address.
6. Publication and materialization assessment.
7. Direct readiness verdict.

## Competency criteria

The lab is complete when the answer:

- preserves `downstream_Scenario` capitalization;
- attributes list construction to planning and list execution to the current job;
- calculates P1 as 102.8 m, P2 as 102.9 m, P3 as 102.7 m, and P4 as 0.0 m;
- states that current code writes P1, P2, and P3, omits P4, and cannot safely treat P5 as ordinary data;
- explains nominal `bc_value` separately from pointwise `HFIX`;
- identifies every required source and target artifact;
- places exact reuse before localization and simulation;
- identifies that current KWSE code does not forward `save_zarr` into `RunConfig`;
- identifies that the generic true branch creates a directory-backed Zarr store and then fails during file-only hashing;
- states that positive out-of-range indices raise while negative indices can wrap to the opposite raster edge;
- rejects equal array shape as proof of matching transform, bounds, CRS, units, datum, nodata masks, or cell registration;
- identifies convergence priority over a simultaneous edge violation;
- identifies the literal EPSG:5070 fallback as incorrect for the synthetic EPSG:26918 model when `.wd` CRS is absent;
- explains that explicit `identity_hash: null` is schema-valid but is not resolved to the current run identity by current job code;
- explains the one-decimal KWSE address collision risk;
- derives `s3://synthetic/results/reach=U-100/11111111/a1b2c3d4/kwse=103.0/q=400/scenario_manifest.json`;
- identifies the missing STL as a materialization failure despite the present manifest;
- classifies responsibilities across all five categories;
- explains downstream-to-upstream dependency and serial seed order; and
- issues `NOT READY` because the synthetic packet lacks complete transfer policy and hydraulic acceptance evidence.

After completing the lab, compare the reasoning with [Lab 11 Solution](solutions/lab-11-trace-kwse-stage-transfer-solution.md).
