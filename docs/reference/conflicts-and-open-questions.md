# Conflicts and Open Questions

This register preserves material disagreements, unimplemented decisions, and weakly settled policy.
An open record is not permission to choose an answer without project authority.

## Record format

Each record contains an identifier, evidence labels, conflict statement, source boundaries, current consequence, resolution question, and status.
Close a record only when the controlling source or authorized project decision resolves the conflict and the handbook mapping has been updated.

## CONF-001: Boundary-condition terminology and behavior

**Evidence labels:** Selected methodology, Current implementation, Open question.

**Conflict:** DR-003 distinguishes freefall from normal depth and selects freefall for downstream-FIM-informed KWSE edge cells.
Current code uses the `FREE` token for a boundary condition whose schema value is described as a normal-depth slope in m/m.
The ND job supplies an estimated centerline-endpoint terrain slope subject to a configured minimum.
The KWSE job supplies 0.5 m/m in addition to a `TRANSFER` condition and turns all intersections between the downstream inundation polygon and one cardinal edge into one contiguous span over their overall bounds, including dry gaps between disjoint intersections.
The Decision Register and standalone marker identify DR-006 ALT-E for downstream-waterbody-informed edge handling, while the latest standalone decision-history line says the method switched to ALT-D.

**Source boundaries:** The Decision Register controls intended methodology within its status and scope.
The registered DR-006 ALT-E selection remains the methodology statement within that status and scope, while the conflicting history is an Open question requiring governance cleanup.
The current jobs checkout controls implemented behavior.

**Consequence:** Using `FREE`, freefall, steep-slope outflow, and normal depth as synonyms can conceal a material hydraulic difference in downstream control.
A large slope value can promote drainage in the implemented normal-depth interface without proving that the solver represents a physical free overfall.
The contiguous current span can also release water across gaps not described by DR-003's downstream-FIM-informed edge-cell wording.

**Resolution question:** Which physical and numerical behavior is intended for each ND and KWSE edge context, and should the selected methodology, code behavior, code token, schema description, or documentation change so that the physical boundary condition and its name agree?
What comparison evidence will show that the resolved geometry and behavior control WSE and edge drainage acceptably without releasing water across unintended gaps or contaminating the interpretation area?

**Status:** Open.

## CONF-002: Volume convergence and mass balance

**Evidence labels:** Scientific foundation, Selected methodology, Current implementation, Evidence or experiment, Open question.

**Conflict:** DR-022 and DR-028 select a storage-change convergence ratio and threshold for automated termination.
The reviewed register and DR-022 history identify ALT-G, while the standalone DR-022 file also marks ALT-J as `#current`.
The current calculation compares positive-depth raster storage at consecutive saved outputs and normalizes the absolute difference by constant inflow volume over the saved-output interval.
It does not measure outflow or every source and sink, so it is not a complete mass-balance closure.
The manifest stores only the final ratio and termination reason rather than the complete convergence history or balance terms.
When convergence and a disallowed edge violation are both true on the same saved grid, current branch order persists `volume_convergence` and suppresses `edge_error` as the stored reason.

**Source boundaries:** The decision records establish the selected termination method.
The registered ALT-G selection remains the methodology statement within its recorded status and scope, while the competing ALT-J marker remains an Open question.
The code establishes the exact implemented ratio.
The standalone EXP-014 file supplies the methodology plan, while Case-018 records the preliminary scoped metric analysis conducted under that plan rather than universal validation.

**Consequence:** A converged ratio can show small net storage change while opposite local changes cancel in the domain total and while hydraulic adequacy, residual local transients, solver stability, edge behavior, initial-condition sensitivity, and inflow-outflow balance remain unproven.
The persisted convergence reason also cannot prove that the same saved grid had no implemented edge violation.

**Resolution question:** What local-state, boundary-flux, full-balance, edge, sensitivity, observation, and uncertainty evidence is required to accept a scenario as hydraulically adequate for library and FIM use?
Which of those diagnostics must be persisted with each scenario rather than reconstructed later?

**Status:** Open.

## CONF-003: SFINCS documentation and current support

**Evidence labels:** Current implementation, Target design, Open question.

**Conflict:** Schemas, enums, preprocessing code, generated job documentation, and the local solver-comparison document refer to SFINCS.
The comparison document describes LISFLOOD-FP and SFINCS as approved and interchangeable.
The current writer raises `NotImplementedError` for SFINCS, version discovery is unimplemented, and the run path invokes LISFLOOD-FP.
For current runs, the run identity contains only the solver enum and baked `SDR_COMMIT`, not the executable version or build, image digest, run configuration, or hardware identity.

**Source boundaries:** Current code controls current-support claims.
Official solver documentation controls capability claims for its documented version.
The local comparison is candidate or target material and cannot establish an executable equivalent project path.

**Consequence:** A reader could infer that SFINCS is supported because it appears in interfaces and documentation even though the current checkout cannot execute that path.
A reader could also mistake a current manifest identity for proof of version-pinned execution even though materially different solver environments can share that identity.

**Resolution question:** Which version-pinned preprocessing, execution, version-discovery, monitoring, convergence, boundary, post-processing, artifact, failure-handling, deployment, and validation contracts must exist before SFINCS becomes a supported current implementation?
Which schema and producer changes will record executable version and build, image digest, run configuration, and hardware identity for reproducible solver provenance?

**Status:** Open.

## CONF-004: Selected methodology and code defaults

**Evidence labels:** Selected methodology, Current implementation, Open question.

