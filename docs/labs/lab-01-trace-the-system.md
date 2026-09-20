# Lab 1: Trace One Discharge Through the System

This lab asks you to trace one hypothetical discharge from hydrologic meaning to a composite FIM product.
The goal is to identify each change in meaning and ownership, not to run a solver.

## Prerequisites

Complete the three Stage 1 orientation chapters:

- [What Is Flood Inundation Mapping?](../00-orientation/01-what-is-fim.md)
- [End-to-End Mental Model](../00-orientation/02-end-to-end-mental-model.md)
- [Quantities, Units, and Datums](../00-orientation/03-quantities-units-and-datums.md)

Read [Lab Conventions](README.md), [Source Authority](../reference/source-authority.md), [Glossary](../reference/glossary.md), and [Equations and Units](../reference/equations-and-units.md).

## Execution boundary

All required steps are **Core inspection**.
No command or production access is required, and any independent tooling remains optional and user-run without changing the evidence boundary.

## Scenario

A hydrologic source supplies an upstream discharge of 250 m3/s for reach R.
Assume the source has documented the reach, time or library context, units, and uncertainty.
A model for reach R already exists.
A scenario planner chooses a downstream-stage-aware scenario whose upstream discharge is 250 m3/s and whose downstream dependency is scenario D from the next reach downstream.
The scenario runs successfully and publishes a scenario manifest and final depth artifact.
Later processing considers the scenario for a reach library and then for a composite depth product.

The values and identifiers are instructional rather than evidence from a production run.

## Deliverable

Create one trace table with the following columns:

| Step | Value or object being traced | Scientific meaning | Software representation or artifact | Owner | Evidence label and source | Required check before proceeding |
| --- | --- | --- | --- | --- | --- | --- |
| 1. Hydrologic forcing |  |  |  |  |  |  |
| 2. Job input |  |  |  |  |  |  |
| 3. Hydraulic boundary condition |  |  |  |  |  |  |
| 4. Solver response |  |  |  |  |  |  |
| 5. Job response |  |  |  |  |  |  |
| 6. Scenario manifest |  |  |  |  |  |  |
| 7. Verified storage materialization |  |  |  |  |  |  |
| 8. Reach library |  |  |  |  |  |  |
| 9. Composite product |  |  |  |  |  |  |

Use the smallest evidence set that supports each answer.
Separate Scientific foundation, Selected methodology, Current implementation, Target design, Evidence or experiment, and Open question.

## Part A: Establish the model context

**Core inspection:** Explain why the pre-existing reach model is necessary but not sufficient to define the scenario.
Name at least four model assets produced by `build_model` and at least three scenario-specific choices that are not fixed by the model manifest alone.

Use these sources:

- [`BuildModelInputs` and `ModelManifest`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/build_model.py)
- [`BuildModelJob`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/build_model.py)

## Part B: Trace the value into the job

**Core inspection:** Locate the current typed field that carries 250 m3/s in a downstream-stage-aware scenario.
Record the field name, declared unit, validation rule, and enclosing input objects.

Use this source:

- [`KWSEScenario` and `RunKWSEScenariosInputs`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_kwse_scenarios.py)

Then explain what the number means before the job receives it and what the jobs package does not establish about its hydrologic origin.

## Part C: Trace the value into the hydraulic boundary

**Core inspection:** Identify how the current job converts the scenario discharge into a hydraulic boundary condition.
Record the boundary type, boundary geometry, and assigned value.

Use these sources:

- [`RunKWSEScenariosJob`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/run_kwse_scenarios.py)
- [`QFixBC` and `RunScenarioInputs`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/solvers.py)

Explain why a discharge number without its inflow geometry would not completely define the hydraulic forcing.

## Part D: Distinguish forcing from response

