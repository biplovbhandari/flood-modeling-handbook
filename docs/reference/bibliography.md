# Bibliography and Source Map

This source map records the materials used to establish the handbook backbone on 2026-09-17.
Local paths are evidence locations, not public citations.
The two repository revisions date project-specific mappings in this edition.
Revision identifiers do not make external scientific references timeless, and later chapters must reassess time-sensitive scientific or solver claims.

## Reuse boundary

Digital scientific sources are cited for factual support through direct URLs, access dates, use statements, and authority statements.
The handbook does not copy external figures or reproduce substantial source text, so a separate figure-reuse license is not required for these citation-only records.
Any later copied or adapted external visual must receive a direct reuse note in the [Visual Source Register](../assets/source-register.md) before inclusion.

## Local revision snapshot

| Repository | Local commit | Role in this handbook |
| --- | --- | --- |
| `twod-fim-jobs` | `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e` | Authority for current checked-out job behavior, schemas, defaults, and artifacts. |
| `twod-fim-knowledge-base` | `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e` | Authority for the handbook-reviewed Decision Register and target system-design material. |

The reviewed jobs checkout bakes `SDR_COMMIT=826a602ddcaf58bf4081dc04b65ba15b82cc8c8a` into model and run identities.
That identity revision differs from the knowledge-base revision reviewed by this handbook.
The difference is a provenance conflict, not evidence that either revision automatically represents the other.

## Handbook authority

### HB-001: Approved handbook design

- **Source:** Approved handbook design record reviewed for this edition.
- **Use:** Approved scope, audience, competency ladder, learning sequence, evidence labels, known conflicts, and acceptance criteria.
- **Authority:** Approved design authority for this handbook.
- **Access date:** 2026-09-17.

### HB-002: Handbook implementation sequence

- **Source:** Approved handbook implementation-sequence record reviewed for this edition.
- **Use:** Planned chapter, lab, reference, and asset file map plus task interfaces and review gates.
- **Authority:** Approved implementation sequence under the handbook design.
- **Access date:** 2026-09-17.

## Shared project context

### PK-001: Shared project overview

- **Source:** Nonpublic shared project overview record; local path intentionally omitted.
- **Use:** Approved project identity, repository map, component roles, and target-architecture orientation.
- **Authority:** Authoritative for shared project identity and component map, but advisory for current checkout behavior.
- **Access date:** 2026-09-17.

### PK-002: Shared project status

- **Source:** Nonpublic shared project status record; local path intentionally omitted.
- **Use:** Workstream context and explicitly dated status evidence.
- **Authority:** Authoritative for shared-layer workstream status and advisory for current repository or runtime state.
- **Access date:** 2026-09-17.

## Selected methodology and scoped evidence

### SDR-001: System Decision Register

- **File:** `twod-fim-knowledge-base/system-decision-record/02_Decisions/Decision Register.md`
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Current alternatives, statuses, last-update dates, and notes for selected methodology.
- **Authority:** Selected methodology within each record's scope and status.
- **Access date:** 2026-09-17.

### SDR-002: Boundary-condition decisions

- **Files:** `DR-003 - Where and What Boundary Conditions to Apply Along the Edge Cells of a KWSE Run.md` and `DR-039 - Where and What Boundary Conditions to Apply Along the Edge Cells of a Normal Depth Run.md` under `twod-fim-knowledge-base/system-decision-record/02_Decisions/`.
- **Use:** Selected and candidate meanings for freefall, normal-depth, and downstream-informed edge treatment.
- **Authority:** DR-003 has selected-methodology authority within its registered status and scope.
DR-039 contains a file-local `#current` marker for ALT-F, but the reviewed Decision Register has no DR-039 row or registered status.
Treat DR-039 as an Open question rather than selected methodology until the register records it.
- **Access date:** 2026-09-17.

### SDR-003: Domain, inflow, terrain, and convergence decisions

- **Files:** DR-011, DR-013, DR-015, DR-016, DR-017, DR-021, DR-022, DR-028, and DR-029 under `twod-fim-knowledge-base/system-decision-record/02_Decisions/`.
- **Use:** Initial domain, inflow geometry and placement, grid resolution, below-water topobathymetry, termination metric, convergence tolerance, and discharge bounds.
- **Authority:** Selected methodology within each record's recorded status and scope.
DR-011 is internally inconsistent because the register selects ALT-D while the standalone file also marks ALT-E `#current`.
DR-022 is internally inconsistent because the register and history identify ALT-G while the standalone file also marks ALT-J `#current`.
- **Access date:** 2026-09-17.

### SDR-004: Cases, issues, and experiments

- **Location:** `twod-fim-knowledge-base/system-decision-record/01_Cases/`, `03_Experiments/`, and `04_Issues/`.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Nineteen case records, fifteen standalone experiment methods, and twelve issue signatures cataloged as scoped results, partial results, plans, methods, or unresolved symptoms.
- **Authority:** Evidence or experiment only, unless an authorized decision record explicitly adopts the result.
The standalone EXP-013 and EXP-014 files contain descriptions and methodologies.
Case-018 records the completed Winooski reach-composite comparison and candidate convergence-metric analysis under sections linked to those experiments.
Issue records preserve symptoms and do not independently establish root causes.
- **Access date:** 2026-09-18.

### SDR-005: Reach inflow placement decisions

- **Files:** Decision records DR-013, DR-014, and DR-016 under `twod-fim-knowledge-base/system-decision-record/02_Decisions/`.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Selected upstream-mainstem line placement, selected headwater inflow method, and selected upstream offset.
- **Authority:** Selected methodology within each record's scope and Alternate Selected status.
- **Access date:** 2026-09-17.

### SDR-006: Discharge bounds, adaptive selection, and joint frequency

- **Files:** `DR-029 - What Should be the Lower and Upper Discharge Bounds for Each Reach.md` and `DR-030 - How to Determine Library Discharges for Each Reach.md` under `twod-fim-knowledge-base/system-decision-record/02_Decisions/`, and `EXP-015 - Characterize Joint Frequency Distribution of Adjacent Reach Flows.md` under `twod-fim-knowledge-base/system-decision-record/03_Experiments/`.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Selected NWM-retrospective per-reach discharge-bound rule, selected adaptive-response sampling intent, and proposed joint-frequency analysis for adjacent-reach KWSE planning.
- **Authority:** DR-029 ALT-A and DR-030 ALT-C are selected methodology with Alternate Selected status.
The scientific definition and derivation of the `high flow threshold` input remain unresolved in the reviewed project record.
DR-030's monitoring-point stage quantities and stated default bands differ from the reviewed current implementation's wet-cell depth and area quantities and current defaults.
EXP-015 is Evidence or experiment only and is not implemented or adopted replacement behavior.
- **Access date:** 2026-09-17.

