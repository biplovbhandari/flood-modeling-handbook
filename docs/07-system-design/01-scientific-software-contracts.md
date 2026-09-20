# Scientific Software Contracts

A scientific software contract states what a scientific object means, which inputs and assumptions define it, how software must preserve those meanings, and what evidence is required before another component may use it.
The contract is wider than a function signature or JSON schema.
It joins scientific method, geospatial reference, software behavior, identity, artifacts, operations, and acceptance.

## Why this matters

The 2D FIM system turns scientific assumptions into reusable model and scenario artifacts.
If an assumption changes without a corresponding contract change, the software can produce valid JSON and plausible maps that no longer represent the intended method.
The failure can remain hidden when two incompatible results share an address, a raster is misregistered, a retry adopts incomplete assets, or a success response is mistaken for scientific acceptance.

## Prerequisites

Review [Quantities, Units, and Datums](../00-orientation/03-quantities-units-and-datums.md), [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md), [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md), [Compositing, Identity, and Provenance](../05-scenario-libraries/05-compositing-identity-and-provenance.md), and [Validation Framework](../06-validation-and-qc/01-validation-framework.md).
Use [Source Authority](../reference/source-authority.md) to label every project claim.

## Learning objectives

After this chapter, the reader should be able to:

- translate a scientific assumption into an explicit software, data, artifact, and operational contract;
- identify compatibility and identity consequences when a scientific method changes;
- explain why manifest presence, exact reuse, publication, materialization, and scientific acceptance are separate claims;
- design retry, idempotency, atomicity, migration, and rollback behavior for scientifically versioned artifacts; and
- identify the owner and evidence needed before a methodology change can be called ready.

## 1. Follow the contract from meaning to use

A complete contract has six connected layers.

1. **Scientific meaning** defines the physical quantity, assumptions, method, intended use, and limits.
2. **Input contract** gives every required value a type, unit, reference system, allowable range, source authority, and compatibility rule.
3. **Identity contract** states which changes create a scientifically different object and therefore require a different identity and address.
4. **Artifact contract** states the required files, schemas, dimensions, transforms, nodata rules, checksums, provenance, and one-generation consistency rules.
5. **Operational contract** states publication, observation, retry, idempotency, failure, cleanup, immutability, migration, and rollback behavior.
6. **Acceptance contract** states the evidence, criteria, authority, restrictions, verdict, and monitoring triggers for a named intended use.

A schema can enforce part of the input or artifact layer.
It cannot supply scientific meaning, prove a datum transformation, observe storage, authorize acceptance, or make a retry safe by itself.

## 2. Translate each scientific assumption into an explicit contract

| Scientific or operational concern | The contract must state | Failure when left ambiguous |
| --- | --- | --- |
| Physical quantity and units | Quantity name, dimension, unit, sign convention, temporal support, and permitted conversions. | Discharge, depth, stage, slope, area, or time can be compared or applied as though their numbers were interchangeable. |
| Horizontal reference | CRS identifier, axis order, linear units, coordinate operation, and accuracy or applicability of any transformation. | Distances, areas, slopes, cell locations, and sampled boundaries can be wrong while arrays still have compatible shapes. |
| Vertical reference | Vertical datum, geoid or ellipsoid relation where applicable, epoch when material, unit, transformation, and transformation uncertainty. | Depth plus terrain can produce a misregistered WSE, or two apparently similar elevations can differ by an unrecorded offset. |
| Grid transform | Shape, affine transform, bounds, cell registration, orientation, pixel convention, resolution, and resampling rule. | Equal-shaped arrays can be added cell by cell even when their cells represent different locations. |
| Nodata and wet-dry meaning | Nodata sentinel, mask authority, valid zero meaning, dry-cell rule, activation threshold, and propagation through calculations. | Nodata can become terrain, zero can become dry or missing inconsistently, and transfer points can sample invalid cells. |
| Boundary semantics | Mathematical or solver condition, geometry, units, positive direction, value construction, spatial support, and applicable regime. | `FREE`, freefall, normal depth, fixed WSE, and transferred WSE can be treated as synonyms despite different hydraulic effects. |
| Solver and methodology identity | Solver name, executable version or build, image digest, methodology revision, relevant options, run settings, and hardware when it affects reproducibility. | Results produced by materially different methods or environments can appear to belong to one run generation. |
| Convergence evidence | Metric definition, interval, strict or inclusive comparison, threshold, local and global diagnostics, full-balance terms, edge status, and retained history. | A final storage-change ratio can be mistaken for local steady behavior, full mass balance, or hydraulic adequacy. |
| Warnings and failures | Checks that ran, checks not implemented, severity, persisted detail, precedence among simultaneous conditions, retryability, and required response. | Absence of a warning can be mistaken for a passed check, and one termination reason can suppress another material condition. |
| Manifest | Schema version, identity, realization, complete inputs, transformations, producer, properties, assets, checksums, and compatibility declarations. | A manifest can be present while omitting the method or transformation needed to interpret its assets. |
| Asset integrity | Required assets, content digest algorithm and length, size or shape checks, generation identifier, and verification responsibility. | A manifest can refer to absent, corrupt, stale, or mixed-generation objects. |
| Publication | Staging location, write order, atomic promotion unit, final marker, failure cleanup, and visibility guarantees. | Readers can observe a mixture of old and new assets at one address after partial failure. |
| Reuse | Required equality or compatibility predicate, asset re-observation, acceptance state, and invalidation rule. | A stored manifest can be adopted even though a referenced asset is gone or a newly important input was excluded from comparison. |
| Immutability intent | Which scientifically distinct generations must retain distinct addresses and whether in-place mutation is forbidden. | A new method can overwrite the only copy of an older reproducible result. |
| Retry and idempotency | Stable request identity, safe repetition rule, partial-publication recovery, duplicate suppression, and terminal versus retryable failures. | A retry can create duplicate work, mix generations, overwrite evidence, or report success from an incomplete prior attempt. |
| Materialization | Observer, expected address, required assets, schema and identity checks, checksum verification, compatibility checks, and evidence record. | A returned path or present manifest can be mistaken for a complete usable object. |
| Acceptance | Intended use, required verification and validation evidence, uncertainty, criteria, authorized reviewer, verdict, restrictions, and monitoring. | Operational completion can be reported as scientific readiness without adequate evidence or authority. |

