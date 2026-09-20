# Flood Modeling Handbook

An independently maintained handbook for learning hydrology, hydraulics, and flood-inundation modeling.

This handbook teaches the scientific reasoning, project methodology, implementation boundaries, artifacts, and diagnostic judgment needed to work independently with the 2D FIM system.
It connects hydrology and hydraulics to model development, scenario libraries, validation, and scientific software contracts without treating the current workflow as a black box.

## First-edition status

Implementation is complete, and the independent review findings have been integrated.
Rendered inspection, interactive link clicking, focused independent re-review, and user-run repository checks remain validation-pending and are not represented as passed.

## Audience

The primary reader has training comparable to a bachelor's degree in Geomatics Engineering and a master's degree in Earth System Science.
The reader may understand hydrology, deployment, infrastructure, or SEPEX without having the full water-resources background of a civil or hydraulic engineer.
The team-neutral learning path also supports future engineers who need to understand both scientific meaning and software consequences.

## Competency ladder

### Level A: Science-literate reviewer

The reader can explain the terminology, physical concepts, methodology, assumptions, and outputs used by the project.
The reader can distinguish scientific foundation, selected methodology, current implementation, target design, evidence, and open questions during reviews.

### Level B: Independent operator and diagnostician

Level B includes all Level A competencies.
The reader can prepare and assess model inputs, understand job parameters, inspect outputs, recognize suspicious results, trace decisions and code, and explain plausible causes of failure.
Level B is the primary target for the first complete reading path.

### Level C: Methodology owner and team lead

Level C includes Levels A and B.
The reader can evaluate and defend choices concerning domains, boundary conditions, roughness, convergence, scenario sampling, uncertainty, validation, solver behavior, scientific software contracts, and rollout evidence.

## Prerequisites

Readers should be comfortable with algebra, unit conversion, maps, coordinate reference systems, raster and vector data, and basic probability.
Basic Python and JSON literacy helps with code and manifest tracing but is not required for the scientific foundation.
Later project labs assume access to the local checkouts or provided artifacts named in the exercise.
Solver, container, cloud, and large-data execution remains optional unless a lab is explicitly labeled for specialized runtime work.

Before using project claims, read [Source Authority](reference/source-authority.md).
Use [Glossary](reference/glossary.md) and [Equations and Units](reference/equations-and-units.md) throughout the learning path.

## Eight-stage learning path and progress

The estimated evidence column describes what a learner should be able to produce after completing the stage.
It does not report that project validation has occurred.

| Progress | Stage | Prerequisites | Estimated learner evidence |
| --- | --- | --- | --- |
| [ ] | 1. Orientation and vocabulary | General geospatial literacy | A labeled end-to-end system trace and a correct distinction among forcing, hydraulic response, and FIM products. |
| [ ] | 2. Hydrology needed by this system | Stage 1 and basic probability | A catchment-to-discharge explanation, hydrograph interpretation, AEP calculation, and forcing-uncertainty statement. |
| [ ] | 3. Open-channel hydraulic foundations | Stages 1 and 2 plus algebra | Unit-checked discharge, Froude, Manning, normal-depth, and boundary-control reasoning. |
| [ ] | 4. Two-dimensional hydraulic modeling | Stage 3 | A grid and wetting explanation, CFL estimate, and distinction among stability, convergence, mass balance, and adequacy. |
| [ ] | 5. Reach-model development | Stages 1 through 4 plus GIS concepts | A trace from hydrofabric and source rasters to model domain, boundary geometry, grid, manifest, and warnings. |
| [ ] | 6. Scenario-library methodology | Stages 3 through 5 | A reconstruction of ND sampling, KWSE stage transfer, scenario planning, library identity, and compositing boundaries. |
| [ ] | 7. Validation and diagnosis | Stages 1 through 6 | A prioritized diagnosis, sensitivity plan, uncertainty statement, and evidence-based validation decision. |
| [ ] | 8. Scientific software and team-lead judgment | Stages 1 through 7 | A decision-code-artifact review with acceptance conditions, unresolved risk, ownership, and rollout evidence. |

## How to read source labels

The handbook uses six labels defined in [Source Authority](reference/source-authority.md): Scientific foundation, Selected methodology, Current implementation, Target design, Evidence or experiment, and Open question.
Do not interpret one label as a substitute for another.
Use [Decision-Code-Artifact Crosswalk](reference/decision-code-artifact-crosswalk.md) to trace major concepts across source layers.
Use [Conflicts and Open Questions](reference/conflicts-and-open-questions.md) when sources disagree or a required responsibility is outside current jobs.

