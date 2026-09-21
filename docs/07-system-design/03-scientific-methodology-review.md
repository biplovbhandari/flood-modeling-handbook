# Scientific Methodology Review

A methodology change is ready only when its scientific meaning, software contracts, artifact generations, validation evidence, operational controls, responsibility assignments, and unresolved risks support one direct verdict for a named intended use.
This chapter is a decision aid and does not supply evidence that a change satisfies any item.

## Why this topic matters

A scientifically reasonable change can still fail when a consumer is missed, identity does not change, calibration evidence is mistaken for validation, publication mixes generations, or rollback cannot restore compatible artifacts.
A complete review follows the change from scientific rationale through implementation, migration, operation, and acceptance.

## Prerequisites

Read [Scientific Software Contracts](01-scientific-software-contracts.md), [Evidence Boundaries in Scientific Software](02-current-target-and-evidence-boundaries.md), [Validation Framework](../06-validation-and-qc/01-validation-framework.md), and [Sensitivity and Uncertainty](../06-validation-and-qc/03-sensitivity-and-uncertainty.md).

## Learning objectives

After this chapter, the reader should be able to:

- review a methodology change without reducing it to a software diff;
- identify missing decisions, contracts, consumers, evidence, migration, or responsibility;
- distinguish evidence required before any use from evidence that can be gathered in a bounded shadow stage;
- require explicit stop and rollback actions; and
- issue `READY`, `READY WITH CONDITIONS`, or `NOT READY` for a precisely stated scope.

## Define the decision and responsibilities

Record these fields at the beginning of the review:

- **Change title:** Use one stable name for the proposed change.
- **Decision requested:** State exactly what permission or adoption is being considered.
- **Intended use:** Name the product, users, physical and scenario scope, and excluded uses.
- **Method decision responsibility:** Name the accountable person or group for choosing the scientific method.
- **Implementation responsibility:** Name the accountable person or group for software, schemas, configuration, and migration.
- **Operational responsibility:** Name the accountable person or group for rollout, monitoring, halt, rollback, and incident response.
- **Acceptance responsibility:** Name the accountable person or group that applies the criteria and issues the verdict.
- **Evidence cutoff:** Record the revisions, artifact generations, observations, runtime window, and date included.
- **Requested verdict:** Use only `READY`, `READY WITH CONDITIONS`, or `NOT READY`.

One person or group can hold several responsibilities when appropriate.
Every assignment must be explicit, and missing responsibility is a gap rather than an invitation to infer ownership.

## Review scientific meaning and intended use

- [ ] State the problem the change is intended to solve using evidence rather than preference.
- [ ] State the existing method and the decision record that defines it.
- [ ] State the proposed method, alternatives considered, and expected decision benefit.
- [ ] Name every physical, numerical, statistical, and geospatial assumption introduced, removed, or changed.
- [ ] Name affected quantities with units, datums, spatial support, time support, scenario range, and sign conventions.
- [ ] State the consequences of false acceptance and false rejection.
- [ ] Identify calibration parameters and prevent them from compensating for input, structure, boundary, or numerical errors.
- [ ] Define the applicability domain and reassessment triggers.
- [ ] Preserve open questions that the change does not resolve.

Runtime cost, visual plausibility, one successful example, or a default already present in software is not sufficient scientific rationale.

## Review input authority and data contracts

- [ ] Identify an authoritative or governed source for every forcing, network, terrain, bathymetry, structure, roughness, boundary, initial-state, and validation input.
- [ ] Record immutable source identity or a versioned retrieval contract for every remote input.
- [ ] State horizontal reference, axis order, units, transformation, and transformation applicability.
- [ ] State vertical datum, units, epoch when relevant, transformation, and transformation uncertainty.
- [ ] State raster shape, transform, bounds, resolution, registration, orientation, resampling, and mask rules.
- [ ] Define nodata, zero, dry, wet, invalid, and out-of-coverage semantics.
- [ ] Define vector topology, direction, adjacency, coverage, and index constraints.
- [ ] Validate values at external boundaries before calculation and identity derivation.
- [ ] State which missing or ambiguous inputs cause rejection, fallback, warning, or escalation.