## 3. Treat compatibility as a scientific claim

Compatibility is not just whether a parser accepts two files.
It is the claim that a producer and consumer assign sufficiently equivalent meaning to the same quantities and artifacts for the intended use.

A compatibility decision should answer all of these questions:

1. Are the quantities, units, horizontal references, vertical references, time supports, grids, masks, and nodata rules compatible?
2. Are the methodology, solver, boundary, convergence, and initialization assumptions compatible?
3. Does the consumer require an artifact or provenance field that the producer did not record?
4. Can old and new artifacts coexist, or must a consumer migrate before the new producer is enabled?
5. Does a stored object need reinterpretation, revalidation, recomputation, or retirement?

Backward compatibility means an existing consumer can use the new producer output under its existing contract without a silent change in scientific meaning.
Parsing success is necessary only when the format is relevant.
Parsing success is not sufficient for scientific backward compatibility.

## 4. Put every output-affecting decision in the right identity layer

An identity contract separates a scientific recipe from one realization of that recipe.
The target [system design guide](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/guide.md) says that a change important enough to invalidate an object should generally move identity and therefore the object address.
That statement is **Target design**, not proof that every current identity is complete.

**Current implementation:** The reviewed run identity contains only the solver enum and the jobs image's baked Decision Register revision.
It omits the executable version, image digest, run configuration, convergence tolerance, output interval, boundary details, hot start, and hardware.
The complete scenario inputs are stored in the manifest, so exact manifest reuse can distinguish some omitted settings after reaching the predicted address.
That later comparison does not prevent two unequal requests from targeting the same address.

An identity review should classify each field as one of these types:

- **Identity field:** A change creates a scientifically distinct recipe and must move the object to a new address.
- **Realization field:** A change creates another point, domain, or axis value within the same recipe and must still be represented without collision.
- **Provenance field:** The value records how the object was made but does not by itself define interchangeability.
- **Acceptance field:** The value records whether an object is permitted for a named use and must not be inferred from identity.

If a field is omitted from identity, the review must explain why different values remain scientifically interchangeable or how the address and promotion protocol prevent collision.

## 5. Understand the current convergence-threshold example

**Selected methodology:** DR-022 ALT-G selects the volume-convergence metric and DR-028 ALT-A selects \(10^{-3}\), both with Alternate Selected status in the reviewed register.
DR-028 describes the choice as preliminary and cites the Case-018 work under EXP-014.

**Current implementation:** Both ND and KWSE public inputs expose `volume_convergence_tolerance` and default it to `0.001`.
The watcher terminates only when the computed ratio is strictly less than the supplied tolerance.
The manifest records the complete scenario input, the final ratio, and the stored termination reason.
The current run identity and scenario address omit the tolerance.