## Implementation status and navigation

This table is the implementation-status map for the complete first-edition learning path.
Every listed first-edition unit is available through a Markdown link.

### Stage 1: Orientation and vocabulary

| Status | Chapter or lab | Path |
| --- | --- | --- |
| Available | What is flood inundation mapping? | [00-orientation/01-what-is-fim.md](00-orientation/01-what-is-fim.md) |
| Available | End-to-end mental model | [00-orientation/02-end-to-end-mental-model.md](00-orientation/02-end-to-end-mental-model.md) |
| Available | Quantities, units, and datums | [00-orientation/03-quantities-units-and-datums.md](00-orientation/03-quantities-units-and-datums.md) |
| Available | Lab 1: Trace the system | [labs/lab-01-trace-the-system.md](labs/lab-01-trace-the-system.md) |

### Stage 2: Hydrology needed by this system

| Status | Chapter or lab | Path |
| --- | --- | --- |
| Available | Watersheds, reaches, and hydrofabrics | [01-hydrology-for-fim/01-watersheds-reaches-and-hydrofabrics.md](01-hydrology-for-fim/01-watersheds-reaches-and-hydrofabrics.md) |
| Available | Water balance, runoff, and hydrographs | [01-hydrology-for-fim/02-water-balance-runoff-and-hydrographs.md](01-hydrology-for-fim/02-water-balance-runoff-and-hydrographs.md) |
| Available | Discharge, stage, and routing | [01-hydrology-for-fim/03-discharge-stage-and-routing.md](01-hydrology-for-fim/03-discharge-stage-and-routing.md) |
| Available | Flood frequency, AEP, and bounds | [01-hydrology-for-fim/04-flood-frequency-aep-and-bounds.md](01-hydrology-for-fim/04-flood-frequency-aep-and-bounds.md) |
| Available | Forcing sources and uncertainty | [01-hydrology-for-fim/05-forcing-sources-and-uncertainty.md](01-hydrology-for-fim/05-forcing-sources-and-uncertainty.md) |
| Available | Lab 2: Catchment to discharge | [labs/lab-02-catchment-to-discharge.md](labs/lab-02-catchment-to-discharge.md) |
| Available | Lab 3: AEP and discharge bounds | [labs/lab-03-aep-and-discharge-bounds.md](labs/lab-03-aep-and-discharge-bounds.md) |

### Pass 1 learner checkpoint

Before beginning Pass 2, review the three Stage 1 chapters and the five Stage 2 chapters in the order shown above.
Attempt [Lab 1](labs/lab-01-trace-the-system.md), [Lab 2](labs/lab-02-catchment-to-discharge.md), and [Lab 3](labs/lab-03-aep-and-discharge-bounds.md).
Proceed when the learner can distinguish forcing from hydraulic response, use stage, WSE, WSEL, depth, discharge, units, CRS, and datum consistently, and explain why a selected method, current implementation, target design, scoped observation, and Open question carry different authority.
This checkpoint requests review and practice only and does not mark any learner progress checkbox as complete.

### Stage 3: Open-channel hydraulic foundations

| Status | Chapter or lab | Path |
| --- | --- | --- |
| Available | Conservation, discharge, and storage | [02-open-channel-flow/01-conservation-discharge-and-storage.md](02-open-channel-flow/01-conservation-discharge-and-storage.md) |
| Available | Energy, momentum, and flow regimes | [02-open-channel-flow/02-energy-momentum-and-flow-regimes.md](02-open-channel-flow/02-energy-momentum-and-flow-regimes.md) |
| Available | Manning flow and normal depth | [02-open-channel-flow/03-manning-flow-and-normal-depth.md](02-open-channel-flow/03-manning-flow-and-normal-depth.md) |
| Available | Backwater and boundary control | [02-open-channel-flow/04-backwater-and-boundary-control.md](02-open-channel-flow/04-backwater-and-boundary-control.md) |
| Available | Lab 4: Flow regime and normal depth | [labs/lab-04-flow-regime-and-normal-depth.md](labs/lab-04-flow-regime-and-normal-depth.md) |
| Available | Lab 5: Backwater and boundaries | [labs/lab-05-backwater-and-boundaries.md](labs/lab-05-backwater-and-boundaries.md) |

### Stage 4: Two-dimensional hydraulic modeling