Equal array shape, matching reference names, or a shared unit string is not complete compatibility evidence.

## Review method, solver, and interface contracts

- [ ] Link the proposed method to an authorized decision and status.
- [ ] Identify every input, default, configuration source, schema, record field, warning, result, and generated reference affected by the change.
- [ ] Define boundary behavior mathematically or through version-specific solver semantics.
- [ ] Record solver name, executable build, relevant options, and environment when it affects reproducibility.
- [ ] Define convergence metrics, thresholds, operators, intervals, histories, balance evidence, and simultaneous-condition precedence.
- [ ] State which warnings and diagnostics are implemented, persisted, or absent.
- [ ] Preserve backward compatibility unless the decision explicitly approves a breaking change and migration.
- [ ] Identify every consumer that parses, addresses, caches, selects, transfers, combines, monitors, or interprets the changed object.

An accepted input value demonstrates software capability rather than scientific approval.

## Review identity, provenance, and compatibility

- [ ] Classify each changed field as identity, realization, provenance, acceptance, or operational state.
- [ ] Explain whether the changed value requires a new address or immutable generation.
- [ ] Demonstrate that unequal requests cannot collide through rounding, normalization, or omission.
- [ ] Define exact reuse and any broader compatibility predicate separately.
- [ ] Require reuse to observe the assets, integrity values, schema, generation, and acceptance state needed by the consumer.
- [ ] Record method revision, source identities, transformations, complete settings, producer build, and parent objects.
- [ ] Define compatibility among old and new producers, consumers, and dependencies.
- [ ] State whether historical artifacts remain interpretable, require repair, require recomputation, or must be excluded.
- [ ] Define retention for accepted and superseded generations.

If a change affects output meaning while identity and address remain unchanged, treat overwrite and mixed-generation publication as blockers until another safe generation mechanism exists.

## Review artifacts and consumers

- [ ] Inventory every model, scenario, selected library, transfer dependency, composite, export, cache, and acceptance record affected.
- [ ] Identify upstream and downstream dependency propagation.
- [ ] State which existing artifacts remain valid and provide compatibility evidence.
- [ ] State which artifacts require recomputation and how the migration inventory will prove completeness.
- [ ] Define required assets, integrity values, schemas, dimensions, coordinate references, datums, nodata, and one-generation consistency.
- [ ] Identify external interfaces and analyses that consume changed outputs.
- [ ] Prevent partial collections from combining incompatible generations unless the review validates and authorizes the mixed state.

## Review verification, validation, sensitivity, and acceptance

**Scientific foundation:** [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations), [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability), and [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) collectively support claim-appropriate verification, validation, uncertainty, and intended-use evidence.

- [ ] Define software-verification checks against the changed contract.
- [ ] Define numerical-verification evidence for resolution, convergence, conservation, and relevant solver settings.
- [ ] Separate calibration data from independent validation or justify a cross-validation design.
- [ ] Define benchmark and observation comparability, quantities, datums, support, scenario range, and uncertainty.
- [ ] Test material interactions rather than assuming independent additive effects.
- [ ] State predeclared metrics, thresholds, uncertainty allowances, sample scope, and pass or fail rules.
- [ ] Include representative boundary, structure, data-quality, and scenario classes for the intended use.
- [ ] Retain failed, ambiguous, and out-of-scope results.
- [ ] State what evidence is required to transfer conclusions beyond the sample.

A stricter numerical threshold is not automatically more accurate or more suitable for the product.

## Review observability and failure meaning

- [ ] Record process state, termination, escalation, raw failure details, and final solver state where required.
- [ ] Persist convergence history, local diagnostics, balance terms, boundary results, and simultaneous conditions needed by acceptance criteria.
- [ ] Distinguish warning, retryable failure, terminal failure, rejected scientific result, incomplete publication, materialization failure, and acceptance failure.
- [ ] Define structured logs, metrics, trace identifiers, retention, dashboards, and alerts.
- [ ] Define drift checks for sources, method revision, solver build, identity, schema, and product distributions.
- [ ] Set monitoring thresholds and reassessment triggers before broader use.
- [ ] Assign response responsibility and response time to every signal.

