# Scientific Software Contracts

A scientific software contract states what an object means, which inputs and assumptions define it, how software preserves those meanings, and what evidence is required before another component may use it.
The contract is broader than a function signature or file schema.
It joins scientific method, typed data, geospatial references, software behavior, identity, artifacts, operation, and acceptance.

## Why this topic matters

A file can be syntactically valid while representing the wrong quantity, datum, scenario, or method.
A plausible result can also become unsafe when two unequal objects share an address, a retry exposes mixed generations, or a successful response is mistaken for materialization.
Explicit contracts make those failures detectable and assign responsibility for the evidence needed at each boundary.

## Prerequisites

Review [Quantities, Units, and Datums](../00-orientation/03-quantities-units-and-datums.md), [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md), [Compositing, Identity, and Provenance](../05-scenario-libraries/05-compositing-identity-and-provenance.md), and [Validation Framework](../06-validation-and-qc/01-validation-framework.md).

## Learning objectives

After this chapter, the reader should be able to:

- translate scientific meaning into typed input, identity, artifact, and operational contracts;
- distinguish identity, provenance, compatibility, publication, materialization, reuse, and acceptance;
- design retry, migration, and rollback behavior for versioned scientific objects;
- identify monitoring signals and responsibility assignments required for safe use; and
- recognize failures that remain invisible when syntax is treated as meaning.

## Follow the contract from meaning to use

A complete contract has six connected layers.

1. **Scientific meaning** defines the quantity, method, assumptions, intended use, and limits.
2. **Input contract** gives each required value a type, unit, reference, range, source, and validation rule.
3. **Identity and provenance contract** states which changes create a different object and records how each realization was produced.
4. **Artifact contract** states required records and files, schemas, dimensions, transforms, nodata, integrity values, and generation rules.
5. **Operational contract** states publication, observation, retry, failure, cleanup, immutability, reuse, migration, rollback, and monitoring behavior.
6. **Acceptance contract** states the intended use, criteria, evidence, responsibility, restrictions, verdict, and reassessment triggers.

**Design principle:** A schema can enforce syntax and some local constraints, but it cannot establish scientific meaning, observe storage, authorize acceptance, or make publication atomic.

## Define typed inputs at the boundary

Every external input should be validated before scientific calculation or identity derivation.

| Concern | Contract content | Failure when omitted |
| --- | --- | --- |
| Physical quantity | Quantity, numeric type, dimension, unit, sign convention, time support, and permitted conversions. | Numbers with different meanings can be combined or compared. |
| Horizontal reference | Coordinate reference, axis order, linear units, transformation, and transformation applicability. | Geometry and rasters can appear aligned while representing different locations. |
| Vertical reference | Datum, unit, epoch when relevant, transformation, and transformation uncertainty. | Elevations can differ by an unrecorded offset. |
| Grid | Shape, affine transform, bounds, registration, orientation, resolution, mask, and resampling rule. | Equal-shaped arrays can be combined cell by cell despite spatial incompatibility. |
| Nodata and wet-dry state | Nodata value, valid zero meaning, wet threshold, mask authority, and propagation rule. | Missing, dry, zero, and valid shallow values can be confused. |
| Boundary | Mathematical meaning, geometry, source, units, sign, spatial support, and applicable regime. | Short labels can hide materially different hydraulic behavior. |
| Initial state | State variables, source identity, target compatibility, and omitted dynamic state. | A reusable file can initialize an incompatible experiment. |
| Solver and method | Solver build, method revision, relevant options, run settings, and environment when material. | Different calculations can appear to belong to one method. |
| Failure behavior | Rejection, warning, fallback, retryability, and retained diagnostic evidence. | Invalid input can be silently replaced or reported as success. |

Reject unknown or ambiguous values when their interpretation can change scientific meaning.
If a fallback is permitted, record the realized choice and include it in identity when it affects output.

## Make identity complete enough to prevent collision

Identity answers which scientific object this is.
Provenance answers how this realization was made.
Acceptance answers whether the object may be used for a named purpose.
Those records can reference one another, but they must not be collapsed.

Classify each field explicitly:

- An **identity field** changes when the scientific recipe changes.
- A **realization field** distinguishes a point, scenario, domain, or axis value within one recipe.
- A **provenance field** records production history without defining interchangeability by itself.
- An **acceptance field** records a use-specific decision and must not be inferred from identity.
- An **operational field** records state such as attempt, publication, or observation status.

Identity commonly needs source content identities, network lineage, method revision, solver build, grid, domain, boundary method and values, forcing, initial state, numerical controls, and output interpretation.
The exact set depends on which changes can alter the object for its intended consumers.

**Design principle:** If two values can produce scientifically unequal results, either identity must distinguish them or a governed compatibility rule must explain why reuse remains valid.

Human-readable addresses can aid navigation, but they should not be the only identity evidence.
Store the canonical identity object and a collision-resistant digest in the record.
Avoid lossy rounding or normalization that maps unequal scenarios to one address.