### SDR-007: Lake and coastal boundary decisions

- **Files:** DR-005, DR-006, DR-008, and DR-009 under `twod-fim-knowledge-base/system-decision-record/02_Decisions/`.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Lake and coastal downstream-condition alternatives, water-body-informed edge handling, stage-transfer geometry, and transfer location.
- **Authority:** Selected methodology within each registered record's status and scope.
DR-006 is internally inconsistent because the register and file marker identify ALT-E while the latest standalone history line says the method switched to ALT-D.
The approved handbook design still identifies lake and coastal policy as unresolved or weakly settled, and these records do not prove complete current implementation or validation.
- **Access date:** 2026-09-17.

### SDR-008: Network preparation decisions

- **Files:** DR-007, DR-023, DR-024, DR-034, DR-037, and DR-038 under `twod-fim-knowledge-base/system-decision-record/02_Decisions/`.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Lake trimming and tags, short-reach merge eligibility and thresholds, lake dataset status, lake breakpoints, and coastal trim or removal rules.
- **Authority:** Selected methodology within each registered status and scope.
DR-034 is Needs-Review, and DR-007 and DR-038 retain unresolved thresholds, datasets, and handoff limitations.
These decisions do not prove implementation in current modeling jobs.
- **Access date:** 2026-09-17.

### SDR-009: Terrain, structures, and roughness decisions

- **Files:** DR-010, DR-017, DR-018, DR-019, DR-020, and DR-021 under `twod-fim-knowledge-base/system-decision-record/02_Decisions/`, with Case-003, Case-011, Case-015, EXP-012, ISU-007, and ISU-011 as scoped evidence.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Culvert terrain treatment, model-grid resolution, DEM source, LULC source, class-to-Manning lookup, below-water topobathymetry, and associated case or experiment evidence.
- **Authority:** The Decision Register controls registered methodology status, while cases, issues, and experiments remain scoped evidence.
DR-021 is internally inconsistent because the register selects ALT-A, the standalone file marks ALT-A and ALT-E `#current`, and its history says ALT-G was selected later.
- **Access date:** 2026-09-17.

### SDR-010: Domain, boundary, and expansion decisions

- **Files:** DR-003, DR-009, DR-011, DR-012, DR-013, DR-014, DR-015, DR-016, DR-017, DR-025, and DR-039 under `twod-fim-knowledge-base/system-decision-record/02_Decisions/`, with EXP-007, ISU-003, and ISU-006 as scoped evidence.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Initial domain composition, WSE-informed domain expansion, ordinary and headwater inflow placement, inflow width and offset, grid resolution, downstream transfer location and geometry, edge-condition choices, edge-pooling evidence, and clipping consequences.
- **Authority:** The Decision Register controls registered alternatives and status.
  DR-011 ALT-D remains the registered selection within its status and scope, while the standalone ALT-E `#current` marker remains an Open question.
  DR-039 has a file-local `#current` marker but no row in the reviewed Decision Register, so its selection remains an Open question.
  Cases, issues, and experiments remain scoped evidence and do not prove current implementation or validation.
- **Access date:** 2026-09-17.

### SDR-011: KWSE, stage transfer, compositing, and waterbody-stage decisions

- **Files:** DR-001, DR-004, DR-005, DR-006, DR-007, DR-008, DR-009, DR-025, DR-026, and DR-031 through DR-038 under `twod-fim-knowledge-base/system-decision-record/02_Decisions/`.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** KWSE coverage, current selected edge and transfer intent, pixelwise-maximum compositing, run-specific STL geometry, cell-specific transfer, discharge-specific stage bounds, zero-anchored stage grids, lake data and stage policy, Flows2FIM lake breakpoints, and coastal network modification.
- **Authority:** Selected methodology within each registered record's exact status and scope.
  DR-006 ALT-E remains the registered selection within its status and scope, while the latest standalone history line naming ALT-D remains an Open question.
  DR-034 and DR-035 are Needs-Review, DR-036 is Draft with no alternatives, and the remaining named selected records are Alternate Selected.
  The records do not prove current job support, planner deployment, compositing production, or scientific validation.
- **Access date:** 2026-09-17.

## Current job behavior

### JOB-001: Jobs package README

- **File:** `twod-fim-jobs/README.md`.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Use:** Package purpose, major jobs, execution examples, solver-image status, and repository-level operating context.
- **Authority:** Supporting documentation for current implementation, subordinate to current code when they differ.
- **Access date:** 2026-09-17.

### JOB-002: Generated job documentation

- **Files:** `twod-fim-jobs/docs/jobs/build_model/build_model.md`, `twod-fim-jobs/docs/jobs/run_scenarios/run_nd_scenarios.md`, and `twod-fim-jobs/docs/jobs/run_scenarios/run_kwse_scenarios.md`.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Use:** Typed inputs, defaults, processing scope, out-of-scope responsibilities, artifacts, checks, and responses.
- **Authority:** Generated contract documentation that supports code inspection.
- **Access date:** 2026-09-17.

### JOB-003: Current implementation locations

- **Files:** `twod_fim_jobs/consts.py`, `models/build_model.py`, `models/solvers.py`, `jobs/build_model.py`, `jobs/run_nd_scenarios.py`, `jobs/run_kwse_scenarios.py`, and `hydraulic_solvers/` under `twod-fim-jobs/`.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Use:** Current defaults, input validation, boundary types, solver support, identity revision pins, storage-change convergence, and artifact production.
- **Authority:** Current implementation for the checked-out revision.
- **Access date:** 2026-09-17.

### JOB-004: Reach topology and steady forcing contracts

