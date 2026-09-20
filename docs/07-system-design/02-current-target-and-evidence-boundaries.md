# Current, Target, and Evidence Boundaries

A trustworthy review asks each source only the question that source can answer.
Current code, a selected decision, a target design, a fixture, a case, a prototype, an operational observation, and a presentation can all be accurate while supporting different claims.

## Why this matters

Methodology reviews often fail through source substitution.
A reviewer sees a field in a schema and infers runtime behavior, sees target architecture and infers deployment, sees one successful case and infers general validation, or sees a presentation and infers a durable contract.
The result is a confident claim whose evidence answers a different question.

## Prerequisites

Read [Source Authority](../reference/source-authority.md), [Scientific Software Contracts](01-scientific-software-contracts.md), [Validation Framework](../06-validation-and-qc/01-validation-framework.md), and [Case, Issue, and Experiment Catalog](../06-validation-and-qc/04-case-issue-and-experiment-catalog.md).

## Learning objectives

After this chapter, the reader should be able to:

- choose the correct source class for a current-behavior, intended-method, target-design, operational, or readiness claim;
- distinguish pinned current implementation from checked-in unpinned prototype evidence;
- state what a fixture, case, experiment, or presentation can and cannot prove;
- label an inference and show its premises; and
- assemble a cross-source review without merging unresolved conflicts.

## 1. Use the source that controls the claim

| Evidence class or qualifier | Question it can answer | Question it cannot answer by itself | Concrete project example |
| --- | --- | --- | --- |
| **Scientific foundation** | What does a scientific or numerical concept mean, and what general evidence should support it? | Which method the project selected, what the checkout implements, or whether a product passed. | NASA, NIST, and EPA define verification, validation, calibration, uncertainty, and intended-use assessment, but they do not select the project's convergence threshold. |
| **Selected methodology** | Which alternative is intended within the decision record's exact status and scope? | Whether code implements it, whether it ran, or whether it is validated for every use. | DR-028 ALT-A selects \(10^{-3}\) with Alternate Selected status, but the record does not prove current execution or universal adequacy. |
| **Current implementation** | What the reviewed checkout accepts, calculates, records, addresses, reuses, publishes, or returns? | Whether the behavior is intended, deployed, operationally successful, or scientifically acceptable. | Current jobs code exposes `volume_convergence_tolerance`, applies a strict-less-than test, and omits that value from run identity. |
| **Target design** | What architecture, ownership, identity, materialization, or data flow is intended? | Whether the target is deployed or whether observed products satisfy it. | The system design guide assigns materialization to storage observation and treats important invalidating changes as identity changes. |
| **Checked-in unpinned prototype or implementation evidence** | What logic is present in a local checkout when an exact revision is unavailable? | Stable current behavior, deployed configuration, runtime success, or scientific acceptance. | The checked-in reconciler builds explicit tolerance inputs and observes manifests, but its unpinned presence does not prove deployment or complete checksum enforcement. |
| **Fixture evidence** | What one checked-in artifact set contains and whether its internal fields agree under inspection? | Production provenance, current storage state, source immutability, or representative scientific validity. | The build-model fixture supports inspection of one model manifest and raster realization, not production readiness. |
| **Case evidence** | What was observed under a recorded case scope, method, data, and conditions? | Universal thresholds, transfer to other reaches, or authority to change a selected decision. | Case-018 records bounded convergence-metric analysis under the EXP-014 heading, but it does not validate one threshold for every reach and product. |
| **Experiment plan evidence** | What comparison, factors, metrics, and procedure were proposed? | That the experiment ran or produced the claimed result. | The standalone EXP-014 file provides a method, while Case-018 contains the scoped completed observations cited by DR-028. |
| **Operational evidence** | What a named deployed version, invocation, storage observation, log, metric, or alert showed at a recorded time? | Intended methodology, broad validation, or behavior outside the observed version and conditions. | A runtime manifest plus independently observed assets could prove one materialization event, but no such observation is supplied merely by current source code. |
| **Presentation evidence** | What orientation, summary, demonstration, or result was communicated in the presentation's scope? | The underlying scientific contract or durable current behavior unless linked evidence establishes it. | A Phase 2 presentation can orient a reviewer to system progress, but current code, manifests, and runtime observations control detailed behavior claims. |
| **Inference** | What conclusion follows from named premises when no source states it directly? | A fact, decision, deployment state, or accepted policy. | Because tolerance is in complete manifest inputs but not the address identity, unequal thresholds can fail exact reuse and still target the same scenario address. |
| **Open question** | What conflict, missing authority, or unavailable evidence prevents a supported conclusion? | Permission to choose a convenient answer. | The project has not authorized whether convergence tolerance must move run identity or how old scenario generations should migrate. |