An empty warning list does not establish that unimplemented checks passed.

## Review publication, retries, and materialization

- [ ] Publish to an isolated generation or staging location.
- [ ] Distinguish failed writes, partial publication, complete publication, and atomic publication.
- [ ] Verify every required artifact and integrity value before promotion.
- [ ] Make the complete generation visible atomically or atomically update a pointer to it.
- [ ] Keep failed staging content invisible to ordinary consumers.
- [ ] Demonstrate idempotent retries for equivalent requests.
- [ ] Reject or create a new generation for unequal requests.
- [ ] Observe required assets rather than infer completeness from a response or record alone.
- [ ] Record materialization separately from scientific acceptance.
- [ ] Detect deleted, stale, mixed, or incompatible assets and retract materialization when the contract fails.

Complete publication does not by itself prove integrity, materialization, validity, or acceptance.

## Review migration, rollout, and rollback

- [ ] Inventory affected generations, memberships, dependencies, composites, and consumers.
- [ ] Define whether migration requires metadata repair, recomputation, revalidation, consumer updates, or retirement.
- [ ] Order producer, schema, observer, selector, consumer, and monitoring changes so no unsupported intermediate state is treated as ready.
- [ ] Provide a read-only inventory before changing artifacts.
- [ ] Define mixed-version rules and reconcile progress against the complete inventory.
- [ ] Start with a bounded shadow or comparison stage whose scope is explicit.
- [ ] Prevent experimental outputs from becoming selected dependencies before acceptance.
- [ ] Define entry criteria, exit criteria, stop triggers, and retained evidence for each stage.
- [ ] Preserve the previous compatible generation and acceptance record.
- [ ] Define exact decision, software, configuration, identity, artifact, membership, pointer, and monitor changes for rollback.
- [ ] Demonstrate that restored consumers can read the restored generation.
- [ ] Assign halt and rollback responsibility.

Rollback is incomplete when software is restored but consumers still select artifacts created under an incompatible method.

## Review responsibility and unresolved risk

- [ ] Record the method decision and status in the authoritative decision system.
- [ ] Record decision, implementation, validation, operation, rollback, and acceptance responsibilities.
- [ ] Give every unresolved risk a responsibility assignment, required evidence, review trigger, and effect on the verdict.
- [ ] Distinguish a formally accepted risk from a risk that has only been identified.
- [ ] Record prohibited uses after approval.
- [ ] Record which source, method, solver, environment, or evidence changes require a new review.

Responsibility must not be inferred from technical familiarity or file ownership.

## Issue one direct verdict

The verdict applies only to the named change, intended use, revisions, evidence cutoff, permitted stage, restrictions, and date.
It is a scientific and operational decision about that packet rather than a general status label for a person or organization.

### `READY`

Use `READY` only when every required decision, contract, compatibility, identity, provenance, migration, verification, validation, uncertainty, materialization, rollout, rollback, monitoring, responsibility, and acceptance criterion has passed for the stated use.
No unresolved risk may invalidate the permitted use or prevent safe rollback.

### `READY WITH CONDITIONS`

Use `READY WITH CONDITIONS` only when all evidence required for the specifically permitted bounded use has passed.
Every remaining condition must be explicit, assigned, observable, time-bounded, enforced, and paired with an automatic response if it fails.
The verdict must name the permitted scope and prohibited broader use.

Missing method authority, unknown scientific validity for the permitted use, ambiguous identity, absent rollback, undefined acceptance criteria, or unassigned material responsibility requires `NOT READY`.

### `NOT READY`

Use `NOT READY` when required evidence or responsibility is missing, conflicting, failed, outside scope, or unable to protect the intended use.
State each blocking gap, its impact, the responsibility if assigned, and the smallest evidence or change that could support another review.

## Synthetic methodology-change packet