**Conflict:** DR-015 selects a 100 m inflow line and DR-016 selects placement at 0.25 of upstream reach length.
Current `build_model` defaults use a bankfull-width multiplier of 1.0 and an upstream-walk fraction of 0.1.
DR-011 also expects downstream STL geometry when available, while `build_model` can only include geometry supplied through its current inputs.
The reviewed Decision Register selects DR-011 ALT-D, while the standalone DR-011 file also marks materially different ALT-E as `#current`.
DR-014 ALT-B has Alternate Selected status and selects points distributed along a headwater reach.
Current `make_inflow_line` instead creates one perpendicular line at the reach start when the caller supplies a null upstream mainstem and false for `ds_of_lake`.
The field descriptions assign network meaning to `upstream_reach_ids` and `upstream_mainstem_reach_id`, but current code trusts caller values without verifying completeness, adjacency, mainstem membership, largest drainage area, or that null represents a true headwater.
Current code also exports a grid-snapped domain anchor but stores it under the model-manifest field named `reach_centroid`.
`walk_us_dist_pct` is constrained only to values greater than zero, so values above the documented fraction range at or below 1 pass validation and can clamp or reinterpret interpolation.
`centerline_buffer_bankfull_multiplier` has no bound, so zero or negative values can remove the intended buffered target and approach corridor from computed bounds while other geometries allow the build to continue.

**Source boundaries:** The Decision Register controls intended values and method within each decision's status.
The registered DR-011 ALT-D selection remains the methodology statement within that status and scope, while the competing file-local marker remains an Open question.
The code defaults control behavior only when callers do not override them.

**Consequence:** A successful job can produce a valid artifact that does not realize selected methodology unless the caller supplies the intended values and geometries.
A caller-supplied false headwater or mainstem classification can drive the wrong geometry branch, a headwater model can contradict DR-014, and an artifact consumer can mistake the grid-snapped anchor for the exact geometric reach centroid.
An out-of-range walk fraction can place the inflow at an endpoint or otherwise outside the documented fractional interpretation, while a nonpositive centerline buffer can omit the intended hydraulic corridor without a validation error or warning.

**Resolution question:** Which component proves upstream-list completeness and adjacency, verifies mainstem membership and largest drainage area, and establishes true headwater status before translating selected methodology into explicit job inputs?
How will DR-014's distributed headwater inflows be realized and validated, and should the anchor schema field or artifact description change so its grid-snapped meaning is explicit?
Which authorized input-contract owner will enforce `0 < walk_us_dist_pct <= 1`, `centerline_buffer_bankfull_multiplier > 0`, any required finite upper buffer limit, and post-construction evidence that the inflow and buffered corridor realize the intended geometry?

**Status:** Open.

## CONF-005: Topobathymetry policy

**Evidence labels:** Selected methodology, Current implementation, Evidence or experiment, Open question.

**Conflict:** The reviewed Decision Register lists DR-021 ALT-A, no handling, with Alternate Selected status and a last-update date of 2026-02-02.
The standalone DR-021 file marks both ALT-A and ALT-E with `#current`, while its decision history says ALT-G was selected after a team meeting on 2026-05-08 and ALT-G itself has no `#current` marker.
The same record identifies a rejected Case-015 outcome under ALT-A, EXP-012 proposes comparison evidence, and the approved handbook design calls the policy weakly settled.
Current `build_model` code performs no topobathymetry merge or correction.

**Source boundaries:** The Decision Register controls registered methodology status, but its row conflicts with the standalone record's markers and history.
Case and experiment material can challenge or test that choice but does not silently replace it.
Current code controls implemented behavior and cannot resolve the methodology conflict.

**Consequence:** The handbook cannot identify one unambiguous selected topobathymetry alternative at the reviewed revision.
Unrepresented channel conveyance can affect WSE, depth, velocity, and inundation, especially where below-water geometry is material.

**Resolution question:** Which alternative should authorized project governance record as current in both the Decision Register and standalone DR-021 file?
What reach classes, source hierarchy, merge rules, benchmarks, and acceptance thresholds justify that choice?

**Status:** Open.

## CONF-006: Work outside current modeling jobs

**Evidence labels:** Current implementation, Target design, Selected methodology, Open question.

**Conflict:** Scenario planning, short-reach merging, lake and coastal network modification, downstream-stage decisions, cross-reach library assembly, and compositing are required by selected methodology and target design but sit outside the three current modeling jobs.
The network decisions do not define a complete source-to-prepared identifier-lineage schema.
The current `build_model` job consumes caller-supplied topology, and its manifest assignment currently populates `properties.downstream_reach_id` from the modeled `reach_id` rather than from `reach_to_id`.
Some older target-design files call the orchestration record `current_state`, while the current reconciliation-loop design replaces that broad term with the narrower `materialized_*` tables.

**Source boundaries:** Job code and documentation define current job scope.
Decision records define intended scientific choices.
System-design files define target ownership and data flow.

**Consequence:** Job success cannot establish that the network was prepared, source identifiers retained, topology preserved, scenarios planned, dependencies satisfied, or a composite product assembled according to methodology.
The current model manifest's downstream field cannot be treated as authoritative adjacency evidence.
Using the older state term can also suggest a broader inventory or job-result record than the current target materialization contract defines.

**Resolution question:** Which current or target component owns each external step, which prepared-network artifact records source-to-prepared lineage and authoritative adjacency, and how will the current manifest downstream field be corrected or redefined?
Which stored artifact is authoritative evidence of completion for every external step?

**Status:** Open.

## CONF-007: Decision Register revision mismatch in identities

**Evidence labels:** Current implementation, Target design, Open question.