- **Files:** `twod_fim_jobs/consts.py`, `twod_fim_jobs/utils/storage.py`, `twod_fim_jobs/utils/geospatial.py`, `twod_fim_jobs/models/build_model.py`, `twod_fim_jobs/jobs/build_model.py`, `twod_fim_jobs/models/run_nd_scenarios.py`, `twod_fim_jobs/models/run_kwse_scenarios.py`, `twod_fim_jobs/models/solvers.py`, `twod_fim_jobs/jobs/run_nd_scenarios.py`, and `twod_fim_jobs/jobs/run_kwse_scenarios.py` under `twod-fim-jobs/`.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Use:** Required reach-network fields, caller-supplied upstream topology, unverified upstream and mainstem semantics, direct use of supplied mainstem or null geometry branches, headwater inflow geometry, steady `QFIX` forcing, and the absence of rainfall-runoff or discharge-hydrograph inputs from the three current modeling jobs.
- **Authority:** Current implementation for the checked-out revision.
- **Access date:** 2026-09-17.

### JOB-005: Discharge-bound consumer contracts

- **Files:** `twod_fim_jobs/consts.py`, `models/run_nd_scenarios.py`, `models/solvers.py`, `jobs/common.py`, `jobs/run_nd_scenarios.py`, `models/run_kwse_scenarios.py`, `jobs/run_kwse_scenarios.py`, `hydraulic_solvers/common.py`, `hydraulic_solvers/pre_process.py`, `hydraulic_solvers/run.py`, `hydraulic_solvers/post_process.py`, `utils/naming.py`, and `utils/storage.py` under `twod-fim-jobs/`, with `docs/jobs/run_scenarios/run_nd_scenarios.md` as subordinate generated documentation.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Use:** Whole-m3/s ND bound inputs, `existing_scenarios`, `q_grid_resolution`, boundary construction, solver preprocessing and execution, final-state metrics, adaptive steady-discharge sampling within caller-supplied bounds, hot-start provenance, simultaneous convergence and edge priority, post-processing, scenario-output CRS fallback, `save_zarr` forwarding and hashing gaps, rounded ND boundary names, manifests, job responses, re-adoption, current published-trial semantics, and the absence of retrospective frequency analysis from current scenario jobs.
- **Authority:** Current implementation for the checked-out revision.
The generated ND documentation is subordinate where it says rejected trials remain unpublished or temporary, only selected entries are uploaded, the response contains only accepted scenarios, or edge abort returns an empty scenario list.
The public ND job accepts but does not forward `save_zarr`; the generic true branch creates a directory store and then fails under file-only hashing.
- **Access date:** 2026-09-17.

### JOB-006: Model and run identity revision pin

