# Lab 1 Solution: Trace One Discharge Through the System

This solution shows one evidence-disciplined trace for the hypothetical 250 m3/s discharge.
It is an instructional reference, not evidence that a production library or composite exists.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, and stopping criteria are defined in [Lab 1](../lab-01-trace-the-system.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and preserves missing evidence as an Open question rather than inventing a result.
Stop when the trace satisfies the prompt's competency criteria or records the exact evidence gap that prevents a supported transition.

## Completed trace

| Step | Value or object being traced | Scientific meaning | Software representation or artifact | Owner | Evidence label and source | Required check before proceeding |
| --- | --- | --- | --- | --- | --- | --- |
| 1. Hydrologic forcing | $Q = 250$ m3/s for reach R | Volumetric flow rate associated with a documented hydrologic source, reach, and time or library context. | An upstream forcing or planning record outside the three current jobs. | Hydrologic science and upstream data preparation. | **Scientific foundation:** [Glossary](../../reference/glossary.md#discharge). **Open question:** The hypothetical source record is not supplied. | Verify reach identity, units, temporal or probability meaning, source, and uncertainty. |
| 2. Job input | `scenarios[].upstream_discharge = 250` plus `scenarios[].bc_value` | The selected forcing and nominal downstream-stage or stage-grid planning coordinate for one downstream-stage-aware hydraulic scenario. | `KWSEScenario` nested in `RunKWSEScenariosInputs`. | Scenario planning supplies the values, while the current job validates and consumes them. | **Current implementation:** [`models/run_kwse_scenarios.py`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_kwse_scenarios.py). | Confirm the discharge integer is positive, the model matches reach R, and `bc_value` and the downstream scenario form the intended plan. |
| 3. Hydraulic boundary condition | `QFixBC(value=250)` on the model inflow line | A discharge flux applied at a defined spatial boundary. | A `QFIX` boundary whose vector is `model_manifest.assets.inflow_line`. | Current KWSE job and hydraulic preprocessing. | **Current implementation:** [`jobs/run_kwse_scenarios.py`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/run_kwse_scenarios.py). | Confirm inflow-line geometry, CRS, orientation, model realization, and value units. |
| 4. Solver response | Water state calculated under the full scenario | Depth, WSE when saved, inundated area, storage behavior, and other results caused by the forcing, terrain, roughness, downstream condition, initial state, and solver settings. | Final depth, inundation polygon, stage-transfer line, computed metrics, warnings, and a nullable Zarr field that current public jobs do not populate. | Current scenario runner and hydraulic solver. | **Current implementation:** [`models/solvers.py`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/solvers.py). | Check termination reason, warnings, edges, convergence evidence, quantity units, and hydraulic plausibility. |
| 5. Job response | `RunKWSEScenariosResult` | A software response that summarizes what the job returns to its caller, not a new hydraulic state. | `manifests`, a list of scenario-manifest paths, plus `warnings`. | The current KWSE job constructs and returns the response. | **Current implementation:** [`RunKWSEScenariosResult`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_kwse_scenarios.py) and [`RunKWSEScenariosJob`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/run_kwse_scenarios.py). | Treat each path as a pointer to inspect and do not infer artifact materialization or scientific adequacy from the response alone. |
| 6. Scenario manifest | Self-describing record for one realized run | Provenance, identity, inputs, computed properties, assets, and warnings for the scenario. | `RunScenarioManifest` read from a path in the job response. | The current job either constructs and publishes a new record or re-adopts a matching existing record. | **Current implementation:** [`RunScenarioManifest`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/solvers.py). | Read and validate the manifest, confirm its scenario identity and 250 m3/s input, and identify every required asset without yet claiming those assets exist in storage. |
| 7. Verified storage materialization | Separate evidence that the intended manifest and assets exist at their recorded locations | Operational confirmation that the expected scenario record and artifacts are present, not a new hydraulic result or proof of scientific adequacy. | Storage checks against the manifest and each required asset location. | The target orchestrator observes storage before updating the applicable `materialized_*` state. | **Target design:** [orchestrator design](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/orchestrator-design.md). **Open question:** No runtime verification is supplied for this hypothetical scenario. | Confirm the manifest and every required asset exist and match the expected identities, then assess scientific adequacy separately. |
| 8. Reach library | Verified scenario manifest considered with other selected responses | One sampled point in a bounded discharge and downstream-stage response space. | A selected manifest set plus durable scenario-plan or library-index evidence. | Scientific planning and orchestration select coverage, while the KWSE job executes supplied points. | **Current implementation:** [KWSE job documentation](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/docs/jobs/run_scenarios/run_kwse_scenarios.md). **Target design:** [orchestrator design](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/orchestrator-design.md). | Confirm intended bounds, stage grid, dependency order, completeness, and accepted scenario identities. |
| 9. Composite product | Selected reach depth combined with compatible reach results | A cross-reach depth product under an explicit overlap rule. | Composite raster and provenance linking every source scenario and processing rule. | Product science and compositing outside the current modeling jobs. | **Selected methodology:** DR-004 ALT-D, Pixelwise Max, Alternate Selected, mapped in [XW-008](../../reference/decision-code-artifact-crosswalk.md#xw-008-composite-fim-pixel-calculation). **Current implementation:** No current modeling-job entry point performs compositing. | Confirm quantity, units, grid, terrain meaning, scenario compatibility, identities, nodata handling, and the selected pixel rule. |

## Part A: Why the model is necessary but insufficient

**Current implementation:** `build_model` produces the terrain, roughness, centerline, inflow line, domain, and grid-snapped domain-anchor assets recorded by the model manifest.
The current schema stores the anchor under `reach_centroid`, although the exported point is the reach centroid floored to the model grid rather than the exact centroid.
These assets define the spatial model realization and provide the surfaces and geometry needed by a hydraulic scenario.

The model manifest alone does not choose the scenario's upstream discharge, downstream stage or slope condition, initial state or hot start, solver run settings, or network dependency.
The same model can therefore support multiple scenarios with different hydraulic responses.

The scientific meaning changes at this boundary from a reusable spatial model to one configured hydraulic experiment.

## Part B: The job input

**Current implementation:** `KWSEScenario.upstream_discharge` carries the 250 m3/s value.
The field is an integer greater than zero and is documented as flow applied at the top of the reach in `cms`.
Each `KWSEScenario` belongs to the `scenarios` list in `RunKWSEScenariosInputs`.

Before the job receives it, 250 is hydrologic forcing selected for reach R under a stated context.
The typed field validates a positive whole-number discharge, but it does not establish the hydrologic source, probability, forecast time, uncertainty, or scientific reason for choosing that scenario point.

The evidence therefore changes from hydrologic provenance to a current job contract.
Neither evidence class substitutes for the other.

## Part C: The hydraulic boundary

**Current implementation:** `RunKWSEScenariosJob` creates a `QFixBC` with `bc_type="QFIX"`, `vector=model_manifest.assets.inflow_line`, and `value=scenario.upstream_discharge`.
For the hypothetical scenario, the assigned value is 250.

This conversion gives the number a spatial application point.
A discharge without a boundary geometry would specify how much water passes per unit time but not where it enters the hydraulic domain.
The inflow line without a discharge would specify location but not the forcing magnitude.

## Part D: Forcing and response

The imposed 250 m3/s is forcing.
Final depth and inundation polygon are computed response artifacts.
The supplied `bc_value` is a nominal downstream-stage or stage-grid planning coordinate used in scenario labeling and addressing.
`RunScenarioResults.nominal_wse` is the achieved upstream-end nominal WSE computed during post-processing and rounded to one decimal place for storage in scenario properties.
Transferred pointwise `HFIX` values are imposed WSE boundary values derived from downstream depth plus terrain and are not set by `bc_value`.
Modeled WSE when saved, maximum depth, median depth, and flooded area are computed responses or response properties.
Volume-convergence and termination fields are diagnostics.
Model identity, run identity, scenario code, timestamps, and input records are provenance and identity information.

A zero process exit indicates only that the process completed under its implemented conditions.
A plausible depth raster adds useful visual evidence, but it does not verify discharge provenance, downstream-boundary suitability, vertical compatibility, convergence adequacy, edge behavior, mass balance, or library coverage.

## Part E: Job response, manifest, and materialization

**Current implementation:** `RunKWSEScenariosResult` contains `manifests`, a list of paths, and `warnings`.
For each supplied scenario, the job either simulates and publishes a new result or re-adopts a manifest already present at the identity-derived path when its inputs match exactly.
The job appends each processed scenario manifest's `self_href` to the response list.
Manifest-only re-adoption does not itself verify that every asset referenced by that manifest still exists.
The response is a compact software contract for the caller.
It is not the scenario manifest, a copy of the scenario artifacts, or proof that storage contains every intended object.

Each returned path should be dereferenced and validated as a `RunScenarioManifest`.
The manifest then identifies the scenario inputs, properties, assets, identities, and warnings.
The intended manifest and assets must still be checked at their recorded locations, and scientific adequacy must be assessed from the relevant evidence.

**Target design:** The orchestrator observes storage before updating the applicable `materialized_*` state.
This establishes a separate responsibility boundary between receiving a job response and confirming materialization.
The target assignment does not prove that a deployed verifier performed the check for the hypothetical scenario.

## Part F: Manifest and library

**Current implementation:** The KWSE job processes a caller-provided ordered list of scenarios.
It simulates and publishes each scenario when needed or re-adopts a matching existing manifest.
Its generated documentation explicitly lists choosing the `(q, z)` scenario points, determining hot starts, and stage-transfer planning as out of scope.

**Target design:** The orchestrator owns `plan_scenarios`, schedules work, verifies stored artifacts, updates the `materialized_*` tables, and propagates downstream changes upstream.
This target assignment must not be described as verified deployment.

A scenario manifest proves what the current contract records about one realized scenario.
It does not prove that the selected discharge and stage bounds are correct, that every required combination exists, or that dependencies are complete.
A durable plan or library index must connect intended scenario coverage to the complete verified manifest set.

The meaning changes from one executed hydraulic response to one candidate member of a scientific response library.

## Part G: Library and composite

**Selected methodology:** DR-004 ALT-D selects pixelwise maximum with status Alternate Selected for composite pixel calculation.

**Current implementation:** No current `twod-fim-jobs` entry point performs cross-reach compositing.
The selected rule therefore does not prove that a current composite producer exists or that a particular composite applied the rule.

Before combining the reach R depth raster with another result, verify at least the following conditions:

1. Both inputs represent depth rather than WSE, stage, or another quantity.
2. Units, horizontal CRS, grid alignment, resolution, and resampling treatment are compatible.
3. Each depth uses terrain from the intended model realization and a compatible wet or dry convention.
4. Scenario conditions are appropriate for the same composite product context.
5. Source manifests and artifact identities are preserved.
6. Nodata and overlap handling apply the selected composite method exactly.

The meaning changes from a library of reach responses to a product assembled for a stated use.
Storage location alone cannot authorize or explain that scientific selection.

## Part H: Transition summary

1. Hydrologic forcing becomes a hydraulic-model input when a sourced discharge is selected for one model scenario.
The source evidence does not prove that the job received the correct typed value and identities.
2. The job input becomes a spatial boundary when the KWSE job binds the discharge to the model inflow line as `QFIX`.
The validated number alone does not prove correct placement or geometry.
3. The forcing produces a modeled response when the solver applies all initial and boundary conditions to the model realization.
The forcing value alone does not determine depth, WSE, extent, or velocity.
4. The executed scenario produces a job response that names manifest paths.
The returned paths do not prove that the named manifests and assets materialized or are scientifically adequate.
5. A returned path becomes an inspected scenario manifest only after the record is read and validated against the manifest contract.
The manifest names expected artifacts but does not by itself prove that every named asset exists.
6. A manifest becomes verified storage materialization only after the record and required assets are checked at their recorded locations.
Materialization does not prove scientific adequacy or library completeness.
7. The verified scenario becomes a candidate library member when planning or selection logic evaluates it against intended coverage.
The existence of one library member does not prove the intended response space is covered.
8. Stored reach results become composite inputs when product processing selects compatible scenarios and applies the documented overlap rule.
The library and storage records do not by themselves prove compatible quantity, grid, datum, or event meaning.
9. Responsibility moves from hydrologic science to tooling at scenario input, from tooling to orchestration at planning and materialization checks, and from verified libraries to product science at compositing.
Each owner requires evidence suited to its claim.

## Part I: Evidence classification

**Evidence or experiment:** The separate named-case statement is a bounded project observation because it records a result under stated case inputs.
It can support the claim that the configured scenario completed and produced the recorded depth artifact and metric within that case, subject to inspecting the actual case record.
It does not establish that the configuration is selected methodology, that the result is hydraulically adequate for other reaches or scenarios, or that the same behavior is currently deployed.
Those broader claims require their own decision, validation, current-code, and runtime evidence.

## Remaining concern

**Open question:** The hypothetical exercise provides no real forcing record, scenario plan, runtime evidence, storage listing, or composite artifact.
The trace explains required evidence and ownership but does not validate a deployed end-to-end workflow.