**Conflict:** The handbook reviews the Decision Register at knowledge-base revision `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
The reviewed jobs checkout sets `SDR_COMMIT` to `826a602ddcaf58bf4081dc04b65ba15b82cc8c8a`, and current model and run identities embed that different revision.

**Source boundaries:** The handbook bibliography identifies the methodology revision reviewed for this edition.
Current jobs code controls the revision embedded in generated identities.
The target system-design guide treats the baked revision as an identity input and drift control.

**Consequence:** A current manifest identity cannot be cited as proof that the model or scenario used the Decision Register revision reviewed by the handbook.
The mismatch can also prevent target desired state and observed materialization from agreeing on object identity.

**Resolution question:** Will project authority review and adopt the baked revision as the handbook methodology snapshot, or authorize a jobs image and desired-state update that use the approved revision?
Resolution also requires storage observation of manifests whose identities contain the authorized value.

**Status:** Open.

## CONF-008: Unregistered DR-039 selection

**Evidence labels:** Current implementation, Open question.

**Conflict:** The standalone DR-039 file marks ALT-F with `#current` and records a decision-history note.
The reviewed Decision Register has no DR-039 row or registered status.

**Source boundaries:** The Decision Register controls Selected methodology status under the handbook authority model.
The standalone file supplies file-local context but cannot supply the missing registered status by itself.
Current code separately controls implemented ND boundary behavior.

**Consequence:** Treating DR-039 as selected methodology would grant authority that the controlling register does not record.

**Resolution question:** Should authorized project governance add DR-039 and its status to the Decision Register, or revise the standalone record and affected documentation so the intended authority is explicit?

**Status:** Open.

## CONF-009: Scenario publication, membership, reuse, and discharge-grid authority

**Evidence labels:** Current implementation, Target design, Open question.

**Conflict:** The current ND job publishes every newly simulated non-edge-error trial before the adaptive verdict, including trials later rejected from adaptive library selection, and can re-adopt caller-supplied manifests when only reach ID, full model ID, run identity hash, and inclusive discharge range match.
The ND adoption predicate does not compare full inputs, run settings, boundary geometry or slope, hot start, convergence tolerance, output interval, wall-time setting, or referenced-asset existence.
Separately, a requested baseline or later scenario can be reused from its predicted address when the stored full `RunScenarioInputs` equal the requested inputs exactly.
The baseline does not pass through the adaptive edge-abort check, so a newly simulated or exactly reused edge-error baseline can become the reference.
A later exact-reused edge-error manifest already exists in storage but still triggers the adaptive abort before comparison, while a newly simulated edge-error trial returns before publication.
The current KWSE job processes every supplied scenario but can re-adopt a matching existing manifest without simulating or uploading it again.
These manifest-level reuse checks do not verify every referenced asset.
The shared remote-input cache uses each asset's declared 16-character checksum as a directory key but returns existing or newly copied bytes without hashing and comparing them with that declaration.
Stale, corrupted, changed, or prefix-colliding bytes can therefore be passed to preprocessing while the manifest still carries the expected declared checksum.
ND slope directory names use one decimal place in scientific notation, and KWSE directory names use one decimal place.
Distinct raw boundary values can therefore map to the same address.
Exact input comparison prevents unequal manifest reuse but does not prevent a later new run from sequentially overwriting or mixing files at that shared address.
The current `Q_GRID_RESOLUTION` code comment cites DR-041, but the reviewed Decision Register has no DR-041 row.
Finite-window midpoint proposals and the first below-window candidate are zero-grid aligned.
The no-grid-value fallback can instead return `position + q_grid_resolution`, which remains off-grid when the position came from an off-grid adopted scenario or opening trial.
No-window, infinite-close, and at-or-above-maximum branches return the unsnapped maximum directly.
The `_adopt_existing` path, minimum and maximum endpoints, and initial `min_upstream_inflow + delta_upstream_inflow` trial are not checked for grid alignment.
The generated ND documentation says rejected trials remain unpublished in the working directory and says only accepted scenarios plus endpoints are uploaded.
Current code instead publishes each newly simulated non-edge-error trial before measuring its verdict.
The generated response description says the comparison list is for accepted scenarios and uses `None` for baseline and maximum, while current code appends a concrete accepted baseline comparison and concrete ordinary trial comparisons.
The generated edge-error text says the job returns an empty scenario list, while current code returns the comparisons accumulated before the abort plus a job warning.

**Source boundaries:** Current code defines input localization, publication, and reuse behavior.
Target design requires storage observation for materialization.
The Decision Register controls selected-methodology status and does not currently authorize DR-041.

**Consequence:** Published artifacts can be mistaken for selected ND library members, the limited ND predicate can be mistaken for full-input equality, returned or re-adopted manifests can be mistaken for verified compatible assets, and the discharge grid can be mistaken for registered methodology or a fully enforced current contract.
An ND library can currently contain off-grid adopted, endpoint, initial-trial, position-relative fallback, or direct-maximum discharges.
An edge-error manifest can also be treated differently depending on whether it becomes the baseline, is already in the loose adopted `done` set, or is found later through exact full-input reuse.
The generated documentation can also cause an operator to delete or retain the wrong objects, misread a partial response as empty or complete, or treat published search points as selected members.
Two scientifically distinct raw boundary values can also collide at one lossy address even though exact reuse refuses the unequal manifest.
An input path and manifest can appear checksum identified even though current execution never verifies that localized bytes match that declaration.

**Resolution question:** Which durable record identifies selected ND library membership, which materialization check verifies every required asset for published and re-adopted scenarios, and what authorized record defines the discharge-grid rule?
Which localization owner will rehash every cached or downloaded input before use, require the declared checksum prefix to match, fail closed with actionable context on mismatch, and decide whether the asset contract must carry a full SHA-256 digest?
Should ND adoption require full-input equality or an explicit compatibility contract for run settings, boundaries, hot starts, and referenced assets?
Should current input validation or job logic enforce alignment for adopted manifests, endpoints, the initial authored-step trial, and every position-relative fallback?
Should baseline and already-adopted edge-error manifests follow the same abort contract as later exact-reused edge-error manifests?
Should boundary values be validated onto the naming grid, encoded at greater precision, or distinguished through a content-complete scenario identity?

