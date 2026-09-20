# Lab 11 Solution: Trace KWSE Stage Transfer

This solution applies the reviewed current contracts to the synthetic packet.
It does not validate a project reach, approve the current dry-cell rule, or prove deployed orchestration.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, execution boundary, and readiness stopping condition are defined in [Lab 11](../lab-11-trace-kwse-stage-transfer.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and keeps planning, execution, publication, materialization, and acceptance distinct.
Stop when the answer satisfies the prompt's competency criteria or retains `NOT READY` with the smallest evidence set that could change the verdict.

## 1. Exact target payload

The exact scenario object is:

```json
{
  "upstream_discharge": 400,
  "bc_value": 103.0,
  "downstream_Scenario": "<D-200 ND scenario manifest path>",
  "hotstart": {
    "upstream_discharge": 400,
    "bc_type": "ND",
    "bc_value": 0.0003,
    "identity_hash": "a1b2c3d4"
  }
}
```

Planning owns selection of 400 m3/s, the 103.0 m grid target, the bound `D-200` source, and the hot-start sequence.
The current `run_kwse_scenarios` job receives the ordered list and executes it.
The job does not discover network adjacency or calculate the stage grid.

Only the top-level `RunKWSEScenariosInputs` model forbids extras, so an unknown top-level key is rejected.
Nested `KWSEScenario` and `HotStart` use default Pydantic extra handling, so unknown nested keys are ignored.
Missing required nested fields, invalid values, unsupported `bc_type`, invalid identity-hash form, and wrong types that cannot be validated still fail.
The required field name remains case-sensitive because `downstream_scenario` is treated as an ignored extra and does not satisfy missing `downstream_Scenario`.

An otherwise identical hot-start object with `"identity_hash": null` is schema-valid because the field permits null.
Current job code passes that null into source-address construction instead of resolving it to `a1b2c3d4`, so it fails to locate the intended current-identity source.
The production contract must either forbid explicit null or resolve it to the current run identity before address construction through validated model construction.

The dependency points downstream to upstream because the completed `D-200` scenario supplies boundary evidence for `U-100`.
Within one `U-100` discharge chain, the first KWSE scenario starts from the ND depth at 400 m3/s and each later stage would start from the prior lower stage if the planner ordered such a chain.

## 2. Required artifacts and responsibility

| Required item | Why it is required | Primary responsibility |
| --- | --- | --- |
| Prepared-network adjacency and lineage | Proves `D-200` is the intended downstream neighbor of `U-100`. | Planning |
| Stored scenario plan | Proves why 400 m3/s, 103.0 m, the bound source, and hot start were selected. | Planning |
| Upstream model manifest | Supplies target model, domain, grid, assets, and full model ID. | Execution |
| Upstream terrain, roughness, inflow line, and centerline | Supply target hydraulic grid, resistance, forcing geometry, and post-processing endpoint. | Execution |
| Downstream scenario manifest | Supplies bounded source identity, inputs, results, and transfer assets. | Execution |
| Downstream depth and terrain | Supply source WSE as compatible depth plus elevation. | Execution |
| Downstream STL | Supplies transfer geometry. | Execution |
| Downstream inundation polygon | Supplies current outer-edge `FREE` geometry. | Execution |
| Downstream domain and grid | Map transfer coordinates into source row-column indices. | Execution |
| Hot-start source manifest and depth | Supply durable initial-depth provenance and file. | Execution |
| Target run settings and expected address | Define exact reuse and execution settings. | Planning |
| Target depth, inundation polygon, STL, nullable Zarr status, and manifest | Form the current output contract; the public job does not currently produce Zarr. | Execution |
| Bytes at every named address | Make the producer's outputs available to consumers. | Storage |
| Observed asset and manifest proof | Establish that current intent is materialized rather than merely returned. | Reconciliation |
| Datum and dry-cell transfer rules | Define whether arithmetic and boundary-point selection are scientifically defensible. | Science |

Planning depends on scientific policy and observed downstream evidence.
Execution consumes the plan and creates bounded outputs.
Storage persists bytes but does not decide scientific meaning.
Reconciliation observes storage against intent but does not make a hydraulically inadequate scenario adequate.

## 3. Transfer-point calculations and limitations

The current transfer equation is `WSE = depth + terrain`.

For P1:

\[
WSE_1=1.2+101.6=102.8\ \text{m}
\]

For P2:

\[
WSE_2=0.8+102.1=102.9\ \text{m}
\]

For P3:

\[
WSE_3=0.0+102.7=102.7\ \text{m}
\]

For P4:

\[
WSE_4=0.4-0.4=0.0\ \text{m}
\]

Current code writes P1, P2, and P3 because each WSE is strictly positive.
P3 demonstrates the current limitation: dry depth does not prevent a positive-terrain cell from becoming `HFIX`.

Current code omits P4 because its WSE is not greater than zero, even though its depth is positive.
This proves that the current filter is a WSE-sign filter rather than a wet-cell filter.

P5 cannot safely be treated as ordinary arithmetic from the packet.
Adding a nodata sentinel to terrain would create a meaningless value unless the source representation and masking behavior are explicitly handled.
Current transfer code performs raw array addition without a separate nodata mask, so a common large negative sentinel would likely be omitted by the later positive-WSE test without being recognized as nodata.

The nominal `bc_value` of 103.0 m names the target stage and output folder.
It does not replace 102.8, 102.9, or 102.7 m with one uniform value.

Acceptance also requires compatible horizontal CRS, vertical datum, units, transforms, array bounds, exact cell coordinates, and valid-data masks.
The synthetic packet states compatible NAVD88 metres for the two terrains, but it does not establish an authorized dry-cell policy or nodata behavior.

The additional query maps to row `-1`, column `10`.
Current NumPy indexing can interpret row `-1` as the opposite raster edge and silently return a plausible value from the wrong cell, whereas a positive row or column beyond the array size raises an index error.
An explicit bounds check is required before sampling.

Equal depth and terrain array shapes do not make their cells congruent.
The supplied half-cell transform shift means the arrays represent different horizontal supports even though element-wise addition is mechanically possible.
Acceptance requires depth-terrain-manifest agreement in shape, affine transform, bounds, CRS, horizontal units, vertical datum, nodata masks, and cell registration.

## 4. Ordered execution and failure trace

1. `Job.run` validates the request and creates temporary workspace.
Unknown top-level fields and invalid required values fail before job logic, while unknown fields nested inside `KWSEScenario` or `HotStart` are ignored under their default Pydantic configuration.
2. The job reads and validates the upstream model manifest.
A missing or invalid model manifest stops the job.
3. For the scenario, the job reads and validates `downstream_Scenario`.
A missing downstream manifest stops before target construction.
4. The job creates upstream `QFIX`, downstream-inundation-based `FREE` at 0.5 m/m, and downstream-asset-based `TRANSFER` objects.
Wrong or absent source fields prevent valid construction.
5. The job constructs the target input and then reconstructs the hot-start source address.
A missing or invalid source manifest stops hot-start resolution.
An explicit null identity passes schema validation but is not replaced with the target run identity, so that accepted payload does not locate the intended source.
6. `run_scenario` predicts the target manifest address and checks exact stored `RunScenarioInputs` equality.
A valid exact match returns without localization or simulation, although referenced assets still need independent observation.
7. For a new run, preprocessing localizes all required assets and creates boundary elements.
An STL wholly outside the upstream domain can produce no transfer elements.
Positive coordinates outside downstream array bounds raise during sampling, while negative indices can wrap silently to the opposite edge.
The current path does not verify depth-terrain-manifest shape, transform, bounds, CRS, horizontal units, vertical datum, nodata masks, or registration before addition and sampling.
No retained positive WSE points raises the custom transfer failure after points exist.
8. The writer creates `lisflood.bci` and `lisflood.par`.
File or asset conversion failures stop before execution.
9. The solver starts and the watcher monitors saved grids, convergence, edges, wall time, and process exit.
A nonzero process exit raises an error.
For the supplied simultaneous convergence and disallowed edge violation, current branch order persists `volume_convergence` and suppresses `edge_error` as the stored reason.
The convergence reason therefore does not prove the saved grid had acceptable edges.
10. Post-processing requires at least one `.wd` file.
No saved grid raises `FileNotFoundError`.
11. Current public-job code creates final depth, inundation, run-specific STL, and the scenario manifest object with a null Zarr field.
Invalid contour or raster processing prevents completion.
The reviewed KWSE job accepts `save_zarr` but does not forward it into `RunConfig`, so that public input cannot activate the optional Zarr branch in this path.
If a generic caller supplies `RunConfig.save_zarr=true`, the branch creates a directory-backed store and then fails when file-only hashing tries to open the directory, so manifest construction does not complete.
When the solver `.wd` profile omits CRS, depth conversion assigns EPSG:5070 instead of recovering the target model's EPSG:26918 CRS.
That fallback mislabels the synthetic depth and inherited inundation CRS and can break STL, transfer, and compositing compatibility.
12. Publication copies depth, inundation, STL, and then the manifest for the current public path.
A failure after copying depth can leave a partial addressed directory because publication is not transactional.
13. The job appends the manifest href and returns `manifests` plus an empty warning list under reviewed code.
A returned path is not independent storage observation.

## 5. Identity and expected address

The expected current manifest address is:

```text
s3://synthetic/results/reach=U-100/11111111/a1b2c3d4/kwse=103.0/q=400/scenario_manifest.json
```

The result path uses the model identity half `11111111`, not full model ID `11111111_N30S20E25W25`.
The complete model ID remains inside the scenario manifest.

The current run identity omits at least:

1. Solver executable version and build.
2. Container image digest.
3. Complete `RunConfig`.
4. Boundary geometries and values.
5. Downstream source scenario and transfer assets.
6. Hot-start source.
7. Hardware environment.
8. Full model domain realization.

Exact manifest-input reuse checks many of these values through full `RunScenarioInputs` equality.
It still does not verify every referenced asset exists or matches its checksum, that the plan remains scientifically intended, that all required targets are complete, or that the result is hydraulically adequate.

KWSE boundary names use one decimal place.
Distinct raw stages such as 103.01 m and 103.04 m can both target `kwse=103.0` even though exact input comparison refuses to reuse a stored manifest whose raw input differs.
The later run can still overwrite or mix files at the shared address unless the contract validates values onto the naming grid, increases precision, or uses a content-complete identity.

The eight-character hash is also a truncated recipe identifier rather than a complete scientific proof.

## 6. Publication and materialization assessment

The job reported success according to the synthetic observation because it returned a manifest path.
That report cannot be used as proof of complete storage state.

The scenario is not fully materialized under a contract requiring all manifest assets because `stl.geojson` is absent.
The present manifest makes the inconsistency more explicit: it claims an asset that storage does not contain.

The scenario cannot be accepted as a valid boundary source for the next upstream reach because the next transfer requires the STL.
Using only the present depth and inundation polygon would bypass the selected and current transfer contract.

Reconciliation should detect the gap by observing the target manifest and every required asset at the expected address.
The observer should verify at least:

- manifest readability and schema;
- reach ID, scenario code, run identity, full model ID, and self address;
- existence of depth, inundation polygon, and STL;
- explicit Zarr absence for the current public path;
- depth CRS agreement with the realized EPSG:26918 model rather than the missing-CRS EPSG:5070 fallback;
- checksums for every required asset;
- complete match to the stored plan and current revision; and
- no refused source dependency.

Storage owns the actual presence of bytes.
Reconciliation owns the conclusion that those bytes satisfy current intent.
Science still owns whether the result is acceptable for hydraulic use.

## 7. Responsibility classification

| Responsibility | Primary category | Boundary note |
| --- | --- | --- |
| Select DR-032 and DR-033 methodology and acceptance evidence | Science | Planning implements only an authorized, testable policy. |
| Calculate target stage grid and bind a downstream source run | Planning | It consumes selected science and observed source libraries. |
| Convert source depth plus terrain into point `HFIX` entries | Execution | Science defines validity requirements and reviews the rule. |
| Persist bytes at addressed object paths | Storage | Execution initiates writes, but storage is the persistence layer. |
| Observe manifests and assets and record materialized state | Reconciliation | It compares storage evidence with current intent. |
| Define vertical-datum compatibility and dry-cell transfer policy | Science | Execution must validate and realize the resulting contract. |
| Produce current run-specific STL during post-processing | Execution | Science determines whether the method is acceptable. |
| Trigger affected upstream work after downstream change | Reconciliation | Planning supplies dependency provenance and target scenarios. |

## 8. Readiness verdict

**NOT READY.**

The packet is synthetic and does not establish an authorized dry-cell or nodata transfer policy, bounds-safe and congruent source rasters, current asset materialization, separately persisted convergence and edge evidence, hot-start independence, collision-safe addressing, correct scenario-output CRS propagation, or hydraulic acceptance.
The supplied missing-STL outcome also directly fails the materialization contract and prevents use as a source for the next upstream reach.

The smallest follow-up evidence set that could change the verdict includes:

1. An authorized wet-cell, nodata, nonpositive-WSE, and interpolation policy aligned with code and tests.
2. Cell-by-cell transfer evidence showing bounded source indices, matching transforms and registration, valid masks, depth, terrain, WSE, horizontal units, datum, and resulting `HFIX` entries.
3. A durable plan recording bounds, targets, source bindings, skips, and seed order under an authorized planner revision.
4. Storage observation and checksum verification for every source and target artifact, including the missing STL.
5. Persisted convergence history, complete mass-balance evidence, edge diagnostics that remain visible when convergence is simultaneous, solver build, and failure evidence.
6. Cold-start and alternative-hot-start sensitivity for the target scenario.
7. Hydraulic continuity and accuracy evidence across the two-reach overlap.
8. A separate scientific acceptance decision for the intended upstream-library and composite use.
9. A resolved explicit-null hot-start contract, collision-safe boundary addressing, and propagation of the realized model CRS into scenario products.