**Core inspection:** List at least four hydraulic response quantities or artifacts that can result from the scenario.
For each item, state whether it is the imposed 250 m3/s forcing, a computed response, a diagnostic, or provenance.
Classify three KWSE quantities separately.
The supplied `bc_value` is a nominal downstream-stage or stage-grid planning coordinate used in the scenario label and address.
`RunScenarioResults.nominal_wse` is the achieved upstream-end nominal WSE computed during post-processing and rounded for storage in scenario properties.
Transferred pointwise `HFIX` values are imposed WSE boundary values derived from the downstream source scenario rather than from `bc_value`.

Use this source:

- [`RunScenarioResults`, `ScenarioAssets`, and `RunScenarioManifest`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/solvers.py)

Explain why a successful process exit and a plausible depth raster do not by themselves prove hydraulic adequacy.

## Part E: Distinguish the response, manifest, and materialization

**Core inspection:** Identify the two fields in `RunKWSEScenariosResult` and explain what the response tells its caller.
Then distinguish that returned response from each scenario manifest named by its paths and from a separate verification that the manifest and assets materialized in storage.

Use these sources:

- [`RunKWSEScenariosResult`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_kwse_scenarios.py)
- [`RunScenarioManifest`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/solvers.py)
- [Target orchestrator design](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/orchestrator-design.md)

Explain why a returned manifest path is evidence to inspect rather than proof that every intended artifact is present and scientifically adequate.

## Part F: Trace the manifest into a library

**Core inspection:** Explain who chooses the downstream-stage-aware scenario points and who executes them.
Identify what the scenario manifest proves and what additional planning evidence is needed to show that the intended library is complete.

Use these sources:

- [Current KWSE job documentation](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/docs/jobs/run_scenarios/run_kwse_scenarios.md)
- [Target orchestrator design](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/orchestrator-design.md)
- [XW-006: Scenario-library bounds and sampling](../reference/decision-code-artifact-crosswalk.md#xw-006-scenario-library-bounds-and-sampling)

## Part G: Trace the library into a composite product

**Core inspection:** Identify the selected composite pixel-value rule and its decision status.
State whether a current modeling-job entry point performs this step.
List at least four compatibility checks required before a depth raster from reach R can be combined with another reach result.

Use these sources:

- [XW-008: Composite FIM pixel calculation](../reference/decision-code-artifact-crosswalk.md#xw-008-composite-fim-pixel-calculation)
- [Quantities, Units, and Datums](../00-orientation/03-quantities-units-and-datums.md)

## Part H: Mark the transitions

**Core inspection:** Mark every row in your trace where one of the following changes occurs:

- a hydrologic quantity becomes a hydraulic-model input;
- a job input becomes a spatial boundary condition;
- a forcing produces a modeled response;
- a completed job returns paths and warnings to its caller;
- a returned path is read as a scenario manifest;
- a manifest and its required assets are verified in storage;
- an executed scenario becomes a candidate library member;
- stored reach results become inputs to a composite product; or
- responsibility moves between science, tooling, orchestration, and storage verification.

For each transition, write one sentence explaining why the previous evidence is not sufficient for the next claim.

## Part I: Classify bounded project evidence

**Core inspection:** Classify this separate statement using one of the six evidence labels:

> A named project case records that one configured scenario completed and produced a depth raster and a maximum-depth metric under the case's stated inputs.

Explain what the correct label permits you to claim and why the observation does not by itself establish selected methodology, general hydraulic adequacy, or current deployed behavior outside that case.

## Competency criteria

Your trace is complete when it:

- preserves 250 m3/s as discharge rather than confusing it with volume, WSE, stage, or depth;
- identifies the `upstream_discharge` field and `QFIX` boundary;
- distinguishes the solver response from the forcing;
- distinguishes `RunKWSEScenariosResult` from a scenario manifest and verified storage materialization;
- assigns scenario planning and compositing outside the current job boundary;
- uses the six evidence labels without substituting one for another; and
- records at least one Open question or required evidence check.

After completing the lab, compare your reasoning with [Lab 1 Solution](solutions/lab-01-trace-the-system-solution.md).