## Preserve complete provenance

Provenance should make the object explainable and reproducible within stated limits.
Record source identities, transformations, parameter realization, method revision, solver build, configuration, parent objects, timestamps, producer, and environment details that can affect results.

**Evidence note:** A source address is not immutable provenance when different bytes can appear behind the same address.
Content identity or a versioned retrieval contract is required when exact reproduction matters.

Provenance should also record negative information when it affects interpretation.
Examples include an unavailable vertical transformation, an omitted structure relation, an unsupported solver option, or an unexecuted diagnostic.

## Define the manifest and artifact contract

A manifest or scientific record should state:

- schema version and object type;
- canonical identity and digest;
- complete realized inputs and method version;
- source and transformation provenance;
- producer identity and relevant environment;
- required artifacts with scientific roles;
- units, coordinate references, grids, masks, and nodata rules;
- integrity algorithm and value for every required artifact;
- generation or transaction identity;
- warnings, failures, and checks that did not run;
- compatibility declarations and dependencies; and
- publication, materialization, reuse, and acceptance state as separate fields or linked records.

An artifact contract should state required files, formats, schemas, dimensions, spatial metadata, semantic quantity, integrity values, and one-generation consistency.
The contract should also define which missing or incompatible artifact invalidates the object.

**Design principle:** A manifest is a claim about an artifact set, not proof that every referenced artifact is present and intact.

## Treat compatibility as a scientific claim

Compatibility means that a producer and consumer assign sufficiently equivalent meaning to the exchanged object for the intended use.
Parser success is only one possible prerequisite.

A compatibility decision should answer:

1. Are quantity, units, references, time support, grid, mask, and nodata compatible?
2. Are method, solver, boundary, convergence, and initial-state assumptions compatible?
3. Does the consumer require an artifact or provenance field the producer did not record?
4. Can old and new generations coexist without ambiguous selection?
5. Does the object require reinterpretation, metadata repair, revalidation, recomputation, or retirement?

Backward compatibility means an existing consumer can use new output under its existing contract without a silent change in scientific meaning.
A compatible schema version does not guarantee scientific backward compatibility.

## Separate publication, materialization, reuse, and acceptance

These events answer different questions.

| Event | Question answered | Evidence still needed for broader claims |
| --- | --- | --- |
| Publication | Which generation became externally visible, and was visibility partial, complete, or atomic? | Integrity, compatibility, observation, and scientific fitness. |
| Materialization | Did an independent observer find the intended generation and verify its required storage contract? | Scientific adequacy and use-specific acceptance. |
| Reuse | Does the observed object satisfy the defined equality or compatibility predicate for this request? | Any materialization and acceptance checks not included in the reuse rule. |
| Acceptance | Does the complete evidence satisfy criteria for the named use? | Continued conformance after drift, which requires monitoring. |

Publication begins when output becomes externally visible at an intended address.
Atomic publication means consumers move from one complete generation to another without observing a partial mixture.

Materialization requires observation at the final location.
A returned path, completed request, or present manifest does not establish materialization by itself.

Reuse requires both compatibility and current observation of the assets required by the consumer.
An exact record match can still refer to missing, replaced, or superseded artifacts.

Acceptance remains separate because a complete compatible artifact can still be numerically inadequate or invalid for the intended use.

## Design safe publication and retries

An idempotent retry can be repeated without creating a different externally visible scientific result for an equivalent request.

A retry-safe protocol should:

1. Derive a stable and content-complete identity for the intended generation.
2. Write all records and artifacts to a generation-specific staging location.
3. Calculate and verify integrity values before promotion.
4. Promote the complete generation atomically or atomically update a pointer to it.
5. Keep incomplete staging invisible to ordinary consumers.
6. Re-observe required artifacts before recording materialization.
7. Return the same generation for repeated equivalent requests.
8. Reject or create a distinct generation for scientifically unequal requests.
9. Retain the previous accepted generation until rollback requirements expire.
10. Record who may promote, deprecate, restore, quarantine, or remove a generation.