**Status:** Open.

## CONF-010: Depth-only hot starts and initial-condition independence

**Evidence labels:** Current implementation, Scientific foundation, Open question.

**Conflict:** The current ND and KWSE hot-start paths supply a prior scenario's final depth raster to LISFLOOD-FP `startfile`.
They do not supply velocity, momentum, face flux, or a complete solver checkpoint through that path.
For ND, the source can be newly simulated, re-adopted, or reused and can be an accepted reference or a `reject_low` search position rather than the preceding selected library member.
The exact initialization of motion variables by the deployed executable has not been established by the reviewed current code evidence.
The KWSE hot-start schema accepts explicit `identity_hash: null`, but current job code passes null into source-address construction rather than resolving it to the current run identity described by the field contract.

**Source boundaries:** Current code establishes that the project supplies depth only and selects the ND source from the current adaptive search position.
The historical official LISFLOOD-FP manual establishes `startfile` as a depth input for its documented release but does not prove every initialization detail of the project's exact executable.
Hydraulic independence from the initial condition requires target-scenario comparison evidence rather than an interface description.

**Consequence:** A hot start from any of those source paths can reduce adjustment time while still producing a path-dependent target result or an early storage-convergence trigger.
Calling it a continuation of the complete prior dynamic state would overstate the current contract.
An explicit-null KWSE hot start also fails to locate the intended current-identity source despite passing schema validation.

**Resolution question:** How does the version-pinned project executable initialize motion variables when given `startfile`, which source positions are acceptable, and which cold-start or alternative-hot-start comparisons must pass before a target scenario is treated as independent of its initial condition?
Should explicit null identity be forbidden, or should it be resolved to the target run identity through validated model construction before source addressing?

**Status:** Open.

## CONF-011: Terrain source, datum, resampling, and structures

**Evidence labels:** Selected methodology, Current implementation, Scientific foundation, Evidence or experiment, Open question.

**Conflict:** DR-017 selects a 10 m model resolution and DR-018 selects USGS 3DEP, while current code provides environment-dependent DEM and EPSG fallbacks evaluated at process startup and callers can override the realized values.
The checked-in DEM fallback is the continually updated 1/3 arc-second seamless 3DEP VRT, and model identity hashes the realized source string rather than remote source content.
The current raster path horizontally reprojects the DEM to the requested grid without an explicit resampling argument, so Rasterio's nearest-neighbor default applies to continuous elevation.
The input and manifest contracts do not name or transform the vertical datum.
Input validation requires only a positive EPSG integer and does not verify a projected CRS with metre linear units.
Current geometry, bankfull width, buffers, slope length, per-unit-width discharge, squared cell area, stored volume, and flooded-area calculations assume metres unless explicit conversions are applied.
DR-010 selects no culvert terrain modification even though its own case table records rejected outcomes, and current job inputs provide no bridge or culvert structure representation.

**Source boundaries:** DR-010, DR-017, and DR-018 control selected methodology within their registered status and scope.
Current code controls source defaults, identity construction, reprojection, and available inputs.
USGS metadata controls the source product's horizontal and vertical reference and its bridge, culvert, and hydroflattening treatments.
ISU-011 is scoped issue evidence about changing results from the default VRT, not a completed root-cause determination.

**Consequence:** An unchanged model identity input can refer to changed remote terrain content, and a horizontal EPSG code can be mistaken for a complete vertical-datum contract.
It can also be mistaken for proof of metre-based projected coordinates, allowing a geographic or foot-based override to produce dimensionally wrong geometry, forcing, storage, and area values.
Nearest-neighbor sampling can alias continuous terrain, while absent structure inputs can leave false culvert barriers or omit bridge head loss and overtopping behavior.
A 10 m destination grid can also be mistaken for proof that every controlling terrain or structure feature is resolved.

**Resolution question:** Which immutable terrain source identity, vertical-datum field and transformation policy, explicit resampling rule, and terrain acceptance checks should the model contract require?
How should the model contract enforce projected metre units or carry explicit unit conversions through every affected calculation?
Which culvert, bridge, hydroflattening, and drainage-enforcement policies apply by reach class, and what evidence must pass before each model is accepted?

**Status:** Open.

## CONF-012: Roughness lookup validation and warning gaps

**Evidence labels:** Selected methodology, Current implementation, Scientific foundation, Open question.

**Conflict:** DR-019 and DR-020 select Annual NLCD-derived Manning roughness and a project lookup reproduced by checked-in input fallbacks.
The LULC source is an environment-dependent fallback evaluated at process startup and can be overridden by the caller, while `grid_resolution` has a 10-unit input default and the realized EPSG value can also differ from its checked-in environment fallback.
The checked-in LULC source fallback is the 30 m Annual NLCD 2023 Collection 1.0 mosaic.
Current code uses nearest-neighbor class reprojection and casts values to `uint8` before lookup conversion.
Negative, fractional, or out-of-range source values can truncate or wrap into valid codes, and only absent post-cast codes reliably become `-9999`.
Annual NLCD class 12 is absent from the checked-in lookup fallback.
The input contract does not limit lookup keys to 0 through 255, but keys outside that representable raster-code range cannot define distinct reachable codes through the cast input.
The generated job documentation describes a similar-roughness warning, but current job code leaves that check unimplemented.

**Source boundaries:** DR-019 and DR-020 control the selected source and lookup within their status and scope.
Annual NLCD documentation controls source classification and resolution claims.
Current code controls transformation, validation, fill values, and emitted warnings.
USACE guidance supports land-cover-based starting values and local refinement, but it does not make the project table universally calibrated.