The packet below is a self-contained, non-authoritative teaching example.
It makes no claim about an external implementation or operating environment.

### Change and intended use

**Applied example:** The proposed change replaces a slope-based downstream boundary with a spatial stage field for the synthetic `R-200` scenario at 175 cubic metres per second.
The permitted use requested is a shadow comparison for screening water-surface elevation within one kilometre upstream of the downstream boundary.
Operational selection, transfer to other reaches, other discharges, and public decision use remain prohibited.

The spatial field comes from a complete synthetic downstream scenario with a confluence stage of 102.4 m in `VD-1`.
The field and target model share a 10 m grid, horizontal reference, `VD-1`, units, wet mask policy, and generation-specific provenance.

### Responsibilities and evidence cutoff

The packet lists four required responsibility functions:

- method decision responsibility for scientific adoption and scope;
- implementation responsibility for typed inputs, identity, verification, and migration;
- operational responsibility for publication, materialization, monitoring, halt, and rollback; and
- independent acceptance responsibility for applying criteria and recording the verdict.

The packet does not identify an accountable person or group for any function.
It also contains no evidence that a governing authority granted any function its stated decision rights.

The evidence cutoff contains a proposed contract, summary numerical values, three values described as observation residuals, and a description of publication controls.
It does not include immutable software or data provenance, executed test output, runtime records, observed artifacts, authorization records, or evidence that the proposed controls are enforced.

### Proposed contract and identity

The proposed boundary input would require a spatial water-surface field, units, vertical datum, source scenario identity, transform, bounds, wet mask, nodata policy, and integrity value.
The proposed validation rule would reject an input when a required reference or identity is missing.

The proposed scenario identity would include the boundary method, exact source generation, field integrity, grid, mask policy, discharge, initial state, solver build, and numerical settings.
The proposed scenario record would include realized inputs, source lineage, required artifacts, integrity values, diagnostics, publication state, materialization state, and linked acceptance state.

No executed verification demonstrates that invalid inputs are rejected, identities change as proposed, unequal requests cannot collide, or the record contains the required realized values.

### Supplied numerical and observation claims

The stated numerical criteria require a 95th percentile absolute water-surface difference no greater than 0.05 m between the 10 m grid and a 5 m comparison, an absolute final water-balance residual no greater than 1 percent, and no connected wet component at an unintended edge.
The summary reports 0.04 m, 0.6 percent, and no unintended wet-edge contact.

For the reported balance percentage, cumulative volumes cover model time \(t_0=0\) to the final recorded state at \(t_1=6\) h, and the signed residual is:

\[
R_V=100\frac{\Delta S-\left(V_{in}-V_{out}+V_{source}-V_{sink}\right)}{V_{in}}
\]

Here, \(\Delta S=S(t_1)-S(t_0)\), all volume terms are in cubic metres, and \(V_{in}>0\).
Inflow and source volumes are positive additions, while outflow and sink volumes are positive removals.
A positive result means storage increased more than the recorded net input accounts for, and the criterion uses \(\lvert R_V\rvert\).

| \(\Delta S\) | \(V_{in}\) | \(V_{out}\) | \(V_{source}\) | \(V_{sink}\) | Signed \(R_V\) | Absolute residual |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 752,680 m3 | 3,780,000 m3 | 3,050,000 m3 | 0 m3 | 0 m3 | 0.60% | 0.60% |

The packet does not provide the spatial mask, cell population, weighting, locations, or support used to calculate the 95th percentile.
It does not provide source provenance, grid-alignment evidence, the refinement procedure, raw results, or the edge scan output.
The summary values therefore do not establish that numerical verification passed.

The packet lists three residuals of 0.03 m, -0.04 m, and 0.02 m and states a nominal observation uncertainty of 0.05 m.
It does not provide observation locations, spatial or temporal support, source provenance, selection rationale, calibration independence, datum-transformation evidence, or an uncertainty calculation that connects the 0.05 m value to the 0.08 m criterion.
The values therefore cannot support a validation pass.

**Evidence note:** The deliberately incomplete values show why favorable summary numbers do not establish verification or validation when their support and provenance are missing.

