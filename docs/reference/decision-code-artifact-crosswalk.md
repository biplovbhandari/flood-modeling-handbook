# Method-Evidence-Artifact Crosswalk

This crosswalk connects hydraulic and scientific-software methods to synthetic inputs, expected behavior, evidence, failure modes, and review questions.
Every applied-example value is constructed for this handbook and makes no claim about an external implementation.
The `MX-*` identifiers support internal handbook navigation only and do not confer scientific, operational, or decision authority.

## MX-001: Boundary conditions

**Method or design principle:**
Define each boundary by its physical quantity, mathematical or solver behavior, geometry, sign convention, units, reference, and applicable scenario family.

**Applied-example input:**
The `R-200` discharge-only scenario applies a total 250 m3/s as 148 m3/s through the `R-100` inflow line, 90 m3/s through the `R-300` inflow line, and 12 m3/s through a named local contribution.
It uses a terminal normal-depth outflow with a stated slope of 0.0015 m/m.
The `R-100` downstream-stage-aware scenario instead uses a 102.5 m nominal target in datum `VD-1` and a transferred spatial stage field.

**Applied-example behavior:**
The discharge-only scenario distributes the full stated inflow and allows water to leave only through the intended outflow cells.
The downstream-stage-aware scenario preserves the nominal label separately from the spatial transferred values.

**Artifact or evidence:**
A boundary record identifies every boundary role, selected cell or face, geometry, units, reference, realized value, source identity, and time support.
Flux histories, edge checks, and the scenario record show what the realized boundary did.