**Current implementation, exact target reuse:** Exact scenario reuse reads the predicted manifest and requires equality of complete `RunScenarioInputs`.
If the tolerance changes, equality fails, so the solver can run again.
Because the address does not change, the new run can publish depth, inundation, STL, and then the manifest sequentially into the same location.
Each successful copy can make another output externally observable before the manifest write, and the sequence has no directory-level staging, atomic promotion, or rollback.

**Current implementation, ND caller-supplied adoption:** The separate `existing_scenarios` path adopts an ND manifest when reach ID, full model ID, run identity hash, and discharge within the inclusive requested range match.
It does not compare convergence tolerance, other run settings, boundary geometry or slope, hot start, or referenced-asset existence.
An adopted old-tolerance manifest can enter the `done` set without rerunning, become the minimum-discharge reference, contribute to proposal and re-judgment calculations, become an accepted or current search position, and supply depth provenance for a later hot start.

**Inference from current implementation:** The limited ND adoption branch creates a direct invalid-reuse risk because an old-tolerance result can influence the new-tolerance adaptive library despite unequal scientific inputs.
Separately, changing only the tolerance can fail exact target reuse and create a same-address rerun rather than a clean new generation.
Exact equality protects against returning an unequal old manifest as an exact match.
It does not constrain the limited ND adoption branch, protect the older generation from overwrite, prevent partial or mixed publication after failure, or preserve rollback evidence.