**Consequence:** The output can contain a nonphysical negative roughness value while the build still completes without a specific warning, and pre-cast invalid values can alias to apparently valid coefficients.
The 10 m output can be mistaken for 10 m source information, and selected defaults can be mistaken for calibration evidence or portable truth.

**Resolution question:** Should the input contract require pre-cast source-code validation, lookup keys restricted to 0 through 255, complete post-cast class and nodata coverage, positive bounded values, an explicit categorical resampling rule, and post-conversion checks that fail on invalid values?
Which warning or error semantics should replace the current unimplemented marker, and what calibration, sensitivity, and transfer evidence is required for model acceptance?

**Status:** Open.

## CONF-013: Model identity, existing-model, and warning gaps

**Evidence labels:** Current implementation, Target design, Open question.

**Conflict:** Current model identity includes the baked methodology revision, target reach-geometry hash, grid resolution, horizontal EPSG code, DEM source-string hash, LULC source-string hash, and realized lookup hash.
It does not include immutable DEM or LULC source content, the reach-network path, caller-supplied topology, lake-outlet flag, inflow placement values, centerline-buffer multiplier, domain buffer, extra geometries, or authored domain.
Some omitted geometry changes can alter `domain_code`, but identical offsets do not prove identical boundary geometry or source content.

When a manifest exists at the predicted address and reconstructs inputs equal to the current request, the current job returns without verifying every asset, checksum, stored warning, or scientific acceptance condition.
When a present manifest is schema valid but its reconstructed inputs are unequal, the current job rebuilds.
Because omitted output-affecting inputs can change without changing identity or final domain code, that rebuild can target the same full address.
The job then copies six assets sequentially and copies the manifest last without directory-level staging, atomic promotion, rollback, or cleanup.
An interrupted publication can leave earlier copied assets behind, and a same-address rebuild can temporarily or persistently pair an older manifest with newly replaced assets when a later copy fails.
The generated job documentation says the existing-model check returns a warning and says a similar-roughness warning is checked, while current code implements neither warning.
The current warnings also do not report zero intended-channel intersection, an unmapped or negative roughness value, missing vertical datum, mutable source content, clipped floodplain, or required domain expansion.

**Source boundaries:** Current job code controls identity, existing-model, artifact, and warning behavior.
The checked-in reconciler code supplies target-oriented observed-storage verification, but shared status identifies it as a prototype and no handbook artifact proves deployed behavior.
Generated documentation is supporting evidence and is subordinate where code differs.

**Consequence:** Two scientifically different realizations can share an identity half of the address, a stale or incomplete directory can be returned as existing when its manifest alone passes the current input comparison, and an empty warning list can be mistaken for model QC.
Two unequal requests can also reuse the same full address, and non-transactional publication can expose a mixed-generation directory whose manifest and assets do not describe one completed build.
Mutable source content can change while source-string hashes remain unchanged.
A returned `model_dir` can be mistaken for independently observed materialization.

**Resolution question:** Which output-affecting inputs and immutable source identities must enter model identity, and which differences belong only in domain realization?
Should existing-model reuse verify all required assets and checksums and return explicit reuse provenance?
Should rebuild publication use generation-specific staging and atomic promotion or another protocol that prevents mixed manifest-asset generations at a reused address?
What cleanup and recovery contract applies after any individual copy fails?
Which documented warnings should be implemented as warnings, which conditions should be hard errors, and which scientific checks belong to a later validation component?
Which observer is authoritative for storage materialization in deployed operation?

**Status:** Open.

## CONF-014: Domain expansion and clipping evidence

**Evidence labels:** Selected methodology, Current implementation, Evidence or experiment, Open question.

**Conflict:** DR-012 ALT-G selects WSE-informed domain expansion with a maximum of 50 estimated bankfull widths from the initial domain.
Current `build_model` creates one initial rectangle and does not inspect scenario output, classify wet edges, expand the bbox, rebuild, or rerun a scenario.
Its large-domain warning addresses area greater than a provisional threshold and cannot show that a smaller domain is adequate.
Current scenario edge checks and persisted manifests also do not provide all activation, cell-level classification, connectivity, and boundary-role evidence needed to reconstruct why an edge was accepted or rejected.
When convergence and a disallowed edge violation are simultaneous, the persisted `volume_convergence` reason suppresses `edge_error` under current branch priority.

**Source boundaries:** DR-012 controls selected expansion methodology within its Alternate Selected status.
EXP-007, ISU-003, ISU-006, and cited cases are scoped evidence and hypotheses.
Current jobs code controls implemented domain construction, boundary preprocessing, edge checking, and persisted fields.

**Consequence:** A model can complete with connected inundation clipped by a closed edge, or water can be released through an incorrectly open edge, without the build manifest establishing the problem or the required expansion.
Absence of `edge_error` cannot distinguish a completed clean check from a sentinel, withheld check, allowed-edge setting, out-of-range WSE classification, or missing persisted detail.
It also cannot distinguish a clean check from a simultaneous edge violation suppressed by convergence priority.
Artifact presence can therefore be misread as proof of domain sufficiency.

**Resolution question:** Which component owns the DR-012 loop, which scenario defines the expansion test, which cell-level fields and connectivity evidence are persisted, how is expansion direction and distance selected, and how does each revision change model identity and orchestration dependencies?
What acceptance evidence distinguishes an intended outlet or transfer edge from an arbitrarily clipped connected floodplain?

**Status:** Open.

## CONF-015: Adaptive ND criteria and generated documentation

**Evidence labels:** Selected methodology, Current implementation, Open question.