**Failure modes:**
A label can disagree with behavior, a datum can be incompatible, selected cells can miss conveyance, or a domain edge can clip connected flow.
See [CQ-001](conflicts-and-open-questions.md#cq-001-terminology-behavior-mismatch), [CQ-003](conflicts-and-open-questions.md#cq-003-incompatible-datums), and [CQ-004](conflicts-and-open-questions.md#cq-004-domain-clipping).

**Validation question:**
Do the recorded quantity, geometry, reference, sign, units, and realized fluxes establish the intended hydraulic boundary over the complete scenario range?

**Public scientific sources:**
[SCI-022](bibliography.md#sci-022-hec-ras-flow-regime-boundary-guidance) through [SCI-026](bibliography.md#sci-026-hec-ras-2d-external-boundary-conditions) support flow-regime, normal-depth, downstream-control, and two-dimensional boundary concepts.
[SCI-027](bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics) supports the depth-averaged hydraulic context.

## MX-002: Convergence

**Method or design principle:**
Treat storage change, local transients, full mass balance, process exit, boundary behavior, and hydraulic acceptance as separate evidence lanes.

**Applied-example input:**
A synthetic square-grid scenario uses 20 m cells, 50 m3/s inflow, 900 s saved-output spacing, and positive-depth sums of 328.5000 m and 328.5900 m.
Its instructional rule requires a storage-change ratio below \(10^{-3}\) for three consecutive saved intervals.

**Applied-example behavior:**
The first supplied interval gives \(C_V=0.0008\), which satisfies the ratio threshold for one interval but does not satisfy the three-interval rule by itself.
An unintended wet-edge result and the full inflow-outflow balance are recorded independently.

**Artifact or evidence:**
The evidence packet contains saved-output times, the complete ratio history, storage calculations, boundary fluxes, source and sink volumes, local hydraulic histories, termination events, edge checks, and final artifacts.

**Failure modes:**
A final ratio can hide oscillation, local change, compensating storage errors, missing outflow, a simultaneous edge failure, or an incomplete process record.
See [CQ-005](conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence).

**Validation question:**
Does the complete evidence show that every stated quasi-steady, conservation, stability, and intended-use criterion is satisfied without substituting one criterion for another?

**Public scientific sources:**
[SCI-016](bibliography.md#sci-016-hec-ras-continuity-equation), [SCI-027](bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics), and [SCI-029](bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance) support continuity, two-dimensional flow, and space-time evidence.
[SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) support verification, validation, uncertainty, and acceptance boundaries.

## MX-003: Discharge selection

**Method or design principle:**
Select discharge bounds and adaptive candidates through an explicit hydrologic basis, fixed candidate grid, response metric, deterministic tie rules, and stated stopping rule.

**Applied-example input:**
The synthetic `R-200` candidate grid is 100, 125, 150, 175, 200, 225, and 250 m3/s.
The interval-refinement method uses maximum depth, flooded area, scales of 0.50 m and 0.10 km2, and endpoint scenarios at 100 and 250 m3/s.
For adjacent accepted scenarios, \(D(a,b)\) is the maximum of the absolute maximum-depth change divided by 0.50 m and the absolute flooded-area change divided by 0.10 km2.

| Discharge | Maximum depth | Flooded area | Measurement order | Warm-start source |
| ---: | ---: | ---: | ---: | --- |
| 100 m3/s | 1.00 m | 0.500 km2 | 1 | Dry start |
| 250 m3/s | 2.30 m | 0.750 km2 | 2 | 100 m3/s |
| 175 m3/s | 1.60 m | 0.595 km2 | 3 | 100 m3/s |
| 125 m3/s | 1.20 m | 0.530 km2 | 4 | 100 m3/s |
| 200 m3/s | 1.78 m | 0.635 km2 | 5 | 175 m3/s |
| 225 m3/s | 2.01 m | 0.680 km2 | 6 | 200 m3/s |

**Applied-example behavior:**
The endpoint distance is \(D(100,250)=\max(1.30/0.50,0.250/0.10)=2.60\), and the exact midpoint selects 175 m3/s.
The next distances are \(D(100,175)=\max(0.60/0.50,0.095/0.10)=1.20\) and \(D(175,250)=\max(0.70/0.50,0.155/0.10)=1.55\).
Both intervals are 75 m3/s wide, so the lower-endpoint tie rule selects the first interval.
Its midpoint is 137.5 m3/s, and 125 and 150 m3/s are each 12.5 m3/s away, so the lower-candidate tie rule selects 125 m3/s.
The resulting distances are \(D(100,125)=\max(0.20/0.50,0.030/0.10)=0.40\) and \(D(125,175)=\max(0.40/0.50,0.065/0.10)=0.80\).
The 175 to 250 m3/s interval remains at 1.55, its midpoint is 212.5 m3/s, and 200 and 225 m3/s are each 12.5 m3/s away, so the lower-candidate tie rule selects 200 m3/s.
The new distances are \(D(175,200)=\max(0.18/0.50,0.040/0.10)=0.40\) and \(D(200,250)=\max(0.52/0.50,0.115/0.10)=1.15\).
The only remaining eligible interval has the exact midpoint 225 m3/s, so 225 m3/s is measured sixth.
Its final distances are \(D(200,225)=\max(0.23/0.50,0.045/0.10)=0.46\) and \(D(225,250)=\max(0.29/0.50,0.070/0.10)=0.70\).
The final adjacent distances are 0.40, 0.80, 0.40, 0.46, and 0.70, so every interval meets \(D\le1\), no unresolved grid residual remains, and the method stops.
The selected set is 100, 125, 175, 200, 225, and 250 m3/s, while 150 m3/s remains untried inside the accepted 125 to 175 m3/s interval.

**Artifact or evidence:**
A refinement record preserves the candidate grid, bounds, metric definitions, measured responses, interval distances, midpoint and tie decisions, warm-start sources, rejected trials, final membership, and unresolved grid residuals.

**Failure modes:**
An undefined bound, off-grid candidate, repeated candidate, rejected scenario used as a reference, missing tie rule, or implicit library membership can make selection irreproducible.
Missing provenance for the hydrologic estimates also prevents interpretation.

**Validation question:**
Can an independent reader reproduce every candidate choice and explain why the final selected set covers the stated response criterion for the stated grid and intended use?

**Public scientific sources:**
[SCI-009](bibliography.md#sci-009-bulletin-17c) through [SCI-014](bibliography.md#sci-014-nonstationary-flood-frequency-analysis) support frequency estimates, source limitations, and nonstationarity.
[SCI-015](bibliography.md#sci-015-hydraulic-model-sensitivity) and [SCI-029](bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance) support response sensitivity and numerical-resolution caution.

## MX-004: Stage transfer

**Method or design principle:**
Transfer a spatial water-surface field from compatible downstream evidence while keeping the nominal planning stage separate from realized point values.

**Applied-example input:**
The upstream `R-100` and `R-300` families use nominal targets of 102.0, 102.5, and 103.0 m in datum `VD-1`.
The interpolation coordinate is the scalar transfer-support stage at the confluence in `VD-1`.
The 175 m3/s `R-200` source has coordinate 102.4 m, the 200 m3/s source has coordinate 102.7 m, and the target coordinate is 102.5 m.
The upper-source weight is \(w=(102.5-102.4)/(102.7-102.4)=1/3\), so the lower-source weight is \(1-w=2/3\).
The method uses common wet support and a 90 percent minimum coverage rule.

**Applied-example behavior:**
At each mapped point \(i\), source WSE is calculated as \(\eta_{175,i}=h_{175,i}+z_{175,i}\) and \(\eta_{200,i}=h_{200,i}+z_{200,i}\), where each depth is a vertical distance above its represented source terrain and the terrain elevations use compatible `VD-1` metadata.
The target field is \(\eta_i^*=(2/3)\eta_{175,i}+(1/3)\eta_{200,i}\) on points where both source depths and terrain values are valid and both depths are strictly positive.
Coverage is the common-wet point count divided by the intended-interface point count.
No value is emitted outside common wet support, and the scenario is rejected when coverage is below 90 percent.

**Artifact or evidence:**
The transfer packet contains both source scenario identities, source-stage coordinates 102.4 and 102.7 m, target coordinate 102.5 m, weights \(2/3\) and \(1/3\), terrain-elevation and depth artifact identities, transforms, bounds, elevation datum metadata, depth units and definitions, source masks, transfer geometry, coordinate mappings, common-wet mask, intended interface, coverage, omitted points and reasons, and the realized boundary field.

**Failure modes:**
Incompatible datums, mismatched raster support, negative-index wrapping, dry cells treated as wet, nominal labels substituted for spatial fields, or incomplete coverage can create unsupported boundary values.
See [CQ-003](conflicts-and-open-questions.md#cq-003-incompatible-datums) and [CQ-008](conflicts-and-open-questions.md#cq-008-missing-provenance).

**Validation question:**
Can every transferred point be reconstructed from compatible source evidence, and does the coverage and datum evidence support the upstream use?

**Public scientific sources:**
[SCI-024](bibliography.md#sci-024-hec-ras-downstream-boundary-conditions), [SCI-026](bibliography.md#sci-026-hec-ras-2d-external-boundary-conditions), and [SCI-027](bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics) support downstream control and two-dimensional water-surface behavior.
[SCI-030](bibliography.md#sci-030-gdal-geotransform) and [SCI-042](bibliography.md#sci-042-rasterio-reprojection-and-resampling) support coordinate and raster mapping.

## MX-005: Model development

**Method or design principle:**
Separate requested inputs, resolved sources, realized settings, model identity, artifacts, warnings, publication, storage observation, and scientific acceptance.

**Applied-example input:**
The synthetic `R-200` model uses prepared network `N-1`, terrain content `T-200-A`, roughness content `M-200-A`, 10 m square cells, datum `VD-1`, separate `R-100` and `R-300` inflow lines, and a computed domain.

**Applied-example behavior:**
The model-building operation validates the input boundary, resolves immutable sources, realizes roughness, constructs geometry and grid, computes complete identity, produces typed artifacts, publishes one generation, and then observes storage.

**Artifact or evidence:**
The model record contains the canonical request, realized settings, complete identity, source lineage, domain and grid properties, artifact roles and integrity values, structured warnings, producer version, and generation state.

**Failure modes:**
Mutable source addresses, silent defaults, incomplete identity, unknown fields, partial publication, missing artifacts, or warnings interpreted beyond their implemented checks can create a model whose meaning cannot be established.

**Validation question:**
Does the observed model generation contain every required artifact and enough realized provenance to support the stated scenario work without inferring hidden defaults?

**Public scientific sources:**
[SCI-030](bibliography.md#sci-030-gdal-geotransform) and [SCI-035](bibliography.md#sci-035-usgs-3dep-one-third-arc-second-dem) through [SCI-042](bibliography.md#sci-042-rasterio-reprojection-and-resampling) support terrain, grids, land cover, structures, and raster handling.
[SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) support traceability and intended-use evidence.

## MX-006: Roughness

**Method or design principle:**
Convert categorical land cover to Manning roughness through a complete, versioned, unit-aware lookup with explicit nodata, override, and calibration rules.

**Applied-example input:**
The `R-200` model uses synthetic land-cover content `M-200-A`, a complete category-to-roughness lookup, 10 m hydraulic cells, and separately identified local override regions.

**Applied-example behavior:**
Categorical values are mapped without averaging class codes, every realized class is validated, and overrides follow a recorded priority order.
Sensitivity runs vary roughness without changing terrain, boundaries, grid, or forcing.

**Artifact or evidence:**
The evidence set contains source classification and date, source and target grids, categorical resampling method, lookup version and values, unmapped-class counts, override provenance, realized roughness raster, and sensitivity results.

**Failure modes:**
Continuous resampling of categories, unmapped classes, silent nodata conversion, unrecorded overrides, or calibration that compensates for terrain and boundary error can make roughness uninterpretable.

**Validation question:**
Can every realized roughness value be traced to a valid class or override, and do sensitivity and calibration evidence support the intended use without masking other errors?

**Public scientific sources:**
[SCI-023](bibliography.md#sci-023-hec-ras-uniform-flow-computations), [SCI-039](bibliography.md#sci-039-annual-nlcd-collection-1-user-guide), and [SCI-040](bibliography.md#sci-040-usace-land-cover-and-mannings-n-guidance) support roughness meaning, land-cover scope, mappings, overrides, and calibration context.

## MX-007: Domain adequacy

**Method or design principle:**
Treat domain extent, active mask, grid snapping, boundary geometry, and hydraulic containment as one explicit model contract.

**Applied-example input:**
The synthetic `R-200` model uses a 10 m grid and a computed rectangle from 21,020 to 22,640 m east and 48,100 to 49,620 m north in a projected metre-based reference.
The required geometry includes two upstream inflow lines, a terminal outflow region, and the connected floodplain needed by the largest planned scenario.

**Applied-example behavior:**
Construction bounds are buffered and snapped outward to full cells.
Scenario evidence then checks intended boundaries, connected wet areas, depth and WSE gradients, alternate pathways, and proximity to unintended edges.

**Artifact or evidence:**
The domain record contains the construction geometries, buffer values, unsnapped and snapped bounds, grid transform, dimensions, active mask, selected boundary cells, edge diagnostics, and expansion or rejection decisions.

**Failure modes:**
A rectangle can contain the centerline while clipping floodplain storage, backwater, a side channel, or an outflow path.
See [CQ-004](conflicts-and-open-questions.md#cq-004-domain-clipping).

**Validation question:**
Does evidence across the full planned scenario range show that the domain contains relevant storage and pathways without relying only on a centerline buffer or one plausible map?

**Public scientific sources:**
[SCI-028](bibliography.md#sci-028-hec-ras-2d-computational-mesh) through [SCI-030](bibliography.md#sci-030-gdal-geotransform) support mesh, time-step, terrain, and grid concepts.
[SCI-035](bibliography.md#sci-035-usgs-3dep-one-third-arc-second-dem) through [SCI-041](bibliography.md#sci-041-usace-bridges-in-2d-flow-areas) support terrain, connectivity, land cover, and structure considerations.

## MX-008: Identity

**Method or design principle:**
Identity must change whenever an output-affecting scientific input or method changes, while realization metadata records where and when that identity was produced.

**Applied-example input:**
The `R-200` scenario identity includes the complete model identity, 200 m3/s total forcing, both named upstream allocations, local contribution and geometry, downstream condition, initial state, numerical settings, method version, and producer build.

**Applied-example behavior:**
A canonical identity object is serialized deterministically and assigned a full digest.
Readable labels can accompany the digest but cannot replace the complete object.

**Artifact or evidence:**
The scenario record stores the canonical identity object, digest algorithm and full digest, parent identities, realized settings, generation identifier, artifact integrity values, and acceptance link.

**Failure modes:**
Omitted topology, source bytes, boundary geometry, datum, method version, or run settings can make unequal scientific objects appear identical.
See [CQ-002](conflicts-and-open-questions.md#cq-002-incomplete-identity).

**Validation question:**
Do controlled changes to every output-affecting field create distinct identity while repeated equivalent requests produce the same canonical identity?

**Public scientific sources:**
[SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) support data pedigree, traceability, configuration control, and evidence proportional to intended use.

## MX-009: Reuse

**Method or design principle:**
Reuse an existing generation only after complete identity, required artifacts, integrity, compatibility, selected membership, and applicable acceptance state have been observed.

**Applied-example input:**
A new `R-200` request asks for the same 200 m3/s scenario identity as an existing accepted generation.
The stored scenario record is present, but one required depth artifact has not yet been checked.

**Applied-example behavior:**
The observer withholds reuse until it verifies the record, every required artifact, integrity values, generation consistency, source membership, and current intent.
If any check fails, the operation rejects reuse and does not overwrite the existing generation.

**Artifact or evidence:**
A reuse decision record lists the requested identity, observed generation, checks performed, integrity results, compatibility result, membership and acceptance state, and final reuse or rejection decision.

**Failure modes:**
Folder presence, a readable label, partial input equality, or a schema-valid record can accept missing, stale, mixed, rejected, or incompatible output.
See [CQ-006](conflicts-and-open-questions.md#cq-006-unsafe-reuse).

**Validation question:**
Would the reuse decision remain correct if an artifact were deleted, replaced, superseded, or removed from the selected library after the record was written?

**Public scientific sources:**
[SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) support traceability, configuration management, and intended-use evidence.

## MX-010: Publication

**Method or design principle:**
Stage and verify one complete immutable generation before making it visible through an atomic promotion or equivalent fail-closed protocol.

**Applied-example input:**
A synthetic `R-100` scenario produces a scenario record, final depth, inundation geometry, transfer-support geometry, diagnostics, and integrity metadata.

**Applied-example behavior:**
The producer writes all outputs to an isolated generation, verifies the required set, and then promotes one generation reference.
A failure before promotion leaves the previous complete generation visible and the incomplete staging content unavailable to ordinary consumers.

**Artifact or evidence:**
Publication evidence contains the staging generation, required artifact inventory, integrity results, promotion event, final generation reference, and failure or rollback record.

**Failure modes:**
Sequential writes to a live address can expose a partial or mixed generation, and writing the record last detects but does not prevent every partial overwrite.
See [CQ-007](conflicts-and-open-questions.md#cq-007-partial-publication).

**Validation question:**
Can a consumer observe any combination other than the complete previous generation or the complete promoted generation during failure and retry?

**Public scientific sources:**
[SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) support controlled evidence, configuration, provenance, and result reporting.
The atomic publication protocol is a handbook design principle rather than a claim made by those scientific sources.

## MX-011: Materialization

**Method or design principle:**
Materialization is an observer conclusion about a specific intended generation, not an inference from execution success, a returned address, or storage presence.

**Applied-example input:**
The synthetic plan requires one exact `R-300` scenario generation with a record, depth raster, inundation geometry, transfer-support geometry, diagnostics, and matching identity and integrity metadata.

**Applied-example behavior:**
An independent observer derives the intended identity, reads final storage, verifies the generation and every required artifact, and records materialization separately from scientific acceptance.

**Artifact or evidence:**
The materialization record contains intended identity, observed generation, observation time, required roles, existence and integrity results, reference and unit checks, plan-membership result, and any retraction trigger.

**Failure modes:**
A returned path, present record, stale pointer, missing asset, mixed generation, or incompatible metadata can create a false completion signal.
Missing provenance can also prevent the observer from deciding what was materialized.
See [CQ-007](conflicts-and-open-questions.md#cq-007-partial-publication) and [CQ-008](conflicts-and-open-questions.md#cq-008-missing-provenance).

**Validation question:**
Does the observation prove that the exact generation required by current intent exists and satisfies the complete observation contract, while leaving scientific acceptance as a separate decision?

**Public scientific sources:**
[SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) support traceability, data pedigree, result reporting, and intended-use boundaries.
The materialization distinction is a handbook design principle.

## MX-012: Compositing

**Method or design principle:**
Select scientifically compatible source scenarios before applying a stated overlap rule, and give the composite its own identity, provenance, diagnostics, and acceptance state.

**Applied-example input:**
A candidate packet contains `R-100` at 100 m3/s, `R-300` at 60 m3/s, and a legacy `R-200` candidate at 150 m3/s.
The upstream candidates use a 102.5 m transferred field derived from `R-200` sources at 175 and 200 m3/s, while the legacy downstream candidate reports 102.2 m at 150 m3/s.

**Applied-example behavior:**
The compatibility audit rejects the packet before pixelwise maximum because the upstream total is 160 m3/s before local inflow and the downstream flow and stage-source realization do not match.
A corrected compatible packet would combine depth as \(h_{comp}(x,y)=\max_r h_r(x,y)\).

**Artifact or evidence:**
The composite packet contains explicit source membership, joint hydrologic condition, source identities and observed artifacts, output grid, reference and datum checks, transformations, nodata and overlap rules, output integrity, diagnostics, and acceptance.

**Failure modes:**
Maximum can combine incompatible quantities, datums, generations, discharges, stage sources, grids, or obsolete trials into a deterministic but meaningless raster.
See [CQ-003](conflicts-and-open-questions.md#cq-003-incompatible-datums), [CQ-006](conflicts-and-open-questions.md#cq-006-unsafe-reuse), and [CQ-009](conflicts-and-open-questions.md#cq-009-validation-gaps).

**Validation question:**
Do source selection and compatibility evidence support the stated joint condition before the overlap calculation is applied?

**Public scientific sources:**
[SCI-030](bibliography.md#sci-030-gdal-geotransform) and [SCI-042](bibliography.md#sci-042-rasterio-reprojection-and-resampling) support raster transforms and resampling.
[SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) support validation, uncertainty, provenance, and intended-use limits.

## MX-013: Validation

**Method or design principle:**
Validate stated model quantities against comparable independent referents under predeclared metrics, uncertainty treatment, applicability limits, and acceptance criteria.

**Applied-example input:**
A synthetic `R-200` comparison supplies modeled and reference water-surface elevations at declared locations, one vertical datum, stated observation support, independent calibration status, and a predeclared error metric.

**Applied-example behavior:**
The calculation reports residuals and uncertainty without extending the result beyond the sampled locations, discharges, model generation, or intended use.
Software verification, numerical verification, calibration, plausibility, monitoring, and validation remain distinct records.

**Artifact or evidence:**
The validation packet contains the intended use, model and reference identities, sampling design, quantities, units, datums, spatial and time support, calibration separation, raw comparisons, metrics, uncertainty, exclusions, criteria, and decision.

**Failure modes:**
A favorable summary without raw support, incompatible datum, reused calibration data, unclear reference quality, small sample, or post hoc criterion cannot establish validation.
See [CQ-003](conflicts-and-open-questions.md#cq-003-incompatible-datums) and [CQ-009](conflicts-and-open-questions.md#cq-009-validation-gaps).

**Validation question:**
Does the evidence support the stated intended use within a bounded domain of validity, and are unsupported transfers and prohibited uses explicit?

**Public scientific sources:**
[SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations), [SCI-044](bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability), and [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) define verification, validation, calibration, uncertainty, reliability, and intended-use evidence.

## MX-014: Sensitivity

**Method or design principle:**
Vary scientifically material factors over defensible ranges while holding other inputs controlled, and test interactions when separate one-factor changes cannot answer the decision question.

**Applied-example input:**
The synthetic `R-200` baseline is compared with roughness, grid, downstream-boundary, domain, wetting, and initial-state alternatives under fixed forcing and recorded identities.

**Applied-example behavior:**
Each comparison reports changes in maximum depth, flooded area, selected locations, storage and balance diagnostics, runtime, and acceptance-relevant outcomes.
An interaction run tests whether roughness and grid effects remain interpretable when changed together.

**Artifact or evidence:**
The sensitivity record contains factor definitions, ranges and rationale, controlled settings, complete scenario identities, outputs, interaction design, failed runs, plots or tables, and implications for the intended decision.

**Failure modes:**
Changing multiple undocumented factors, using one unrepresentative scenario, ignoring failures, or treating sensitivity magnitude as probability or accuracy can misstate uncertainty and robustness.

**Validation question:**
Do the tested factors, ranges, interactions, metrics, and scenario classes cover the decisions and failure modes that matter for the intended use?

**Public scientific sources:**
[SCI-015](bibliography.md#sci-015-hydraulic-model-sensitivity), [SCI-029](bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance), and [SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) support hydraulic sensitivity, numerical refinement, and model-evaluation practice.

## MX-015: Uncertainty

**Method or design principle:**
Identify input, parameter, structural, numerical, referent, and operational uncertainty separately, then connect them to the quantities and decisions they can change.

**Applied-example input:**
The `R-200` evidence packet includes uncertainty in discharge frequency estimates, terrain, roughness, boundary stage, grid resolution, initial state, observation datum transformation, and publication completeness.

**Applied-example behavior:**
The analysis distinguishes quantified distributions or intervals from scenario alternatives and unresolved unknowns.
It reports interactions and preserves uncertainty that cannot be combined defensibly into one number.

**Artifact or evidence:**
An uncertainty register records each source, representation, evidence basis, dependence, propagated quantity, decision effect, residual unknown, reassessment trigger, and responsible review function.

**Failure modes:**
One undifferentiated error bar can hide incompatible meanings, dependence, structural alternatives, or operational failure.
Missing provenance or validation gaps can make a numerical uncertainty estimate appear more complete than its evidence.
See [CQ-008](conflicts-and-open-questions.md#cq-008-missing-provenance) and [CQ-009](conflicts-and-open-questions.md#cq-009-validation-gaps).

**Validation question:**
Are material uncertainty sources represented at a level appropriate to the decision, with dependence, propagation limits, and unquantified gaps stated explicitly?

**Public scientific sources:**
[SCI-009](bibliography.md#sci-009-bulletin-17c), [SCI-013](bibliography.md#sci-013-national-water-model-output-and-forcing-fields) through [SCI-015](bibliography.md#sci-015-hydraulic-model-sensitivity), and [SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) support statistical, forcing, hydraulic, validation, and decision uncertainty.

## MX-016: Methodology change

**Method or design principle:**
Review a methodology change as a bounded scientific and operational contract covering authority, rationale, interfaces, identity, provenance, compatibility, evidence, migration, rollout, rollback, monitoring, and acceptance.

**Applied-example input:**
A synthetic proposal replaces a slope-based downstream boundary with a spatial stage field for the `R-200` scenario at 175 m3/s and requests a shadow comparison within one kilometre upstream of the boundary.
The supplied packet intentionally omits accountable responsibility assignments, immutable provenance, executed verification, complete validation support, and enforced scope controls.

**Applied-example behavior:**
The review applies predeclared criteria, records missing evidence rather than inferring success, and issues `NOT READY` for the requested shadow use and every broader use.

**Artifact or evidence:**
The change packet contains the intended use, existing and proposed methods, alternatives, assumptions, affected contracts and artifacts, identity and compatibility effects, evidence cutoff, verification and validation results, uncertainty, migration, rollout, rollback, monitoring, responsibilities, conditions, and verdict.

**Failure modes:**
A change can alter output meaning without changing identity, rely on unexecuted controls, leave old artifacts selected, lack rollback, or assign no acceptance authority.
See [CQ-002](conflicts-and-open-questions.md#cq-002-incomplete-identity), [CQ-006](conflicts-and-open-questions.md#cq-006-unsafe-reuse), [CQ-009](conflicts-and-open-questions.md#cq-009-validation-gaps), and [CQ-010](conflicts-and-open-questions.md#cq-010-acceptance-authority).

**Validation question:**
Does the complete packet justify one direct verdict for the named change and use while protecting incompatible generations, consumers, and rollback paths?

**Public scientific sources:**
[SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations), [SCI-044](bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability), and [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) support methodology control, verification, validation, uncertainty, intended use, and acceptance criteria.