The six approved labels remain Scientific foundation, Selected methodology, Current implementation, Target design, Evidence or experiment, and Open question.
The additional terms in this table are required qualifiers within those labels.
For example, fixture, case, operational, and presentation evidence remain **Evidence or experiment**, while checked-in unpinned prototype evidence remains a qualified and revision-limited **Current implementation** observation.

## 2. Keep selected method separate from implemented parameter

Consider the statement, "The project uses a convergence tolerance of \(10^{-3}\)."
That sentence hides several different claims.

**Selected methodology:** DR-028 ALT-A selects \(10^{-3}\) with Alternate Selected status in the reviewed Decision Register.
This supports a statement about intended methodology within that record's scope and revision.

**Current implementation:** Current ND and KWSE input models default `volume_convergence_tolerance` to `0.001`, and the checked-in jobs construct `RunConfig` with the supplied value.
This supports a statement about the reviewed checkout.

**Current implementation, checked-in unpinned prototype evidence:** The local reconciler configuration also contains `1e-3` and sends it in ND and KWSE payloads.
Because the deployment checkout observation is unpinned, it supports only a qualified local-code statement.

**Evidence or experiment, operational evidence:** Proving what a deployed run used requires a versioned runtime manifest or payload, producer identity, and associated storage or execution evidence.
None of the three preceding sources proves that fact by itself.

**Evidence or experiment, acceptance evidence:** Proving the value is adequate for an intended use requires numerical, hydraulic, sensitivity, uncertainty, materialization, and authorized acceptance evidence.
The selected decision and code default do not supply that conclusion.

## 3. Keep target ownership separate from deployment

The target system design says the database records intent, storage holds objects, and the reconciliation loop observes whether the intended object is materialized.
It also says a job return value does not establish materialization.

That design can support these claims:

- storage observation should be the authoritative path to target materialization;
- desired and materialized state should remain separate;
- identity inputs must be available to the reconciler when it predicts addresses; and
- a scientifically invalidating identity change should produce another object rather than mutate the old one in place.

The same design cannot support these claims without separate evidence:

- the reconciler is deployed;
- every required asset and checksum is currently verified;
- retries are operationally idempotent;
- publication is atomic;
- an observed scenario is hydraulically adequate; or
- rollback restores a previously accepted methodology generation.

The checked-in reconciler files provide useful prototype evidence for how the target might be realized.
They do not close the deployment or acceptance boundary.

## 4. Keep code, schema, generated documentation, and runtime distinct

Within the Current implementation label, sources can still have different authority.

1. Executable code controls exact current behavior in the reviewed checkout.
2. Schemas control accepted and serialized shapes when the runtime uses those schemas.
3. Generated documentation describes the declared interface but remains subordinate when code differs.
4. Tests and fixtures show bounded expected examples, not every runtime path.
5. Runtime evidence shows what a named deployed revision did under one invocation.

For example, the scenario manifest schema records complete `RunScenarioInputs`.
The exact-reuse code compares those inputs at the predicted address.
The separate ND `existing_scenarios` adoption branch instead checks only reach ID, full model ID, run identity hash, and inclusive discharge range before the manifest enters adaptive state.
Generated text cannot broaden that comparison into asset re-observation when the code does not do so.
A successful fixture cannot prove that remote assets still exist.
Only a current storage observer can make that bounded materialization observation.

## 5. Separate one case from a general decision

Case and experiment evidence is strongest when its scope is narrow and explicit.
Record the reach, source data, method revision, solver build, settings, scenarios, comparison quantity, spatial and temporal support, referent, uncertainty, and observed result.

Case-018 can support a statement that candidate quasi-steady metrics were compared with modeler-selected times in that recorded work.
It cannot by itself support these broader claims:

- \(10^{-3}\) is adequate for every reach, flow regime, grid, boundary, and intended product;
- the metric closes full mass balance;
- local WSE and flux are stable whenever the domain-total ratio is small;
- the current implementation preserves every relevant diagnostic; or
- the selected threshold may be changed without a new decision and validation record.

An experiment plan can define a future evidence activity.
It must not be written as though the planned observations already exist.

## 6. Use operational evidence precisely

Operational evidence should name:

- deployment or image revision;
- invocation or request identity;
- start and end time;
- realized inputs and environment;
- logs, exit status, metrics, warnings, and failure details;
- published and independently observed assets;
- checksum and generation results;
- materialization state;
- monitoring window; and
- known missing observations.

An alert-free interval means only that configured alerts did not fire over that interval.
It does not prove every relevant check existed or that the accepted scientific envelope remained valid.

