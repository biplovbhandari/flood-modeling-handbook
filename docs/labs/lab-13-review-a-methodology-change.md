# Lab 13: Review a Methodology Change

This lab reviews a synthetic proposal to replace a uniform specified stage with a transferred spatial stage field for R-100.
The proposal is generic teaching material and does not authorize a software, artifact, operational, or scientific change.

## Prerequisites

Complete [Scientific Methodology Review](../07-system-design/03-scientific-methodology-review.md), [Scientific Software Contracts](../07-system-design/01-scientific-software-contracts.md), [Evidence Boundaries in Scientific Software](../07-system-design/02-current-target-and-evidence-boundaries.md), and [Applied Evidence Catalog](../06-validation-and-qc/04-case-issue-and-experiment-catalog.md).
Read [MX-016](../reference/decision-code-artifact-crosswalk.md#mx-016-methodology-change), [CQ-002](../reference/conflicts-and-open-questions.md#cq-002-incomplete-identity), [CQ-006](../reference/conflicts-and-open-questions.md#cq-006-unsafe-reuse), [CQ-007](../reference/conflicts-and-open-questions.md#cq-007-partial-publication), [CQ-009](../reference/conflicts-and-open-questions.md#cq-009-validation-gaps), and [CQ-010](../reference/conflicts-and-open-questions.md#cq-010-acceptance-authority).

## Execution boundary

Complete the review from the supplied packet and neutral handbook references.
Do not implement the proposal, change storage, run a solver, or infer an assignment that the packet does not make.

## Learning objectives

After completing this lab, the learner should be able to:

- frame a methodology change around one stated use;
- distinguish capability, proposal, evidence, and approval;
- identify affected contracts and consumers;
- analyze identity, address, reuse, publication, and compatibility risks;
- replace a weak validation plan with a bounded one;
- define migration, rollout, monitoring, and rollback requirements;
- preserve responsibility gaps; and
- issue one generic methodology-change verdict.

## Change and intended use

The existing synthetic R-100 method applies one uniform specified stage of 102.5 m in VD-1 at ten downstream interface points.
The proposed method would instead interpolate a spatial stage field from accepted R-200 scenarios at 175 and 200 m3/s.
It would use source-stage coordinates 102.4 and 102.7 m, lower and upper weights of two-thirds and one-third, strict common-wet support, and a minimum coverage of 90 percent.

The requested first use is a shadow comparison for WSE and depth within one kilometre upstream of the R-100 downstream boundary at discharge 100 m3/s.
The shadow result must not enter a selected library, supply a transfer to another reach, enter a composite, replace an accepted generation, or support an external decision.

The stated rationale is that one constant stage cannot represent lateral variation in the downstream water surface.
Two exploratory synthetic comparisons reported maximum absolute WSE differences of 0.12 m and 0.18 m near the boundary.
Their raw fields, masks, locations, time support, numerical evidence, and provenance are not supplied.

## Method status and responsibility packet

| Item | Supplied status |
| --- | --- |
| Existing method | Method revision E1 is the currently recorded method for the synthetic selected R-100 family. |
| Proposed method | Draft method revision P2 has not been approved. |
| Method decision responsibility | Unassigned |
| Implementation responsibility | A functional assignment is proposed, but no accountable assignee or authority record is supplied. |
| Validation responsibility | Unassigned |
| Operational and halt responsibility | Unassigned |
| Migration responsibility | Unassigned |
| Rollback responsibility | Unassigned |
| Independent acceptance responsibility | Unassigned |
| Evidence cutoff | Proposal text, two summary differences, a draft interface, and unexecuted control descriptions |

No supplied evidence grants any responsibility function authority to approve the change or accept its use.

## Existing contract and proposed contract

| Area | Existing E1 method | Proposed P2 method |
| --- | --- | --- |
| Boundary quantity | One specified WSE value | Ordered spatial WSE values |
| Datum | VD-1 | VD-1 required for target and sources |
| Boundary support | Ten named points | Same intended points, subject to common wet support |
| Source provenance | Nominal stage and method revision | Exact R-200 source generations, depth and terrain identities, transformations, masks, and interpolation |
| Coverage | Ten values required | At least 90 percent of the ten-point intended interface |
| Input interface | Scalar stage, units, datum | Scalar nominal stage plus source identities, field integrity, points, mapping, masks, nodata, coverage, and omission reasons |
| Scenario record | Uniform stage and existing scenario identity | Realized field, source lineage, weights, masks, coverage, omissions, and all existing scenario evidence |

The proposal calls the change backward compatible because a draft input parser can accept the added fields.
No executed verification is supplied for validation, rejection behavior, identity change, serialization, observation, or old-reader behavior.

## Identity, address, reuse, and publication packet

The existing scenario identity includes the R-100 model generation, 100 m3/s discharge, nominal stage 102.5 m, datum VD-1, initial state, numerical settings, method revision E1, and producer build.

The draft P2 identity keeps the same fields and changes only the readable method label.
It omits:

- both R-200 source generations;
- source depth and terrain integrity values;
- source-stage coordinates and interpolation weights;
- transfer geometry and point mapping;
- source masks and common-wet mask;
- coverage denominator, threshold, result, and omission reasons; and
- realized boundary-field integrity.

The readable scenario address contains R-100, model label, discharge 100, and nominal stage 102.5.
It does not contain the method revision, source generations, mask, coverage result, or field integrity.

The current reuse description compares the readable address, model label, discharge, and nominal stage before adopting an existing generation.
It does not compare the omitted P2 fields.

The proposed publisher writes the transferred boundary, final depth, inundation, diagnostics, and scenario record sequentially to the live address.
The scenario record is written last.
No isolated generation, atomic promotion, concurrent-reader test, idempotent retry proof, retained prior pointer, or independent observer result is supplied.

## Consumer and migration inventory

A read-only draft inventory reports:

| Consumer or artifact class | Count | Supplied compatibility evidence |
| --- | ---: | --- |
| Existing R-100 generations with nominal stage 102.5 m | 18 | Method revision is recorded for 12 and absent for 6. |
| Selected-library memberships | 7 | All point to E1 generations. |
| Composite products | 2 | Both name selected R-100 generations but do not record boundary-field identity. |
| Screening exports | 4 | Each records source generation but no method-compatibility decision. |
| Pending requests at the readable 102.5 m address | 3 | Requested method revision is not recorded. |

The proposed migration would apply P2 only to new requests.
It would leave E1 generations, P2 generations, memberships, composites, exports, and pending requests together under the same nominal label.
No mixed-method compatibility rule, complete-inventory reconciliation, selected-membership guard, or consumer update is defined.

## Proposed validation study

The draft study would:

1. Run E1 and P2 at R-100 discharges 60 and 100 m3/s on one model generation.
2. Compare maximum depth and runtime.
3. Choose a WSE-difference threshold after reviewing the two results.
4. Use the same runs to tune interpolation details and declare validation.
5. Keep terrain, roughness, domain, grid, time step, initial state, and source scenarios fixed without testing interactions.

The study omits:

- the 140 m3/s R-100 scenario;
- nominal stages 102.0 and 103.0 m;
- cases near the 90 percent coverage boundary;
- alternate wet-mask and nodata patterns;
- local WSE histories, full balance, edge behavior, flux, and extent;
- grid, time-step, domain, initial-state, terrain, roughness, and source-stage sensitivity;
- immutable source, producer, and solver provenance;
- independent observations or a defined benchmark;
- datum-transformation uncertainty;
- separation of tuning and validation evidence;
- predeclared metrics, thresholds, uncertainty allowances, and failure rules; and
- representative failure and ambiguous results.

## Proposed operations, monitoring, and rollback

The proposal describes isolated staging, integrity verification, atomic promotion, independent materialization observation, and a shadow-only pointer as future controls.
It supplies no implementation or executed evidence for those controls.

Monitoring would count completed scenarios and average runtime.
It would not monitor source identity, coverage, masks, field integrity, local WSE, balance, edge contact, output distributions, selected membership, consumer compatibility, or materialization retraction.
No alert threshold, retained diagnostic packet, response responsibility, or reassessment trigger is supplied.

Rollback would restore the E1 configuration value.
The proposal does not guarantee retention of the prior accepted generation, restore pointers or selected memberships, retract P2 dependencies, rebuild affected composites or exports, cancel pending requests, or demonstrate that restored consumers can read the restored generation.

## Part A: Frame the change and responsibility

State the existing and proposed methods, intended shadow use, prohibited uses, decision status, and evidence cutoff.
List every responsibility function and retain Unassigned where the packet supplies no accountable authority.
Explain why parser capability does not constitute scientific approval or backward compatibility.

## Part B: Audit affected contracts and consumers

Create a table with these columns:

| Area | Supplied state | Required change or evidence | Failure if omitted |
| --- | --- | --- | --- |

Include at least:

- method authority;
- input validation and record schema;
- scenario identity and address;
- exact reuse and broader compatibility;
- source provenance, masks, datum, and coverage;
- publication, retry, and materialization;
- selected memberships and pending requests;
- composites and exports;
- verification, validation, sensitivity, and uncertainty;
- monitoring and failure meaning; and
- responsibility, migration, halt, acceptance, and rollback.

## Part C: Analyze identity and compatibility

Explain how E1 and P2 can collide at one readable address.
State why changing only a readable method label is insufficient.
Identify the minimum P2 identity and generation fields.
Define what must be observed before reuse.
Classify the 6 generations with no method revision and the 3 pending requests without inventing compatibility.

## Part D: Replace the validation study

Explain why the two-run post hoc study cannot support even the stated shadow comparison.
Design the smallest defensible program for the stated use.
Include predeclared criteria, representative stages and discharges, coverage and mask cases, numerical evidence, local and spatial outputs, sensitivity and interactions, independent comparison, provenance, failed results, and a separate acceptance decision.
Do not invent a universal threshold.

## Part E: Design migration, rollout, monitoring, and rollback

Provide an ordered, fail-closed plan.
It must inventory and classify all affected generations and consumers before mutation.
It must use collision-safe identity, isolated publication, independent observation, a nonselectable shadow stage, explicit promotion and halt criteria, complete monitoring, retained prior evidence, and rollback of artifacts and consumers as well as configuration.
Assign no responsibility that the packet leaves unassigned.

## Part F: Issue the methodology-change verdict

Choose one verdict from [Scientific Methodology Review](../07-system-design/03-scientific-methodology-review.md):

- READY
- READY WITH CONDITIONS
- NOT READY

State the verdict first.
Then list each blocking gap, its impact, the unassigned or assigned responsibility, the smallest evidence or change needed for another review, and the uses that remain prohibited.

Do not use READY WITH CONDITIONS to defer missing authority, ambiguous identity, absent rollback, undefined acceptance criteria, incomplete validation, or unassigned material responsibility.

## Deliverable

Submit a review with these sections:

1. Verdict and intended use.
2. Change status and responsibility table.
3. Contract and consumer impact table.
4. Identity, compatibility, reuse, and publication analysis.
5. Replacement validation program.
6. Migration, rollout, monitoring, and rollback plan.
7. Blocking gaps and prohibited uses.

## Competency criteria

The lab is complete when the answer:

- distinguishes E1 from the unapproved P2 proposal;
- preserves every responsibility gap;
- rejects parser capability as evidence of scientific compatibility;
- identifies same-address collision, unsafe reuse, and partial-publication risks;
- inventories selected memberships, composites, exports, and pending requests;
- replaces post hoc tuning and validation with predeclared independent evidence;
- defines a nonselectable shadow stage and complete rollback;
- uses only READY, READY WITH CONDITIONS, or NOT READY; and
- issues NOT READY because authority, identity, validation, migration, rollback, monitoring, and responsibility evidence are missing.

After completing the lab, compare the reasoning with [Lab 13 Solution](solutions/lab-13-review-a-methodology-change-solution.md).
