# Lab Conventions

The labs turn handbook concepts into inspectable calculations, source traces, artifact checks, diagnoses, and design reviews.
They are learning exercises, not production acceptance tests.

## Prerequisites

Before starting a lab, complete the handbook stages named in that lab's prerequisite section.
Read [Source Authority](../reference/source-authority.md) and use its six evidence labels in every answer.
Use [Glossary](../reference/glossary.md) and [Equations and Units](../reference/equations-and-units.md) for stable terms, variables, units, and assumptions.
Confirm that the referenced local source files or artifacts exist before relying on them.
Do not use secrets, production credentials, private production values, or sensitive data in lab evidence.

## Execution labels

Every lab step uses one of these labels.

- **Core inspection:** Complete the step by reading, calculating, or inspecting provided material without running a project command.
- **User-run optional:** The handbook provides a command that the learner may run in an appropriate local environment.
- **Specialized runtime optional:** The step requires solver, container, cloud, large-data, or other specialized access and is not required for the core learning path.

Commands are for the user to run.
The handbook author does not execute project tests, builds, solver jobs, infrastructure commands, or other project commands under the workspace rules.
A command that exits successfully proves only that the command completed under those conditions.
It does not by itself prove scientific correctness, hydraulic adequacy, artifact completeness, or production readiness.

## Expected evidence

Each lab answer should produce the smallest evidence set that supports its conclusion.
Depending on the exercise, evidence may include:

- a dimensional calculation with units and assumptions;
- a source trace connecting a decision, code path, input, and artifact;
- an inspected manifest field or geospatial artifact property;
- a figure or table with provenance;
- a diagnostic hypothesis and the next discriminating check;
- a list of unresolved conflicts or missing evidence;
- a short review decision with acceptance conditions.

Record the evidence label and source path beside every project-specific claim.
Separate observed facts from calculations, inference, and open questions.

## Answer discipline

State the question in your own words before answering it.
Show units for every numerical quantity and carry units through calculations.
State assumptions before using them.
Preserve decision status and source scope.
Do not infer current behavior from target design or documentation when current code is available.
Do not treat a plausible map, zero exit code, or generated file as sufficient validation.
When evidence conflicts, identify the conflict and state what would resolve it.
When a required value or source is unavailable, state the limitation instead of inventing a value.

## Prompts and solutions

Each lab prompt is a standalone file under `labs/`.
Each solution is a separate file under `labs/solutions/` so the learner can attempt the work before reading the reasoning.
Solutions explain the evidence chain and judgment process rather than only giving a final value.
A solution is an instructional reference, not proof that a different dataset or checkout will produce the same result.

## Lab sequence and availability

| Lab | Status | Prompt path | Primary evidence |
| --- | --- | --- | --- |
| 1 | Available, with solution | `labs/lab-01-trace-the-system.md` | End-to-end source and artifact trace. |
| 2 | Available, with solution | `labs/lab-02-catchment-to-discharge.md` | Network and hydrograph interpretation. |
| 3 | Available, with solution | `labs/lab-03-aep-and-discharge-bounds.md` | Probability conversion and bounded decision critique. |
| 4 | Available, with solution | `labs/lab-04-flow-regime-and-normal-depth.md` | Discharge, Froude, Manning, and sensitivity calculations. |
| 5 | Available, with solution | `labs/lab-05-backwater-and-boundaries.md` | Boundary-control prediction with stated assumptions. |
| 6 | Available, with solution | `labs/lab-06-grid-stability-and-wetting.md` | Grid, wetting, and CFL calculation. |
| 7 | Available, with solution | `labs/lab-07-convergence-and-solver-evidence.md` | Termination and adequacy evidence assessment. |
| 8 | Available, with solution | `labs/lab-08-inspect-a-built-model.md` | Model artifact and manifest inspection. |
| 9 | Available, with solution | `labs/lab-09-diagnose-domain-and-boundaries.md` | Domain and boundary diagnosis. |
| 10 | Available, with solution | `labs/lab-10-follow-adaptive-nd-selection.md` | Adaptive scenario-decision reconstruction, re-judgment, publication-versus-membership classification, and readiness boundary. |
| 11 | Available, with solution | `labs/lab-11-trace-kwse-stage-transfer.md` | Exact KWSE payload, cross-reach transfer, failure, identity, materialization, responsibility, and readiness trace. |
| 12 | Available, with solution | `labs/lab-12-validate-and-triage.md` | Competing hypotheses, discriminatory next-check selection, stopping and escalation, evidence labels, and a readiness verdict. |
| 13 | Available, with solution | `labs/lab-13-review-a-methodology-change.md` | Scientific contract, evidence-boundary, identity, validation, migration, rollout, rollback, ownership, and direct-verdict review. |

Labs 1 through 13 and their separate solutions are available.
