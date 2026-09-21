# Validation Framework

Hydraulic adequacy is a conclusion about a stated use, not a permanent property of a model.
The conclusion requires distinct evidence for software behavior, numerical behavior, physical representation, uncertainty, and acceptance.

## Why this topic matters

A completed calculation can be internally consistent and still answer the wrong question.
A plausible map can also result from incomplete provenance, incompatible datums, unresolved numerical error, or compensating parameter choices.
Separating the evidence prevents one successful check from standing in for the whole credibility argument.

## Prerequisites

Review [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md), [Domain and Boundary Geometry](../04-model-development/04-domain-and-boundary-geometry.md), [Compositing, Identity, and Provenance](../05-scenario-libraries/05-compositing-identity-and-provenance.md), and [Source Authority](../reference/source-authority.md).

## Learning objectives

After this chapter, the reader should be able to:

- distinguish software verification, numerical verification, validation, calibration, benchmark comparison, plausibility review, diagnosis, monitoring, and acceptance;
- explain what each evidence type can and cannot establish;
- define validation around an intended use and predeclared criteria;
- build a traceable evidence record without extending a result beyond its scope; and
- issue a bounded conclusion that states missing evidence and restrictions directly.

## Start with the intended use

**Scientific foundation:** Validation evaluates the degree to which a model represents the relevant real system for an intended use.
[SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) ties verification, validation, uncertainty, and acceptance criteria to intended use.
[SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) distinguishes code verification, solution verification, and validation.
[SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) connects environmental-model quality to the decision the model will support.

Record these items before gathering evidence:

1. State the decision that the result will support.
2. Name the hydraulic quantities, units, datums, locations, spatial support, and time or scenario range being judged.
3. State the consequences of false acceptance and false rejection.
4. Identify the model, data, method, solver, and artifact generations in scope.
5. Name the acceptance responsibility and the evidence available to that responsibility.
6. Define permitted uses, prohibited uses, and conditions that require reassessment.

**Design principle:** Define the claim before selecting a metric or threshold.
A depth raster can support broad screening while remaining unsuitable for a decision that requires accurate local velocity, structure head loss, or arrival time.

## Keep the evidence types distinct

| Evidence type | Question answered | What it cannot establish alone |
| --- | --- | --- |
| Software verification | Does the software implement its specified calculation and failure behavior? | Whether the equations, inputs, and abstractions represent the real system adequately. |
| Numerical verification | Does the computed solution adequately approximate the selected mathematical model for the stated quantities? | Whether the mathematical model represents reality for the intended use. |
| Validation | Does the model agree adequately with independent physical observations for the stated use? | Adequacy outside the tested places, regimes, quantities, and conditions. |
| Calibration | Which defensible parameter values improve agreement with calibration data? | Independent predictive performance or freedom from compensating error. |
| Benchmark comparison | How does the model compare with a defined analytical, numerical, or observational referent? | Universal validity or accuracy beyond the benchmark scope. |
| Plausibility review | Are patterns physically coherent and free of obvious contradictions? | Quantified accuracy, provenance, numerical adequacy, or uncertainty. |
| Diagnosis | Which explanation is best supported for a particular symptom? | Acceptance for use unless the diagnosis also closes every required criterion. |
| Operational monitoring | Does repeated operation remain within recorded limits and expected contracts? | Missing verification, validation, or initial acceptance. |
| Acceptance | Does the complete evidence packet satisfy authorized criteria for the stated use? | Continued adequacy after relevant drift without monitoring and reassessment. |

### Software verification

**Scientific foundation:** Software verification asks whether implemented behavior satisfies its specification.
Useful evidence includes unit and integration tests, schema validation, regression tests, conservation test cases, failure-path tests, and comparison with analytical or manufactured solutions where appropriate.

**Evidence note:** A passing software test establishes only the behavior and conditions exercised by that test.
It does not establish that the selected physical model or inputs are adequate.

### Numerical verification

**Scientific foundation:** Numerical verification examines discretization, convergence, conservation, and solution error for the selected mathematical model.
Relevant work can include grid and time-step refinement, iterative convergence, transient histories, balance residuals, and comparisons with known solutions.

**Evidence note:** A small change in domain-total storage between saved outputs is not a full mass balance and does not prove local stability.
The evidence must match the quantity and scale used in the decision.

### Validation

**Scientific foundation:** Validation compares model results with independent observations of the relevant physical system.
The observation uncertainty, representativeness, timing, datum, units, and spatial support are part of the comparison.

Validation does not prove universal truth.
It supports a bounded conclusion over the tested conditions and stated use.

### Calibration

**Scientific foundation:** Calibration adjusts uncertain model parameters against a referent under stated constraints.
[SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) and [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) distinguish calibration from validation.

Calibration data are not independent validation data because they influenced parameter selection.
When independent observations are scarce, a justified cross-validation design must preserve held-out evidence and disclose its limits.
Parameter changes must not conceal errors in terrain, datum, forcing, boundaries, structures, or numerical settings.

### Benchmark comparison and plausibility review

A benchmark comparison requires a defined referent, comparable quantities, compatible references, a metric, and an explicit scope.
The referent can be an observation, an analytical result, a controlled test case, or another model with stated limitations.

A plausibility review asks whether the result follows represented connectivity, responds in the expected direction to forcing, and avoids obvious contradictions.
Plausibility is valuable for triage but cannot replace quantified comparison and provenance.

## Define acceptance criteria before inspecting the answer

**Scientific foundation:** Acceptance criteria are qualitative or quantitative rules used to judge fitness for a named purpose.
The criteria should identify the quantity, metric, support, datum, uncertainty treatment, threshold, responsible decision, and response to failure.

