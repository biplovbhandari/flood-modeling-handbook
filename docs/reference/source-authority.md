# Source Authority

This reference defines how the handbook labels claims and decides which source controls each kind of claim.
The labels describe evidence type, not confidence by themselves.
A claim can require more than one label when selected methodology, current implementation, and target design differ.

## Approved evidence labels

### Scientific foundation

Use **Scientific foundation** for established hydrologic, hydraulic, geospatial, statistical, or numerical concepts.
Prefer authoritative textbooks, standards, government publications, solver documentation, and primary research for these claims.
A scientific foundation explains physical or mathematical meaning, but it does not prove that the project selected or implemented a method.

### Selected methodology

Use **Selected methodology** for the current alternative recorded in the System Decision Register.
Always preserve the recorded decision status, such as Draft, Proposed, Alternate Selected, Needs-Review, or Superseded.
A selected alternative is authoritative for intended methodology only within the decision's stated scope and status.

### Current implementation

Use **Current implementation** for behavior verified in the checked-out code.
Current checkout code is authoritative for what the checkout does.
Schemas and generated contracts are supporting evidence for declared data shapes and interfaces, but they are not sufficient proof of runtime behavior.
Current implementation does not automatically replace a selected methodology when the question concerns intended behavior.

### Target design

Use **Target design** for intended architecture, ownership, data flow, or operational contracts recorded in system-design materials.
Target design must not be described as deployed or operational behavior without separate runtime evidence.

### Evidence or experiment

Use **Evidence or experiment** for a project case, issue, experiment, benchmark, presentation, or other bounded observation.
Preserve the source's stated scope, assumptions, result, and verification limits.
Evidence can support or challenge a decision without automatically replacing it.

Qualify the evidence when its role affects the claim.

- **Fixture evidence** describes one checked-in artifact set and does not prove production provenance, current storage state, or representativeness.
- **Case evidence** describes observations under the recorded case, method, data, and conditions and does not establish universal transfer.
- **Experiment plan evidence** describes a proposed procedure until completed observations and provenance are available.
- **Operational evidence** describes a named deployed version, invocation, storage observation, log, metric, alert, or monitoring interval and does not establish intended methodology or universal validation.
- **Presentation evidence** describes what a presentation communicated in its scope and remains subordinate to the underlying decision, code, artifact, case, or runtime record for detailed claims.

### Open question

Use **Open question** for a conflict, draft, gap, unvalidated assumption, or policy question that requires project resolution.
Do not silently choose an answer to make the handbook narrative simpler.

### Required implementation qualifiers

Use **Current implementation** without qualification only when the claim is tied to a reviewed checkout revision.
Use **checked-in unpinned prototype evidence** when useful local code is available but an exact revision is unavailable.
That qualifier can support a bounded statement about code present in the inspected checkout.
It cannot support a stable current-behavior, deployment, runtime-success, production-readiness, or scientific-acceptance claim.

Use **Inference from current implementation**, **Inference from target design**, or another explicit inference label when a conclusion joins premises that no source states directly.
Name the premises and the condition under which the inference holds.
Do not restate an inference as an observed event without operational evidence.

## Authority hierarchy by claim type

| Claim type | Primary authority | Required boundary |
| --- | --- | --- |
| Established science | Authoritative scientific source | Project use must be mapped separately. |
| Intended project methodology | Selected alternative in the Decision Register | Preserve the decision status and scope. |
| Current job behavior | Current checkout code | Schemas and generated contracts support declared interfaces but do not prove runtime behavior. |
| Target architecture and ownership | System-design documents | Do not present target design as deployed behavior. |
| Observed project result | Case, issue, experiment, benchmark, or presentation | Preserve the observation's scope and verification limits. |
| Checked-in unpinned code | Inspected local file with an explicit unpinned qualifier | Do not infer a stable revision, deployment, execution, or acceptance state. |
| Operational event or state | Versioned runtime record, independently observed storage, logs, metrics, or monitoring evidence | Preserve invocation, time, environment, and observation limits. |
| Project identity and shared status | Approved shared project pages | Current repository and runtime claims remain advisory until verified. |
| Unresolved conflict | The conflicting sources together | Record the disagreement as an Open question. |

When two sources answer different questions, keep both answers with their labels.
When two sources answer the same question differently, record the conflict in [Conflicts and Open Questions](conflicts-and-open-questions.md).

## Validation evidence discipline