| Status | Chapter or lab | Path |
| --- | --- | --- |
| Available | Shallow-water models | [03-2d-hydraulics/01-shallow-water-models.md](03-2d-hydraulics/01-shallow-water-models.md) |
| Available | Grids, wetting, and drying | [03-2d-hydraulics/02-grids-wetting-and-drying.md](03-2d-hydraulics/02-grids-wetting-and-drying.md) |
| Available | Time stepping and stability | [03-2d-hydraulics/03-time-stepping-and-stability.md](03-2d-hydraulics/03-time-stepping-and-stability.md) |
| Available | Convergence, mass balance, and hot starts | [03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md](03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md) |
| Available | LISFLOOD-FP and SFINCS | [03-2d-hydraulics/05-lisflood-fp-and-sfincs.md](03-2d-hydraulics/05-lisflood-fp-and-sfincs.md) |
| Available | Lab 6: Grid stability and wetting | [labs/lab-06-grid-stability-and-wetting.md](labs/lab-06-grid-stability-and-wetting.md) |
| Available | Lab 7: Convergence and solver evidence | [labs/lab-07-convergence-and-solver-evidence.md](labs/lab-07-convergence-and-solver-evidence.md) |

### Pass 2 learner checkpoint

Before beginning Pass 3, review [Manning Flow and Normal Depth](02-open-channel-flow/03-manning-flow-and-normal-depth.md), [Backwater and Boundary Control](02-open-channel-flow/04-backwater-and-boundary-control.md), [Grids, Wetting, and Drying](03-2d-hydraulics/02-grids-wetting-and-drying.md), [Time Stepping and Stability](03-2d-hydraulics/03-time-stepping-and-stability.md), [Convergence, Mass Balance, and Hot Starts](03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md), and [LISFLOOD-FP and SFINCS](03-2d-hydraulics/05-lisflood-fp-and-sfincs.md).
Attempt [Lab 4](labs/lab-04-flow-regime-and-normal-depth.md), [Lab 5](labs/lab-05-backwater-and-boundaries.md), [Lab 6](labs/lab-06-grid-stability-and-wetting.md), and [Lab 7](labs/lab-07-convergence-and-solver-evidence.md) before starting the Pass 3 project-methodology chapters.
This checkpoint requests review and practice only and does not mark any learner progress checkbox as complete.

### Stage 5: Reach-model development

| Status | Chapter or lab | Path |
| --- | --- | --- |
| Available | Network preparation | [04-model-development/01-network-preparation.md](04-model-development/01-network-preparation.md) |
| Available | Terrain, topobathymetry, and structures | [04-model-development/02-terrain-topobathymetry-and-structures.md](04-model-development/02-terrain-topobathymetry-and-structures.md) |
| Available | Roughness and land cover | [04-model-development/03-roughness-and-land-cover.md](04-model-development/03-roughness-and-land-cover.md) |
| Available | Domain and boundary geometry | [04-model-development/04-domain-and-boundary-geometry.md](04-model-development/04-domain-and-boundary-geometry.md) |
| Available | The build-model job | [04-model-development/05-build-model-job.md](04-model-development/05-build-model-job.md) |
| Available | Lab 8: Inspect a built model | [labs/lab-08-inspect-a-built-model.md](labs/lab-08-inspect-a-built-model.md) |
| Available | Lab 9: Diagnose domain and boundaries | [labs/lab-09-diagnose-domain-and-boundaries.md](labs/lab-09-diagnose-domain-and-boundaries.md) |

### Stage 6: Scenario-library methodology

| Status | Chapter or lab | Path |
| --- | --- | --- |
| Available | Normal-depth libraries | [05-scenario-libraries/01-normal-depth-libraries.md](05-scenario-libraries/01-normal-depth-libraries.md) |
| Available | Adaptive discharge selection | [05-scenario-libraries/02-adaptive-discharge-selection.md](05-scenario-libraries/02-adaptive-discharge-selection.md) |
| Available | KWSE and stage transfer | [05-scenario-libraries/03-kwse-and-stage-transfer.md](05-scenario-libraries/03-kwse-and-stage-transfer.md) |
| Available | Scenario planning and propagation | [05-scenario-libraries/04-scenario-planning-and-propagation.md](05-scenario-libraries/04-scenario-planning-and-propagation.md) |
| Available | Compositing, identity, and provenance | [05-scenario-libraries/05-compositing-identity-and-provenance.md](05-scenario-libraries/05-compositing-identity-and-provenance.md) |
| Available | Lab 10: Follow adaptive ND selection | [labs/lab-10-follow-adaptive-nd-selection.md](labs/lab-10-follow-adaptive-nd-selection.md) |
| Available | Lab 11: Trace KWSE stage transfer | [labs/lab-11-trace-kwse-stage-transfer.md](labs/lab-11-trace-kwse-stage-transfer.md) |

