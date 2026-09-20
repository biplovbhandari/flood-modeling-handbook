# Decision-Code-Artifact Crosswalk

The crosswalk connects scientific meaning, selected methodology, current code, job contracts, artifacts, project evidence, and unresolved questions.
Each record preserves source status instead of implying that every layer agrees.

## Record schema

### Concept

Name the scientific or methodological topic in stable handbook language.

### Scientific foundation

State the physical, mathematical, statistical, or geospatial principle that the project choice depends on.

### Decision record and status

Name the Decision Register record, selected alternative, and current status.
If no applicable decision exists, state that absence as an Open question.

### Current code

Name the current checkout location that implements, partly implements, or contradicts the concept.
Use `Not in the current jobs checkout` when the responsibility lies elsewhere or is absent.

### Job input

Name the typed input or configuration value that carries the choice into a job.
Use `No current job input` when the choice is outside the current job boundary.

### Artifact

Name the manifest, raster, vector, metric, or other output that can show what was realized.
Do not treat a returned path or process exit code as proof that the artifact is scientifically adequate.

### Evidence

Name the case, issue, experiment, test artifact, presentation, or other bounded evidence relevant to the mapping.
Preserve the source's scope and result.

### Validation question

Ask the most useful question for deciding whether the implementation and artifacts satisfy the scientific intent.

### Open conflict

Name the unresolved difference, missing consumer, weak evidence, or authority gap.
Link to the corresponding record in [Conflicts and Open Questions](conflicts-and-open-questions.md) when one exists.

## Initial records

### XW-001: KWSE edge boundary behavior

**Concept:** Downstream edge treatment for a KWSE scenario.

**Scientific foundation:** A downstream boundary controls how water leaves the domain and can influence upstream WSE, inundation, and the transition zone.

**Decision record and status:** DR-003 ALT-D, downstream-FIM-informed edge cells get freefall, Alternate Selected.

**Current code:** `twod_fim_jobs/jobs/run_kwse_scenarios.py` supplies the downstream inundation polygon and a steep normal-depth slope of 0.5 m/m, while `twod_fim_jobs/models/solvers.py` represents slope-based outflow with the `FREE` token.
`twod_fim_jobs/hydraulic_solvers/pre_process.py` intersects the polygon with each cardinal domain edge, reduces all intersections on one edge to their overall bounds, and emits one contiguous span per touched edge, including dry gaps between disjoint intersections.

**Job input:** The downstream scenario manifest supplies the inundation polygon used to derive the current contiguous cardinal-edge spans.

**Artifact:** The scenario manifest, final depth raster, inundation polygon, and STL describe or show the realized scenario.

**Evidence:** DR-003 cites Case-001 and ISU-003 as evidence against freefall on all edges, but that scoped evidence does not establish equivalence between the selected and implemented conditions.

**Validation question:** Does the implemented slope-based contiguous-span treatment reproduce the intended no-resistance behavior without distorting transition-zone WSE or releasing water across dry gaps or unintended edges?