Software verification, numerical verification, scientific validation, calibration, benchmark comparison, plausibility review, acceptance criteria, operational monitoring, and post-run diagnosis answer different questions.
Do not collapse them into a generic claim that a model is validated.

An acceptance statement must name the intended use, evidence scope, criteria, authority, restrictions, and unresolved uncertainty.
The presence of a manifest, artifact, warning-free result, convergence value, visually plausible map, or one benchmark is evidence only for its bounded claim.
None independently establishes hydraulic adequacy.

Cases, issues, and experiments retain the label **Evidence or experiment**.
An experiment description is a plan until completed observations and their provenance are available.
A case result remains bounded by its recorded data, methodology revision, conditions, and verification limits.
An issue signature does not establish its cause.

Synthetic teaching packets carry no project authority.
They can demonstrate reasoning and calculations but cannot establish project behavior, thresholds, readiness, or validation.

## Example: KWSE edge boundary behavior

**Selected methodology:** DR-003 selects ALT-D with status Alternate Selected.
That alternative calls for freefall on edge cells informed by the downstream reach's inundation.

**Current implementation:** The current `run_kwse_scenarios` path applies a steep normal-depth slope of 0.5 m/m on domain edges intersecting the downstream inundated area, while separately transferring cell-specific water-surface elevations along the stage-transfer line.
The implementation uses the `FREE` boundary-condition token for a slope-based condition.

**Open question:** The selected use of the word freefall, the `FREE` code token, and the implemented steep normal-depth slope are not demonstrated to be scientifically equivalent.
The handbook therefore teaches the selected and implemented states separately until the project resolves the terminology and behavior.

## Citation discipline

Every project-specific claim should identify its evidence label and a local source path or source record.
Every external scientific claim should identify a source record with an access date when the source is digital.
Revision identifiers date a project mapping, but they do not make scientific claims timeless.
Inference must be labeled as inference and must name the evidence from which it was derived.

## Scientific-change readiness discipline

Scientific methodology reviews use the direct verdicts `READY`, `READY WITH CONDITIONS`, and `NOT READY` defined in [Team-Lead Review Checklist](../07-system-design/03-team-lead-review-checklist.md).
These verdicts are narrower than a general statement that the system is ready.
Each verdict must name the change, intended use, revisions, evidence scope, rollout stage, date, authority, and unresolved risk.

`READY` requires passed decision, contract, compatibility, implementation, identity, provenance, migration, software verification, numerical verification, validation, materialization, rollout, rollback, monitoring, ownership, and acceptance evidence for the stated use.
`READY WITH CONDITIONS` requires all evidence needed for the specifically permitted bounded use plus conditions that are explicit, owned, observable, time-bounded, and enforced before broader use.
Missing methodology authority, unknown scientific validity, identity collision, absent rollback, missing acceptance criteria, or unknown ownership require `NOT READY` rather than a conditional verdict.

The three-verdict change gate does not replace the diagnostic vocabulary in [Validation Framework](../06-validation-and-qc/01-validation-framework.md).
It translates the evidence packet into an authorization decision for a methodology change.

## Example: convergence-threshold change

**Selected methodology:** DR-028 ALT-A supports the claim that \(10^{-3}\) is selected with Alternate Selected status in the reviewed register.
It cannot support a claim that a hypothetical replacement threshold is approved.

**Current implementation:** Current input, run, identity, reuse, and publication code supports the claim that tolerance is a complete scenario input but is omitted from current run identity and address.
Exact target reuse compares complete `RunScenarioInputs`, while ND caller-supplied `existing_scenarios` adoption checks only reach ID, full model ID, run identity hash, and inclusive discharge range.
It cannot support a claim that an overwrite has occurred in a deployed environment.

**Inference from current implementation:** An old-tolerance ND manifest can pass limited caller-supplied adoption and influence new-tolerance adaptive state without proving scientific compatibility.
Separately, unequal tolerance values can fail exact target reuse while still targeting one address, so same-address replacement or mixed publication is a design risk.
Operational storage history would be required to claim that the risk occurred in a particular run.

**Target design:** The system design guide supports the intended principle that an invalidating identity change should create another object and that storage observation establishes materialization.
It cannot prove that current publication is atomic, the reconciler is deployed, or the new generation is accepted.

**Evidence or experiment, case evidence:** Case-018 supplies bounded preliminary convergence-metric evidence under the EXP-014 heading.
It cannot establish universal adequacy or authorize a new threshold.

**Open question:** The required identity, migration, validation, rollback, and ownership policy for a threshold change remains unresolved until authorized project evidence closes it.