- **Files:** `twod_fim_jobs/consts.py`, `twod_fim_jobs/jobs/build_model.py`, and `twod_fim_jobs/hydraulic_solvers/identities.py` under `twod-fim-jobs/`.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Use:** Baked `SDR_COMMIT`, model identity construction, and scenario run identity construction.
- **Authority:** Current implementation for the checked-out revision.
The baked Decision Register revision is `826a602ddcaf58bf4081dc04b65ba15b82cc8c8a`, which differs from the handbook-reviewed knowledge-base revision `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Access date:** 2026-09-17.

### JOB-007: Convergence, hot-start, and solver-execution paths

- **Files:** `twod_fim_jobs/hydraulic_solvers/run.py`, `hydraulic_solvers/pre_process.py`, `hydraulic_solvers/post_process.py`, `hydraulic_solvers/common.py`, `hydraulic_solvers/identities.py`, `models/solvers.py`, `models/run_nd_scenarios.py`, `models/run_kwse_scenarios.py`, `jobs/run_nd_scenarios.py`, `jobs/run_kwse_scenarios.py`, and `consts.py` under `twod-fim-jobs/`.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Use:** Saved-grid storage calculation, strict termination comparison, simultaneous convergence priority over edge error, output and mass intervals, edge checks, process handling, result and manifest fields, depth-only hot starts, Zarr directory creation and file-only hashing, `.wd` CRS fallback, LISFLOOD-FP invocation, and blocked SFINCS paths.
- **Authority:** Current implementation for the checked-out revision.
The scenario manifest does not persist the complete convergence history, a simultaneous suppressed edge reason, raw return code, boundary-check details, outflow history, or a complete mass-balance residual.
- **Access date:** 2026-09-17.

### JOB-008: Model-development input transformations and warnings

- **Files:** `twod_fim_jobs/consts.py`, `models/common.py`, `models/build_model.py`, `models/warnings.py`, `jobs/build_model.py`, and `utils/geospatial.py` under `twod-fim-jobs/`, with generated `docs/jobs/build_model/build_model.md` as supporting contract documentation.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Use:** Environment-dependent DEM, Annual NLCD, and EPSG fallbacks evaluated at process startup, caller overrides, 10 m grid input default, exact Manning lookup fallback, prepared-network inputs, model identity, raster grid construction, Rasterio reprojection, categorical conversion, fill values, artifacts, and implemented versus documented warning behavior.
- **Authority:** Current implementation for the checked-out revision.
The current code omits an explicit resampling argument, does not record or transform a vertical datum, and casts source values to `uint8` before lookup.
It validates only a positive EPSG integer and does not enforce the projected metre units assumed by current geometry, slope, forcing-density, cell-area, storage, and flooded-area calculations.
Negative, fractional, or out-of-range values can truncate or wrap into valid codes, while only unpopulated post-cast codes reliably map to `-9999`.
Lookup keys are not constrained to 0 through 255 even though the cast input can reach only that index range, and the documented similar-roughness warning remains unimplemented.
The current manifest assignment also populates `downstream_reach_id` from `reach_id` rather than `reach_to_id`.
- **Access date:** 2026-09-17.

### JOB-009: Build-model lifecycle, artifacts, and existing-model behavior

- **Files:** `twod_fim_jobs/jobs/common.py`, `jobs/build_model.py`, `models/build_model.py`, `models/common.py`, `models/warnings.py`, `utils/geospatial.py`, `utils/hashing.py`, `utils/storage.py`, `consts.py`, and `tests/test_build_model.py` under `twod-fim-jobs/`, with generated `docs/jobs/build_model/build_model.md` as supporting contract documentation.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Use:** Input validation, target and mainstem lookup, caller-trusted topology, bankfull-width estimation, inflow branches, computed and authored domains, grid snapping, identity, model addressing, existing-manifest short circuit, raster and vector artifacts, properties, manifest creation, copy order, warnings, result fields, and current tests of those contracts.
- **Authority:** Current implementation for the checked-out revision.
  Generated documentation is subordinate when it describes an existing-model warning or similar-roughness warning that current code does not emit.
  The result names the intended output address but does not independently observe storage or establish scientific adequacy.
- **Access date:** 2026-09-17.

### JOB-010: Current KWSE execution, transfer preprocessing, and fixture

- **Files:** `twod_fim_jobs/models/run_kwse_scenarios.py`, `jobs/run_kwse_scenarios.py`, `models/solvers.py`, `jobs/common.py`, `hydraulic_solvers/common.py`, `hydraulic_solvers/pre_process.py`, `hydraulic_solvers/run.py`, `hydraulic_solvers/post_process.py`, `utils/geospatial.py`, `utils/naming.py`, `utils/hashing.py`, `utils/storage.py`, `tests/test_run_kwse_scenarios.py`, `tests/test_solvers.py`, and `docs/jobs/run_scenarios/run_kwse_scenarios.md` under `twod-fim-jobs/`.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Use:** Exact KWSE payload, explicit-null hot-start identity, serial scenario order, downstream manifest consumption, QFIX, current 0.5 m/m FREE spans, cell-specific TRANSFER values, asymmetric positive and negative index behavior, depth-terrain-manifest congruence gaps, dry positive-terrain points, nonpositive-WSE omission, depth-only hot-start lookup, exact reuse, one-decimal boundary-name collision, simultaneous convergence and edge priority, scenario-output CRS fallback, `save_zarr` forwarding and hashing gaps, post-processing, run-specific STL construction, publication, response, and checked-in fixture provenance.
- **Authority:** Current implementation and checked-in fixture evidence for the reviewed revision.
  Generated documentation is subordinate to code when exact field behavior or failure semantics differ.
  Current code does not prove scenario planning, deployed orchestration, source suitability, materialization, compositing, or hydraulic adequacy.
  Current transfer code does not validate index bounds or depth-terrain-manifest shape, transform, bounds, CRS, horizontal units, vertical datum, nodata masks, or cell registration before sampling.
- **Access date:** 2026-09-17.

## Target design

### SYS-001: System design guide

- **File:** `twod-fim-knowledge-base/system-design/guide.md`.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Target system landscape, data flow, repository ownership, run sequence, artifact contracts, and navigation to detailed design documents.
- **Authority:** Target design, not verified deployed behavior.
- **Access date:** 2026-09-17.

### SYS-002: Upstream scenario propagation

- **Files:** `twod-fim-knowledge-base/system-design/orchestrator-design.md` and `twod-fim-knowledge-base/system-design/triggers-and-propagation.md`.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Target ownership and topology-plus-provenance logic for propagating changed downstream hydraulic dependencies to affected upstream reaches.
- **Authority:** Target design, not verified deployed behavior and not a hydrologic flood-routing implementation.
- **Access date:** 2026-09-17.

### SYS-003: Authored discharge bounds and emergent scenario sets

- **File:** `twod-fim-knowledge-base/system-design/guide.md`.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Target distinction between authored `q_*_bound` and hydraulic-response-density intent and an emergent adaptive ND `q_set`, plus target materialization checks against authored intent.
- **Authority:** Target design, not verified deployed bound derivation or planning behavior.
- **Access date:** 2026-09-17.

### SYS-004: Solver comparison document

- **File:** `twod-fim-knowledge-base/solvers/lisflood_sfincs_comparison.md`.
- **Revision:** `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
- **Use:** Candidate LISFLOOD-FP and SFINCS file mappings, boundary concepts, deployment considerations, and a historical or target claim of interchangeability.
- **Authority:** Project comparison and candidate or target material only.
Its claim that both solvers are approved and interchangeable does not establish current implementation because the reviewed jobs checkout raises `NotImplementedError` for SFINCS preprocessing and lacks an equivalent execution path.
- **Access date:** 2026-09-17.

### SYS-005: Checked-in reconciler build-model caller

- **Files:** `twod-fim-deployment/orchestrator/recon/check.py`, `identity.py`, and `observe.py`.
- **Revision:** Unpinned checkout observation because no `twod-fim-deployment` revision was available from the existing Task 10 evidence and Git validation was prohibited.
- **Use:** Checked-in caller construction of explicit model inputs, current downstream-STL path supplied through `other_geometries`, reach-partitioned output base paths, identity prediction, manifest verification, and observed-storage materialization logic.
- **Authority:** checked-in unpinned prototype evidence for build-model caller and target-oriented observation roles only.
  It does not prove deployed behavior, successful current execution, source availability, or scientific adequacy.
  Its own comments state that extra geometries are outside model identity and that a model without a changed geometry can still satisfy current intent when the domain is unauthored.
  Refresh this observation against an exact repository revision before relying on it for a later current-code claim.
- **Access date:** 2026-09-17.

### SYS-006: Checked-in reconciler KWSE planner and observer

- **Files:** `twod-fim-deployment/orchestrator/recon/plan.py`, `scenarios.py`, `gap.py`, `check.py`, `observe.py`, `identity.py`, `storage.py`, and `jobs.py`.
- **Revision:** Unpinned checkout observation because Git validation was prohibited for this authoring task.
- **Use:** Checked-in prototype dependency ladder, terminal behavior, downstream-run indexing, discharge-specific planning, zero-anchored target grids, source binding, skipped targets, hot-start chains, one-job-per-discharge grouping, exact pending-scenario lookup, manifest observation, and materialized KWSE rows.
- **Authority:** checked-in unpinned prototype evidence for KWSE planning, submission, and observation roles only.
  It is outside the three current modeling jobs and does not prove deployed execution, runtime configuration, operational state, source availability, or scientific adequacy.
  Its comments cite DR-042 through DR-045, which are absent from the handbook-reviewed Decision Register, so those formulas do not have registered selected-methodology authority in this edition.
- **Access date:** 2026-09-17.

### SYS-007: Checked-in Flows2FIM export and maximum-VRT path