**Conflict:** DR-030 ALT-C describes maximum and median stage at fixed monitoring points plus an extent criterion.
Its stated bands are 0.75 through 1.25 m for maximum stage, 0.25 through 0.75 m for median stage, and 7.5 through 12.5 percent for extent.
Current code instead calculates maximum and median depth over all strictly positive-depth cells in the final processed saved raster and flooded area from wet-cell count and squared raster resolution.
Current defaults are 0.75 through 1.25 m for maximum depth, 0.25 through 0.50 m for median depth, and 10 through 15 percent for flooded area.
When reference flooded area is zero, current measured comparison sets the percentage change to zero even when a trial has positive flooded area.
Proposal construction separately makes both relative area targets zero.
If later area points are positive, crossing logic can return the reference or lower endpoint of the first rising segment for both targets and can therefore create a degenerate or otherwise influential predicted window.
The generated ND documentation describes these current metric names but does not identify the quantity and default mismatch with DR-030.
It describes flat curves as a window opening at infinity, while current code represents no floor crossing as no window and sends the proposal directly to the maximum.
Its separate statement that a window is never empty applies only after a window exists and should not be read as eliminating the no-window branch.

**Source boundaries:** DR-030 controls selected methodology within its Alternate Selected status.
Current code controls the implemented metric population, zero-area behavior, defaults, crossings, proposal fallbacks, and verdicts.
Generated documentation is supporting contract material and is subordinate when it conflicts with code.

**Consequence:** A reader can mistake whole-grid wet-cell depth statistics for fixed-point stage statistics, apply the wrong median or area band, or assume a zero-reference-area comparison measured the trial's area response.
The same configuration name can therefore appear aligned while selecting scenarios under materially different hydraulic quantities and thresholds.
An operator can also misdiagnose a flat-curve maximum proposal if the no-window state is described as a literal infinite opening rather than the code's `None` fallback.

**Resolution question:** Which response quantities, spatial population, wet-cell threshold, zero-area rule, bands, and scale adjustments should authorized methodology require?
Should DR-030, code, schemas, generated documentation, or all of them change so selected and implemented criteria agree?
Which reference cases and sensitivity evidence will show that the resolved criteria capture important transitions without excessive library density across reach sizes and grid resolutions?

**Status:** Open.

## CONF-016: KWSE transfer, planning, and materialization contract

**Evidence labels:** Selected methodology, Current implementation, Target design, Open question.

**Conflict:** DR-025 and DR-026 select a downstream-FIM WSEL contour and one STL per run, while current post-processing creates a run-specific contour by masking nonpositive depth, adding depth to terrain, applying multiscale NaN-aware Gaussian smoothing, sampling smoothed WSE at the run's upstream centerline endpoint, extracting every contour at that level, mapping contour vertices to cell centers, and clipping the result by inundation.
DR-031 selects cell-specific transfer, and current preprocessing samples downstream depth plus terrain at coordinates selected by rasterizing the STL on the upstream grid.
It writes `HFIX` only when calculated WSE is positive, without separately requiring positive source depth.
Dry positive-terrain cells can therefore become transfer points, while wet cells at zero or negative WSE are omitted.
The transfer calculation adds raw arrays without an explicit nodata mask, so a nodata sentinel can be omitted or propagated because of its numeric value rather than recognized through a deliberate validity contract.
The nominal KWSE `bc_value` labels and addresses the target but does not set pointwise WSE.

Current `run_kwse_scenarios` executes an ordered caller-provided list and does not own network planning.
The checked-in reconciler contains unpinned prototype planning, source binding, hot-start chaining, submission, and materialization code, but it sits outside the three current jobs and does not prove deployment.
Its comments cite DR-042 through DR-045, which are absent from the handbook-reviewed Decision Register.
The prototype also treats terminal reaches as having no KWSE library while selected and unresolved lake or coastal records describe additional boundary and stage needs.

Current scenario publication copies assets and then the manifest without an atomic directory transaction or rollback.
Exact manifest-input reuse and returned manifest paths do not verify every referenced asset.
Both public ND and KWSE input models accept `save_zarr`, but neither job passes it into `RunConfig`, so those public inputs are ignored and cannot enable the optional branch in reviewed code.
The generic true branch creates a directory-backed Zarr store and then attempts file-only hashing, so current scenario-manifest construction cannot complete that branch.
The `.wd` to depth conversion assigns literal EPSG:5070 when solver output lacks CRS instead of recovering the realized model CRS.
For a non-5070 model, that fallback can mislabel depth, inundation, STL relationships, transfer inputs, and compositing inputs.
Current transfer preprocessing performs no explicit index-range check, so positive out-of-range indices raise while negative NumPy indices can wrap silently to the opposite raster edge.
It also does not verify depth-terrain-manifest shape, transform, bounds, CRS, horizontal units, vertical datum, nodata masks, or cell registration before raw array addition and sampling.
The KWSE hot-start schema accepts explicit null identity, but current job code does not resolve it to the current run identity before source addressing.
Lossy one-decimal KWSE and one-decimal scientific-notation ND boundary names can direct distinct raw scenarios to the same publication address.
No current `twod-fim-jobs` entry point performs cross-reach compositing or Flows2FIM production.
The unpinned current local `twod-fim-deployment/orchestrator/scripts/f2f.py` path exports materialized scenario records and grids, invokes Flows2FIM in Docker, requests VRT output, and rewrites every VRT band to use `PixelFunctionType` value `max`.
That checked-in path does not establish deployed operation, complete composite provenance, scenario compatibility, grid or datum compatibility, validation, or scientific acceptance.

**Source boundaries:** Registered Decision Register status controls selected methodology.
Current jobs code controls exact execution and transfer behavior.
The unpinned reconciler checkout supplies prototype controller evidence only.
System-design documents supply target ownership and propagation intent rather than verified deployment.

