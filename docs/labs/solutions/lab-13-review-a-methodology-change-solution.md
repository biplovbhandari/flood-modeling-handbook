# Lab 13 Solution: Review a Methodology Change

This solution reviews only the synthetic packet in [Lab 13](../lab-13-review-a-methodology-change.md).
It applies the generic gate in [Scientific Methodology Review](../../07-system-design/03-scientific-methodology-review.md).

## 1. Verdict and intended use

**NOT READY.**

The verdict applies to draft method P2 and the requested R-100 shadow comparison at 100 m3/s.
P2 lacks approved method authority, accountable responsibility, collision-safe identity, safe publication, a defensible validation design, a complete migration contract, enforceable shadow isolation, complete monitoring, and a recoverable rollback.

The proposed method must not produce a selected-library member, supply another transfer, enter a composite, replace an accepted generation, update an export, or support an external decision.
The packet does not justify running the shadow comparison under the proposed storage and identity behavior because an unaccepted result could collide with or partially replace an E1 generation.

## 2. Change status and responsibility table

E1 applies one uniform 102.5 m specified stage in VD-1 across ten interface points.
P2 proposes a spatial field interpolated from accepted R-200 source scenarios at 102.4 and 102.7 m using common wet support and a 90 percent coverage threshold.
P2 is a draft rather than an approved method.

The two exploratory differences are rationale for investigation.
They are not verification, validation, or approval because their raw support and provenance are absent.
Parser acceptance shows only that a draft interface can represent additional fields.
It does not establish scientific compatibility or safe behavior by producers and consumers.

| Responsibility function | Packet status | Review disposition |
| --- | --- | --- |
| Method decision | Unassigned | Blocks approval of P2 and its scope. |
| Implementation | No accountable assignment or authority record | Treat as unassigned for the change contract. |
| Validation | Unassigned | Blocks approval of criteria, evidence, and applicability. |
| Operation and halt | Unassigned | Blocks enforceable shadow isolation, response, and halt action. |
| Migration | Unassigned | Blocks classification and movement of affected artifacts and consumers. |
| Rollback | Unassigned | Blocks a recoverable rollout. |
| Independent acceptance | Unassigned | Blocks any READY or READY WITH CONDITIONS verdict. |

