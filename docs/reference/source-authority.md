# Source Authority

This reference defines how the handbook labels claims and matches evidence to the question being answered.
The labels describe the role of evidence, not confidence by themselves.
A claim can require more than one label when scientific sources, examples, engineering principles, and supplied evidence address different parts of the question.

## Evidence labels

### Scientific foundation

Use **Scientific foundation** for established hydrologic, hydraulic, geospatial, statistical, or numerical concepts.
Prefer authoritative textbooks, standards, government publications, solver documentation, and primary research for these claims.
A scientific foundation explains physical or mathematical meaning, but it does not prove that a particular model or workflow applies the concept correctly.

### Applied example

Use **Applied example** for a synthetic engineering example constructed to teach reasoning across concepts.
State the assumptions, inputs, calculations, and limits needed to interpret the example.
Synthetic examples carry no claim about a named external implementation.

### Design principle

Use **Design principle** for a reusable engineering or scientific-software rule.
Examples include explicit identity, datum compatibility, bounded validation, deterministic naming, atomic publication, and evidence proportional to the claim.
A design principle guides decisions but does not prove that a particular system follows the rule.

### Evidence note

Use **Evidence note** for a statement about what a supplied packet, calculation, observation, or test can and cannot establish.
Preserve the evidence scope, assumptions, provenance, result, and verification limits.
An evidence note can support or challenge a conclusion without extending beyond the observed conditions.

### Open question

Use **Open question** for a conflict, gap, unvalidated assumption, or policy question that requires resolution.
Do not silently choose an answer to make the handbook narrative simpler.

## Authority by claim type

| Claim type | Primary authority | Required boundary |
| --- | --- | --- |
| Established science | Scientific foundation | Map the general concept to the intended use separately. |
| Synthetic calculation or workflow | Applied example | Preserve assumptions and do not imply behavior by a named external system. |
| Reusable engineering rule | Design principle | Treat the rule as guidance, not proof of conformance. |
| Supplied observation or test result | Evidence note | Preserve provenance, conditions, measured quantities, and limits. |
| Unresolved conflict or gap | Open question | State what evidence or decision would resolve it. |

When sources answer different questions, keep the answers separate and label their evidence roles.
When sources answer the same question differently, preserve the disagreement as an **Open question**.

## Citation discipline

Every external scientific claim should identify a source record from the [Bibliography and Source Map](bibliography.md).
Digital sources should include a direct URL and access date.
Scientific-source attribution must preserve the named authors or public organizations responsible for the work.
Do not use a citation to support a broader claim than the cited source makes.

Inference must be identified as inference when it joins premises that no source states directly.
Name the premises and the conditions under which the inference holds.
Do not restate an inference as an observation.

## Validation evidence discipline

Software verification, numerical verification, scientific validation, calibration, benchmark comparison, plausibility review, acceptance criteria, operational monitoring, and post-run diagnosis answer different questions.
Do not collapse them into a generic claim that a model is validated.

Software verification asks whether the software implements its specified behavior correctly.
Numerical verification asks whether discretization and solution procedures are sufficiently accurate for the stated use.
Calibration estimates or adjusts uncertain parameters against observations under stated constraints.
Validation evaluates whether model results adequately represent the relevant physical system for an intended use.
Uncertainty analysis characterizes how uncertain inputs, assumptions, parameters, and numerical choices affect conclusions.
Operational monitoring observes execution and outputs over time but does not replace scientific validation or acceptance.
Acceptance applies explicit criteria and authority to a defined use.

An acceptance statement must name the intended use, evidence scope, criteria, restrictions, and unresolved uncertainty.
The presence of a manifest, artifact, warning-free result, convergence value, visually plausible map, or one benchmark is evidence only for its bounded claim.
None independently establishes hydraulic adequacy.

## Applied-example discipline

An **Applied example** must be self-contained or depend only on cited public sources and files supplied with the handbook.
Identify synthetic names, values, records, and evidence packets as constructed teaching material.
State the intended lesson, material assumptions, and limits of transfer.

An applied example can demonstrate reasoning, calculations, failure modes, contracts, and review practice.
It cannot establish external system behavior, operational thresholds, readiness, scientific validity, or acceptance.
An experiment description remains a plan until completed observations and provenance are supplied.
An observed symptom does not establish its cause.

## Methodology-change readiness

Scientific methodology reviews may conclude `READY`, `READY WITH CONDITIONS`, or `NOT READY` as defined in [Scientific Methodology Review](../07-system-design/03-scientific-methodology-review.md).
These generic verdicts apply only to the methodology change, intended use, evidence packet, restrictions, and decision context stated in the review.
They are not a general declaration that a model, product, or operating environment is ready.

`READY` means the stated acceptance criteria are satisfied for the stated use and the evidence supports implementation, compatibility, identity, provenance, migration, software verification, numerical verification, validation, materialization, rollout, rollback, monitoring, and acceptance as applicable.
`READY WITH CONDITIONS` means the evidence supports only a bounded use and every condition is explicit, assigned, observable, time-bounded, and enforced before broader use.
`NOT READY` is required when scientific validity is unknown, critical evidence is missing, identity is ambiguous, rollback is absent, acceptance criteria are undefined, or a material responsibility is unresolved.

The three-verdict change gate does not replace the diagnostic vocabulary in [Validation Framework](../06-validation-and-qc/01-validation-framework.md).
It translates the evidence packet into a bounded conclusion about a methodology change.
