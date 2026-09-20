# Team-Lead Review Checklist

A methodology change is ready only when its scientific rationale, software contracts, artifact generations, validation evidence, operational controls, ownership, and unresolved risks support one direct verdict for a named intended use.
This checklist is a decision aid rather than evidence that the current 2D FIM system satisfies every item.

## Why this matters

A scientifically reasonable change can still fail because a consumer was missed, an identity did not move, old and new artifacts collided, validation used the calibration data, a retry mixed generations, or no owner could stop and reverse rollout.
The review must therefore cover the complete change from decision authority through rollback.

## Prerequisites

Complete [Scientific Software Contracts](01-scientific-software-contracts.md), [Current, Target, and Evidence Boundaries](02-current-target-and-evidence-boundaries.md), [Validation Framework](../06-validation-and-qc/01-validation-framework.md), and [Sensitivity and Uncertainty](../06-validation-and-qc/03-sensitivity-and-uncertainty.md).
Use [XW-021](../reference/decision-code-artifact-crosswalk.md#xw-021-scientific-methodology-change-contract-and-readiness) to trace the project example.

## Learning objectives

After this chapter, the reader should be able to:

- lead a methodology-change review without reducing it to a code review;
- identify missing authority, contracts, consumers, migration, validation, or ownership;
- distinguish evidence required before rollout from conditions that can safely be completed during a bounded rollout;
- require explicit rollout stop and rollback actions; and
- issue `READY`, `READY WITH CONDITIONS`, or `NOT READY` without implying more evidence than the review contains.

## 1. Define the decision before reviewing the implementation

Record these fields at the top of the review.

- [ ] **Change title:** Use one stable name for the proposed methodology change.
- [ ] **Decision requested:** State exactly what approval is sought now.
- [ ] **Intended use:** Name the product, decision, users, reach or scenario scope, and uses that remain excluded.
- [ ] **Decision owner:** Name the role authorized to approve the scientific method.
- [ ] **Implementation owner:** Name the role responsible for code, schemas, configuration, and migration.
- [ ] **Operational owner:** Name the role responsible for rollout, monitoring, halt, rollback, and incident response.
- [ ] **Acceptance authority:** Name the role that evaluates evidence and issues the intended-use verdict.
- [ ] **Evidence cutoff:** Record the revisions, artifacts, cases, runtime window, and date included in the review.
- [ ] **Requested verdict:** Use only `READY`, `READY WITH CONDITIONS`, or `NOT READY`.

If any owner is unknown, record the gap rather than assigning it by inference.

## 2. Review scientific rationale and intended use

- [ ] State the problem the change is intended to solve with evidence rather than preference.
- [ ] State the current method and its exact decision status.
- [ ] State the proposed method, alternatives considered, and why the proposal is preferable for the intended use.
- [ ] Name every physical, numerical, statistical, and geospatial assumption introduced, removed, or changed.
- [ ] Name the quantities affected, with units, datums, spatial support, time support, scenario range, and sign conventions.
- [ ] State the consequences of false acceptance and false rejection.
- [ ] Identify calibration parameters and prevent them from compensating silently for terrain, datum, forcing, boundary, structure, or numerical errors.
- [ ] Define the domain of applicability and the conditions that require reassessment.
- [ ] Preserve every related Open question that the change does not resolve.

The rationale is incomplete if it relies only on runtime cost, visual plausibility, one successful case, or a default value already present in code.

## 3. Review input authority and data contracts

- [ ] Identify the authoritative source for every forcing, network, terrain, bathymetry, structure, roughness, boundary, initial-state, and validation input.
- [ ] Record immutable source identity or a versioned retrieval contract for every remote input.
- [ ] State horizontal CRS, axis order, linear units, transformation, and transformation accuracy where relevant.
- [ ] State vertical datum, unit, epoch where relevant, transformation, and transformation uncertainty.
- [ ] State raster shape, affine transform, bounds, resolution, cell registration, orientation, resampling, and mask compatibility rules.
- [ ] Define nodata, zero, dry, wet, invalid, and out-of-coverage semantics.
- [ ] Define vector topology, direction, adjacency, coverage, and indexing constraints.
- [ ] Validate values at the external boundary before scientific calculations or address derivation.
- [ ] State which missing or ambiguous input causes rejection, fallback, warning, or escalation.

Do not accept equal array shape, matching EPSG integers, or a shared unit string as complete compatibility evidence.

## 4. Review methodology, solver, and public contracts

- [ ] Link the proposed method to an authorized decision record and status.
- [ ] Identify every public job input, default, environment setting, schema, manifest field, warning, result, and generated document affected by the change.
- [ ] Define boundary-condition behavior mathematically or through version-specific solver semantics.
- [ ] Record solver name, executable version or build, image digest, relevant options, and hardware when it affects reproducibility.
- [ ] Define convergence metrics, thresholds, comparison operators, intervals, histories, full-balance evidence, and simultaneous-condition precedence.
- [ ] State which warnings and diagnostics are implemented, persisted, or absent.
- [ ] Preserve backward compatibility unless the authorized review approves a breaking change and migration.
- [ ] Identify every consumer that parses, addresses, caches, selects, transfers, composites, monitors, or interprets the changed object.
- [ ] Update generated documentation only through its generator when documentation is in scope.

An input override that lets code run a proposed value is implementation capability, not methodology approval.

## 5. Review identity, provenance, and compatibility

- [ ] Classify each changed field as identity, realization, provenance, acceptance, or operational state.
- [ ] Explain whether a changed value must produce a new address.
- [ ] Prove that scientifically unequal requests cannot collide after rounding, normalization, or omitted identity fields.
- [ ] Define exact reuse and any broader compatibility predicate separately.
- [ ] Require reuse to re-observe the assets, checksums, schema, generation, and acceptance state needed by the intended consumer.
- [ ] Record methodology revision, source versions, transformations, complete run settings, producer build, and parent artifact identities in provenance.
- [ ] Define compatibility among old producers, new producers, old consumers, new consumers, and mixed network dependencies.
- [ ] State whether historical artifacts remain interpretable, require metadata repair, require recomputation, or must be excluded.
- [ ] Define immutable-version intent and retention for both accepted and superseded generations.

If the change affects output meaning but the identity and address remain unchanged, the review must treat same-address overwrite and mixed-generation publication as blockers until another safe generation mechanism exists.

## 6. Review affected artifacts and consumers

- [ ] Inventory every model, ND scenario, KWSE scenario, selected library, transfer dependency, composite, export, cache, and acceptance record affected.
- [ ] Identify downstream and upstream dependency propagation through the prepared network.
- [ ] State which existing artifacts may remain valid and provide the compatibility evidence for that decision.
- [ ] State which artifacts require recomputation and how the migration inventory will prove completeness.
- [ ] Define required manifest assets, checksums, schema versions, dimensions, CRS, datums, nodata, and one-generation consistency.
- [ ] Identify external APIs, operators, analyses, or products that consume the changed outputs.
- [ ] Prevent a partial network from combining incompatible methodology generations unless the review explicitly validates and authorizes that mixed state.

## 7. Review sensitivity, verification, validation, and acceptance evidence

- [ ] Define software-verification tests against the changed contract.
- [ ] Define numerical-verification evidence for resolution, time behavior, convergence, conservation, and relevant solver settings.
- [ ] Separate calibration data from independent validation or use a justified cross-validation design.
- [ ] Define benchmark or observation comparability, quantities, datums, spatial and temporal support, flow or stage range, and uncertainty.
- [ ] Apply the Task 14 review heuristic beginning with downstream condition and domain, then terrain and topobathymetry, roughness and resolution, inflow distribution, wet-dry settings, and convergence controls unless evidence supports another order.
- [ ] Test material interactions rather than assuming independent additive effects.
- [ ] State predeclared metrics, thresholds, uncertainty allowances, sample scope, and pass or fail rules.
- [ ] Identify representative reach, boundary, structure, data-quality, and scenario classes.
- [ ] Retain failed, ambiguous, and out-of-scope results.
- [ ] State the evidence needed to transfer conclusions beyond the tested cases.

The selected convergence metric remains a storage-change proxy rather than full mass balance.
A stricter threshold is not automatically more accurate, more stable, or more suitable for the intended product.

## 8. Review observability and failure semantics

- [ ] Record process exit, termination request, terminate-to-kill escalation, raw return code, and final solver state where required.
- [ ] Persist convergence history, local-state diagnostics, full-balance terms, edge results, and simultaneous conditions needed by the acceptance policy.
- [ ] Distinguish warning, retryable failure, terminal failure, rejected scientific result, incomplete publication, materialization failure, and acceptance failure.
- [ ] Define structured logs, metrics, traces or identifiers, retention, dashboards, and alerts.
- [ ] Define drift checks for source data, method revision, solver build, identity, schema, and product distributions.
- [ ] Set monitoring thresholds and reassessment triggers before general rollout.
- [ ] Name the person or role who receives each alert and the required response time.

An empty warning list is not evidence that unimplemented checks passed.

## 9. Review atomicity, retries, and materialization

- [ ] Publish to an isolated generation or staging location.
- [ ] Distinguish a failed write attempt, partial publication, complete publication, and atomic publication in producer and observer records.
- [ ] Verify every required asset and checksum before promotion.
- [ ] Make the complete generation visible atomically, or atomically update a pointer to an immutable generation.
- [ ] Remove or quarantine failed staging content without exposing it to consumers.
- [ ] Prove retries are idempotent for equivalent requests.
- [ ] Reject or create a new generation for scientifically unequal requests.
- [ ] Re-observe required assets rather than inferring completeness from a return value or manifest alone.
- [ ] Record materialization separately from scientific acceptance.
- [ ] Detect deleted, stale, mixed, or incompatible assets and retract materialization when the contract fails.

The current manifest-last sequence can act as a bounded producer completion marker only after preceding copy calls return.
Assets become externally observable earlier, so a failure can leave partial publication.
Even complete publication under that sequence does not prove integrity, one-generation consistency, materialization, selected membership, validity, or acceptance.

## 10. Review migration and compatibility

- [ ] Inventory stored generations, selected-library memberships, transfer dependencies, and composites affected by the old method.
- [ ] Define whether migration is metadata-only, recomputation, revalidation, consumer upgrade, or retirement.
- [ ] Order producer, schema, observer, planner, consumer, and monitoring changes so no unsupported intermediate state is treated as ready.
- [ ] Provide a dry-run or read-only inventory method before changing artifacts.
- [ ] Preserve the old generation and its acceptance record until rollback requirements are satisfied.
- [ ] Define mixed-version rules during migration.
- [ ] Record migration progress and reconcile it against the complete affected inventory.
- [ ] Define a stop condition for unexpected collision, incompatible consumer, validation failure, or incomplete provenance.

## 11. Review rollout and rollback

- [ ] Start with a bounded shadow, canary, or representative comparison whose scope and users are explicit.
- [ ] Prevent experimental outputs from becoming selected members or production transfer sources before acceptance.
- [ ] Define rollout stages, promotion authority, entry criteria, exit criteria, stop triggers, and evidence retained at each stage.
- [ ] Compare old and new results using predeclared decision-relevant metrics and uncertainty treatment.
- [ ] Monitor scientific, operational, identity, publication, materialization, and consumer signals.
- [ ] Define the exact code, configuration, decision, identity, artifact, membership, and pointer changes required for rollback.
- [ ] Prove the previous generation remains readable and compatible with the restored consumers.
- [ ] Define how partially migrated downstream and upstream dependencies return to a consistent generation.
- [ ] Assign one rollback commander and the authority to halt rollout.

Rollback is incomplete if it restores code while consumers still reference artifacts created under the incompatible method.

## 12. Review governance and unresolved risk

- [ ] Record the decision and its status in the authoritative decision system.
- [ ] Record implementation, validation, rollout, rollback, and acceptance owners.
- [ ] Record every unresolved conflict with an owner, required evidence, decision date or review trigger, and effect on the verdict.
- [ ] Distinguish a risk accepted by authorized governance from a risk merely noticed by the review team.
- [ ] Record uses that remain prohibited after approval.
- [ ] Record when source, method, solver, environment, or evidence drift requires a new review.

No review participant should infer ownership from repository location or technical familiarity.

## 13. Issue one direct verdict

The verdict applies only to the named change, intended use, revisions, evidence scope, rollout stage, and date.

### `READY`

Use `READY` only when all required decision, contract, compatibility, implementation, identity, provenance, migration, software verification, numerical verification, validation, materialization, rollout, rollback, monitoring, ownership, and acceptance evidence has passed.
Every applicable acceptance criterion must have an authorized result.
No unresolved risk may invalidate the stated use or prevent safe rollback.

Required evidence includes:

- an authorized methodology decision;
- complete implemented contracts and affected-consumer updates;
- collision-safe identity and atomic publication behavior;
- passed software and numerical verification;
- passed intended-use validation or authorized benchmark evidence with uncertainty and sensitivity;
- observed materialization under the new contract;
- completed or proven migration;
- operational monitoring and failure response;
- tested rollback to a retained compatible generation; and
- named owners and an authorized acceptance record.

### `READY WITH CONDITIONS`

Use `READY WITH CONDITIONS` only when all evidence required for the specifically permitted bounded use has passed and the remaining conditions are explicit, owned, observable, time-bounded, and enforced before broader use.
The verdict must name the permitted scope, prohibited scope, conditions, owner, deadline or trigger, evidence required to close each condition, and automatic response if a condition fails.

Appropriate conditions can include a bounded canary limit, a monitoring period, or a consumer migration that is not needed inside the permitted scope.
Do not use this verdict to defer missing methodology authority, unbounded scientific validity, unknown identity collisions, missing rollback, absent acceptance criteria, or unknown ownership.

### `NOT READY`

Use `NOT READY` when any required authority or evidence is missing, conflicting, failed, outside scope, or unable to protect the intended use.
State the blocking gaps, their impact, responsible owner if known, and the smallest evidence or change that could support a new review.

Insufficient evidence is a reason for `NOT READY` in this three-verdict methodology-change gate.
This usage does not replace the more detailed diagnostic verdict vocabulary in [Validation Framework](../06-validation-and-qc/01-validation-framework.md).

## 14. Use a concise review record

The final review should contain these sections:

1. **Verdict and permitted use.** State the verdict first.
2. **Change and authority.** Name the current method, proposed method, decision status, and owners.
3. **Scientific rationale and assumptions.** State why the change is needed and its domain of applicability.
4. **Contracts and consumers.** Summarize inputs, references, grids, nodata, boundaries, solver, public interfaces, and affected consumers.
5. **Identity, provenance, artifacts, and compatibility.** State generation and reuse behavior.
6. **Validation and sensitivity evidence.** State passed, failed, unavailable, and out-of-scope evidence.
7. **Operations.** State observability, failure, atomicity, retries, materialization, rollout, and rollback.
8. **Conditions and unresolved risks.** Name owners, triggers, and prohibited uses.

## Example verdict boundary

A proposed convergence-threshold change with a plausible rationale, an accepted input field, and one bounded case is still `NOT READY` when the selected decision is unchanged, the threshold is omitted from run identity, the same address can receive unequal generations, representative validation is absent, migration and rollback are undefined, and owners have not accepted the residual risk.

The direct verdict does not say the proposal is scientifically wrong.
It says the evidence and controls do not yet authorize the intended rollout.

## Competency check

1. Explain why decision authority, acceptance authority, and operational ownership may be different roles.
2. Give one condition appropriate for `READY WITH CONDITIONS` and one blocker that requires `NOT READY`.
3. Explain why a threshold-only change requires consumer, identity, migration, and rollback review even when the input schema already accepts the value.
4. State the evidence that distinguishes a published generation from a materialized and accepted generation.
5. Explain how a rollback can fail when old and new artifacts share one address.

## Further reading and source notes

- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations), [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability), and [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) support the verification, validation, uncertainty, acceptance, and evidence requirements.
- [XW-018](../reference/decision-code-artifact-crosswalk.md#xw-018-intended-use-validation-and-acceptance) preserves the existing intended-use validation boundary.
- [XW-020](../reference/decision-code-artifact-crosswalk.md#xw-020-sensitivity-and-uncertainty-evidence) preserves the established uncertainty taxonomy and sensitivity heuristic.
- [CONF-017](../reference/conflicts-and-open-questions.md#conf-017-intended-use-validation-and-acceptance-policy), [CONF-018](../reference/conflicts-and-open-questions.md#conf-018-sensitivity-priorities-and-uncertainty-propagation), and [CONF-019](../reference/conflicts-and-open-questions.md#conf-019-methodology-change-identity-and-migration-ownership) identify unresolved project-wide ownership and policy.