### Proposed publication, migration, monitoring, and rollback controls

The packet proposes generation-specific staging, integrity verification, atomic promotion to a shadow-only pointer, independent materialization observation, retention of the previous generation, and pointer-based rollback.
It also proposes monitoring source identity, datum, grid, integrity, materialization, balance residual, wet-edge state, and output distribution.

No executed rehearsal, observer record, selection guard, expiry mechanism, alert action, or rollback result is supplied.
Writing a 30-observation or 30-day limit in a review does not enforce expiry.
Calling a pointer shadow-only does not prevent a consumer from selecting it unless an implemented and verified control enforces that boundary.

### Verdict

The verdict is `NOT READY` for the requested shadow comparison and for every broader use.
Accountable responsibility is unassigned, validation support is incomplete, implementation and operational behavior are unexecuted, and the proposed scope and expiry controls are not enforceable from the supplied evidence.

The smallest packet that could support a new review must add:

1. Named accountable people or groups for method decision, implementation, operation, rollback, and independent acceptance, with evidence of their decision rights.
2. Immutable source, method, software-build, input, and artifact provenance for the exact comparison.
3. Executed software and numerical verification with recorded inputs, environment, outputs, spatial support, refinement procedure, edge evidence, and the defined balance calculation.
4. Observation locations, time and spatial support, provenance, selection rationale, calibration independence, datum transformation, and a justified uncertainty calculation for validation.
5. Implemented and executed controls that prevent operational selection, enforce the bounded window, fail closed on the stated triggers, observe materialization, and restore the prior compatible generation.
6. A new review in which the independent acceptance responsibility applies predeclared criteria to the complete packet.

Supplying those items does not change the verdict automatically.
They create the evidence basis for a future review and a new direct verdict.

## Use a concise review record

The final review should contain:

1. **Verdict and permitted use.**
State the verdict first with restrictions and evidence cutoff.
2. **Change and responsibilities.**
Name the existing method, proposed method, decision status, and assignments.
3. **Scientific rationale and assumptions.**
State why the change is needed and where it applies.
4. **Contracts and consumers.**
Summarize typed inputs, references, grids, boundaries, solver, interfaces, and affected consumers.
5. **Identity, provenance, artifacts, and compatibility.**
State generation, reuse, and mixed-version behavior.
6. **Verification, validation, sensitivity, and uncertainty.**
State passed, failed, unavailable, and out-of-scope evidence.
7. **Operations.**
State publication, materialization, observability, failure, retries, migration, rollout, and rollback.
8. **Conditions and unresolved risks.**
Name responsibilities, deadlines or triggers, automatic responses, and prohibited uses.

## Common misconceptions

### One successful example makes the change ready

One example supports only its configuration and evidence scope.
The verdict must match the intended population and consequences.

### Conditions can cover missing evidence for the permitted use

Conditions can bound a supported use or delay broader promotion.
They cannot replace evidence required to protect the use already being permitted.

### Responsibility follows technical ownership

Scientific decisions, implementation, operation, rollback, and acceptance require explicit assignments.
Repository access or expertise alone does not assign them.

### Rollback is an operational detail

Rollback protects scientific meaning by restoring compatible decisions, software, configuration, artifacts, consumers, and acceptance state.

## Competency check

1. Explain why method decision, implementation, operation, and acceptance responsibilities may be assigned separately.
2. Give one valid condition for `READY WITH CONDITIONS` and one blocker that requires `NOT READY`.
3. Explain why a boundary-method change can require new identity, migration, validation, and rollback.
4. State the evidence that distinguishes a published generation from a materialized and accepted generation.
5. Explain why the synthetic packet is `NOT READY` even for shadow use and name the evidence needed for a new review.

## Further reading and source notes

- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) supports method credibility, data pedigree, verification, validation, uncertainty, acceptance criteria, and reporting.
- [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) supports distinctions among software verification, numerical verification, validation, and bounded validity.
- [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) supports decision-specific quality, calibration, corroboration, sensitivity, uncertainty, and model evaluation.