**Applied example:** The [partial generation withheld](../06-validation-and-qc/04-case-issue-and-experiment-catalog.md#operational-observation-partial-generation-withheld) record shows a staging observer rejecting promotion when one required raster is absent.

**Evidence note:** That event demonstrates one bounded control response.
It does not prove that all partial-publication modes are detected or that either generation is scientifically accepted.

## Govern reuse and invalidation

A reuse contract should state:

- the exact equality or compatibility predicate;
- which assets are re-observed and how integrity is checked;
- which source, method, solver, or schema changes invalidate reuse;
- whether acceptance for one use transfers to another use;
- whether superseded objects remain discoverable; and
- which responsibility can approve an exception.

Never infer selected membership from storage presence.
Never infer current materialization from a historical observation.
Never infer acceptance from compatibility.

## Plan migration as a contract transition

A method or schema change can affect producers, observers, selectors, caches, dependencies, composites, exports, monitors, and acceptance records.
A migration plan should inventory every affected generation and consumer before changing them.

The plan should define:

1. the old and new meanings and versions;
2. compatibility between old and new producers and consumers;
3. metadata repair, recomputation, revalidation, or retirement rules;
4. deployment order and supported intermediate states;
5. a read-only inventory and dry-run report;
6. progress reconciliation against the complete inventory;
7. stop conditions for collision, missing provenance, or consumer incompatibility; and
8. retention of evidence required for rollback and audit.

**Design principle:** A migration is incomplete while a consumer can silently combine incompatible generations.

## Make rollback restore scientific meaning

Rollback must name the method decision, software, configuration, identity rules, artifact generation, membership state, consumers, and monitoring configuration to restore.
Restoring an executable while consumers still reference artifacts created under the incompatible method is not a complete rollback.

The previous generation must remain readable, intact, compatible with restored consumers, and linked to its acceptance record.
If rollback requires recomputation, the retained inputs and method must be sufficient to reproduce the old meaning.

## Monitor contracts and scientific drift

Monitoring should cover:

- source identity and data-quality drift;
- method, solver, schema, and configuration drift;
- identity collisions and incompatible reuse attempts;
- incomplete or mixed publication;
- materialization loss and integrity failure;
- unexpected numerical, balance, edge, and warning patterns;
- product-distribution changes; and
- acceptance-envelope violations.

Each signal needs a threshold or condition, retained evidence, response responsibility, response time, and reassessment rule.
An alert-free interval means only that configured alerts did not fire during that interval.
It does not prove that every relevant control exists or that the accepted scientific envelope remains valid.

## Assign responsibilities explicitly

At minimum, name responsibility for:

- scientific method decisions;
- input and schema contracts;
- implementation and verification;
- artifact publication and observation;
- compatibility and migration;
- operational monitoring and incident response;
- rollback authorization and execution; and
- intended-use acceptance.

One person or group can hold more than one responsibility, but the assignments must be explicit.
Do not infer responsibility from repository location, familiarity, or who noticed the problem.

## Synthetic failure scenarios

### Identity omission

**Applied example:** Two `R-200` scenarios use the same discharge and grid but different downstream-stage fields.
The address excludes boundary-source identity, so both requests resolve to one location.
Exact comparison rejects reuse, but the later request can still target the prior address.

**Evidence note:** This construction demonstrates collision risk when an output-affecting input is omitted from identity.
It does not claim that a collision occurred in an external system.

### Grid misregistration

**Applied example:** Two rasters have equal shape and resolution, but one transform is shifted by half a cell.
Cell-by-cell addition succeeds syntactically and produces a spatially wrong water-surface field.

**Design principle:** Compare transform, bounds, registration, reference, units, masks, and nodata before raster arithmetic.

### Misleading success

**Applied example:** A process returns a scenario address after writing depth, but the required record and diagnostic history are absent.
The response establishes an intended location, not complete publication or materialization.

### Incomplete rollback

**Applied example:** Software is restored to the previous method, but the active pointer still selects artifacts generated under the newer method.
The restored process and selected artifacts no longer share one scientific contract.

## Record the contract change

A methodology-change record should include:

1. the decision requested and named decision responsibility;
2. scientific rationale, intended use, assumptions, and alternatives;
3. affected inputs, producers, consumers, schemas, identities, records, artifacts, warnings, and monitors;
4. compatibility and mixed-generation analysis;
5. verification, validation, sensitivity, and uncertainty evidence with predeclared criteria;
6. migration inventory, order, stop conditions, and responsibility;
7. rollout scope, monitoring, halt triggers, and evidence retention;
8. rollback steps for decisions, software, configuration, artifacts, membership, and consumers; and
9. unresolved risks and a direct verdict.

## Common misconceptions

### A valid schema proves a valid scientific object

Schema validity cannot establish correct units, compatible datums, complete provenance, or hydraulic adequacy unless those claims are explicitly represented and verified.

### Exact record equality proves safe reuse

Assets can be deleted, replaced, or superseded after the record was written.
Reuse must include the observations required by the consumer contract.

### Complete publication proves materialization

Publication describes producer-visible state.
Materialization requires independent observation of the final generation.

### Rollback means redeploying old code

Scientific rollback also restores compatible configuration, identities, artifacts, dependencies, membership, and acceptance state.

## Competency check

1. Explain why a unit belongs in the input contract and can also affect identity.
2. Explain why equal raster shape does not establish compatibility.
3. Distinguish publication, materialization, reuse, and acceptance for one scenario.
4. State what an idempotent retry must do after a staging failure.
5. Explain why an old executable and a new artifact generation can make rollback incomplete.
6. Name the responsibilities required before a method change can be accepted and operated.

## Further reading and source notes

- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) supports verification, validation, uncertainty, credibility, and intended-use acceptance.
- [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) distinguishes software verification, numerical verification, validation, and bounded validity.
- [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) connects model purpose, data quality, sensitivity, uncertainty, calibration, corroboration, and decision use.