- **File:** `twod-fim-deployment/orchestrator/scripts/f2f.py`.
- **Revision:** Unpinned checkout observation because Git validation was prohibited for this authoring task.
- **Use:** Current local export of materialized ND and KWSE scenario records, selected depth-grid library construction, start-reach and network preparation, Flows2FIM Docker invocation for controls and VRT FIM output, local or storage publication, and VRT band rewriting with `PixelFunctionType` set to `max`.
- **Authority:** checked-in unpinned prototype evidence for Flows2FIM export and maximum-VRT generation roles outside `twod-fim-jobs` only.
  It does not prove deployed operation, successful execution, complete composite provenance, scenario compatibility, grid or datum compatibility, validation, or scientific acceptance.
- **Access date:** 2026-09-17.

## Checked-in artifact evidence

### ART-001: Checked-in build-model test fixture

- **Location:** `twod-fim-jobs/tests/test_data/models/reach=1257410937935512/10850311_N48S45E47W42/`.
- **Revision:** `40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e`.
- **Files inspected:** `model_manifest.json`, `dem.tif`, `roughness.tif`, `reach.geojson`, `inflow.geojson`, `anchor.geojson`, `domain.geojson`, and the unmanifested `outflow_area.geojson` present beside them.
- **Use:** Lab 8 inspection of manifest fields, schema drift, horizontal CRS, grid dimensions, raster transforms and bounds, vector roles and extents, checksum prefixes, identity coverage, warnings, downstream assignment, and missing evidence.
- **Authority:** Checked-in fixture evidence only.
  File presence and internal agreement do not prove current-job production, current storage materialization, remote-source immutability, vertical datum, boundary adequacy, hydraulic validation, or production readiness.
- **Access date:** 2026-09-17.

## Authoritative digital scientific references

### SCI-003: Watersheds and drainage basins

- **Organization:** U.S. Geological Survey Water Science School.
- **Title:** Watersheds and Drainage Basins.
- **URL:** https://www.usgs.gov/water-science-school/science/watersheds-and-drainage-basins
- **Use:** Outlet-dependent watershed definition, drainage divides, drainage area, water-budget framing, infiltration, evapotranspiration, storage, and delayed streamflow.
- **Authority:** Official USGS scientific education source.
- **Access date:** 2026-09-17.

### SCI-004: Strahler stream order

- **Organization:** U.S. Geological Survey 3D Hydrography Program.
- **Title:** Streamorder.
- **URL:** https://www.usgs.gov/media/images/streamorder
- **Use:** Strahler stream-order rules and sensitivity to the cartographic source scale.
- **Authority:** Official USGS hydrography source.
- **Access date:** 2026-09-17.

### SCI-005: Infiltration and baseflow

- **Organization:** U.S. Geological Survey Water Science School.
- **Title:** Infiltration and the Water Cycle.
- **URL:** https://www.usgs.gov/water-science-school/science/infiltration-and-water-cycle
- **Use:** Infiltration, soil and land-cover controls, groundwater storage, and sustained baseflow context.
- **Authority:** Official USGS scientific education source.
- **Access date:** 2026-09-17.

### SCI-006: Surface runoff and catchment response

- **Organization:** U.S. Geological Survey Water Science School.
- **Title:** Surface Runoff and the Water Cycle.
- **URL:** https://www.usgs.gov/water-science-school/science/surface-runoff-and-water-cycle
- **Use:** Runoff response, infiltration effects, and the relationship between faster drainage, runoff volume, and flood peaks.
- **Authority:** Official USGS scientific education source.
- **Access date:** 2026-09-17.

### SCI-007: Streamgaging and stage-discharge ratings

- **Organization:** U.S. Geological Survey Water Resources Mission Area.
- **Title:** Streamgaging Basics.
- **URL:** https://www.usgs.gov/mission-areas/water-resources/science/streamgaging-basics
- **Use:** Site-specific stage-discharge relationships, paired measurements, continuous discharge estimation, rating maintenance, and changing channel controls.
- **Authority:** Official USGS streamgaging source.
- **Access date:** 2026-09-17.

### SCI-008: National Weather Service routing glossary

- **Organization:** NOAA National Weather Service.
- **Title:** NOAA's National Weather Service Glossary: Routing.
- **URL:** https://forecast.weather.gov/glossary.php?word=routing
- **Use:** Channel-routing, flood-routing, and attenuation terminology.
- **Authority:** Official National Weather Service glossary.
- **Access date:** 2026-09-17.

### SCI-009: Bulletin 17C

- **Organization:** U.S. Geological Survey and the Advisory Committee on Water Information, Hydrologic Frequency Analysis Work Group.
- **Authors:** John F. England Jr., Timothy A. Cohn, Beth A. Faber, Jery R. Stedinger, Wilbert O. Thomas Jr., Andrea G. Veilleux, Julie E. Kiang, and Robert R. Mason Jr.
- **Title:** Guidelines for Determining Flood Flow Frequency, Bulletin 17C, version 1.1.
- **URL:** https://pubs.usgs.gov/publication/tm4B5
- **Use:** Annual peak-flow frequency framework, LP3 and Expected Moments Algorithm context, data assumptions, stationarity, influential low floods, historical information, skew, and confidence intervals.
- **Authority:** Federal flood-frequency guideline and primary USGS publication.
- **Access date:** 2026-09-17.

### SCI-010: AEP and the 100-year flood

- **Organization:** U.S. Geological Survey Water Science School.
- **Title:** The 100-Year Flood.
- **URL:** https://www.usgs.gov/water-science-school/science/100-year-flood
- **Use:** Annual exceedance probability, recurrence-interval conversion, and the warning that recurrence interval is not a fixed event schedule.
- **Authority:** Official USGS scientific education source.
- **Access date:** 2026-09-17.

### SCI-011: National Water Model retrospective dataset

- **Organization:** National Oceanic and Atmospheric Administration National Weather Service, distributed through the NOAA Open Data Dissemination Program.
- **Title:** NOAA National Water Model CONUS Retrospective Dataset.
- **URL:** https://registry.opendata.aws/nwm-archive/
- **Use:** NWM retrospective versions and periods, version 3.0 AORC version 1.1 forcing, hourly streamflow context, and the absence of streamflow assimilation in the retrospective simulations.
- **Authority:** Official NOAA-managed dataset description and distribution record.
- **Access date:** 2026-09-17.

### SCI-012: National Water Model configurations and forcing