A returned scenario-manifest path is job-response evidence.
A readable manifest is storage-presence evidence.
A manifest plus independently verified required assets, checksums, identity, and generation consistency can support materialization.
None of these automatically grants scientific acceptance.

## 7. Label inference at the sentence where it occurs

Inference is necessary when a review connects facts that no single source states together.
The inference must name its premises and remain no stronger than them.

Use this structure:

> **Inference from current implementation:** Premise A and premise B imply consequence C under stated condition D.

For the threshold example:

> **Inference from current implementation:** Complete input inequality prevents exact reuse, while the address omits the tolerance and publication writes to that predicted address.
> A threshold-only change can therefore run again and replace or mix assets at the same address unless another generation or promotion control intervenes.

This inference does not prove that an overwrite occurred operationally.
That stronger claim would require runtime storage history or logs.

## 8. Assemble evidence by claim, not by document

A methodology-change review should build one row per claim.

| Claim | Controlling source | Supporting source | Missing evidence | Allowed conclusion |
| --- | --- | --- | --- | --- |
| \(10^{-3}\) is the selected threshold. | DR-028 and the Decision Register at the reviewed revision. | Case-018 as preliminary scoped evidence cited by the decision. | None for the bounded selection claim. | Selected methodology with Alternate Selected status. |
| The reviewed jobs default to `0.001`. | Current input models and constants. | Generated job documentation when consistent. | Runtime deployment revision. | Current checkout behavior only. |
| A threshold change moves run identity. | No current source supports this. | Target identity principles suggest invalidating inputs should move identity. | Authorized identity classification and implementation. | Open question, not a current fact. |
| An old-tolerance ND manifest can enter new-tolerance adaptive state through `existing_scenarios`. | Current ND adoption and adaptive-loop code. | CONF-009 and XW-021. | Runtime evidence if claiming a particular adoption event. | Current implementation risk and bounded inference about downstream influence. |
| A threshold-only change can collide at one address. | Current identity, address, exact-reuse, and publication code. | XW-017 and CONF-009. | Runtime evidence if claiming an actual collision. | Labeled design-risk inference. |
| A stricter threshold improves hydraulic products. | No supplied evidence. | A proposed validation plan could test it. | Representative numerical, sensitivity, and validation evidence. | Open question. |
| The change is ready to deploy. | Authorized readiness record. | Decision, implementation, validation, migration, rollout, rollback, ownership, and monitoring evidence. | Most required evidence in the hypothetical lab. | NOT READY. |

## 9. Do not resolve a source conflict by averaging

When sources disagree about the same question, preserve both with their labels and record an Open question.
Do not choose the newest date, most detailed prose, current code, or desired architecture automatically.
Authority depends on the claim type.

Current code can be correct evidence of implemented behavior while conflicting with selected methodology.
The decision remains controlling for intended methodology within its status, and the mismatch remains a readiness and governance problem.

## Common misconceptions

### "The target design says it, so the system does it"

Target design states intended architecture and ownership.
Deployment and runtime claims require operational evidence.

### "The code accepts the value, so the method is approved"

Input acceptance establishes an implementation capability.
It does not supply methodology authority or validation.

### "The case result proves the default"

A case supports its recorded scope.
Selection authority comes from the decision record, and general adequacy requires broader evidence.

### "The presentation is the source"

A presentation can summarize evidence and communicate orientation.
The durable decision, code, artifact, case, or runtime record controls the detailed claim.

### "This inference is obvious, so it is a fact"

An inference can be strong and useful while still requiring its premises and limitations to remain visible.

## Competency check

1. Name the source needed to establish selected convergence methodology, current default behavior, deployed value, and hydraulic adequacy.
2. Explain why the checked-in reconciler is not operational evidence.
3. Explain what Case-018 can and cannot contribute to a threshold review.
4. Write one labeled inference about same-address collision without claiming that a collision occurred.
5. Explain why a target materialization rule does not prove scientific acceptance.

## Further reading and source notes

- [Source Authority](../reference/source-authority.md) defines the controlling labels and qualifiers.
- [Decision-Code-Artifact Crosswalk](../reference/decision-code-artifact-crosswalk.md) traces project concepts across decisions, code, inputs, artifacts, evidence, and validation questions.
- [Conflicts and Open Questions](../reference/conflicts-and-open-questions.md) preserves unresolved authority and implementation gaps.
- [SDR-003](../reference/bibliography.md#sdr-003-domain-inflow-terrain-and-convergence-decisions), [JOB-007](../reference/bibliography.md#job-007-convergence-hot-start-and-solver-execution-paths), [SYS-001](../reference/bibliography.md#sys-001-system-design-guide), [SYS-006](../reference/bibliography.md#sys-006-checked-in-reconciler-kwse-planner-and-observer), and [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) provide the example source boundaries used here.