**Consequence:** A nominal scenario can be mistaken for a uniform-stage boundary, dry source terrain can be forced as water, a negative source index can silently sample the opposite edge, incongruent rasters can be added by array position, transfer geometry can be treated as exact despite smoothing and contour uncertainty, and a source line can appear to overlap while extending beyond valid raster coverage or using incompatible units or datum.
Prototype planner formulas can be granted decision authority they do not have, waterbody gaps can be hidden by terminal scheduling, and a returned or manifest-present scenario can be treated as complete despite a missing depth, inundation, or STL asset.
An accepted Zarr request can produce no artifact, a generic Zarr request cannot complete its manifest, a non-5070 scenario can receive the wrong CRS label, explicit-null hot-start identity can fail after validation, and distinct boundary values can overwrite or mix at one rounded address.
The checked-in local maximum-VRT path can also be mistaken for verified production or validated composite output.

**Resolution question:** What authorized index-range, shape, transform, bounds, registration, wet-cell, nodata, nonpositive-WSE, interpolation, CRS, horizontal-unit, vertical-datum, source-coverage, and STL-construction contract should govern transfer?
Which registered decisions authorize every discharge-stage bound and planner formula, and how should terminal, lake, coast, and confluence cases be planned?
Which versioned plan artifact records targets, sources, skips, and hot-start chains?
Which publication protocol and observer verify every required asset and checksum before materialization?
How will public Zarr inputs be forwarded or removed, how will directory stores receive deterministic digests, how will realized model CRS propagate into scenario products, how will explicit-null identity be resolved or forbidden, and how will scenario addresses distinguish raw boundary values without collision?
Which deployed producing component, image digest, source manifest, compatibility evidence, product manifest, and validation record prove an operationally and scientifically acceptable Flows2FIM composite?

**Status:** Open.

## CONF-017: Intended-use validation and acceptance policy

**Evidence labels:** Scientific foundation, Selected methodology, Current implementation, Evidence or experiment, Target design, Open question.

**Conflict:** The reviewed project sources contain selected methods, implemented checks, issue signatures, case observations, experiment plans and results, and target ownership contracts, but they do not provide one authorized intended-use acceptance policy for each model, scenario library, transfer dependency, and composite product.
The sources do not define a complete set of required software-verification, numerical-verification, hydraulic-validation, calibration-separation, benchmark-comparison, uncertainty, materialization, monitoring, and acceptance criteria with responsible authorities and permitted uses.
Current solver exit, final storage-change ratio, manifest input equality, artifact references, warning lists, edge behavior, and returned paths each cover only part of that evidence.
Case-018 records bounded reach-composite comparison evidence under the EXP-013 heading and bounded convergence-metric evidence under the EXP-014 heading.
The standalone EXP-013 and EXP-014 files contain descriptions and methodologies, while EXP-012 describes a calibration and topobathymetry experiment plan.
Those records do not establish universal pass thresholds or validate all reaches, regimes, source data, transfers, or products.

**Source boundaries:** NASA, NIST, and EPA sources define general verification, validation, calibration, uncertainty, and intended-use evaluation concepts.
The Decision Register controls selected project methodology within each record's status and scope.
Current code controls implemented checks and artifacts.
Cases, issues, and experiments retain their bounded evidence scope.
Target design controls intended ownership and observation contracts rather than deployed or scientifically accepted operation.

**Consequence:** A reviewer can mistake completion, artifact presence, exact-input reuse, a warning-free result, one convergence proxy, visual plausibility, or one benchmark for hydraulic adequacy.
Different reviewers can also apply unstated thresholds after seeing results, combine calibration and validation evidence, or accept a scientifically plausible scenario whose required assets are not materialized.
Operational monitoring cannot detect deviation from a policy that has not been authorized and recorded.

**Resolution question:** For each intended product use, which authority defines and approves the required verification, numerical, hydraulic, benchmark, uncertainty, sensitivity, materialization, and monitoring evidence?
Which quantities, spatial and temporal supports, flow and stage ranges, datums, metrics, thresholds, uncertainty allowances, independent data, calibration boundaries, failure responses, and permissible uses belong in the acceptance record?
Which project cases will establish transfer across reach classes, boundaries, structures, data qualities, and scenario regimes without treating one benchmark as universal proof?

**Status:** Open.

## CONF-018: Sensitivity priorities and uncertainty propagation

**Evidence labels:** Scientific foundation, Selected methodology, Current implementation, Evidence or experiment, Open question.

**Conflict:** The handbook can identify forcing, terrain, bathymetry, roughness, network, domain, boundary, parameter, numerical, initial-condition or hot-start, structural-model, solver, scenario-sampling, identity and reuse, publication and materialization, composite, and operational uncertainty.
The reviewed project evidence does not estimate comparable sensitivity or uncertainty distributions across representative reaches, scenarios, source qualities, solver versions, and product uses.
Project cases provide bounded results for some boundaries, domains, terrain, bathymetry, inflow geometries, composites, convergence metrics, and normal-depth slope behavior.
The standalone experiment records supply methods, while the issue records supply symptoms.
No reviewed source justifies treating these sources as independent additive error bars or establishes a universal ranking.

**Source boundaries:** NASA, NIST, EPA, and USACE sources provide general sensitivity, uncertainty, verification, validation, and model-evaluation guidance.
The Decision Register controls selected methods within each status and scope.
Current code controls implemented factors and recorded outputs.
Case-018 contains the completed evidence conducted under the EXP-013 and EXP-014 headings, while the standalone experiment files remain method-only.
No source establishes a project-wide uncertainty allowance or representative interaction model.