- **Organization:** National Oceanic and Atmospheric Administration National Weather Service Office of Water Prediction.
- **Title:** About the National Water Model.
- **URL:** https://water.noaa.gov/about/nwm
- **Use:** Operational analysis, open-loop analysis, forecast configurations, meteorological forcing sources, streamflow assimilation boundaries, spatial coverage, and output context.
- **Authority:** Official NOAA National Water Model documentation.
- **Access date:** 2026-09-17.

### SCI-013: National Water Model output and forcing fields

- **Organization:** National Oceanic and Atmospheric Administration National Weather Service Office of Water Prediction.
- **Title:** Output File Contents.
- **URL:** https://water.noaa.gov/about/output_file_contents
- **Use:** NWM forcing variables, units, file types, and the distinction between forcing fields and stream-routing output.
- **Authority:** Official NOAA National Water Model documentation.
- **Access date:** 2026-09-17.

### SCI-014: Nonstationary flood-frequency analysis

- **Organization:** U.S. Geological Survey.
- **Author:** Sara B. Levin.
- **Title:** Nonstationary Flood Frequency Analysis Using Regression in the North-Central United States.
- **URL:** https://pubs.usgs.gov/publication/sir20255034/full
- **Use:** Stationarity assumptions, trends and change points in annual peak flow, climate-adjusted analysis, land-use and regulation evidence, and limits on choosing a nonstationary model.
- **Authority:** Primary USGS Scientific Investigations Report 2025-5034.
- **Access date:** 2026-09-17.

### SCI-015: Hydraulic-model sensitivity

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Model Sensitivity.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/rasum/6.3/performing-a-1d-unsteady-flow-analysis/model-accuracy-stability-and-sensitivity/model-sensitivity
- **Use:** Separation of numerical sensitivity from physical-parameter sensitivity and comparison of model results across realistic parameter or statistical-event ranges.
- **Authority:** Official HEC-RAS user documentation.
- **Access date:** 2026-09-17.

### SCI-016: HEC-RAS continuity equation

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Continuity Equation.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.6/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-unsteady-flow-hydrodynamics/continuity-equation
- **Use:** Control-volume conservation, inflow, outflow, lateral inflow, and storage change.
- **Authority:** Official HEC-RAS Hydraulic Reference Manual documentation.
- **Access date:** 2026-09-17.

### SCI-017: HEC-RAS energy equation

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Equations for Basic Profile Calculations.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.1/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-steady-flow-water-surface-profiles/equations-for-basic-profile-calculations
- **Use:** Section-to-section energy head, bed elevation, depth, corrected velocity head, and energy-head loss.
- **Authority:** Official HEC-RAS Hydraulic Reference Manual documentation.
- **Access date:** 2026-09-17.

### SCI-018: HEC-RAS mean kinetic-energy head

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Evaluation of the Mean Kinetic Energy Head.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.2/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-steady-flow-water-surface-profiles/evaluation-of-the-mean-kinetic-energy-head
- **Use:** Velocity-head weighting, kinetic-energy correction, and limits of one mean cross-section energy.
- **Authority:** Official HEC-RAS Hydraulic Reference Manual documentation.
- **Access date:** 2026-09-17.

### SCI-019: HEC-RAS momentum equation

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Momentum Equation.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.6/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-unsteady-flow-hydrodynamics/momentum-equation
- **Use:** Control-volume momentum, pressure, gravity, boundary drag, momentum flux, and momentum accumulation.
- **Authority:** Official HEC-RAS Hydraulic Reference Manual documentation.
- **Access date:** 2026-09-17.

### SCI-020: HEC-RAS one-dimensional hydraulic equations

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Hydraulic Equations (1D FV).
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.6/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-unsteady-flow-hydrodynamics/semi-implicit-finite-volume-scheme/hydraulic-equations-1d-fv
- **Use:** One-dimensional mass and momentum equations, WSE gradient, friction, acceleration, and bottom-drag context.
- **Authority:** Official HEC-RAS Hydraulic Reference Manual documentation.
- **Access date:** 2026-09-17.

### SCI-021: USACE subdivision Froude number

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Authors:** David H. Schoellhamer, John C. Peters, and Bruce E. Larock.
- **Title:** Subdivision Froude Number.
- **URL:** https://www.hec.usace.army.mil/publications/TechnicalPapers/TP-110.pdf
- **Use:** Simple Froude-number definition, hydraulic depth as area divided by top width, flow-regime interpretation, and limitations of a uniform-velocity whole-section value for compound sections.
- **Authority:** Primary technical paper published by the U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Access date:** 2026-09-17.

### SCI-022: HEC-RAS flow-regime boundary guidance

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Boundary Conditions.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.0/basic-data-requirements/steady-flow-data/boundary-conditions0
- **Use:** Downstream boundary direction for subcritical profiles, upstream boundary direction for supercritical profiles, mixed-regime boundary needs, and boundary-placement sensitivity.
- **Authority:** Official HEC-RAS Hydraulic Reference Manual documentation.
- **Access date:** 2026-09-17.

### SCI-023: HEC-RAS uniform-flow computations

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Uniform Flow Computations.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/latest/stable-channel-design-functions/uniform-flow-computations
- **Use:** Manning variables, steady uniform-flow calculations, and the coupling among depth, geometry, slope, discharge, and roughness.
- **Authority:** Official HEC-RAS Hydraulic Reference Manual documentation.
- **Access date:** 2026-09-17.

### SCI-024: HEC-RAS downstream boundary conditions

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Downstream Boundary Conditions.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.0/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-unsteady-flow-hydrodynamics/implicit-finite-difference-scheme/downstream-boundary-conditions
- **Use:** Stage, flow, rating-curve, and normal-depth downstream conditions, conveyance form, uniform-flow assumption, and advice to keep boundary effects outside the study area.
- **Authority:** Official HEC-RAS Hydraulic Reference Manual documentation.
- **Access date:** 2026-09-17.

### SCI-025: HEC-RAS energy-loss coefficients

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Energy Loss Coefficients.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.2/basic-data-requirements/geometric-data/energy-loss-coefficients
- **Use:** Manning's n variability, influencing factors, calibration guidance, and limits of treating roughness as one fixed material property.
- **Authority:** Official HEC-RAS Hydraulic Reference Manual documentation.
- **Access date:** 2026-09-17.