Define criteria before inspecting results whenever practical.
This timing prevents the answer from selecting its own passing rule.

**Design principle:** A criterion is reviewable only when another person can apply it to the same evidence and reach the same disposition.

Acceptance criteria should cover every material lane for the intended use:

- input identity and provenance;
- software verification;
- numerical verification and conservation;
- calibration separation;
- validation or benchmark evidence;
- uncertainty and sensitivity;
- artifact integrity and materialization;
- restrictions and unresolved questions; and
- monitoring and reassessment triggers.

**Open question:** Which criteria are required for a particular product use remains a decision for the accountable organization.
This handbook does not invent universal hydraulic thresholds.

## Build a reviewable evidence record

A complete record should contain the following sections even when evidence is unavailable:

1. **Question and intended use.**
State the decision, quantities, domain, conditions, and consequences of error.
2. **Configuration and provenance.**
Record input identity, transformations, method version, solver identity, settings, and artifact generation.
3. **Software verification.**
Name the specification, checks, revisions, results, and unresolved failures.
4. **Numerical verification.**
Record resolution studies, convergence histories, balance terms, edge diagnostics, and estimated numerical error.
5. **Calibration.**
Identify adjusted parameters, bounds, objective, data, nonuniqueness, and calibration domain.
6. **Validation or benchmark comparison.**
Identify independent referents, comparability, metrics, uncertainty, results, and applicability.
7. **Plausibility and diagnosis.**
Record reviewed patterns, competing hypotheses, discriminatory checks, and unresolved contradictions.
8. **Sensitivity and uncertainty.**
Describe influential factors, interactions, evidence limits, and unavailable uncertainty information.
9. **Materialization and acceptance.**
Separate observed artifact completeness from the scientific decision and its restrictions.
10. **Monitoring.**
Define drift signals, alert thresholds, evidence retention, response responsibility, and reassessment triggers.

Each evidence statement should use one of the five labels in [Source Authority](../reference/source-authority.md).

## Interpret common observations narrowly

| Observation | Supported conclusion | Unsupported extension |
| --- | --- | --- |
| A process returns successfully. | The process reached a recorded completion state. | Inputs, artifacts, numerics, and hydraulics are correct. |
| An artifact exists. | Bytes are present at an observed address. | The artifact is complete, compatible, current, or accepted. |
| A manifest matches a request. | Declared fields satisfy the stated comparison. | Referenced assets exist or the result remains scientifically suitable. |
| A convergence metric passes. | The defined metric met its threshold over the recorded support. | Full balance, local stability, grid independence, or validation. |
| No warning is reported. | No implemented reporting rule emitted a warning. | Every relevant check ran and passed. |
| A map looks plausible. | No obvious contradiction was identified in that review. | Quantified agreement, correct provenance, or bounded uncertainty. |
| One benchmark agrees. | The model agrees under the benchmark configuration and metric. | Transfer to untested reaches, regimes, methods, or uses. |

## Use bounded conclusion language

A validation record should conclude with one of these statements for the stated use:

- **Meets the stated criteria** when every required criterion passes within the recorded evidence scope.
- **Meets the stated criteria with restrictions** when the criteria explicitly permit a bounded use and every restriction is visible and enforceable.
- **Does not meet the stated criteria** when a required criterion fails or a material condition lies outside the permitted range.
- **Insufficient evidence to assess** when the packet cannot support either acceptance or a bounded rejection.

**Design principle:** Do not translate operational success into a scientific acceptance conclusion.
The `READY`, `READY WITH CONDITIONS`, and `NOT READY` verdicts in [Scientific Methodology Review](../07-system-design/03-scientific-methodology-review.md) apply to a named methodology change and its complete review packet.

## Applied example

**Applied example:** The completed boundary-distance comparison in the [Applied Evidence Catalog](04-case-issue-and-experiment-catalog.md#completed-case-boundary-distance-comparison) examines one synthetic reach, one discharge, and one area of interest.
Its predeclared metric passes for that configuration.

**Evidence note:** The result supports a bounded statement about boundary-placement sensitivity in the supplied packet.
It does not validate the model against the real world because the packet contains no independent physical observations.
It also does not establish a universal placement distance or threshold.

## Common misconceptions

### Calibration made the model valid

Calibration improves agreement with the data used for tuning.
Independent evaluation is still required for a validation claim.

### The benchmark is the truth

A benchmark is a referent with its own scope, assumptions, and uncertainty.
Report agreement or disagreement without broadening the benchmark into universal truth.

### Materialization proves adequacy

Materialization establishes that a required artifact set was independently observed under its storage contract.
It does not establish numerical accuracy or scientific acceptance.

### Monitoring replaces validation

Monitoring detects drift and failures relative to an accepted baseline.
It cannot create the missing credibility evidence for that baseline.

## Competency check

1. Give one example of evidence for each evidence type in the table.
2. Explain why calibration data cannot also serve as independent validation data without a justified design.
3. State the minimum information needed to make a benchmark comparison reviewable.
4. Explain why a present manifest and warning-free result can still be scientifically unacceptable.
5. Write a bounded conclusion for a packet that has numerical evidence but no independent observations or authorized criteria.

## Further reading and source notes

- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) supplies definitions and requirements for verification, validation, calibration, uncertainty, and intended-use acceptance.
- [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) distinguishes code verification, solution verification, validation, and bounded regions of validity.
- [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) covers decision-specific model quality, calibration, corroboration, sensitivity, uncertainty, and evaluation.