**Consequence:** A reviewer can apply a local result as a universal ranking, vary parameters while leaving a dominant boundary or terrain error uncontrolled, double-count correlated uncertainties, or miss an interaction that changes hydraulic control.
Contract failures involving datum, CRS units, transfer registration, identity, source version, reuse, hot starts, publication, or materialization can also be omitted because they do not fit one physical-parameter ranking.

**Resolution question:** Which intended uses, reach classes, scenario ranges, factors, plausible alternatives, controlled variables, outputs, metrics, interaction designs, and replication plans should form the authorized project sensitivity program?
Which evidence supports changing the current review heuristic that begins with downstream condition and domain, then terrain and topobathymetry, roughness and resolution, inflow distribution, wet-dry settings, and convergence controls?
Which authority approves uncertainty combination assumptions, decision thresholds, and transfer limits?

**Status:** Open.

## CONF-019: Methodology-change identity and migration ownership

**Evidence labels:** Selected methodology, Current implementation, Target design, Evidence or experiment, Open question.

**Conflict:** Scientific settings such as `volume_convergence_tolerance` can be supplied through current public job inputs and are stored in complete scenario inputs, but the current run identity and scenario address omit the tolerance and most other run settings.
Exact input equality can reject an unequal stored manifest at the predicted address, after which a new run can publish sequentially to that same address.
Separately, the ND caller-supplied `existing_scenarios` path can adopt an old-tolerance manifest after checking only reach ID, full model ID, run identity hash, and inclusive discharge range.
That limited predicate does not compare convergence tolerance, other run settings, boundary geometry or slope, hot start, or referenced-asset existence before the manifest enters adaptive state.
The current publication path has no generation staging, atomic directory promotion, rollback, or cleanup that preserves the prior complete generation.
The target system-design guide says an invalidating identity change should create another object and uses identity for grouping, rollback, deletion, and invalidation.
The reviewed sources do not provide one authorized rule that classifies every scientific setting as identity, realization, provenance, or acceptance state.
They also do not assign complete ownership for methodology migration, mixed-generation compatibility, selected-library transition, network dependency propagation, composite regeneration, promotion, or rollback.

**Source boundaries:** The Decision Register controls selected methodology within each record's status and scope.
Current jobs code controls current identity, exact reuse, manifest, and publication behavior.
The system-design guide controls target identity and materialization principles rather than deployed behavior.
Case and experiment evidence can inform a change but cannot authorize identity, migration, or rollback policy.
Operational evidence would be required to establish that a same-address overwrite or mixed generation occurred in a particular environment.

**Consequence:** A scientifically material change can be accepted by the schema without moving the object address.
An old-tolerance ND manifest can be re-adopted directly and influence the minimum reference, proposal or re-judgment calculations, current search position, hot-start provenance, or selected-membership reasoning under new-tolerance intent.
The old and new requests can also fail exact target reuse yet still collide during publication, preventing reproducible rollback and allowing mixed methodology generations in selected libraries, stage-transfer dependencies, or composites.
A controller setting can be restored while incompatible or overwritten artifacts remain.
Without named decision, acceptance, implementation, operational, migration, and rollback owners, no role has clear authority to approve, halt, promote, repair, or reverse the change.

**Resolution question:** Which output-affecting scientific settings belong in model or run identity, which belong in a collision-safe realization or immutable generation key, and which can remain provenance-only under an authorized compatibility rule?
Should limited ND caller-supplied adoption require complete input equality, or which authorized compatibility and asset-observation predicate can safely admit a prior scenario into current adaptive state?
Which versioned contract prevents same-address replacement, promotes complete generations atomically, verifies assets and checksums before materialization, and makes equivalent retries idempotent?
Which inventory and dependency record governs recomputation or revalidation of old models, ND and KWSE scenarios, selected memberships, transfers, composites, and exports?
Which roles own the methodology decision, acceptance criteria, implementation, migration, rollout, halt, rollback, and residual-risk acceptance?

**Status:** Open.

## CONF-020: Scenario request-boundary validation

**Evidence labels:** Current implementation, Open question.

**Conflict:** The current ND request model requires positive `min_upstream_inflow` and `delta_upstream_inflow`, but `max_upstream_inflow` has no positivity constraint and is not required to be at least the minimum.
Its convergence tolerance, simulation length, save interval, and wall-time fields have no positive or finite bounds.
Its response-band validators enforce only a minimum span, so negative endpoints can pass when the span is large enough.
The current KWSE request model permits an empty `scenarios` list and repeats the unbounded convergence and time controls.
The KWSE runner processes zero iterations for an empty list and returns `manifests=[]` with `warnings=[]` without stating that no scenario ran or materialized.

**Source boundaries:** Current input models and job code control accepted request shapes and the resulting branches in the reviewed checkout.
The reviewed Decision Register does not authorize these missing bounds or an empty-work success contract.
No runtime evidence reviewed for this handbook establishes that an invalid request occurred operationally.

**Consequence:** A schema-valid ND request can place a nonpositive or below-minimum maximum into adaptive selection and later `QFIX` construction.
A nonpositive convergence tolerance can disable the strict-less-than convergence trigger, while nonpositive time or save controls can force immediate termination or invalid solver-output behavior.
Negative response bands can direct adaptive selection toward scientifically invalid comparison targets.
An empty KWSE request can return an apparent successful response without a scenario, manifest, warning, or materialized artifact.

**Resolution question:** Which owner defines and enforces the scenario request contract?
At minimum, should ND reject a nonpositive maximum and a maximum below the minimum, require finite positive convergence and time controls, and require finite nonnegative response-band endpoints in addition to the existing span rules?
Should KWSE require at least one scenario and the same finite positive convergence and time controls?
Which tests, user-facing errors, warnings, generated documentation, and migration checks prove that callers cannot mistake rejected or empty work for a completed scientific scenario set?

**Status:** Open.