### SCI-026: HEC-RAS 2D external boundary conditions

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** External Boundary Conditions.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/r2dum/latest/boundary-and-initial-conditions-for-2d-flow-areas/external-boundary-conditions
- **Use:** Two-dimensional flow, stage, rating-curve, and normal-depth boundaries, cell-based normal-depth calculation, and the distinction between stage boundaries and optional stage-based initialization.
- **Authority:** Official HEC-RAS 2D User's Manual documentation.
- **Access date:** 2026-09-17.

### SCI-027: HEC-RAS 2D unsteady-flow hydrodynamics

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** 2D Unsteady Flow Hydrodynamics.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.6/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/2d-unsteady-flow-hydrodynamics
- **Use:** Hydrostatic and depth-averaging assumptions, WSE and depth definitions, two-dimensional mass and momentum conservation, terrain and friction terms, finite-volume context, and subgrid-bathymetry scope.
- **Authority:** Official HEC-RAS Hydraulic Reference Manual documentation.
- **Access date:** 2026-09-17.

### SCI-028: HEC-RAS 2D computational mesh

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Development of the 2D Computational Mesh.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/r2dum/6.6/development-of-a-2d-or-combined-1d-2d-model/development-of-the-2d-computational-mesh
- **Use:** Two-dimensional flow-area, cell-center, cell-face, cell-face-point, roughness, terrain, and hydraulic-property-table concepts.
- **Authority:** Official HEC-RAS 2D User's Manual documentation.
- **Access date:** 2026-09-17.

### SCI-029: HEC-RAS grid-size and time-step guidance

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Selecting an Appropriate Grid Size and Time Step.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/r2dum/latest/running-a-model-with-2d-flow-areas/selecting-an-appropriate-grid-size-and-time-step
- **Use:** Terrain and face representation, rapidly varying WSE and velocity, cell-size and time-step interaction, Courant guidance, dry-start wetting-front caution, and joint space-time sensitivity.
- **Authority:** Official HEC-RAS 2D User's Manual documentation with HEC-RAS-specific recommendations.
- **Access date:** 2026-09-17.

### SCI-030: GDAL geotransform

- **Organization:** Open Source Geospatial Foundation GDAL project.
- **Title:** Geotransform Tutorial.
- **URL:** https://gdal.org/en/stable/tutorials/geotransforms_tut.html
- **Use:** Six-coefficient affine raster transform, pixel and line to map-coordinate equations, north-up conventions, and cell-center coordinates.
- **Authority:** Official GDAL documentation.
- **Access date:** 2026-09-17.

### SCI-031: LISFLOOD-FP local-inertial formulation

- **Authors:** Paul D. Bates, Matthew S. Horritt, and Timothy J. Fewtrell.
- **Title:** A Simple Inertial Formulation of the Shallow Water Equations for Efficient Two-Dimensional Flood Inundation Modelling.
- **URL:** https://doi.org/10.1016/j.jhydrol.2010.03.027
- **Use:** Primary research on a local-inertial shallow-water formulation, floodplain face flux, friction, wetting, time stepping, and numerical efficiency in the LISFLOOD-FP model family.
- **Authority:** Peer-reviewed primary research in the *Journal of Hydrology*.
- **Access date:** 2026-09-17.

### SCI-032: SFINCS user manual

- **Organization:** Deltares.
- **Title:** User Manual - General, documentation release 2.0.6.
- **URL:** https://sfincs.readthedocs.io/en/2.0.6/input.html
- **Use:** Solver-specific rectilinear-grid, active-mask, elevation, roughness, subgrid-table, adaptive CFL diagnostic, wet-state limiter, and numerical-parameter documentation.
- **Authority:** Official SFINCS documentation for documentation release 2.0.6, not evidence of current project implementation or an exact LISFLOOD-FP rule.
- **Access date:** 2026-09-17.

### SCI-033: LISFLOOD-FP user manual

- **Organization:** University of Bristol School of Geographical Sciences.
- **Authors:** Paul Bates, Mark Trigg, Jeff Neal, Amy Dabrowa, and contributors named by the manual.
- **Title:** LISFLOOD-FP User Manual, Code Release 5.9.6.
- **URL:** https://www.bristol.ac.uk/media-library/sites/geography/migrated/documents/lisflood-manual-v5.9.6.pdf
- **Use:** Official documentation for the historical release's raster inputs, saved outputs, `QFIX`, `FREE`, `HFIX`, `HVAR`, and water-depth `startfile` semantics.
- **Authority:** Official University of Bristol solver documentation for release 5.9.6.
It supports bounded external capability claims and does not establish the exact behavior, version, build, or validation status of the current project executable.
- **Access date:** 2026-09-17.

### SCI-034: SFINCS forcing documentation

- **Organization:** Deltares.
- **Title:** User Manual - Forcing, documentation release 2.0.6.
- **URL:** https://sfincs.readthedocs.io/en/2.0.6/input_forcing.html
- **Use:** Official documentation for water-level boundary points and time series, discharge source points and time series, forcing units, and time-reference semantics in the documented release.
- **Authority:** Official SFINCS documentation for documentation release 2.0.6, not evidence of current project implementation or solver equivalence.
- **Access date:** 2026-09-17.

### SCI-035: USGS 3DEP one-third arc-second DEM

- **Organization:** U.S. Geological Survey.
- **Title:** 1/3rd arc-second Digital Elevation Models (DEMs) - USGS National Map 3DEP Downloadable Data Collection.
- **URL:** https://data.usgs.gov/datacatalog/data/USGS%3A3a81321b-c153-416f-98b7-cc8e5f0e17c3
- **Use:** Bare-earth product scope, approximately 10 m source spacing, NAD83 geographic coordinates, meter elevations, NAVD88 coverage statement for the conterminous United States, continual updates, and public-domain status.
- **Authority:** Official USGS dataset record.
- **Access date:** 2026-09-17.

### SCI-036: USGS DEM surface treatments

- **Organization:** U.S. Geological Survey National Geospatial Program.
- **Title:** Lidar Base Specification: Digital Elevation Model Surface Treatments.
- **URL:** https://www.usgs.gov/ngp-standards-and-specifications/lidar-base-specification-digital-elevation-model-surface
- **Use:** Bare-earth bridge removal, interpolated below-bridge terrain, hydroflattening, stream continuity at bridges, and road surfaces retained at culverts.
- **Authority:** Official USGS 3DEP specification.
- **Access date:** 2026-09-17.