### Pass 3 learner checkpoint

Before beginning Pass 4, review [Network Preparation](04-model-development/01-network-preparation.md), [Terrain, Topobathymetry, and Structures](04-model-development/02-terrain-topobathymetry-and-structures.md), [Roughness and Land Cover](04-model-development/03-roughness-and-land-cover.md), [Domain and Boundary Geometry](04-model-development/04-domain-and-boundary-geometry.md), [The Build-Model Job](04-model-development/05-build-model-job.md), [Normal-Depth Libraries](05-scenario-libraries/01-normal-depth-libraries.md), [Adaptive Discharge Selection](05-scenario-libraries/02-adaptive-discharge-selection.md), [KWSE and Stage Transfer](05-scenario-libraries/03-kwse-and-stage-transfer.md), [Scenario Planning and Propagation](05-scenario-libraries/04-scenario-planning-and-propagation.md), and [Compositing, Identity, and Provenance](05-scenario-libraries/05-compositing-identity-and-provenance.md).
Attempt [Lab 8](labs/lab-08-inspect-a-built-model.md), [Lab 9](labs/lab-09-diagnose-domain-and-boundaries.md), [Lab 10](labs/lab-10-follow-adaptive-nd-selection.md), and [Lab 11](labs/lab-11-trace-kwse-stage-transfer.md) before starting the Pass 4 scientific-judgment chapters.
This checkpoint requests review and practice only and does not mark any learner progress checkbox as complete.

### Stage 7: Validation and diagnosis

| Status | Chapter or lab | Path |
| --- | --- | --- |
| Available | Validation framework | [06-validation-and-qc/01-validation-framework.md](06-validation-and-qc/01-validation-framework.md) |
| Available | Diagnostic workflow | [06-validation-and-qc/02-diagnostic-workflow.md](06-validation-and-qc/02-diagnostic-workflow.md) |
| Available | Sensitivity and uncertainty | [06-validation-and-qc/03-sensitivity-and-uncertainty.md](06-validation-and-qc/03-sensitivity-and-uncertainty.md) |
| Available | Case, issue, and experiment catalog | [06-validation-and-qc/04-case-issue-and-experiment-catalog.md](06-validation-and-qc/04-case-issue-and-experiment-catalog.md) |
| Available | Lab 12: Validate and triage | [labs/lab-12-validate-and-triage.md](labs/lab-12-validate-and-triage.md) |

### Stage 8: Scientific software and team-lead judgment

| Status | Chapter or lab | Path |
| --- | --- | --- |
| Available | Scientific software contracts | [07-system-design/01-scientific-software-contracts.md](07-system-design/01-scientific-software-contracts.md) |
| Available | Current, target, and evidence boundaries | [07-system-design/02-current-target-and-evidence-boundaries.md](07-system-design/02-current-target-and-evidence-boundaries.md) |
| Available | Team-lead review checklist | [07-system-design/03-team-lead-review-checklist.md](07-system-design/03-team-lead-review-checklist.md) |
| Available | Lab 13: Review a methodology change | [labs/lab-13-review-a-methodology-change.md](labs/lab-13-review-a-methodology-change.md) |

### Pass 4 and first-edition checkpoint

Review all Stage 7 and Stage 8 chapters, then attempt [Lab 12](labs/lab-12-validate-and-triage.md) and [Lab 13](labs/lab-13-review-a-methodology-change.md).
Use the [Decision-Code-Artifact Crosswalk](reference/decision-code-artifact-crosswalk.md), [Conflicts and Open Questions](reference/conflicts-and-open-questions.md), and [Bibliography and Source Map](reference/bibliography.md) to verify every project-specific conclusion.
The first-edition learning path is complete only when the learner can state a direct readiness verdict, preserve unresolved conflicts, identify deferred production concerns, and name the exact evidence and owner needed to change the verdict.
This checkpoint does not assert production acceptance or mark any learner progress checkbox as complete.

### Lab solutions

Solutions remain separate from prompts so the learner can attempt each exercise first.