Technical familiarity, parser work, or access to artifacts cannot fill these assignments.
[CQ-010](../../reference/conflicts-and-open-questions.md#cq-010-acceptance-authority) remains unresolved.

## 3. Contract and consumer impact table

| Area | Supplied state | Required change or evidence | Failure if omitted |
| --- | --- | --- | --- |
| Method authority | E1 recorded; P2 unapproved | Authorized P2 decision, scope, alternatives, assumptions, and prohibited uses | An unapproved method can enter use. |
| Input contract | Scalar E1 input; draft P2 fields accepted by a parser | Versioned validation for source identities, references, units, points, masks, coverage, integrity, and unknown fields | Structurally accepted input can remain scientifically ambiguous. |
| Scenario record | E1 records a uniform stage | P2 must record the realized field, source lineage, weights, masks, coverage, omissions, and complete existing evidence | Consumers cannot reconstruct the boundary. |
| Identity | P2 changes only a readable method label | Canonical identity must include every output-affecting P2 field | E1 and unequal P2 scenarios can appear identical. |
| Address | Same readable nominal-stage address | New immutable generation key or collision-safe address plus protected pointers | New output can overwrite or obscure old output. |
| Reuse | Partial comparison of address, model label, discharge, and nominal stage | Exact canonical identity plus observed artifacts, integrity, compatibility, membership, and acceptance | An E1 or incompatible P2 generation can be adopted. |
| Source provenance | Draft requires R-200 evidence but identity omits it | Exact source generations, depth and terrain integrity, references, transformations, masks, and acceptance links | Boundary values cannot be reproduced or trusted. |
| Publication and retry | Sequential live writes with record last | Isolated staging, complete verification, atomic promotion, idempotent retry, retained prior generation, and failure tests | A reader can observe a partial or mixed generation. |
| Materialization | No P2 observer result | Independent observation of the intended identity and every required role | A returned or present record can be mistaken for completion. |
| Selected memberships | Seven E1 memberships | Method-aware selection guards and explicit migration or exclusion decisions | P2 can enter selection before acceptance or E1 can be misread as P2. |
| Pending requests | Three omit method revision | Block and resolve each request before dispatch | A request can be executed under an unintended method. |
| Composites and exports | Six consumers lack method-compatibility decisions | Record source method identity and re-evaluate or exclude each consumer | Products can mix incompatible boundary meanings. |
| Verification and validation | Two post hoc exploratory runs | Predeclared, traceable, representative, independent evidence | A tuned summary can be mistaken for validation. |
| Monitoring | Count and runtime only | Scientific, identity, coverage, edge, balance, materialization, consumer, and drift signals | Harmful drift or selection can remain invisible. |
| Migration, halt, and rollback | New requests only; configuration-only rollback | Complete inventory, staged migration, stop triggers, retained E1 generation, pointer and membership restoration, dependency retraction, and assigned authority | Rollback can leave P2 artifacts selected or consumed. |

This impact surface follows [MX-016](../../reference/decision-code-artifact-crosswalk.md#mx-016-methodology-change).

## 4. Identity, compatibility, reuse, and publication analysis

E1 and P2 share R-100, model label, discharge 100, nominal stage 102.5, and the same readable address.
P2 changes the spatial boundary values and their provenance.
The proposed comparison can therefore route scientifically unequal scenarios to one address.

Changing only a readable method label is insufficient because the draft identity still omits the facts needed to distinguish two P2 fields.
At minimum, P2 identity needs:

- approved method revision and interpolation rule;
- upstream model generation, discharge, datum, initial state, numerical settings, and producer build;
- exact lower and upper R-200 scenario generations;
- source record, depth, and terrain integrity values;
- source-stage coordinates and interpolation weights;
- horizontal reference, vertical datum, units, transforms, bounds, and mapping rule;
- transfer geometry and ordered intended points;
- source masks, common-wet mask, nodata and wet rules;
- coverage denominator, threshold, realized result, and omission reasons; and
- realized boundary-field integrity.

Equivalent requests should reproduce one canonical identity.
Any change to those output-affecting values should produce a distinct identity and protected generation.

Reuse must observe the requested canonical identity, stored canonical identity, generation, complete required-role inventory, integrity values, schema, spatial and datum compatibility, membership, acceptance state, and current intent.
An address or nominal-stage match is not enough.
[CQ-002](../../reference/conflicts-and-open-questions.md#cq-002-incomplete-identity) and [CQ-006](../../reference/conflicts-and-open-questions.md#cq-006-unsafe-reuse) are direct blockers.

The six existing generations with no method revision are Unknown.
They cannot be relabeled E1 or P2 from the nominal stage.
They require reconstructible provenance or exclusion.
The three pending requests are also Unknown and must remain blocked until each request names an approved method and complete identity.

Sequential live writes can expose a new boundary with old depth, new depth with old diagnostics, or a new record with missing companions.
Writing the record last may reveal some incompleteness after the fact, but it does not prevent ordinary readers from observing partial state.
No P2 generation should use the live E1 address.

## 5. Replacement validation program

The proposed two-run study is insufficient because it uses a convenience sample, omits R-100 at 140 m3/s and two nominal stages, chooses a threshold after seeing results, uses the same runs for tuning and validation, and supplies no independent comparison.
It also omits the transfer failure classes and numerical evidence needed for the stated boundary change.

The smallest defensible program for the requested shadow use is:

1. Assign the decision, validation, operation, rollback, and independent acceptance responsibilities before execution.
2. Approve a testable P2 specification and predeclare quantities, metrics, spatial and temporal support, uncertainty treatment, acceptance rules, stop rules, and the shadow-only use.
3. Execute software verification for required-field rejection, datum and unit checks, identity changes, mask and coverage behavior, collision rejection, publication failure, retry, observation, and old-reader failure behavior.
4. Include R-100 discharges 60, 100, and 140 m3/s and nominal stages 102.0, 102.5, and 103.0 m across a bounded representative design rather than assuming one pair covers every combination.
5. Include common-wet coverage above and at the 90 percent boundary, dry and nodata patterns, differing spatial fields with one nominal label, and source replacement or missing-artifact failures.
6. Record local WSE histories, depth, extent, flux, complete balance, convergence, edge behavior, termination, failed runs, and the full transferred field.
7. Test grid, time step, domain, initial state, terrain, roughness, source stage, wet-mask, and mapping sensitivity, including material interactions selected before results are inspected.
8. Use independent observations or a defined benchmark with immutable provenance, comparable quantity, location, support, time, datum, transformation, uncertainty, and applicability limits.
9. Separate tuning cases from held-out validation evidence or approve and document a bounded cross-validation design.
10. Verify collision-safe publication, independent materialization, shadow-selection guards, expiry or stop controls, monitoring, and restoration of E1.
11. Retain failures, ambiguous results, exclusions, raw evidence, and the complete domain of validity.
12. Have the independent acceptance responsibility apply the predeclared criteria in a new review.

The program does not need a universal WSE threshold.
It needs an authorized threshold and uncertainty treatment that match this stated use.

## 6. Migration, rollout, monitoring, and rollback plan

No mutating step is authorized by the current packet.
A safe future plan would follow this order.

1. Assign accountable responsibilities and approve P2's scientific scope, contract, identity, compatibility, and acceptance plan.
2. Complete a read-only inventory of all 18 generations, 7 memberships, 2 composites, 4 exports, 3 pending requests, pointers, observers, and other consumers.
3. Classify each object as traceable E1, traceable P2, provably compatible, requires recomputation, requires consumer update, or Unknown and excluded.
4. Introduce a versioned P2 input and record contract plus collision-safe canonical identity and immutable generation addressing.
5. Update producers, observers, selectors, composites, exports, monitors, and readers so unsupported versions fail closed.
6. Implement isolated staging, complete integrity verification, atomic pointer promotion, idempotent retry, independent observation, and retention of the prior E1 generation.
7. Verify those controls with normal, failure, retry, concurrent-read, missing-role, changed-source, and integrity-mismatch evidence.
8. Run the approved validation program in a nonselectable shadow namespace that has no path into membership, transfer, composite, export, or external-decision consumers.
9. Monitor source identity, mask and coverage, field integrity, convergence, balance, local WSE, edges, materialization, output distributions, selection attempts, consumer versions, and drift.
10. Halt automatically on an identity collision, missing role, integrity mismatch, coverage failure, datum failure, unintended selection, scientific criterion failure, or unavailable response responsibility.
11. Permit promotion only after the independent acceptance responsibility issues a new verdict and every migration prerequisite passes.
12. Migrate, recompute, revalidate, or exclude each inventoried object explicitly, then reconcile the completed actions against the original inventory.
13. Preserve E1 generations, evidence, pointers, memberships, consumer compatibility, and restoration instructions through the approved rollback horizon.

Rollback must restore the approved method decision, configuration, code, identity pointers, selected memberships, transfer dependencies, composites, exports, pending-request handling, observers, and monitors.
It must retract incompatible P2 dependencies and verify that restored consumers read the restored E1 generation.
Restoring one configuration value is not sufficient.

## 7. Blocking gaps and prohibited uses

| Blocking gap | Impact | Responsibility status | Minimum basis for another review |
| --- | --- | --- | --- |
| P2 is unapproved | No authority exists for even the bounded method use. | Method decision unassigned | Approved decision and scope |
| P2 identity and address are ambiguous | E1 and unequal P2 generations can collide or be reused incorrectly. | Implementation accountability absent | Canonical identity, protected generation, and executed collision tests |
| Publication is not fail-closed | Readers can observe partial or mixed output. | Operation unassigned | Implemented staging, atomic promotion, retry, and observer evidence |
| Validation design is post hoc and incomplete | The exploratory differences cannot establish adequacy. | Validation unassigned | Predeclared representative verification and independent validation |
| Migration and consumer compatibility are undefined | Old, new, unknown, selected, and derived products can mix. | Migration unassigned | Complete inventory classification and reconciled migration plan |
| Monitoring lacks scientific and contract signals | Selection, drift, edge, coverage, and integrity failures can remain invisible. | Operation and halt unassigned | Defined signals, thresholds, retention, action, and authority |
| Rollback restores configuration only | P2 artifacts and dependencies can remain selected or consumed. | Rollback unassigned | Retained E1 state and executed full restoration evidence |
| Acceptance responsibility is absent | No accountable function can apply criteria or accept residual risk. | Independent acceptance unassigned | Named authority and a new signed verdict |

Because these are blockers for the requested shadow use, READY WITH CONDITIONS is not available.
All selected-library, transfer-source, composite, replacement, export, and external-decision uses remain prohibited.
The shadow comparison itself remains prohibited until collision-safe isolation, accountable authority, and the minimum evidence controls exist.