### SCI-037: USGS lidar survey types

- **Organization:** U.S. Geological Survey Office of Surface Water.
- **Title:** Global Positioning System Projects, Types of LiDAR.
- **URL:** https://water.usgs.gov/osw/gps/GPS_projects.html
- **Use:** Infrared topographic-lidar inability to measure below the water surface and the different wavelength and water-clarity scope of bathymetric lidar.
- **Authority:** Official USGS technical overview.
- **Access date:** 2026-09-17.

### SCI-038: NOAA topobathy lidar

- **Organization:** National Oceanic and Atmospheric Administration National Geodetic Survey.
- **Title:** Topobathy Lidar.
- **URL:** https://www.ngs.noaa.gov/RSD/topobathy.shtml
- **Use:** Coastal topobathymetric lidar purpose, point-cloud and DEM products, near-shore depth scope, and applications to inundation mapping.
- **Authority:** Official NOAA program source.
- **Access date:** 2026-09-17.

### SCI-039: Annual NLCD Collection 1 user guide

- **Organization:** U.S. Geological Survey and Multi-Resolution Land Characteristics Consortium.
- **Title:** Annual National Land Cover Database Collection 1 Science Product User Guide, version 1.1.
- **URL:** https://www.mrlc.gov/sites/default/files/docs/LSDS-2103%20Annual%20National%20Land%20Cover%20Database%20%28NLCD%29%20Collection%201%20Science%20Product%20User%20Guide%20-v1.1%202025_06_11.pdf
- **Use:** Collection and release semantics, sixteen categorical land-cover classes, 30 m map units, 2023 Collection 1.0 baseline context, cloud distribution, validation, and documented caveats.
- **Authority:** Official Annual NLCD product guide.
- **Access date:** 2026-09-17.

### SCI-040: USACE land cover and Manning's n guidance

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Creating Land Cover, Manning's n values, and Percent Impervious Layers.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/r2dum/6.6/developing-a-terrain-model-and-geospatial-layers/creating-land-cover-mannings-n-values-and-impervious-layers
- **Use:** User-assigned base values, complete class mapping including nodata, priority layers, channel-specific polygons, overrides, and calibration regions.
- **Authority:** Official HEC-RAS 2D User's Manual guidance.
- **Access date:** 2026-09-17.

### SCI-041: USACE bridges in 2D flow areas

- **Organization:** U.S. Army Corps of Engineers Hydrologic Engineering Center.
- **Title:** Modeling Bridges Inside 2D Flow Areas.
- **URL:** https://www.hec.usace.army.mil/confluence/rasdocs/r2dum/6.6/development-of-a-2d-or-combined-1d-2d-model/modeling-bridges-inside-2d-flow-areas
- **Use:** Bridge deck, roadway, pier, abutment, roughness, cross-section, head-loss, pressure-flow, and overtopping information required by one official hydraulic-structure method.
- **Authority:** Official USACE solver guidance used for structure concepts, not evidence that the project implements HEC-RAS.
- **Access date:** 2026-09-17.

### SCI-042: Rasterio reprojection and resampling

- **Organization:** Rasterio project.
- **Title:** `rasterio.warp` module and Resampling documentation.
- **URL:** https://rasterio.readthedocs.io/en/stable/api/rasterio.warp.html
- **Use:** `reproject` API behavior and the `Resampling.nearest` default.
- **Authority:** Official Rasterio API documentation for the library behavior invoked by current code.
- **Access date:** 2026-09-17.

### SCI-043: NASA standard for models and simulations

- **Organization:** National Aeronautics and Space Administration, Office of the Chief Engineer.
- **Title:** NASA-STD-7009B, Standard for Models and Simulations.
- **URL:** https://standards.nasa.gov/sites/default/files/standards/NASA/B/1/NASA-STD-7009B-Final-3-5-2024.pdf
- **Use:** Direct definitions of verification, validation, calibration, intended use, uncertainty characterization, data pedigree, sensitivity analysis, domains of verification and validation, and requirements for project-defined acceptance criteria and result reporting.
- **Authority:** Active official NASA technical standard for models and simulations.
- **Access date:** 2026-09-18.

### SCI-044: NIST assessment of accuracy and reliability

- **Authors:** Ronald F. Boisvert, Ronald Cools, and Bo Einarsson.
- **Title:** Assessment of Accuracy and Reliability.
- **URL:** https://tsapps.nist.gov/publication/get_pdf.cfm?pub_id=150040
- **Use:** Direct distinctions among code verification, solution verification, validation, numerical approximations, uncertainty, and bounded regions of validity in scientific computing.
- **Authority:** NIST-hosted scientific-computing chapter with NIST-contributed content.
- **Access date:** 2026-09-18.

### SCI-045: EPA environmental-model guidance

- **Organization:** U.S. Environmental Protection Agency, Council for Regulatory Environmental Modeling.
- **Title:** Guidance on the Development, Evaluation, and Application of Environmental Models.
- **URL:** https://nepis.epa.gov/Exe/ZyPURL.cgi?Dockey=P1003E4R.TXT
- **Use:** Direct official guidance on decision-specific model quality specifications, calibration, corroboration, sensitivity analysis, uncertainty analysis, data quality, and model evaluation.
- **Authority:** Official EPA guidance for environmental models.
- **Access date:** 2026-09-18.

## Supporting hard-copy scientific references

### SCI-001: Open-Channel Hydraulics

- **Author:** Ven Te Chow.
- **Use:** Supporting study for continuity, energy, momentum, uniform flow, normal depth, gradually varied flow, and backwater.
- **Access boundary:** The hard-copy book was not directly inspected for this task.
- **Citation rule:** Do not invent page or chapter references.

### SCI-002: Applied Hydrology

- **Authors:** Ven Te Chow, David R. Maidment, and Larry W. Mays.
- **Use:** Supporting study for water balance, rainfall-runoff response, hydrographs, routing, flood frequency, and discharge uncertainty.
- **Access boundary:** The hard-copy book was not directly inspected for this task.
- **Citation rule:** Do not invent page or chapter references.