**Open conflict:** See [CONF-001](conflicts-and-open-questions.md#conf-001-boundary-condition-terminology-and-behavior).

### XW-002: Quasi-steady termination

**Concept:** Automated termination based on modeled water-volume change.

**Scientific foundation:** Storage continuity relates storage change to inflow, outflow, and sources or sinks, while a full mass balance accounts for every material term.

**Decision record and status:** DR-022 ALT-G selects volume convergence and DR-028 ALT-A selects \(10^{-3}\), both Alternate Selected.
The standalone DR-022 file also marks ALT-J as `#current`, while its history and the reviewed register identify ALT-G.
The registered ALT-G selection remains methodology within its recorded status and scope, and the competing marker remains an Open question.

**Current code:** `twod_fim_jobs/hydraulic_solvers/run.py` calculates positive-depth storage from consecutive saved `.wd` grids, divides the absolute domain-storage difference by inflow volume over `save_interval_seconds`, and terminates when the ratio is strictly below the configured tolerance.
The first saved grid receives a sentinel value of `1` because no preceding grid exists.
If convergence and a disallowed edge violation are both true on a later saved grid, current branch order persists `volume_convergence` and suppresses `edge_error` as the stored reason.
The ND loop can then continue through publication and comparison, and the KWSE job can return the scenario without a job warning for that simultaneous violation.
The watcher also records `max_wall_time` after the configured wall-time limit is exceeded, sends terminate, waits three seconds, and kills the process if it has not exited.
The public ND and KWSE input models place no positive or finite bound on convergence tolerance, simulation length, save interval, or wall-time limit.
A nonpositive convergence tolerance can prevent the strict-less-than convergence branch from triggering, while nonpositive time or save controls can create an immediate termination or invalid solver-output schedule.
These are accepted-value consequences, not evidence of operational occurrence.

**Job input:** `volume_convergence_tolerance`, `max_simulation_length_seconds`, `save_interval_seconds`, `max_simulation_wall_time_seconds`, upstream discharge, and grid resolution determine the calculation, solver schedule, and termination comparisons.

**Artifact:** The scenario manifest records the final volume-convergence metric and termination condition.
It does not persist the complete history, a simultaneous suppressed edge reason, boundary-check details, outflow terms, raw process return code, termination-signal outcome, terminate-to-kill escalation, or a full balance residual.

**Evidence:** DR-028 identifies EXP-014 and Case-018 as preliminary support for the selected threshold.
The standalone EXP-014 file supplies the methodology plan, while Case-018 records the completed metric tables and analysis conducted under that heading.

**Validation question:** Do request models first require finite positive convergence and time controls before representative reaches, flow regimes, output intervals, and defensible initial conditions are used to show that the storage-change threshold stops only after local hydraulic change, edge behavior, full balance, depth, WSE, and extent are adequate for the intended product?

**Open conflict:** See [CONF-002](conflicts-and-open-questions.md#conf-002-volume-convergence-and-mass-balance) and [CONF-020](conflicts-and-open-questions.md#conf-020-scenario-request-boundary-validation).

### XW-003: Hydraulic solver support

**Concept:** Solver selection for scenario execution.

**Scientific foundation:** Different solvers can implement different equations, approximations, numerical methods, boundary semantics, and output contracts.

**Decision record and status:** The current Decision Register does not establish SFINCS as an implemented equivalent execution path.

**Current code:** Solver enums and SFINCS preprocessing methods exist, but `write_model_files` raises `NotImplementedError` for SFINCS, version discovery is unimplemented, and scenario execution, monitoring, and post-processing call LISFLOOD-FP paths.

**Job input:** The environment can name a scenario solver, but choosing SFINCS does not produce a supported run in the current checkout.

**Artifact:** Current scenario manifests record a run identity hash derived only from the solver enum and baked `SDR_COMMIT`.
The underlying identity does not record the executable version or build, image digest, run configuration, or hardware identity and therefore does not prove version-pinned execution.

**Evidence:** The jobs README labels the SFINCS image target as not implemented, while generated job documentation lists SFINCS as a dependency option.
The local solver-comparison document describes the solvers as approved and interchangeable, but that candidate or target statement conflicts with the current code path.

**Validation question:** Which version-pinned executable, build or image, configuration, hardware, solver-specific behaviors, artifacts, convergence checks, and boundary contracts must pass before SFINCS can be described as supported?

**Open conflict:** See [CONF-003](conflicts-and-open-questions.md#conf-003-sfincs-documentation-and-current-support).

### XW-004: Inflow placement and model domain

**Concept:** Inflow-line geometry, upstream placement, grid resolution, and initial model extent.

**Scientific foundation:** Boundary placement and domain extent affect how forcing enters the grid, how flow develops, and whether inundation reaches artificial edges.

**Decision record and status:** DR-011 ALT-D, DR-013 ALT-A, DR-014 ALT-B, DR-015 ALT-A, DR-016 ALT-A, and DR-017 ALT-A are Alternate Selected.
DR-014 ALT-B selects point inflows distributed along a headwater reach.
The standalone DR-011 file also marks ALT-E as `#current`, so the registered ALT-D selection remains methodology within status and scope while the file-marker conflict remains open.

**Current code:** `twod_fim_jobs/models/build_model.py` and `twod_fim_jobs/consts.py` define configurable inputs whose defaults include 10 m resolution, a 0.1 upstream-walk fraction, bankfull-width multiplier 1.0, and centerline-buffer multiplier 15.
`walk_us_dist_pct` is constrained only to values greater than zero rather than the documented fraction range at or below 1.
`centerline_buffer_bankfull_multiplier` has no bound, so zero and negative values pass validation.
The field descriptions say that `upstream_reach_ids` are immediate upstream reaches and that `upstream_mainstem_reach_id` is the largest-drainage-area immediate upstream reach or null for a headwater.
Current code trusts the caller and does not verify completeness, adjacency, membership of the mainstem in the upstream list, largest drainage area, or that null identifies a true network headwater.
When the caller supplies null for `upstream_mainstem_reach_id` and false for `ds_of_lake`, `twod_fim_jobs/utils/geospatial.py` follows the null branch directly and creates one perpendicular inflow line at the reach start rather than the distributed points selected by DR-014.
The same module floors the exact reach-centroid coordinates to the model grid to create `domain.anchor`.
`twod_fim_jobs/jobs/build_model.py` exports that anchor and stores it in the model-manifest schema field named `reach_centroid`.
When no authored bbox is supplied, current code includes the target reach, inflow line, buffered target centerline, retained upstream-mainstem segment, and caller-supplied extra geometries in total bounds, applies `domain_buffer`, and snaps minima down and maxima up to the grid.
When an authored bbox is supplied, current code validates positive extent and grid alignment and then uses the bbox exactly without those computed-domain buffers or extra-geometry bounds.
The current job creates one rectangle and does not implement DR-012 domain expansion.
Input validation requires only a positive EPSG integer and does not enforce a projected CRS with metre linear units.
Geometry, bankfull-width buffers, later slope length, per-unit-width discharge, squared cell area, stored volume, and flooded-area calculations assume metres unless explicit conversions are applied.

**Job input:** `upstream_reach_ids`, `upstream_mainstem_reach_id`, `ds_of_lake`, `grid_resolution`, `walk_us_dist_pct`, `bankfull_width_multiplier`, `centerline_buffer_bankfull_multiplier`, `other_geometries`, and optional authored `domain` control the realization.
The documented contracts require `0 < walk_us_dist_pct <= 1` and `centerline_buffer_bankfull_multiplier > 0`, while any finite upper buffer limit requires authorization by the domain-method owner.

**Artifact:** The model manifest, inflow GeoJSON, domain GeoJSON, DEM, roughness raster, model identifier, and grid-snapped domain-anchor GeoJSON record the built result.
The anchor artifact is exposed through the field named `reach_centroid`, creating a code-schema naming mismatch that artifact consumers must not interpret as exact geometry.
An outflow-area polygon or STL found beside a model is not a current `build_model` manifest asset and needs separate producer and provenance evidence.

**Evidence:** Decision records cite cases and issues for domain clipping, excessive extent, and inflow behavior, but the defaults are not a complete encoding of every selected parameter.

**Validation question:** For each built model, does authoritative prepared-network evidence prove completeness, adjacency, mainstem membership and selection, and true headwater status before explicit inputs or orchestrator-authored geometry are accepted?
Do those inputs realize selected inflow and domain methodology despite differing code defaults, and does any headwater model implement DR-014's distributed-point method rather than the current single line?
Can an artifact consumer distinguish the grid-snapped domain anchor from the exact reach centroid despite the current schema field name?
Which authorized input-contract owner will enforce the documented walk fraction, a positive centerline buffer, any required finite upper buffer limit, and evidence that the realized inflow and corridor remain inside the intended geometry?

**Open conflict:** See [CONF-004](conflicts-and-open-questions.md#conf-004-selected-methodology-and-code-defaults).

### XW-005: Below-water topobathymetry

**Concept:** Representation of channel geometry below the LiDAR-observed water surface.

**Scientific foundation:** Missing channel bathymetry can reduce represented conveyance and bias depth, WSE, velocity, and inundation.

**Decision record and status:** The reviewed Decision Register lists DR-021 ALT-A, no handling, with Alternate Selected status.
The standalone DR-021 file also marks ALT-E `#current`, while its decision history says ALT-G was selected later and ALT-G itself has no `#current` marker.
The registered status and file-local record are therefore inconsistent, so no one alternative is unambiguous handbook authority at this revision.

**Current code:** `build_model` clips and resamples the supplied DEM and does not add a selected bathymetric correction method.

**Job input:** `dem_source` selects the terrain source, but there is no current topobathymetry-policy input.

**Artifact:** The model DEM and model manifest show the terrain realization used by a scenario.

**Evidence:** DR-021 records Case-015 as rejecting no handling because of WSE higher than benchmark FIM, and EXP-012 proposes a scoped comparison of including and excluding below-LiDAR bathymetry.

**Validation question:** Which alternative should authorized project governance record consistently, and for which reach types does the resulting method produce acceptable conveyance, WSE, depth, velocity, and inundation under stated evidence thresholds?

**Open conflict:** See [CONF-005](conflicts-and-open-questions.md#conf-005-topobathymetry-policy).

### XW-006: Scenario-library bounds and sampling

**Concept:** Selection of discharge and downstream-stage points for ND and KWSE libraries.

**Scientific foundation:** Discharge bounds, hydraulic-response sampling, and downstream-stage grids determine which portion of a reach's response a scenario library represents.
The event, statistic, dataset variable, time support, and derivation of every source discharge must be defined before the bound has a reproducible scientific meaning.

**Decision record and status:** DR-001 ALT-A, For All Reaches, is Alternate Selected.
DR-029 ALT-A, Fixed Recurrence Interval Bounds from NWM Retrospective, is Alternate Selected.
DR-030 ALT-C, Adaptive Discharge Stepping Based on Hydraulic Response, is Alternate Selected.
DR-032 ALT-D, Same as D/S Reach Max and Min STL WSEL for Nearest Discharge that is Below Reach's Own Discharge, is Alternate Selected.
DR-033 ALT-B, Snap to a Per-Reach Standard Stage Grid, is Alternate Selected.
DR-029 names \(Q_{HFT}\) as a high-flow-threshold input but does not define its event, statistic, dataset variable, time support, or derivation in the reviewed project record.
That scientific definition remains an Open question and cannot be inferred from the symbol.
DR-030 describes maximum and median stage at fixed monitoring points and an extent criterion, with stated bands of 0.75 through 1.25 m, 0.25 through 0.75 m, and 7.5 through 12.5 percent.
The reviewed Decision Register has no DR-041 row, although the current `Q_GRID_RESOLUTION` code comment cites DR-041.
That code reference is an Open question and does not establish selected-methodology authority for the discharge-grid contract.

**Current code:** `twod_fim_jobs/models/run_nd_scenarios.py` defines positive whole-m3/s `min_upstream_inflow` and whole-m3/s `max_upstream_inflow` inputs.
The maximum has no positivity constraint and is not required to be at least the minimum.
Convergence and time controls have no positive or finite bounds, and adaptive response-band validation checks only the span, allowing negative endpoints when the span is large enough.
It also defines caller-supplied `existing_scenarios` and positive whole-m3/s `q_grid_resolution`.
`twod_fim_jobs/jobs/run_nd_scenarios.py` re-adopts existing manifests only when the reach ID, full model ID, run identity hash, and inclusive discharge range match, then re-judges them under the response bands currently in force.
This predicate does not compare full `inputs`, `RunConfig`, boundary geometry or slope, hot start, convergence tolerance, output interval, wall-time setting, or referenced-asset existence.
It is therefore not full-input equality, unlike `check_run_exists` for an exact target scenario.
When the minimum baseline or a later requested discharge is not already in the adopted `done` set, `run_scenario` can still reuse a manifest at the predicted address when its full stored `RunScenarioInputs` equal the request.
The baseline branch does not inspect `edge_error`, so a newly simulated or exactly reused edge-error baseline can become the reference.
For a later exact-reused edge-error manifest, the existing object remains in storage but the ND loop returns partial prior comparisons plus `WaterOnEdgeWarning` before comparison or a publication call.
A newly simulated later edge-error trial takes the same return before publication, while a loose adopted edge-error manifest already in `done` bypasses that later termination check.
The `_adopt_existing` path does not validate that an adopted discharge is aligned to `q_grid_resolution`.
The job includes the minimum endpoint directly, sets the initial trial to `min_upstream_inflow + delta_upstream_inflow`, and can use the maximum endpoint directly without checking grid alignment.
Finite-window midpoint proposals and the first below-window candidate calculated by `_propose` are zero-grid aligned.
If the below-window candidate is at or below position, `_propose` returns `position + q_grid_resolution`; an off-grid position therefore produces an off-grid later proposal.
No-window, infinite-close, and at-or-above-maximum branches return the unsnapped maximum.
Current code therefore does not enforce one grid across all ND scenarios.
It publishes every newly simulated non-edge-error trial before the adaptive verdict, including trials later classified as rejected, so publication is not evidence of selected library membership.
The current response curves use running maxima over maximum depth, median depth, and flooded area from every finished scenario point, use straight segments between points, and extend the final positive segment for extrapolation.
The current final-state metrics are maximum and median depth over strictly positive-depth cells and flooded area from wet-cell count times squared raster resolution.
Current default bands are 0.75 through 1.25 m, 0.25 through 0.50 m, and 10 through 15 percent respectively, which do not fully match DR-030's quantities or defaults.
When reference flooded area is zero, current comparison code sets measured flooded-area percentage change to zero.
Proposal construction nevertheless sets both relative area targets to zero, and later positive area points can make both crossings resolve to the reference or lower endpoint of the first rising segment and influence the combined window.
The current verdict is `reject_high` when any ceiling is exceeded, otherwise `accept` when any floor is reached, and otherwise `reject_low`.
Free-pass re-judgments are not appended to the job result, and the result has no explicit selected-member list.
The measured `reject_high` comparison for a finest-grid step remains recorded as high even when control flow treats that step as accepted.
Current maximum behavior likewise forces a non-edge-error maximum into membership without a dedicated returned membership field and continues below it when its measured verdict is high.
For a later requested discharge not already in `done`, an `edge_error` manifest returns partial prior comparisons and a `WaterOnEdgeWarning` before comparison.
A newly simulated trial has not been published, while an exactly reused manifest already remains stored.
The generated ND documentation conflicts with current code where it describes rejected trials as unpublished temporary files, limits response comparisons to accepted scenarios, uses `None` for baseline and maximum, or describes the edge-abort result as empty.
`twod_fim_jobs/models/run_kwse_scenarios.py` and `twod_fim_jobs/jobs/run_kwse_scenarios.py` consume and execute a caller-provided scenario list.
The schema permits an empty list and repeats the unbounded convergence and time controls.
An empty request executes no scenario and returns `manifests=[]` with `warnings=[]` without warning that nothing materialized.
For each supplied KWSE scenario, the current runner simulates and publishes when needed or re-adopts an existing manifest whose inputs match exactly.
The manifest-only reuse check does not itself verify every referenced asset.
Neither job reads NWM retrospective flow, extracts annual maxima, fits a flood-frequency distribution, applies the DR-029 factors, computes joint-frequency percentiles, or authors network-wide bounds.

**Job input:** The ND job consumes `min_upstream_inflow`, `max_upstream_inflow`, `delta_upstream_inflow`, `existing_scenarios`, and `q_grid_resolution` in whole m3/s where applicable, along with hydraulic-response bands used for adaptive sampling.
In current implementation, `q_grid_resolution` constrains finite-window midpoint proposals and the first below-window candidate rather than validating every supplied, adopted, endpoint, initial-trial, position-relative fallback, or direct-maximum discharge.
Both jobs also consume convergence tolerance, simulation length, save interval, and wall-time controls without positive or finite bounds.
The KWSE job consumes an ordered `scenarios` list whose entries contain upstream discharge, downstream WSE condition, and downstream scenario provenance, but the list need not contain an entry.

**Artifact:** Scenario manifests record realized job runs, including published ND trials that are not selected library members and KWSE manifests that may have been re-adopted.
An empty KWSE request produces no scenario manifest and returns an empty manifest list with no warning.
Each ND scenario manifest records full scenario inputs, bounded run identity, final termination and response metrics, final depth, inundation polygon, STL, and a nullable Zarr field, but it does not record later re-judgments or membership.
The public ND job does not forward `save_zarr`, and the generic true branch cannot complete manifest construction because file-only hashing is applied to a directory store.
The ND job result records a baseline comparison, main-loop trial comparisons, and job warnings, but omits free-pass comparisons and an explicit selected set.
A selected manifest set or library index must distinguish membership from the broader set of published trials.
A separate durable plan or desired-state provenance artifact is required to identify the NWM source version and reach, exact record period, AMS extraction, frequency fit, uncertainty, stationarity assumption, DR-029 calculation, rounding policy, authored bounds, and intended scenario set.
Target materialization must observe the required manifest and assets in storage rather than infer their presence from publication, re-adoption, the limited ND adoption predicate, or a returned path.

**Evidence:** EXP-015 is Evidence or experiment and proposes a joint-frequency analysis of adjacent NWM version 3.0 reaches using annual maxima, LP3 fits, recurrence normalization, and empirical or fitted bivariate distributions.
The proposed conditional percentiles address downstream-flow and KWSE scenario planning rather than validating or replacing DR-029's per-reach bounds.

**Target design:** The system-design guide treats `q_*_bound` as authored intent and the adaptive ND `q_set` as emergent.
This is target design rather than verified deployed planning behavior.

**Checked-in controller prototype:** `twod-fim-deployment/orchestrator/recon/plan.py`, `scenarios.py`, `gap.py`, `check.py`, and `observe.py` contain unpinned local prototype code that calculates KWSE targets, binds downstream runs, preserves skips and seed chains, submits missing scenarios, and observes the complete plan.
Its current local ceiling path calculates `others = (1 - own_da / downstream_da) ** 0.7 * downstream_q_upper`, uses the nearest downstream-run discharge at or below the upstream discharge for the floor and clamps to the downstream minimum when none exists, sets `cap = q + others`, rounds the cap up to the first downstream selected discharge or clamps to the selected maximum, takes every downstream run at or below that selected discharge as the candidate pool, uses the pool's maximum achieved WSE as the raw ceiling, and applies an optional authored upper cap with `min`.
The planner refuses nonpositive or inconsistent drainage areas and downstream upper bound, nonpositive stage increment, missing downstream runs or selected discharges, negative or NaN `others`, and a downstream selected discharge with no corresponding run.
This code sits outside the three current modeling jobs, is not verified deployment evidence, and cites DR-042 through DR-045, which are absent from the handbook-reviewed Decision Register.
Those additional formulas therefore have prototype-code authority only in this edition.

**Validation question:** Which component derives, reviews, stores, and supplies the DR-029 bounds, and does durable provenance plus an explicit membership record show that the selected library spans those bounds under the intended source, network, uncertainty, and density rules?
Which request owner requires a positive ND maximum at least as large as the minimum, finite positive convergence and time controls, finite nonnegative response-band endpoints in addition to span checks, and at least one KWSE scenario?
Which current library members are off-grid, and should an authorized contract require validation or snapping for adopted manifests, endpoints, the initial authored-step trial, and position-relative fallbacks?
Should ND adoption require full-input equality or an explicit compatibility contract for run settings, boundaries, hot starts, and referenced assets?
Should baseline, loose-adopted, newly simulated, and exact-reused edge-error manifests follow one consistent abort and publication contract?
Does storage observation confirm every required asset for newly published and re-adopted manifests?
Which authorized metric population and default bands reconcile DR-030 with current code, and which durable trace preserves proposals, measured verdicts, re-judgments, endpoint rules, and selected membership?

**Open conflict:** See [CONF-006](conflicts-and-open-questions.md#conf-006-work-outside-current-modeling-jobs), [CONF-009](conflicts-and-open-questions.md#conf-009-scenario-publication-membership-reuse-and-discharge-grid-authority), [CONF-015](conflicts-and-open-questions.md#conf-015-adaptive-nd-criteria-and-generated-documentation), and [CONF-020](conflicts-and-open-questions.md#conf-020-scenario-request-boundary-validation).

### XW-007: Short-reach, lake, and coast network modification

**Concept:** Preprocessing that changes reach topology or tags reaches before model building and scenario execution.

**Scientific foundation:** Reach segmentation and connectivity determine modeled inflows, downstream dependencies, terminal conditions, and where independent model libraries begin and end.

**Decision record and status:** DR-007 ALT-A, Trim and Tag Reaches Against a Dead Pool Polygon, is Alternate Selected.
DR-023 ALT-B, Merge Continuous Reaches that have Negligible Drainage Area Difference Up to Some River Mile Length, is Alternate Selected.
DR-024 ALT-B, 5% Drainage Area Difference, 5 km Minimum Merged Length, is Alternate Selected.
DR-034 ALT-A, Shrink an Existing Waterbody Dataset, is Needs-Review.
DR-035 ALT-A, Lower Bound be Dead Pool Elevation, is Needs-Review.
DR-036 remains Draft with no alternatives documented.
DR-037 ALT-B, Flows2FIM doesn't Cater Lake Reaches and Lakes are Treated as Breakpoints, is Alternate Selected.
DR-038 ALT-A, Omit Reaches where Reach overlaps NOAA Tidal Surface Coverage, is Alternate Selected.
DR-006 ALT-E is registered for downstream-waterbody-informed edge handling, while its latest standalone history line says the method switched to ALT-D.
The registered selection remains methodology within its status and scope, and the history mismatch remains an Open question.

**Current code:** `twod_fim_jobs/jobs/build_model.py` consumes a prepared reach network, reach identifier, caller-supplied upstream identifiers, caller-supplied upstream-mainstem identifier, and caller-supplied `ds_of_lake` flag.
It trusts those topology inputs and uses the supplied mainstem identifier or null geometry branch without verifying their network semantics.
No current `twod-fim-jobs` job entry point performs the selected short-reach, lake, or coast network modifications.
The current model-manifest assignment populates `properties.downstream_reach_id` from the modeled `reach_id` rather than from the required `reach_to_id` network field.

**Job input:** `reach_network_path` and `reach_id` identify the already prepared network and reach consumed by `build_model`.

**Artifact:** A versioned prepared reach network and its provenance must show direction, topology, source-to-prepared identifier lineage, removed reaches, selection rules, and tags consumed by the modeling jobs.
The current model manifest cannot independently prove downstream adjacency because of its current field assignment.

**Evidence:** The Decision Register notes that some lake and coast selections were drafted from meetings or engineering discussion, and DR-034 remains Needs-Review.

**Validation question:** Which component realizes each network modification, how are direction and topology validated before any reach model is built, and which artifact authoritatively maps source identifiers to prepared identifiers?
How will the current manifest downstream field be corrected or redefined?

**Open conflict:** See [CONF-006](conflicts-and-open-questions.md#conf-006-work-outside-current-modeling-jobs).

### XW-008: Composite FIM pixel calculation

**Concept:** Combination of reach-level results into a composite FIM product.

**Scientific foundation:** Overlapping reach results require an explicit rule because the selected pixel value affects final depth, WSE, extent, and interpretation.

**Decision record and status:** DR-004 ALT-D, Pixelwise Max, is Alternate Selected.

**Current code:** No current `twod-fim-jobs` entry point performs cross-reach composite FIM or Flows2FIM processing.
The unpinned current local file `twod-fim-deployment/orchestrator/scripts/f2f.py` contains a separate export path that reads materialized ND and KWSE records, exports a selected Flows2FIM library, invokes Flows2FIM `controls` and `fim` in Docker for AEP columns, requests VRT output, and post-processes each VRT raster band as `VRTDerivedRasterBand` with `PixelFunctionType` set to `max`.
This is checked-in local implementation evidence outside the modeling jobs and does not prove deployed execution or validated output.

**Job input:** No current modeling-job input requests a cross-reach composite product.
The local script consumes materialized database rows, an optional AOI configuration, flow statistics, a new output directory, source depth grids, and a Flows2FIM image selection.

**Artifact:** The checked-in path writes `scenarios.db`, `start_reaches.csv`, optional `models.gpkg`, an exported depth library, per-AEP `flows.csv`, `controls.csv`, and `depth.vrt` whose bands are rewritten to maximum.
A complete composite provenance record must still identify its source scenarios, selected membership and materialization evidence, quantity, units, grid, datum where relevant, resampling and nodata rules, waterbody handling, image digest, and the applied pixel rule.

**Evidence:** The system-design guide describes target product ownership, and `scripts/f2f.py` is unpinned current local implementation evidence.
Neither source proves deployed operation, successful execution, complete provenance, scenario compatibility, grid or datum compatibility, validation, or scientific acceptance.

**Validation question:** Does the producing component apply pixelwise maximum to compatible quantities, grids, datums, and scenario conditions while retaining source provenance?

**Open conflict:** See [CONF-006](conflicts-and-open-questions.md#conf-006-work-outside-current-modeling-jobs) and [CONF-016](conflicts-and-open-questions.md#conf-016-kwse-transfer-planning-and-materialization-contract).

### XW-009: Stage-transfer geometry and values

**Concept:** Transfer of downstream WSE information into an upstream reach simulation.

**Scientific foundation:** The location, geometry, scenario specificity, and spatial variation of a transferred downstream condition affect upstream WSE and the hydraulic transition between reach models.

**Decision record and status:** DR-008 ALT-B, Intersection of Model Domain and Water Body Polygon Boundary, is Alternate Selected for lake and coastal reaches.
DR-009 ALT-B, At `Reach Outlet`, is Alternate Selected.
DR-025 ALT-B, WSEL Contour From D/S FIM, is Alternate Selected.
DR-026 ALT-B, STL Derived Separately For Each Run, is Alternate Selected.
DR-031 ALT-B, Cell-by-Cell Stage Transfer from Downstream Reach Simulation, is Alternate Selected.

**Current code:** `twod_fim_jobs/jobs/run_kwse_scenarios.py` consumes a downstream scenario and supplies its STL, depth, terrain, grid, domain, and nominal `bc_value` to the transfer boundary.
`twod_fim_jobs/hydraulic_solvers/pre_process.py` calculates WSE as downstream depth plus downstream terrain and writes a cell-specific `HFIX` point only when the result is greater than zero.
The writer does not require positive depth, so a dry source cell with positive terrain can produce a point, while zero or negative WSE is omitted.
The current calculation adds raw arrays without an explicit nodata mask.
It performs no explicit index-range check, so a positive out-of-range index raises while a negative NumPy index can wrap silently to the opposite raster edge.
It also does not verify depth-terrain-manifest shape, affine transform, bounds, CRS, horizontal units, vertical datum, nodata masks, or cell registration before addition and sampling.
The nominal `bc_value` participates in scenario labeling or identity but does not set the per-cell `HFIX` values.
Scenario post-processing produces an STL artifact for each realized run.
It masks nonpositive depth, adds positive depth to terrain, applies multiscale NaN-aware Gaussian smoothing, samples smoothed WSE at the run's upstream centerline endpoint, extracts every contour at that level, maps vertices to cell centers, and clips the result with the final inundation polygon.
Raster resolution, masking, smoothing, contour interpolation, multipart geometry, and clipping therefore contribute transfer-geometry uncertainty.

**Job input:** Each KWSE scenario supplies a downstream scenario manifest, and the downstream manifest provides the transfer geometry and depth or elevation artifacts consumed by the job.

**Artifact:** The downstream and upstream scenario manifests, downstream STL, transfer elevations, and resulting upstream depth raster record the transfer realization.

**Evidence:** Decision records and system-design materials describe intended transfer behavior, while realized scenario artifacts show bounded outputs without independently proving code behavior.

**Validation question:** Does each upstream run apply the selected per-run, cell-specific transfer at the intended location with bounded indices and depth-terrain-manifest agreement in shape, transform, bounds, CRS, horizontal units, vertical datum, nodata masks, cell registration, and scenario provenance?
Should transferred points require positive downstream depth, how should zero or negative WSE be represented, and do the realized points match the intended STL geometry and hydraulic response?

**Open conflict:** See [CONF-006](conflicts-and-open-questions.md#conf-006-work-outside-current-modeling-jobs) and [CONF-016](conflicts-and-open-questions.md#conf-016-kwse-transfer-planning-and-materialization-contract).

### XW-010: Normal-depth edge decision authority

**Concept:** Methodology authority for edge conditions in an ND scenario.

**Scientific foundation:** The location and physical meaning of an outflow boundary can change modeled WSE, depth, inundation, and whether water leaves through intended edges.

**Decision record and status:** The standalone DR-039 file marks ALT-F with a file-local `#current` marker.
The reviewed Decision Register has no DR-039 row or registered status, so this file-local selection is an Open question rather than Selected methodology.

**Current code:** `twod_fim_jobs/jobs/run_nd_scenarios.py` constructs the current slope-based downstream boundary, using current code behavior independently of whether DR-039 is registered.
In `twod_fim_jobs/hydraulic_solvers/run.py`, the first saved grid returns the convergence sentinel before boundary checking, and later checks return no result until the downstream centerline endpoint is wet.
Once active, the check flags only wet perimeter cells whose WSE lies inclusively between the two centerline-endpoint WSE values.
An `edge_error` termination requires such a flagged result and `allow_water_on_edges` set to false.

**Job input:** Model geometry, an optional outflow-area polygon, and current slope-related inputs determine the realized boundary treatment.

**Artifact:** The scenario manifest records the termination condition and final depth and inundation assets, while the ND job result can carry a water-on-edge warning when the adaptive sequence encounters `edge_error`.
The detailed `BoundaryCheckResult` used by the watcher is not persisted in the scenario manifest, so the stored artifacts do not by themselves prove acceptable edge behavior or grant methodology status to DR-039.
Absence of `edge_error` does not distinguish a skipped or withheld check, an allowed-edge configuration, wet edge cells outside the endpoint-WSE range, or a completed check with no flagged cells.

**Evidence:** The standalone file records a 2026-06-04 decision-history note for ALT-F, but the controlling register does not record that decision or status in the reviewed revision.

**Validation question:** Will authorized project governance add DR-039 and its status to the Decision Register, or revise the standalone file so that its authority is unambiguous?
Does persisted evidence distinguish check activation and each unflagged case before a scenario is accepted as having suitable edges?

**Open conflict:** See [CONF-008](conflicts-and-open-questions.md#conf-008-unregistered-dr-039-selection).

### XW-011: Decision Register revision in model and run identity

**Concept:** Provenance alignment between the methodology revision reviewed by the handbook and the revision embedded in current model and scenario identities.

**Scientific foundation:** An identity pin can detect and organize changes only when its value is interpreted against the exact source revision it names.

**Decision record and status:** The handbook reviews the Decision Register at knowledge-base revision `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
No decision establishes that this revision is equivalent to the jobs checkout's baked identity revision.

**Current code:** `twod_fim_jobs/consts.py` sets `SDR_COMMIT` to `826a602ddcaf58bf4081dc04b65ba15b82cc8c8a`.
`twod_fim_jobs/jobs/build_model.py` includes that value in model identity.
`twod_fim_jobs/hydraulic_solvers/identities.py` defines scenario run identity as the solver enum plus that baked value and hashes only those two fields.
The run identity does not include the executable version or build, image digest, run configuration, or hardware identity.

**Job input:** The revision is baked into the jobs image rather than supplied as a request field.

**Artifact:** Model manifests and scenario manifests record identities derived from the baked revision.
Those identities prove the revision they contain, not use of the different revision reviewed by the handbook.
The scenario run identity also does not prove version-pinned execution or a complete solver-environment identity.

**Evidence:** The target system-design guide treats the baked SDR revision as an identity input and materialization-drift control.

**Validation question:** Does the authorized methodology revision expected by target desired state exactly match the revision baked into the deployed jobs image, and do observed manifests record that expected value?

**Open conflict:** Resolve the mismatch by either reviewing and authorizing the baked revision as the handbook methodology snapshot or updating the authorized jobs image and desired-state identity pin to the approved revision, then observing newly materialized manifests with the matching identity.
See [CONF-007](conflicts-and-open-questions.md#conf-007-decision-register-revision-mismatch-in-identities).

### XW-012: Hot-start state and sensitivity

**Concept:** Initialization of a target scenario from a prior modeled state.

**Scientific foundation:** A compatible initial state can reduce spinup, but agreement under target forcing and boundaries must be tested before claiming that the final result is independent of the initial condition.

**Decision record and status:** No reviewed Decision Register entry establishes that a depth-only hot start is a complete dynamic-state continuation or defines target-scenario initial-condition acceptance criteria.

**Current code:** `twod_fim_jobs/jobs/run_nd_scenarios.py` passes the current adaptive search position's final depth asset to each newly simulated trial.
That position can be a newly simulated, re-adopted, or reused scenario and can be an accepted reference or a `reject_low` search position.
`twod_fim_jobs/jobs/run_kwse_scenarios.py` can resolve a specified ND or KWSE source manifest's final depth asset.
`twod_fim_jobs/hydraulic_solvers/pre_process.py` writes that asset as LISFLOOD-FP `startfile` and supplies no velocity, momentum, face-flux, or checkpoint state through the hot-start path.
The KWSE hot-start schema accepts explicit `identity_hash: null`, but job code passes null into source-address construction instead of resolving it to the current run identity.

**Job input:** ND sequencing chooses the current search position automatically rather than guaranteeing the preceding selected library member, while each optional KWSE `hotstart` identifies source discharge, source boundary type and value, and run identity used to locate the source manifest.

**Artifact:** The target scenario manifest records its `hot_start` depth asset within `inputs`, and the source scenario manifest records the referenced final depth asset and source inputs.
Those records identify a depth source but do not prove motion-state continuity, source adequacy, or target independence from initialization.

**Evidence:** The historical official LISFLOOD-FP manual describes `startfile` as an initial water-depth raster for its documented release.
No target cold-start versus hot-start result was inspected for this handbook task.

**Validation question:** Does the version-pinned executable initialize omitted motion variables acceptably, do cold and defensible hot-start pathways converge to hydraulically equivalent target results under adequate duration and evidence criteria, and should explicit null identity be forbidden or resolved through validated model construction?

**Open conflict:** See [CONF-010](conflicts-and-open-questions.md#conf-010-depth-only-hot-starts-and-initial-condition-independence).

### XW-013: Terrain, grid, datum, and structures

**Concept:** Transformation of source elevation into the hydraulic terrain used for storage, pathways, barriers, WSE, and depth.

**Scientific foundation:** Hydraulic terrain needs an explicit horizontal grid, vertical datum and units, source provenance, resampling method, surface-treatment interpretation, and structure limitations.
Bare-earth and hydroflattened DEMs do not automatically contain submerged bathymetry or complete bridge and culvert hydraulics.

**Decision record and status:** DR-010 ALT-A, no culvert modification, is Alternate Selected even though its record lists rejected case outcomes.
DR-017 ALT-A, 10 m, and DR-018 ALT-A, USGS 3DEP, are Alternate Selected.
DR-021 has the internal authority conflict recorded in XW-005 and CONF-005.

**Current code:** `DEFAULT_DEM_SOURCE` and `DEFAULT_EPSG_CODE` are environment-dependent fallbacks evaluated at process startup, with checked-in fallback values for the continually updated 1/3 arc-second 3DEP seamless VRT and EPSG:5070.
`BuildModelInputs.grid_resolution` has a 10-unit input default, and callers can override the realized `dem_source`, `epsg_code`, and `grid_resolution` values.
Validation requires only a positive EPSG integer and does not enforce projected coordinates or metre linear units.
`_extract_raster` creates a destination transform from model bounds and dimensions and calls Rasterio `reproject` without an explicit resampling argument, so the documented nearest-neighbor default applies.
The code performs a horizontal reprojection but has no vertical-datum input or transformation.
The reviewed job contracts have no bridge, culvert, burn, breach, opening, blockage, or structure-loss input.

**Job input:** `dem_source`, `grid_resolution`, `epsg_code`, and domain geometry determine the realized terrain grid.
No current input selects a vertical datum, resampling method, topobathymetry merge, culvert treatment, or bridge model.

**Artifact:** `model_manifest.inputs.dem_source` and `model_manifest.inputs.epsg_code`, together with the terrain raster CRS, identify the realized source string and horizontal CRS.
The model manifest also records source-string hashes, grid properties, and a terrain asset with output checksum, source string, and retrieval time.
The artifact does not pin the remote source content through model identity or name the terrain vertical datum and structure coverage.

**Evidence:** USGS metadata defines 3DEP source scope and surface treatments.
ISU-011 records differing results from successive default-source calls as scoped issue evidence.
DR-010 case rows and EXP-012 challenge selected terrain assumptions without replacing them.

**Validation question:** Does each model pin immutable source content, use a projected metre-based horizontal CRS or explicit conversions, preserve a documented vertical reference, use a reviewed terrain resampling rule, retain controlling features, and state which below-water and structure behaviors are represented or absent?

**Open conflict:** See [CONF-005](conflicts-and-open-questions.md#conf-005-topobathymetry-policy) and [CONF-011](conflicts-and-open-questions.md#conf-011-terrain-source-datum-resampling-and-structures).

### XW-014: LULC-derived Manning roughness

**Concept:** Conversion of categorical Annual NLCD land cover into a spatial Manning's n raster.

**Scientific foundation:** Manning's n is an effective scale-dependent resistance parameter.
Land-cover classes can supply initial regions or values, but they do not directly measure calibrated hydraulic resistance.

**Decision record and status:** DR-019 ALT-A selects National Land Cover Database land cover converted to Manning's n, and DR-020 ALT-A selects the project lookup attributed to USACE guidance.
Both records have Alternate Selected status.

**Current code:** `DEFAULT_LULC_SOURCE` is an environment-dependent fallback evaluated at process startup, with the Annual NLCD 2023 conterminous United States Collection 1.0 mosaic as its checked-in fallback value.
A caller-supplied `lulc_source` can override it, and `DEFAULT_LULC_LOOKUP` supplies the checked-in lookup fallback reproducing the DR-020 table unless the caller supplies another dictionary or JSON path.
The categorical source is reprojected to the model grid with Rasterio's nearest-neighbor default and cast to `uint8` before conversion through a 256-entry array filled with `-9999` for unpopulated post-cast codes.
Negative, fractional, or out-of-range source values can truncate or wrap into valid post-cast indices, so only absent post-cast codes reliably receive the fill value.
Annual NLCD code 12 is unspecified by the checked-in lookup fallback.
The input contract does not require positive values, complete post-cast code and nodata coverage, or keys restricted to 0 through 255.
Lookup keys outside that representable raster-code range cannot define distinct reachable codes through the cast input and can instead fail or alias during lookup-array construction.
The current code leaves the documented similar-roughness warning unimplemented and emits no missing-class warning.

**Job input:** `lulc_source`, `lulc_lookup`, `grid_resolution`, `epsg_code`, and domain geometry determine the roughness raster.
The lookup may be supplied as a dictionary or a path to a JSON dictionary.

**Artifact:** `model_manifest.inputs.lulc_source`, `model_manifest.inputs.lulc_lookup`, `model_manifest.inputs.epsg_code`, and `model_manifest.inputs.grid_resolution`, together with roughness raster metadata, identify the realized source, lookup, and destination grid.
The model manifest also records hashes of the source string and realized lookup and a roughness asset with output checksum, source string, and retrieval time.
These records describe one realization but do not establish calibration, uncertainty, transferability, or immutable remote source content.

**Evidence:** Official Annual NLCD documentation defines the categorical source and its resolution and caveats.
Official USACE guidance supports complete class mapping, local channel refinement, overrides, and calibration regions rather than one universal lookup.

**Validation question:** Are all observed class and nodata values mapped to positive reviewed coefficients, are invalid outputs rejected, and do calibration and sensitivity evidence support the roughness field for the intended reach, event, grid, and result quantities?

**Open conflict:** See [CONF-012](conflicts-and-open-questions.md#conf-012-roughness-lookup-validation-and-warning-gaps).

### XW-015: Build-model lifecycle, identity, and materialization evidence

**Concept:** Transformation of one validated request into model assets, a manifest, an addressed output directory, and a bounded result.

**Scientific foundation:** Reproducible model development requires input identity, transformation provenance, artifact integrity, spatial reference, source content, and scientific acceptance evidence to remain distinguishable.

**Decision record and status:** DR-011, DR-013, DR-014, DR-015, DR-016, DR-017, DR-018, DR-019, and DR-020 have registered alternatives and statuses that control selected methodology within their separate scopes.
DR-012 selects a later expansion method that current `build_model` does not implement.

**Current code:** `twod_fim_jobs/jobs/common.py` validates inputs before calling `BuildModelJob._run` inside a temporary directory.
`jobs/build_model.py` loads the target and optional mainstem reach, resolves the lookup, estimates bankfull width, constructs inflow and domain geometry, builds identity and address, checks for an equal-input existing manifest, extracts terrain and roughness, writes four vector assets, constructs properties and manifest, copies assets sequentially and then copies the manifest last, and returns identity hash, model ID, model directory, and warnings.
`models/build_model.py` forbids unknown input fields and validates scalar bounds and authored-domain extent and alignment, but it does not validate topology, source immutability, vertical datum, roughness coverage, or domain sufficiency.
Its positive-EPSG check also does not verify a projected horizontal CRS with metre linear units despite downstream metre-based geometry, slope, forcing-density, cell-area, storage, and flooded-area assumptions.

The identity object hashes a baked methodology revision, reach WKT, grid resolution, horizontal EPSG code, DEM source string, LULC source string, and realized lookup.
It does not hash immutable raster source content or several output-affecting topology and geometry inputs.
The domain code records integer grid offsets from the floored reach-centroid anchor rather than content.

If an existing manifest validates and its reconstructed inputs equal the current request, the job returns before checking every referenced asset or checksum.
If a present manifest is schema valid but its reconstructed inputs are unequal, the job rebuilds.
An omitted identity input can change while the identity and final domain code remain unchanged, so that rebuild can publish to the same full address.
The sequential copy loop has no directory-level transaction, atomic promotion, rollback, or cleanup.
A failure can leave earlier copied assets behind, and a same-address rebuild can expose an older manifest with newly replaced assets before or after a later copy failure.
The generated documentation's existing-model warning is not implemented, and the result does not independently observe destination storage.

**Job input:** The complete `BuildModelInputs` request controls the realized lookup, reach and mainstem selection, inflow, domain, grid, source strings, output base address, and recorded provenance.
Environment-dependent DEM, LULC, and EPSG values are process fallbacks only when the caller does not override them.

**Artifact:** Current manifest assets are terrain, roughness, centerline, inflow line, grid-snapped anchor exposed as `reach_centroid`, and domain.
The manifest records their destination hrefs, checksum prefixes, source strings, and retrieval times together with inputs, identity, domain, properties, and implemented warnings.
The six assets and manifest are one logical publication set, but current storage writes do not make that set atomic.
`outflow_area.geojson` and STL geometry are not current model-manifest asset roles.

**Evidence:** ART-001 is a checked-in test fixture whose six named assets match recorded checksum prefixes.
Its DEM and roughness rasters share dimensions, extent, horizontal CRS, and affine transform.
Its vector artifacts use compatible horizontal CRS coordinates, while their hydraulic relationship to raster cells still requires explicit intersection, rasterization, or cell-mapping checks.
The fixture also shows that file presence and internal agreement can coexist with missing vertical datum, incomplete source-content identity, an unmanifested outflow-area file, the anchor role mismatch, and a downstream field that repeats the modeled reach ID.

**Validation question:** Do versioned prepared inputs, immutable source identities, exact transformations, complete asset observation, datum contracts, geometry checks, domain evidence, and intended-use validation support adoption of the addressed model?
Which omitted identity dimensions must trigger a distinct address, and which current reuse and warning statements must be implemented or corrected?
Which publication protocol prevents readers from adopting partial or mixed generations when a same-address copy fails?

**Open conflict:** See [CONF-013](conflicts-and-open-questions.md#conf-013-model-identity-existing-model-and-warning-gaps).

### XW-016: Domain expansion, clipping, and edge evidence

**Concept:** Detection and correction of an initial model rectangle that truncates hydraulically connected water or assigns an inappropriate edge role.

**Scientific foundation:** Clipping removes represented storage and conveyance and replaces the omitted area with an artificial closed or open boundary that can change WSE, flow splits, velocities, and inundation extent.

**Decision record and status:** DR-012 ALT-G, Informed by Water-Surface Elevation with Regression Expansion Limit, is Alternate Selected and sets a maximum expansion of 50 estimated bankfull widths from the initial domain.
DR-003 has registered selected KWSE edge methodology.
DR-039 has a file-local selection but no reviewed Decision Register row, so its ND edge authority remains an Open question.

**Current code:** `build_model` emits a provisional large-area warning but has no small-domain or clipping warning and no expansion loop.
Current ND scenario code can derive or consume an outflow polygon and later boundary preprocessing reduces each polygon-edge intersection to one cardinal span across the overall bounds, including gaps between disjoint pieces.
Current edge checks can return early for a first-grid sentinel or dry downstream endpoint, can be bypassed when edge water is allowed, and do not persist enough cell-level detail in the scenario manifest to reconstruct every unflagged case.
When convergence and a disallowed edge violation are simultaneous, current branch order persists `volume_convergence` and suppresses `edge_error` as the stored reason.

**Job input:** Initial domain inputs, optional outflow-area polygon, scenario edge settings, downstream transfer inputs, and the selected scenario forcing jointly affect the realized edge evidence.

**Artifact:** Required evidence includes the model domain and grid, intended boundary roles, rasterized inflow and outflow cells, STL and source coverage, saved depth and WSE, terrain, wet perimeter cells, connectivity, check activation, settings, classification results, and any revised model manifest.
The current model and scenario manifests preserve only part of that evidence.

**Evidence:** EXP-007 proposes alternative edge-pooling criteria, while ISU-003 and ISU-006 record unintended domain loss and arbitrary FIM cutoffs as scoped issue evidence.
The synthetic Lab 9 packet demonstrates why connected unintended-edge water, an adverse signed endpoint profile, partial STL coverage, unknown datum, and missing identity evidence require the next discriminating check before expansion.

**Validation question:** Which component owns the selected expansion loop, which scenario and wet-component rule trigger it, how are intended outlet and transfer edges excluded, how are simultaneous convergence and edge evidence both persisted, and how does a revised bbox propagate through model address and upstream dependencies?

**Open conflict:** See [CONF-014](conflicts-and-open-questions.md#conf-014-domain-expansion-and-clipping-evidence).

### XW-017: Scenario identity, publication, and materialization

**Concept:** Proof that one addressed scenario is the intended, complete, reproducible, and usable hydraulic realization.

**Scientific foundation:** Reproducibility requires stable input identity, exact realization, source provenance, transformation history, artifact integrity, observed completeness, and intended-use acceptance.

**Decision record and status:** No reviewed Decision Register record establishes that the current bounded run identity is a complete solver-environment identity or that sequential manifest-last publication is immutable or atomic.
DR-004 controls composite pixel selection but does not define scenario identity or publication guarantees.

**Current code:** A run identity hashes only the solver enum and baked `SDR_COMMIT` to eight hexadecimal characters.
It omits executable version and build, image digest, run configuration, hardware, boundary details, downstream source, hot start, and full model realization.
`run_scenario` can exactly reuse a stored manifest when its complete `RunScenarioInputs` equal the target request, but that check does not independently observe every referenced asset.
The shared input cache places remote files under directories named by each asset's declared 16-character checksum, but it returns cache hits and newly copied files without rehashing their bytes against that declaration.
The cache path therefore does not prove that the localized solver input matches the manifest's integrity metadata.
Both public input models accept `save_zarr`, but neither job's `RunConfig` constructor forwards that field and both leave Zarr disabled in the reviewed paths.
The generic true branch creates a directory-backed Zarr store and then applies file-only hashing, so manifest construction cannot complete.
The `.wd` conversion assigns literal EPSG:5070 when solver output has no CRS instead of recovering the realized model CRS.
For non-5070 models, the fallback can mislabel or spatially misassociate depth, inundation, STL geometry, transfer inputs, and compositing outputs.
The KWSE hot-start schema accepts explicit null identity, but current job code does not resolve it to current run identity before source addressing.
ND boundary names use one decimal place in scientific notation, and KWSE boundary names use one decimal place, so distinct raw values can target one address even though exact input equality prevents unequal reuse.
New public-scenario publication copies depth, inundation polygon, STL, and then the manifest without directory-level staging, atomic promotion, rollback, or cleanup.

**Job input:** `RunScenarioInputs` records domain, grid, terrain, roughness, boundary conditions, hot-start asset, run configuration, result base, reach ID, full model ID, centerline, and run identity hash.

**Artifact:** A scenario manifest records bounded identities, complete scenario inputs, final properties, output hrefs, checksum prefixes, and provenance fields.
Those declared input checksums do not establish which bytes a current cache hit or remote copy actually supplied to preprocessing.
The current synchronous job result returns manifest hrefs or comparison information but does not independently observe storage.
The checked-in reconciler prototype reads storage and writes materialized rows only after manifest checks, but its unpinned local code does not prove deployment, full asset checksum verification, or hydraulic adequacy.

**Evidence:** The checked-in KWSE fixture demonstrates that target inputs can preserve exact downstream depth, terrain, STL, inundation, grid, domain, and hot-start asset provenance.
Fixture presence does not prove current target storage or production behavior.

**Validation question:** Which identity fields and boundary-value precision must move a scientifically distinct scenario to a new address, which generation or promotion protocol prevents mixed same-address publication, and which observer verifies every required asset and checksum before recording materialization?
Which localization owner will compute SHA-256 over every cached or downloaded input before use, require the declared prefix to match, fail closed on mismatch, and decide whether the asset schema must carry full digests?
How will public Zarr inputs be forwarded or removed, directory stores be hashed deterministically, realized model CRS replace the EPSG:5070 fallback, and explicit-null hot-start identity be forbidden or resolved?
Which later gate records hydraulic acceptance separately from storage completeness?

**Open conflict:** See [CONF-007](conflicts-and-open-questions.md#conf-007-decision-register-revision-mismatch-in-identities), [CONF-009](conflicts-and-open-questions.md#conf-009-scenario-publication-membership-reuse-and-discharge-grid-authority), [CONF-010](conflicts-and-open-questions.md#conf-010-depth-only-hot-starts-and-initial-condition-independence), and [CONF-016](conflicts-and-open-questions.md#conf-016-kwse-transfer-planning-and-materialization-contract).

### XW-018: Intended-use validation and acceptance

**Concept:** Separation and assembly of software verification, numerical verification, scientific validation, calibration, benchmark comparison, plausibility review, uncertainty, acceptance criteria, operational monitoring, and post-run diagnosis for one stated use.

**Scientific foundation:** NASA-STD-7009B defines verification against requirements and specifications, validation against the real world for intended uses, calibration as parameter adjustment to improve referent agreement, uncertainty characterization, and project-approved acceptance criteria.
NIST separates code verification, solution verification, and validation and requires attention to numerical approximation and regions of validity.
EPA environmental-model guidance treats calibration, corroboration, sensitivity, uncertainty, data quality, and decision-specific model evaluation as related but distinct activities.

**Decision record and status:** DR-002 ALT-A, Composite 2D Model with Same Input Data, is registered as Alternate Selected for the model-connectivity benchmark.
That benchmark selection is scoped to connectivity testing and does not independently establish real-world validation or universal adequacy.
No registered Decision Register record governs the complete crosscutting validation contract, calibration separation, acceptance authority, permissible use, uncertainty allowance, or operational-monitoring policy for model artifacts, ND or KWSE scenarios, selected libraries, transfer dependencies, or composites.
Those parts remain an Open question.

**Current code:** `twod-fim-jobs/twod_fim_jobs/models/build_model.py`, `twod-fim-jobs/twod_fim_jobs/models/run_nd_scenarios.py`, and `twod-fim-jobs/twod_fim_jobs/models/run_kwse_scenarios.py` define current request, result, manifest, and warning schemas rather than a validation or acceptance record.
`twod-fim-jobs/twod_fim_jobs/jobs/build_model.py` constructs model inputs and artifacts, emits implemented warnings, writes a model manifest, and can return an existing model when its manifest reconstructs equal inputs.
`twod-fim-jobs/twod_fim_jobs/jobs/run_nd_scenarios.py` and `twod-fim-jobs/twod_fim_jobs/jobs/run_kwse_scenarios.py` construct and execute current scenario families and return adaptive information or manifest paths.
`twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/run.py` starts LISFLOOD-FP and produces current termination, storage-change convergence, inundation-metric, and edge-check evidence.
`twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/post_process.py` creates depth, inundation, STL, and optional Zarr outputs, while `twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/common.py` checks exact stored-input equality, constructs scenario manifests, and publishes scenario assets.
Those paths do not implement an authorized hydraulic-validation tolerance, independent-data comparison, uncertainty allowance, permissible-use decision, acceptance authority, or operational-monitoring policy.
The checked-in `twod-fim-deployment/orchestrator/recon/observe.py` and `twod-fim-deployment/orchestrator/recon/identity.py` provide prototype storage-observation and bounded identity checks, but they do not implement scientific validation or acceptance and do not prove deployed monitoring.

**Job input:** `BuildModelInputs` in `twod-fim-jobs/twod_fim_jobs/models/build_model.py`, `RunNDScenariosInputs` in `twod-fim-jobs/twod_fim_jobs/models/run_nd_scenarios.py`, and `RunKWSEScenariosInputs` in `twod-fim-jobs/twod_fim_jobs/models/run_kwse_scenarios.py` supply bounded model, forcing, boundary, source, run, identity, and output inputs.
No current jobs input supplies one complete validation plan or authorized acceptance record for the resulting product.

**Artifact:** A reviewable acceptance record would need to identify intended use, source and methodology revisions, realized configuration, verification evidence, numerical evidence, calibration data and parameter changes, independent validation or benchmark evidence, uncertainty, sensitivity, materialization observation, criteria, authority, restrictions, verdict, and monitoring triggers.
Current model and scenario manifests cover only a subset of those fields and remain production artifacts rather than scientific acceptance decisions.

**Evidence:** Case-018 records a bounded Winooski reach-composite comparison under the EXP-013 heading and identifies DEM and WSE artifacts.
Case-018 also records a bounded comparison of candidate quasi-steady metrics with modeler-selected times under the EXP-014 heading.
The standalone EXP-013 and EXP-014 files contain descriptions and methodologies rather than the completed observations.
EXP-012 describes a planned topobathymetry, calibration, and rating-curve comparison.
The issue register supplies diagnostic signatures without generally establishing causes.
These sources do not establish universal project thresholds or validation proof.

**Validation question:** For each intended use, which authority defines the required verification, numerical, observation or benchmark, uncertainty, sensitivity, materialization, and monitoring evidence?
Which independent data, comparison quantities, supports, flow and stage ranges, datums, metrics, uncertainty allowances, and restrictions are required before a model, scenario, selected library, transfer source, or composite receives a ready verdict?
How are calibration data separated from independent evaluation, and which reassessment triggers apply after acceptance?

**Open conflict:** See [CONF-002](conflicts-and-open-questions.md#conf-002-volume-convergence-and-mass-balance), [CONF-013](conflicts-and-open-questions.md#conf-013-model-identity-existing-model-and-warning-gaps), [CONF-016](conflicts-and-open-questions.md#conf-016-kwse-transfer-planning-and-materialization-contract), and [CONF-017](conflicts-and-open-questions.md#conf-017-intended-use-validation-and-acceptance-policy).

### XW-019: Diagnostic signatures and discriminatory evidence

**Concept:** Ordered, hypothesis-driven diagnosis of forcing, provenance, network, data, terrain, roughness, reference systems, geometry, boundaries, solver state, convergence, balance, edges, scenario membership, network continuity, compositing, uncertainty, and method limitations.

**Scientific foundation:** A hydraulic symptom can arise from several mechanisms, so diagnosis should compare competing explanations and select observations that distinguish them before changing inputs or parameters.
Evidence must retain compatible quantity, units, datum, spatial support, time, scenario, and provenance.

**Decision record and status:** DR-002 ALT-A for the connectivity benchmark, DR-003 ALT-D for KWSE edge treatment, DR-004 ALT-D for pixelwise-maximum compositing, DR-009 ALT-B for transfer at the reach outlet, and DR-025 ALT-B, DR-026 ALT-B, and DR-031 ALT-B for per-run cell-specific stage transfer are each registered as Alternate Selected.
These records are relevant to low or high downstream WSE, confluence continuity, WSE anomalies, benchmark disagreement, transfer misregistration, and composite artifacts.
DR-010 ALT-A for no culvert terrain modification, DR-017 ALT-A for 10 m DEM resolution, DR-018 ALT-A for USGS 3DEP terrain, DR-019 ALT-A for Annual NLCD-derived roughness, DR-020 ALT-A for the USACE lookup, and DR-021 ALT-A for no below-water topobathymetry handling are each registered as Alternate Selected.
These records are relevant to culvert blockage, terrain and roughness bias, DEM differences, divergent pathways, and WSE anomalies.
DR-011 ALT-D for the initial domain, DR-012 ALT-G for WSE-informed expansion, DR-013 ALT-A for non-headwater inflow geometry, DR-014 ALT-B for headwater inflow points, DR-016 ALT-A for upstream offset, DR-023 ALT-B for short-reach merging, and DR-024 ALT-B for merge thresholds are each registered as Alternate Selected.
These records are relevant to oversized or clipped domains, unintended edge leakage, confluence narrowing, divergent paths, forcing placement, and network continuity.
DR-029 ALT-A for discharge bounds, DR-030 ALT-C for adaptive ND selection, DR-032 ALT-D for KWSE bounds, and DR-033 ALT-B for the per-reach stage grid are each registered as Alternate Selected.
These records are relevant to forcing provenance, scenario membership, missing or incompatible neighbor scenarios, and network continuity.
DR-005 ALT-C, DR-006 ALT-E, DR-007 ALT-A, DR-008 ALT-B, DR-037 ALT-B, and DR-038 ALT-A are each registered as Alternate Selected for lake or coastal boundaries, network treatment, transfer geometry, breakpoints, or reach omission.
DR-034 ALT-A and DR-035 ALT-A are registered as Needs-Review, while DR-036 is Draft without a registered alternative.
These lake and coastal records are relevant to downstream control, transfer, edge, network-continuity, and method-limitation hypotheses without proving a settled or implemented policy.
DR-022 ALT-G for volume convergence and DR-028 ALT-A for the `1e-3` threshold are registered as Alternate Selected and are relevant to failure to reach quasi-steady state.
The standalone DR-011 file also marks ALT-E and the standalone DR-022 file also marks ALT-J, so the registered selections control within their recorded scope while the file-marker conflicts remain Open questions.
The standalone DR-039 file marks ALT-F for ND edge treatment, but no DR-039 row or status exists in the reviewed Decision Register, so ND edge authority remains an Open question.
No registered decision governs the universal diagnostic order, diagnostic-record schema, identity-collision response, storage-materialization proof, or corrective action for every issue signature.

**Current code:** `twod-fim-jobs/twod_fim_jobs/jobs/build_model.py` produces current terrain, roughness, inflow, domain, identity, manifest, and implemented warning evidence and has no scenario-driven domain-expansion loop.
`twod-fim-jobs/twod_fim_jobs/jobs/run_nd_scenarios.py` constructs current ND boundaries, estimates endpoint terrain slope, derives or consumes outflow geometry, runs adaptive comparisons, publishes trials, and returns current warnings without a durable selected-member index.
`twod-fim-jobs/twod_fim_jobs/jobs/run_kwse_scenarios.py` binds downstream scenario manifests, inundation, STL, depth, terrain, nominal stage, and hot-start inputs for current KWSE execution.
`twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/pre_process.py` rasterizes boundary geometry and writes current `QFIX`, `FREE`, and cell-specific `HFIX` transfer inputs without first enforcing the complete raster-registration and datum contract identified in XW-009.
`twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/run.py` exposes current process exit, watcher termination, final storage-change ratio, inundation metrics, and edge-check behavior but does not persist a complete boundary audit or full inflow-outflow balance.
`twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/common.py` and `twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/post_process.py` implement current scenario reuse, manifest construction, depth, inundation, STL, and sequential publication evidence.
`twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/identities.py` implements the bounded current run identity.
No current `twod-fim-jobs` path implements a hypothesis record, diagnostic next-check selector, authorized root-cause decision, or scientific readiness verdict.
The checked-in `twod-fim-deployment/orchestrator/recon/plan.py`, `twod-fim-deployment/orchestrator/recon/scenarios.py`, `twod-fim-deployment/orchestrator/recon/identity.py`, and `twod-fim-deployment/orchestrator/recon/observe.py` provide unpinned prototype planning, lookup, identity, and materialization evidence only.
The checked-in `twod-fim-deployment/orchestrator/scripts/f2f.py` contains a local maximum-VRT composite path outside the current modeling jobs and does not prove deployed execution or validated output.

**Job input:** Exact requests defined in `twod-fim-jobs/twod_fim_jobs/models/build_model.py`, `twod-fim-jobs/twod_fim_jobs/models/run_nd_scenarios.py`, `twod-fim-jobs/twod_fim_jobs/models/run_kwse_scenarios.py`, and `twod-fim-jobs/twod_fim_jobs/models/solvers.py`, together with prepared-network data, source artifacts, downstream scenario bindings, hot starts, and any external planner intent, form the starting diagnostic packet.

**Artifact:** A diagnostic record should preserve the symptom, decision context, evidence labels, competing hypotheses, predicted observations, selected next check, results, stopping or escalation condition, and readiness verdict.
It should link to exact model, scenario, library, transfer, composite, and observed-storage artifacts without treating those links as proof of adequacy.

**Evidence:** ISU-001 through ISU-012 record low downstream WSE, confluence narrowing, unintended edge leakage, high downstream WSE, divergent paths, arbitrary edge cutoff, culvert blockage, WSE anomalies, oversized domains, high benchmark WSE, DEM differences, and failure to reach quasi-steady state.
XW-009 and CONF-016 map transfer misregistration risk.
XW-015, XW-017, CONF-013, and CONF-016 map identity collisions and missing materialization evidence.
Issue records are scoped symptom evidence and do not by themselves establish root cause.

**Validation question:** Which minimum evidence packet must be retained so that each known signature can be traced through the ordered dependency chain?
Which next observation best separates data, geometry, boundary, numerical, network, transfer, compositing, identity, and storage explanations for the specific symptom?
When must diagnosis stop because a missing policy, source, observation, or method capability requires authorized escalation rather than parameter adjustment?

**Open conflict:** See [CONF-001](conflicts-and-open-questions.md#conf-001-boundary-condition-terminology-and-behavior), [CONF-002](conflicts-and-open-questions.md#conf-002-volume-convergence-and-mass-balance), [CONF-011](conflicts-and-open-questions.md#conf-011-terrain-source-datum-resampling-and-structures), [CONF-014](conflicts-and-open-questions.md#conf-014-domain-expansion-and-clipping-evidence), [CONF-016](conflicts-and-open-questions.md#conf-016-kwse-transfer-planning-and-materialization-contract), and [CONF-017](conflicts-and-open-questions.md#conf-017-intended-use-validation-and-acceptance-policy).

### XW-020: Sensitivity and uncertainty evidence

**Concept:** Decision-relevant characterization of forcing, terrain, bathymetry, roughness, network, domain, boundary, parameter, numerical, initial-condition or hot-start, structural-model, solver, scenario-sampling, identity and reuse, publication and materialization, composite, and operational uncertainty, including interactions.

**Scientific foundation:** NASA-STD-7009B and EPA environmental-model guidance connect sensitivity and uncertainty analysis to intended use, evidence quality, and acceptance decisions.
NIST separates numerical approximation from validation and bounded regions of validity.
USACE sensitivity guidance separates physical-parameter and numerical sensitivity and recommends realistic alternatives.
These sources do not establish a project-specific ranking or threshold.

**Decision record and status:** The Decision Register selects methods that expose sensitivity factors, including boundaries, domains, terrain, resolution, roughness, inflows, topobathymetry, convergence, scenario bounds, sampling, transfer, and compositing.
No registered decision defines a complete uncertainty taxonomy, representative sampling program, interaction design, uncertainty-combination rule, or authorized uncertainty allowance.
The chapter order of downstream condition and domain, terrain and topobathymetry, roughness and resolution, inflow distribution, wet-dry settings, and convergence controls is a review heuristic rather than Selected methodology.

**Current code:** At the reviewed `twod-fim-jobs` revision, `twod_fim_jobs/models/build_model.py` defines `BuildModelInputs` fields for `dem_source`, `lulc_source`, `domain`, `domain_buffer`, `grid_resolution`, `walk_us_dist_pct`, `ds_of_lake`, `epsg_code`, `bankfull_width_multiplier`, `lulc_lookup`, and `centerline_buffer_bankfull_multiplier`.
`twod_fim_jobs/jobs/build_model.py` implements those factors in `BuildModelJob` and implements existing-model reuse in `_check_model_built`.
The defaults and identity pin include `DEFAULT_DEM_SOURCE`, `DEFAULT_EPSG_CODE`, `Q_GRID_RESOLUTION`, and `SDR_COMMIT` in `twod_fim_jobs/consts.py`.
Terrain, grid, datum, roughness, model identity, reuse, and materialization details are mapped in [XW-013](#xw-013-terrain-grid-datum-and-structures), [XW-014](#xw-014-lulc-derived-manning-roughness), and [XW-015](#xw-015-build-model-lifecycle-identity-and-materialization-evidence).

`twod_fim_jobs/models/run_nd_scenarios.py` defines `RunNDScenariosInputs` fields for discharge bounds and steps, outflow geometry, `volume_convergence_tolerance`, edge policy, simulation and save durations, wall time, existing scenarios, `q_grid_resolution`, and adaptive response bands.
`twod_fim_jobs/jobs/run_nd_scenarios.py` implements `RunNDScenariosJob`, `_acceptance_window`, `_propose`, `compare_scenario_changes`, `get_normal_depth_boundary_condition`, `derive_outflow_polygon`, `get_normal_depth_slope`, and `_run_scenario`.
Scenario sampling, boundary, publication, selected-membership, and materialization details are mapped in [XW-006](#xw-006-scenario-library-bounds-and-sampling), [XW-010](#xw-010-normal-depth-edge-decision-authority), [XW-016](#xw-016-domain-expansion-clipping-and-edge-evidence), and [XW-017](#xw-017-scenario-identity-publication-and-materialization).

`twod_fim_jobs/models/run_kwse_scenarios.py` defines `HotStart`, `KWSEScenario`, and `RunKWSEScenariosInputs` for source-state identity, discharge, nominal boundary value, downstream scenario binding, convergence, edge policy, duration, and output choices.
`twod_fim_jobs/jobs/run_kwse_scenarios.py` implements `RunKWSEScenariosJob`.
`twod_fim_jobs/hydraulic_solvers/pre_process.py` implements `process_transfer_bc_line`, `geometry_to_bc_points`, `_poly_to_edge_bc_points`, and `_build_transform` for boundary and transfer realization.
Stage-transfer, raster-registration, and hot-start details are mapped in [XW-009](#xw-009-stage-transfer-geometry-and-values) and [XW-012](#xw-012-hot-start-state-and-sensitivity).

`twod_fim_jobs/models/solvers.py` defines `RunConfig`, `RunIdentity`, `ScenarioAssets`, and `RunScenarioManifest` for solver settings, run identity, and recorded assets.
`twod_fim_jobs/hydraulic_solvers/run.py` implements `solve_scenario`, `watch_run`, `check_status`, `generate_inundation_metrics`, `calculate_volume_convergence`, `check_boundary_errors`, `terminate_run`, and `_is_converged`.
`twod_fim_jobs/hydraulic_solvers/common.py` implements `check_run_exists`, `build_scenario_manifest`, and `publish_scenario`.
`twod_fim_jobs/hydraulic_solvers/post_process.py` implements `post_process_lisflood`, while `twod_fim_jobs/hydraulic_solvers/identities.py` implements `get_model_version`, `get_run_identity`, and `get_run_identity_hash`.
Quasi-steady, solver, run-identity, publication, and materialization details are mapped in [XW-002](#xw-002-quasi-steady-termination), [XW-003](#xw-003-hydraulic-solver-support), [XW-011](#xw-011-decision-register-revision-in-model-and-run-identity), and [XW-017](#xw-017-scenario-identity-publication-and-materialization).

Within these reviewed model, job, utility, and solver paths, no dedicated schema, job, or module was identified for executing a multifactor sensitivity design, representing or propagating a joint uncertainty distribution, or issuing an authorized intended-use acceptance decision.
Whether an unreviewed or future component owns a sensitivity driver, uncertainty model, joint-distribution workflow, interaction analysis, or acceptance policy remains an Open question.

**Job input:** A reviewable design must record the factor, plausible range or alternatives, controlled variables, outputs, metric, scenario or reach class, replication or sample design, acceptance or decision question, and evidence limits.
Contract studies must also include datum, CRS units, transfer registration, identity collisions, source version, reuse compatibility, hot-start state, publication, materialization, and composite membership where relevant.

**Artifact:** The evidence packet should preserve exact source and model identities, alternative configurations, controls, output quantities and units, spatial and temporal support, metrics, sample membership, interaction combinations, realized artifacts, failed checks, result tables, decision rule, and transfer limits.
A hydraulic response table and a contract pass-fail matrix may belong to the same study but answer different questions.

**Evidence:** Case-001, Case-002, Case-007, Case-015, Case-018, and Case-019 provide bounded comparison or sensitivity evidence for particular configurations.
Case-018 contains the completed observations conducted under the EXP-013 and EXP-014 headings.
The fifteen standalone experiment files contain methods without completed observations in those files.
The twelve issue files record symptoms without independently proving causes.
The complete classification is recorded in [Case, Issue, and Experiment Catalog](../06-validation-and-qc/04-case-issue-and-experiment-catalog.md).

**Validation question:** Which factor or interaction can change the intended decision, which alternatives are plausible, and which controls make the comparison interpretable?
Does the sample represent the reach and scenario classes to which the conclusion will be transferred?
Which authorized criterion distinguishes a local sensitivity result, a screening priority, an uncertainty bound, a diagnostic clue, and acceptance evidence?

**Open conflict:** See [CONF-017](conflicts-and-open-questions.md#conf-017-intended-use-validation-and-acceptance-policy) and [CONF-018](conflicts-and-open-questions.md#conf-018-sensitivity-priorities-and-uncertainty-propagation).

### XW-021: Scientific methodology change contract and readiness

**Concept:** Team-lead review of a scientific methodology change across decision authority, public contracts, consumers, identity, provenance, validation, migration, rollout, rollback, ownership, and intended-use acceptance.

**Scientific foundation:** NASA model-and-simulation guidance ties verification, validation, uncertainty, credibility, and acceptance criteria to intended use.
NIST separates code verification, solution verification, and validation.
EPA environmental-model guidance connects purpose, data quality, calibration, corroboration, sensitivity, uncertainty, and decision-specific evaluation.
A methodology change therefore requires more than schema acceptance or successful execution.

**Decision record and status:** DR-022 ALT-G selects volume convergence and DR-028 ALT-A selects \(10^{-3}\), both with Alternate Selected status in the reviewed Decision Register.
DR-028 describes the selection as preliminary and cites the Case-018 analysis under the EXP-014 heading.
No reviewed decision authorizes another convergence threshold, defines a complete methodology-change contract, or assigns project-wide migration and rollback ownership.

**Current code:** `twod-fim-jobs/twod_fim_jobs/models/run_nd_scenarios.py` and `models/run_kwse_scenarios.py` expose `volume_convergence_tolerance` and default it to `0.001`.
`twod-fim-jobs/twod_fim_jobs/jobs/run_nd_scenarios.py` and `jobs/run_kwse_scenarios.py` pass the supplied value into one frozen `RunConfig` for each job.
`twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/run.py` terminates when the stored-volume ratio is strictly less than the tolerance and gives convergence branch priority over a simultaneous disallowed edge violation.
`twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/identities.py` derives run identity only from the solver enum and baked `SDR_COMMIT`.
`twod-fim-jobs/twod_fim_jobs/hydraulic_solvers/common.py` checks complete scenario-input equality at the predicted manifest address, then publishes new depth, inundation, STL, optional Zarr, and manifest sequentially to final locations.
The address omits tolerance and most run settings, so unequal complete inputs can reject exact reuse while retaining one destination.
Before those exact target checks, `twod-fim-jobs/twod_fim_jobs/jobs/run_nd_scenarios.py` can adopt caller-supplied `existing_scenarios` after checking only reach ID, full model ID, run identity hash, and inclusive discharge range.
The limited ND predicate omits convergence tolerance, other run settings, boundary geometry or slope, hot start, and referenced-asset existence.
An adopted manifest enters adaptive `done` state and can influence the minimum reference, proposal curves, re-judgment, current search position, hot-start provenance, and selected-membership reasoning without rerunning.

**Job input:** The tolerance is a direct ND and KWSE input that changes the termination condition applied to every scenario in the job.
The input schema does not establish that an alternate value is selected, validated, backward compatible, collision safe, or accepted.

**Artifact:** A scenario manifest records complete inputs, final convergence ratio, one termination reason, asset hrefs, and checksum prefixes.
It does not persist the full convergence history, complete balance, simultaneous suppressed edge result, raw process return code, complete solver environment, selected-library membership, materialization proof, or intended-use acceptance record.
A complete change record would also need affected-generation and `existing_scenarios` inventory, compatibility decisions for both reuse paths, validation evidence, migration status, rollout stage, rollback generation, owners, and direct verdict.

**Target design:** The target system-design guide states that invalidating identity changes should move an object to a different address and that storage observation, not a job return, establishes materialization.
The checked-in unpinned reconciler supplies the tolerance in current local payload-building code while predicting run identity without it.
That local code is prototype evidence only and does not prove deployed identity, atomic publication, safe migration, operational materialization, or scientific acceptance.

**Evidence:** Case-018 supplies bounded preliminary convergence-metric evidence under the EXP-014 heading.
The standalone EXP-014 file supplies the methodology plan.
Neither source establishes universal transfer, an alternative threshold, a collision-safe identity, migration behavior, rollback, or production readiness.

**Validation question:** For the named intended use, which authorized evidence shows that the proposed method improves or preserves local WSE, depth, extent, flux, full balance, edge behavior, stage transfer, library selection, and composite products across representative reach, boundary, data-quality, and scenario classes?
Which sensitivity and interaction evidence separates convergence-control effects from downstream condition, domain, terrain, topobathymetry, roughness, resolution, inflow distribution, wet-dry behavior, hot start, output interval, and solver build?
Which identity and immutable-generation contract prevents same-address collision, which migration inventory updates every consumer and dependency, which atomic promotion and observer establish materialization, and which retained generation supports tested rollback?
Which complete-equality or authorized compatibility and asset-observation rule prevents limited ND adoption from importing an old methodology generation into new adaptive state?
Which named roles own the methodology decision, implementation, acceptance, migration, rollout, halt, rollback, and residual risk?

**Readiness rule:** Use `READY` only when every required evidence category and owner has passed for the stated use.
Use `READY WITH CONDITIONS` only for a bounded permitted use whose remaining conditions are explicit, owned, observable, time-bounded, and enforced before broader use.
Use `NOT READY` when methodology authority, scientific validity, collision-safe identity, migration, rollback, acceptance criteria, or ownership is missing or failed.

**Open conflict:** See [CONF-002](conflicts-and-open-questions.md#conf-002-volume-convergence-and-mass-balance), [CONF-009](conflicts-and-open-questions.md#conf-009-scenario-publication-membership-reuse-and-discharge-grid-authority), [CONF-010](conflicts-and-open-questions.md#conf-010-depth-only-hot-starts-and-initial-condition-independence), [CONF-016](conflicts-and-open-questions.md#conf-016-kwse-transfer-planning-and-materialization-contract), [CONF-017](conflicts-and-open-questions.md#conf-017-intended-use-validation-and-acceptance-policy), [CONF-018](conflicts-and-open-questions.md#conf-018-sensitivity-priorities-and-uncertainty-propagation), and [CONF-019](conflicts-and-open-questions.md#conf-019-methodology-change-identity-and-migration-ownership).