**Open question:** The project has not authorized whether the tolerance belongs in run identity, another generation key, or a governed compatibility and migration rule.
It has also not authorized the validation program required to replace DR-028's selected value.
See [XW-002](../reference/decision-code-artifact-crosswalk.md#xw-002-quasi-steady-termination), [XW-017](../reference/decision-code-artifact-crosswalk.md#xw-017-scenario-identity-publication-and-materialization), and [CONF-019](../reference/conflicts-and-open-questions.md#conf-019-methodology-change-identity-and-migration-ownership).

## 6. Separate publication, materialization, reuse, and acceptance

These four events answer different questions.

Publication begins only when at least one output becomes externally observable at an intended address.
A failed write attempt that makes no intended output externally observable is not publication.
Partial publication means that some but not all required outputs are externally observable at their intended addresses.
Complete publication means that all required outputs are externally observable under the producer's write contract, but it does not prove their integrity, one-generation consistency, compatibility, validity, materialization, selected membership, or acceptance.
Atomic publication means consumers transition from the complete prior generation to the complete new generation without observing a partial mixture.

| Event | Question answered | Evidence that is still missing |
| --- | --- | --- |
| Publication | Which outputs became externally observable at their intended addresses, and was the state partial, complete, or atomically published? | Integrity, one-generation consistency, compatibility, materialization, selected membership, and scientific fitness. |
| Materialization | Did an observer find the object required by current intent and verify the applicable storage contract? | Hydraulic adequacy and authorized intended-use acceptance unless those are separate required gates. |
| Reuse | Does a stored object satisfy the stated equality or compatibility predicate for this request? | Complete materialization and acceptance unless the reuse contract explicitly rechecks them. |
| Acceptance | Did an authorized reviewer find the evidence sufficient for a named use? | Continued conformance after source, method, software, or operational drift, which requires monitoring. |

Current scenario publication is sequential and non-transactional.
Depth, inundation, STL, optional Zarr, and the manifest can become externally observable one after another.
A failure between writes can leave partial publication, while a completed sequence can establish complete publication only under the bounded producer write contract.
Neither state proves materialization or acceptance.

The target reconciliation design assigns materialization to storage observation rather than a synchronous job return.
The checked-in reconciler is **checked-in unpinned prototype evidence** and does not prove deployed observation, complete checksum enforcement, or hydraulic acceptance.

Input localization has its own integrity gate before execution.
The current scenario cache uses each asset's declared 16-character checksum in the local path, but neither a cache hit nor a new remote copy is rehashed and compared with that declaration.
A declared checksum in a manifest and a checksum-derived directory name therefore do not prove that the bytes supplied to the solver match the intended asset.
Before use, the localization contract must compute SHA-256 over the cached or downloaded bytes, require the declared prefix to match, fail closed with the asset identity on mismatch, and define whether a future schema carries a full digest rather than only a 16-character prefix.
The owner of this input-integrity contract is not assigned in the reviewed sources.

## 7. Design safe retries and rollback

An idempotent retry can be repeated without creating a different externally visible scientific result for the same request.
That property requires more than returning early when a manifest exists.

A retry-safe publication protocol should:

1. Derive a stable, content-complete identity for the intended generation.
2. Write all artifacts to a generation-specific staging location.
3. Calculate and verify content checksums before promotion.
4. Promote the complete generation atomically, or use an immutable generation pointer whose update is atomic.
5. Make incomplete staging invisible to ordinary consumers.
6. Re-observe every required asset before recording materialization.
7. Return the same generation for repeated equivalent requests.
8. Reject or create a new generation for scientifically unequal requests.
9. Retain the previous accepted generation until rollback requirements expire.
10. Record who may promote, deprecate, restore, or delete a generation.

Rollback must name both the software or configuration state and the artifact generation to restore.
Deleting an object so a reconciler rebuilds current intent is not equivalent to restoring the previous methodology generation.
If current intent has already changed, a rebuild can reproduce the new method rather than the old accepted result.

## 8. Preserve warning and failure meaning

A warning contract should enumerate each implemented check and retain enough evidence to reproduce its decision.
It should also identify checks that do not exist.

Current convergence and edge behavior shows why precedence matters.
When both the storage-change convergence condition and a disallowed edge violation are true on the same saved grid, current branch order stores `volume_convergence`.
The stored reason does not prove that the edge check passed.

A stronger contract would retain independent diagnostic outcomes before applying a termination policy.
The policy could then decide whether one condition blocks publication, materialization, selection, or acceptance without erasing the other observation.

## 9. Record the contract change, not only the code diff

A methodology-change record should include:

1. The decision being changed, its current status, and the authorized decision owner.
2. The scientific rationale, intended use, assumptions, and alternatives considered.
3. Every affected producer, consumer, schema, configuration source, default, identity, address, manifest, artifact, warning, and monitor.
4. Backward-compatibility and mixed-generation analysis.
5. Validation and sensitivity evidence with predeclared criteria.
6. Migration inventory, ordering, stop conditions, and ownership.
7. Rollout scope, canary or shadow comparison, monitoring, and halt triggers.
8. Rollback artifact, configuration, code, and decision-record steps.
9. Unresolved risks and an explicit readiness verdict.

Changing code before the decision and contract are approved reverses the authority chain.
Changing a decision without tracing consumers and identities leaves implementation and stored artifacts semantically stale.

## Common failure modes

### Invalid reuse

A consumer adopts an object because a manifest matches a limited predicate, while a new scientific dependency, required asset, or acceptance state is absent from that predicate.

### Same-address collision

Two scientifically unequal requests derive the same address.
Exact comparison can reject reuse yet still allow the later request to overwrite or mix files at that address.

### Misregistration

Arrays have the same shape but incompatible transforms, bounds, cell registration, masks, horizontal units, or vertical references.
Cell-by-cell arithmetic then produces a spatially wrong result without a shape error.

### Hidden incompatibility

Producer and consumer agree on syntax but disagree on quantity, datum, boundary behavior, convergence meaning, or method revision.

### Misleading success

A zero exit, returned manifest path, final convergence ratio, or absent warning is reported as readiness even though materialization, validation, or acceptance evidence is missing.

### Irreproducible product

The artifact lacks immutable source identity, transformation history, solver build, complete run settings, or retained generation assets needed to reproduce or explain it.

## Competency check

1. Explain why a unit field belongs in the input contract while a unit-changing methodology decision can also belong in identity.
2. Explain why equal array shape does not establish grid compatibility.
3. Trace how a convergence-threshold change can be directly re-adopted through limited ND `existing_scenarios` checks, while separately failing exact target reuse and still colliding at the same address.
4. Distinguish publication, materialization, reuse, and acceptance for one scenario.
5. State what an idempotent retry must do after failure between writing depth and writing the manifest.
6. Explain why restoring an old executable without restoring its compatible artifact generation may not complete rollback.

## Further reading and source notes

- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) supplies verification, validation, uncertainty, credibility, and intended-use acceptance guidance.
- [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) separates code verification, solution verification, and validation.
- [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) connects model purpose, data quality, sensitivity, uncertainty, calibration, corroboration, and decision use.
- [SDR-003](../reference/bibliography.md#sdr-003-domain-inflow-terrain-and-convergence-decisions) supplies the selected convergence decisions and their recorded status.
- [JOB-005](../reference/bibliography.md#job-005-discharge-bound-consumer-contracts) and [JOB-007](../reference/bibliography.md#job-007-convergence-hot-start-and-solver-execution-paths) supply the current input, execution, reuse, and manifest behavior.
- [SYS-001](../reference/bibliography.md#sys-001-system-design-guide) supplies the target identity, manifest, storage, and materialization model.
