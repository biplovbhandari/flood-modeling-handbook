# Lab 13: Review a Methodology Change

This lab asks you to lead a complete scientific change review for a hypothetical convergence-threshold proposal.
The packet is synthetic except where it explicitly cites a current project source.
It does not authorize a Decision Register, code, configuration, artifact, deployment, or production change.

## Prerequisites

Complete Stage 7 and all three Stage 8 chapters.
Read [Source Authority](../reference/source-authority.md), [XW-002](../reference/decision-code-artifact-crosswalk.md#xw-002-quasi-steady-termination), [XW-017](../reference/decision-code-artifact-crosswalk.md#xw-017-scenario-identity-publication-and-materialization), [XW-018](../reference/decision-code-artifact-crosswalk.md#xw-018-intended-use-validation-and-acceptance), [XW-020](../reference/decision-code-artifact-crosswalk.md#xw-020-sensitivity-and-uncertainty-evidence), and [XW-021](../reference/decision-code-artifact-crosswalk.md#xw-021-scientific-methodology-change-contract-and-readiness).

## Execution label

**Core inspection:** Complete the review from the supplied packet and cited sources.
Do not edit project sources, run a solver, mutate storage, or treat the solution as production authorization.

## Scenario

An internal proposal asks to change the volume-convergence tolerance from the selected and current default value \(10^{-3}\) to the hypothetical value \(5 \times 10^{-4}\).
The stated goal is to reduce the chance that an upstream transfer scenario stops while local WSE is still changing.
The proposed value is synthetic and has no project authority.

The requested rollout would apply the new tolerance to all new ND and KWSE requests and would allow existing scenarios to remain in selected libraries unless a consumer happens to request them again.
The proposal calls this a backward-compatible configuration change because the public job inputs already accept the value.

## Supplied evidence packet

### A. Decision and scientific evidence

| Item | Supplied evidence |
| --- | --- |
| Current selected method | DR-022 ALT-G selects volume convergence and DR-028 ALT-A selects \(10^{-3}\), both with Alternate Selected status in the reviewed Decision Register. |
| Decision record basis | DR-028 describes the selection as preliminary and cites Case-018 work under EXP-014. |
| Proposed decision | No approved Decision Register update exists for \(5 \times 10^{-4}\). |
| Proposed rationale | Two synthetic transfer scenarios had local WSE changes that a reviewer considered material after their domain-total storage-change ratio first fell below \(10^{-3}\). |
| Proposed evidence | No exact manifests, grids, local histories, full-balance terms, edge details, observations, uncertainty analysis, or representative-reach sample is supplied for those two synthetic scenarios. |
| Intended use | New ND and KWSE libraries used for stage transfer and composite FIM production. |
| Acceptance policy | No authorized intended-use policy or tolerance for the proposed comparison is supplied. |

### B. Current implementation evidence

| Item | Supplied evidence |
| --- | --- |
| Public input | Current ND and KWSE input models expose `volume_convergence_tolerance` and default it to `0.001`. |
| Solver logic | The watcher terminates when the final storage-change ratio is strictly less than the supplied tolerance. |
| Metric scope | The ratio compares positive-depth storage at consecutive saved outputs with inflow volume over the output interval and does not include outflow or all source and sink terms. |
| Simultaneous condition | When convergence and a disallowed edge violation are both true on one saved grid, the stored reason is `volume_convergence`. |
| Run identity | The current run identity contains the solver enum and baked Decision Register revision, but not convergence tolerance, executable build, image digest, full run configuration, or hardware. |
| Address | Scenario addresses include reach, model identity portion, run identity hash, boundary realization, and discharge, but not convergence tolerance. |
| Exact target reuse | At the predicted address, current exact reuse requires equality of complete `RunScenarioInputs` and therefore rejects an old-tolerance manifest for a new-tolerance request. |
| ND caller-supplied adoption | The separate `existing_scenarios` path adopts a schema-valid manifest when reach ID, full model ID, run identity hash, and discharge within the inclusive requested range match. It does not compare convergence tolerance, other run settings, boundary geometry or slope, hot start, or referenced-asset existence. |
| ND adoption effects | An adopted manifest enters the `done` set without rerunning. It can become the minimum-discharge reference, contribute to proposal and re-judgment calculations, become an accepted or current search position, and supply depth provenance for a later hot start. |
| Publication | A new run copies depth, inundation, STL, optional Zarr when available, and then the manifest into final locations without directory-level staging, atomic promotion, or rollback. Each successful copy can make another output externally observable, so failure can leave partial publication. |
| Manifest evidence | The manifest records complete inputs, final ratio, termination reason, assets, and checksum prefixes, but not complete convergence history, full balance, simultaneous suppressed edge detail, raw return code, or complete environment identity. |

### C. Target and checked-in prototype evidence

| Item | Supplied evidence |
| --- | --- |
| Target identity principle | The target system design says a change important enough to invalidate an object should generally move identity and address. |
| Target materialization | The target design requires storage observation rather than a job return value to establish materialization. |
| Checked-in reconciler | An unpinned local reconciler setting sends one convergence tolerance to both ND and KWSE payloads and predicts run identity without that tolerance. |
| Evidence boundary | The unpinned local code does not prove deployment, current runtime configuration, complete checksum verification, hydraulic acceptance, or safe migration. |

### D. Hypothetical validation proposal

The proposal offers this synthetic study:

1. Run the current and proposed thresholds on three convenient reaches.
2. Compare final maximum depth and runtime.
3. Pass if the proposed threshold changes maximum depth by less than a synthetic reviewer-chosen limit and increases runtime by less than a synthetic reviewer-chosen percentage.
4. Reuse current terrain, boundaries, hot starts, save intervals, and grid resolutions without testing interactions.
5. Use the same results both to tune the proposed limit and to declare validation success.

No downstream-stage, domain, terrain, topobathymetry, roughness, resolution, inflow-distribution, wet-dry, hot-start, save-interval, solver-build, local-WSE, full-balance, edge, extent, or observation comparison is proposed.

### E. Hypothetical rollout and ownership proposal

| Item | Supplied proposal |
| --- | --- |
| Rollout | Change the controller setting for all new work after the three-reach study. |
| Existing artifacts | Leave old and new scenarios together because exact target reuse will detect unequal inputs. The proposal does not analyze the separate limited ND `existing_scenarios` adoption path. |
| Migration | Recompute only scenarios requested again. |
| Monitoring | Count completed jobs and average runtime. |
| Rollback | Restore the old controller setting. |
| Decision owner | Not named. |
| Acceptance authority | Not named. |
| Migration owner | Not named. |
| Rollback owner | Not named. |
| Retention | No requirement preserves the prior accepted artifact generation. |

## Part A: Frame the change and authority

State:

1. The current selected method and status.
2. The hypothetical proposed method.
3. The intended use and excluded uses.
4. The decision that must be approved before code or configuration rollout.
5. The decision owner, acceptance authority, implementation owner, operational owner, migration owner, and rollback owner, using `Unassigned` where the packet supplies no authority.

## Part B: Identify every affected contract and consumer

Create a table with these columns:

| Area | Current or supplied evidence | Required change or evidence | Failure if omitted |
| --- | --- | --- | --- |

Include at least:

- Decision Register authority.
- Public ND and KWSE inputs and defaults.
- Controller configuration.
- Run identity and scenario address.
- Exact target reuse predicate.
- ND caller-supplied `existing_scenarios` adoption predicate.
- Manifest and convergence evidence.
- Publication and asset checksums.
- Materialization observer.
- Selected ND and KWSE membership.
- Stage-transfer dependencies.
- Composite products and exports.
- Validation and acceptance records.
- Monitoring, retry, and failure semantics.

## Part C: Analyze identity, compatibility, and reuse

Answer these questions:

1. Is the proposal scientifically backward compatible merely because the schema accepts the new value?
2. What does exact target reuse do when the manifest at the predicted address contains the old tolerance and the new request contains the proposed tolerance?
3. What does the separate ND `existing_scenarios` path compare, and why can it re-adopt that old-tolerance manifest directly?
4. How can an old-tolerance adopted scenario affect the minimum reference, proposal curves, re-judgment, current search position, hot start, and eventual selected-membership reasoning?
5. Why can exact input inequality prevent exact reuse without preventing same-address overwrite or mixed-generation publication?
6. Which fields should be considered for run identity or another immutable generation key?
7. Which old and new consumers can safely share artifacts, and what evidence would establish that compatibility?
8. What must a retry or caller-supplied adoption path observe before it can return or use an existing generation idempotently?

Label direct facts as **Current implementation** or **Target design**.
Label the collision consequence as an **Inference from current implementation**.

## Part D: Critique and replace the validation plan

Explain why the supplied three-reach study cannot establish readiness.
Then propose the smallest defensible validation program for the stated stage-transfer and composite use.

Your program must include:

- predeclared acceptance criteria and authorized acceptance authority;
- representative reach, boundary, data-quality, and scenario classes;
- local WSE, depth, extent, flux, full-balance, edge, and convergence-history evidence;
- cold-start or alternative-hot-start evidence where initial-condition sensitivity is material;
- downstream-condition and domain sensitivity before lower-priority convergence controls unless evidence justifies another order;
- terrain, topobathymetry, roughness, resolution, inflow, wet-dry, save-interval, and solver-build controls or interactions;
- independent observation or a clearly scoped benchmark with datum, support, timing, and uncertainty;
- separation of tuning data from validation evidence;
- operational, identity, publication, and materialization checks; and
- retained failed and ambiguous results.

Do not invent a universal pass threshold.

## Part E: Design rollout, migration, and rollback

Provide an ordered plan that includes:

1. Decision approval and contract versioning.
2. Collision-safe identity or generation behavior.
3. Producer, schema, observer, planner, consumer, and monitoring changes.
4. A read-only inventory of affected models, scenarios, every `existing_scenarios` input, selected memberships, transfer dependencies, and composites, classified by recorded convergence tolerance and compatibility evidence.
5. A bounded shadow or canary comparison that cannot become a production transfer source before acceptance.
6. Recalculation, revalidation, or exclusion rules for old artifacts, including a rule that prevents limited ND adoption from admitting an old-tolerance manifest into new-tolerance adaptive state without authorized compatibility evidence.
7. Atomic publication and idempotent retry behavior.
8. Materialization observation with required asset and checksum verification.
9. Promotion criteria, halt triggers, and monitoring.
10. Rollback of decision, configuration, code, identities or pointers, selected memberships, transfer dependencies, and composites.
11. Retention and compatibility evidence for the prior generation.
12. Named owners for every irreversible or production-affecting step.

The plan is a review artifact only.
Do not write commands that change production.

## Part F: Issue the direct verdict

Choose one verdict from [Team-Lead Review Checklist](../07-system-design/03-team-lead-review-checklist.md):

- `READY`;
- `READY WITH CONDITIONS`; or
- `NOT READY`.

State the verdict first.
Then state:

1. The blocking or conditioning evidence.
2. Why the verdict applies to the intended use.
3. The smallest evidence and ownership set that could change the verdict.
4. The uses that remain prohibited.

Do not use `READY WITH CONDITIONS` to defer missing methodology authority, collision-safe identity, representative validation, rollback, acceptance criteria, or ownership.

## Deliverable

Submit a review with these sections:

1. Verdict and intended use.
2. Change, decision status, and owners.
3. Contract and consumer impact table.
4. Identity, compatibility, reuse, and provenance analysis.
5. Validation and sensitivity plan.
6. Rollout, migration, monitoring, and rollback plan.
7. Unresolved risks and prohibited uses.

## Competency criteria

The lab is complete when the answer:

- preserves DR-022 and DR-028 status and does not treat the hypothetical value as selected;
- traces the proposal through code, inputs, artifacts, consumers, identities, provenance, validation, rollout, rollback, and ownership;
- distinguishes exact target reuse from limited ND caller-supplied adoption;
- explains how limited ND adoption can directly reuse an old-tolerance manifest and influence reference, proposal, re-judgment, hot-start, or membership reasoning;
- explains why exact input inequality and same-address collision can coexist;
- rejects schema acceptance as proof of scientific backward compatibility;
- separates publication, materialization, selected membership, and acceptance;
- applies the established sensitivity heuristic and uncertainty taxonomy;
- defines collision-safe migration, atomic publication, idempotent retry, and complete rollback requirements;
- names unassigned owners rather than inventing authority;
- issues a direct verdict with evidence requirements; and
- does not authorize a production change.

After completing the lab, compare the reasoning with [Lab 13 Solution](solutions/lab-13-review-a-methodology-change-solution.md).