| Status | Lab solution | Path |
| --- | --- | --- |
| Available | Lab 1 solution | [labs/solutions/lab-01-trace-the-system-solution.md](labs/solutions/lab-01-trace-the-system-solution.md) |
| Available | Lab 2 solution | [labs/solutions/lab-02-catchment-to-discharge-solution.md](labs/solutions/lab-02-catchment-to-discharge-solution.md) |
| Available | Lab 3 solution | [labs/solutions/lab-03-aep-and-discharge-bounds-solution.md](labs/solutions/lab-03-aep-and-discharge-bounds-solution.md) |
| Available | Lab 4 solution | [labs/solutions/lab-04-flow-regime-and-normal-depth-solution.md](labs/solutions/lab-04-flow-regime-and-normal-depth-solution.md) |
| Available | Lab 5 solution | [labs/solutions/lab-05-backwater-and-boundaries-solution.md](labs/solutions/lab-05-backwater-and-boundaries-solution.md) |
| Available | Lab 6 solution | [labs/solutions/lab-06-grid-stability-and-wetting-solution.md](labs/solutions/lab-06-grid-stability-and-wetting-solution.md) |
| Available | Lab 7 solution | [labs/solutions/lab-07-convergence-and-solver-evidence-solution.md](labs/solutions/lab-07-convergence-and-solver-evidence-solution.md) |
| Available | Lab 8 solution | [labs/solutions/lab-08-inspect-a-built-model-solution.md](labs/solutions/lab-08-inspect-a-built-model-solution.md) |
| Available | Lab 9 solution | [labs/solutions/lab-09-diagnose-domain-and-boundaries-solution.md](labs/solutions/lab-09-diagnose-domain-and-boundaries-solution.md) |
| Available | Lab 10 solution | [labs/solutions/lab-10-follow-adaptive-nd-selection-solution.md](labs/solutions/lab-10-follow-adaptive-nd-selection-solution.md) |
| Available | Lab 11 solution | [labs/solutions/lab-11-trace-kwse-stage-transfer-solution.md](labs/solutions/lab-11-trace-kwse-stage-transfer-solution.md) |
| Available | Lab 12 solution | [labs/solutions/lab-12-validate-and-triage-solution.md](labs/solutions/lab-12-validate-and-triage-solution.md) |
| Available | Lab 13 solution | [labs/solutions/lab-13-review-a-methodology-change-solution.md](labs/solutions/lab-13-review-a-methodology-change-solution.md) |

### Reference backbone

| Status | Reference | Path |
| --- | --- | --- |
| Available | Source authority | [reference/source-authority.md](reference/source-authority.md) |
| Available | Glossary | [reference/glossary.md](reference/glossary.md) |
| Available | Equations and units | [reference/equations-and-units.md](reference/equations-and-units.md) |
| Available | Decision-code-artifact crosswalk | [reference/decision-code-artifact-crosswalk.md](reference/decision-code-artifact-crosswalk.md) |
| Available | Conflicts and open questions | [reference/conflicts-and-open-questions.md](reference/conflicts-and-open-questions.md) |
| Available | Bibliography and source map | [reference/bibliography.md](reference/bibliography.md) |
| Available | Lab conventions | [labs/README.md](labs/README.md) |
| Available | Visual source register | [assets/source-register.md](assets/source-register.md) |

### Instructional asset availability

| Status | Visual | Path |
| --- | --- | --- |
| Available | Stage, depth, and datum | [assets/stage-depth-datum.svg](assets/stage-depth-datum.svg) |
| Available | Hydrology to hydraulics | [assets/hydrology-to-hydraulics.svg](assets/hydrology-to-hydraulics.svg) |
| Available | Open-channel controls | [assets/open-channel-controls.svg](assets/open-channel-controls.svg) |
| Available | Grid flux and wetting | [assets/grid-flux-wetting.svg](assets/grid-flux-wetting.svg) |
| Available | Convergence evidence | [assets/convergence-evidence.svg](assets/convergence-evidence.svg) |
| Available | Model-development chain | [assets/model-development-chain.svg](assets/model-development-chain.svg) |
| Available | ND adaptive selection | [assets/nd-adaptive-selection.svg](assets/nd-adaptive-selection.svg) |
| Available | KWSE stage transfer | [assets/kwse-stage-transfer.svg](assets/kwse-stage-transfer.svg) |
| Available | Validation evidence chain | [assets/validation-evidence-chain.svg](assets/validation-evidence-chain.svg) |

## Resume point

Use the first unchecked stage in the progress table as the learner's resume point.
Within that stage, follow the chapter order shown above and complete the associated labs after their prerequisite chapters.
Update a checkbox only when the learner has produced and reviewed the stated evidence, not merely opened the files or completed a command.
