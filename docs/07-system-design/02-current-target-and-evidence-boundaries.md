# Evidence Boundaries in Scientific Software

A trustworthy review asks each source only the question that source can answer.
A specification, implementation, test, design document, runtime observation, scientific study, and acceptance record can all be accurate while supporting different claims.

## Why this topic matters

Scientific software reviews often fail through source substitution.
A schema field is treated as proof of runtime behavior, design intent is treated as proof of deployment, one test is treated as general validation, or an operational success is treated as scientific acceptance.
The resulting statement can be confident while its evidence answers another question.

## Prerequisites

Read [Source Authority](../reference/source-authority.md), [Scientific Software Contracts](01-scientific-software-contracts.md), [Validation Framework](../06-validation-and-qc/01-validation-framework.md), and [Applied Evidence Catalog](../06-validation-and-qc/04-case-issue-and-experiment-catalog.md).

## Learning objectives

After this chapter, the reader should be able to:

- choose evidence that controls the specific claim being reviewed;
- distinguish design intent, implementation source inspection, executed test evidence, operational evidence, and acceptance evidence;
- state what a synthetic packet can and cannot establish;
- label an inference and expose its premises; and
- preserve conflicts instead of averaging incompatible sources.

## Use the five evidence labels

| Evidence label | Question it can answer | Boundary |
| --- | --- | --- |
| **Scientific foundation** | What does an established scientific, numerical, or evaluation concept mean? | It does not prove that a particular workflow applies the concept correctly. |
| **Applied example** | How can a constructed packet illustrate a calculation, contract, failure, or review? | It makes no claim about an external system. |
| **Design principle** | What reusable engineering rule should guide design or review? | It does not prove that a system conforms to the rule. |
| **Evidence note** | What can and cannot be concluded from the supplied observation, calculation, or test? | The conclusion cannot exceed the supplied scope and provenance. |
| **Open question** | Which conflict, gap, or missing decision prevents a supported conclusion? | It does not authorize a convenient answer. |

These labels describe evidence roles rather than confidence levels.
One claim can require several labels when scientific sources, design rules, examples, and observations address different parts of the reasoning.

## Match the source class to the claim

| Source class | Supported claim | Unsupported extension |
| --- | --- | --- |
| Public scientific source | Definition, theory, standard, or general evaluation expectation. | A particular method decision, implementation behavior, or accepted result. |
| Method decision record | Intended choice, scope, status, and responsibility stated by that record. | Implementation, execution, validation, or deployment. |
| Design specification | Intended architecture, interfaces, responsibilities, and invariants. | Conformance, operational success, or scientific adequacy. |
| Implementation source inspection | Logic present in the reviewed revision and behavior that logic could produce if executed under its assumptions. | Realized behavior, deployment, operational success, or acceptance. |
| Schema | Accepted and serialized structure when the runtime actually uses that schema. | Complete semantics, compatible values, or execution behavior beyond enforcement. |
| Generated documentation | Declared interface as generated from a source. | Behavior that conflicts with the executable source or runtime evidence. |
| Test source or fixture | Intended checks and the structure of one supplied input packet. | That a test executed or that the asserted behavior occurred. |
| Executed test result | Realized behavior of a named build under the recorded inputs, environment, and test procedure. | Untested paths, deployed behavior, representative scientific validity, or production state. |
| Experiment plan | Proposed factors, controls, metrics, and procedure. | An executed result or observed effect. |
| Completed study | Observations and conclusions within its recorded method and sample. | Universal transfer, policy authority, or untested conditions. |
| Runtime observation | What a named invocation, environment, and time window showed. | Intended method, broad validation, or future conformance. |
| Storage observation | Presence, integrity, and generation consistency found at an observed address. | Numerical accuracy, physical validity, or acceptance. |
| Monitoring record | Configured signals and events over a recorded interval. | Absence of unmonitored failures or continued scientific adequacy. |
| Acceptance record | Criteria, evidence, verdict, restrictions, responsibility, and date for a named use. | Use outside the recorded scope or continued adequacy after material drift. |

**Design principle:** Evidence authority depends on the claim type rather than the document's age, detail, or location.

## Separate design intent from conformance

A design specification can define that scientifically unequal objects receive different identities, that publication is atomic, and that materialization requires storage observation.
Those statements establish desired behavior.

Conformance requires separate evidence:

- source inspection can show whether the reviewed revision contains logic intended to implement the design;
- executed software tests can show bounded realized behavior under recorded inputs and an identified environment;
- runtime evidence can show which revision and settings actually ran;
- storage observation can show which generation and assets were present; and
- acceptance evidence can show whether the result met criteria for the stated use.

**Evidence note:** Finding a requirement in a design document does not prove implementation or operation.
Finding corresponding code does not prove deployment.
Finding one runtime success does not prove complete conformance or scientific acceptance.

## Separate source inspection from executed behavior

Source inspection can establish which validation branches, calculations, record fields, address rules, publication steps, and return paths are encoded in a reviewed revision.
It establishes implemented logic and potential behavior rather than proof that a branch executed or produced a particular result.

An executed test result can establish realized behavior for its named build, input packet, test procedure, and environment.
It cannot establish deployed behavior unless the tested build and environment are also shown to be the deployed ones.

Runtime evidence can establish what a named deployed invocation did under its realized inputs and environment.
It cannot establish behavior outside that invocation or prove that unobserved branches conform to the design.

Runtime evidence should name:

- deployed build or image identity;
- request and attempt identity;
- start and end time;
- realized inputs and environment;
- process status, termination, warnings, and failures;
- published and independently observed artifacts;
- integrity and generation results;
- materialization state;
- monitoring window; and
- known missing observations.

A returned artifact address is response evidence.
A readable record is storage-presence evidence.
A record plus independently verified assets, identity, integrity, and generation consistency can support materialization.
None of those automatically establishes acceptance.

## Separate tests from scientific validation

Software tests can verify calculations, validation rules, error paths, identity derivation, or publication controls.
Numerical tests can compare with analytical results, manufactured solutions, or refinement expectations.

Scientific validation requires comparison with relevant independent physical observations under a stated intended use.
A fixture can support a contract or calculation claim without representing the range of real systems.

**Scientific foundation:** [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) and [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) distinguish verification from validation and bound conclusions by their evidence domain.

## Separate a plan from a completed result

An experiment plan can specify a strong design before any observation exists.
A completed result must add realized inputs, source identities, execution evidence, observations, metrics, uncertainty, failed checks, and a bounded conclusion.

**Applied example:** The roughness and grid record in the [Applied Evidence Catalog](../06-validation-and-qc/04-case-issue-and-experiment-catalog.md#experiment-plan-roughness-and-grid-interaction) defines four combinations and an interaction metric.

**Evidence note:** The record supports review of the planned method but no claim about actual sensitivity.

## Keep operational completion separate from scientific acceptance

Operational evidence can show that a request executed, a retry was suppressed, a generation was promoted, required assets were observed, or an alert fired.
Scientific acceptance asks whether the result satisfies criteria for a named use.

The [partial generation withheld](../06-validation-and-qc/04-case-issue-and-experiment-catalog.md#operational-observation-partial-generation-withheld) record shows this boundary.
The observer correctly withholds an incomplete generation, but the event contains no validation evidence for the hydraulic result.

**Design principle:** A successful control action is evidence about the control, not about every property of the object it protects.

## State inference where it occurs

Inference is necessary when a review connects premises that no single source states together.
The sentence must name its premises and remain no stronger than them.

Use this pattern:

> **Evidence note:** Given premise A and premise B, consequence C follows under condition D.

### Synthetic identity inference

**Applied example:** A scenario's complete record includes a downstream-stage source, but the address identity includes only reach, discharge, and grid.
The publication contract allows a new attempt to write to the derived address after exact reuse rejects an unequal record.

**Evidence note:** Given the omitted boundary-source identity and same-address publication, two scientifically unequal scenarios can target one address unless another generation control intervenes.
This is a design-risk inference from the supplied construction.
It does not establish that an overwrite occurred in an external system.

## Build evidence by claim

A review should use one row per decision-relevant claim.

| Claim | Controlling evidence | Supporting evidence | Missing evidence | Allowed conclusion |
| --- | --- | --- | --- | --- |
| The method decision specifies a stage-aware boundary for a stated reach class. | Authorized method decision with scope and status. | Scientific sources and completed studies. | None for the bounded intent claim. | The stated method is intended within that scope. |
| The reviewed source implements checks for a stage field with units and datum. | Source inspection of validation logic and the schema at a named revision. | Test source that exercises the checks. | Executed test evidence. | The reviewed revision contains the stated checks. |
| A named build accepts a valid stage field and rejects invalid references. | Executed test output with inputs, build, procedure, and environment. | Source inspection. | Deployment evidence if a deployed-behavior claim is needed. | The named build produced the observed results under the test conditions. |
| A deployed invocation used that field. | Versioned runtime request, realized record, and build identity. | Logs and metrics. | None for the observed invocation if complete. | The named invocation used the recorded field. |
| The scenario generation is materialized. | Independent storage observation of required assets, identity, integrity, and generation. | Producer publication record. | None for the bounded storage claim if complete. | The required generation was observed under its storage contract. |
| The method is adequate for screening use. | Acceptance record applying predeclared criteria to verification, validation, uncertainty, and operational evidence. | Scientific sources, studies, and monitoring. | Any unmet criterion or unassigned responsibility. | Only the bounded verdict and restrictions stated in the record. |

This structure prevents a strong source for one claim from being stretched to cover another.

## Preserve conflicts and missing evidence

When two sources answer the same question differently, retain both claims with their evidence roles and record an **Open question**.
Do not choose the newer document, more detailed prose, executable behavior, or desired architecture automatically.

Source inspection can accurately describe implemented logic, and executed evidence can accurately describe realized behavior, while either conflicts with a method decision.
The implementation or execution fact and the decision intent should both remain visible until responsibility resolves the mismatch.

**Open question:** If the organization has not defined which record controls a specific method decision, the review cannot create that authority by inference.

## Evaluate evidence scope

For every observation or study, record:

1. object and revision identity;
2. realized inputs and transformations;
3. spatial, temporal, hydraulic, and numerical conditions;
4. quantities, units, datums, metrics, and uncertainty;
5. controls, sample, and excluded conditions;
6. result and failed or missing checks;
7. intended decision and allowed conclusion; and
8. prohibited generalizations and reassessment triggers.

**Evidence note:** A narrow result is not weak merely because it is narrow.
It becomes misleading only when its scope is hidden or extended without support.

## Common misconceptions

### The design says it, so the system does it

Design intent requires conformance and operational evidence before it becomes a behavior claim.

### The code accepts it, so the method is approved

Implementation capability does not supply method authority or validation.

### The completed example proves the default

A completed example supports its recorded configuration and conclusion.
Broader adoption requires decision authority and representative evidence.

### The dashboard is the source of truth

A dashboard summarizes selected observations.
The underlying versioned events, records, artifacts, and criteria control detailed claims.

### The inference is obvious, so it is a fact

An inference can be useful and strong while still requiring visible premises, conditions, and limitations.

## Competency check

1. Name the evidence needed to establish method intent, implemented logic, executed test behavior, deployed behavior, materialization, and acceptance.
2. Explain why checked-in observer logic is not operational evidence.
3. Explain what an experiment plan can and cannot contribute to a decision.
4. Write one labeled inference about address collision without claiming that a collision occurred.
5. Explain why a materialization rule does not prove scientific acceptance.

## Further reading and source notes

- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) supports intended-use credibility, data pedigree, verification, validation, uncertainty, and acceptance boundaries.
- [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) supports distinctions among software correctness, numerical accuracy, physical validation, and bounded validity.
- [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) supports decision-specific model evaluation, data quality, sensitivity, uncertainty, calibration, and corroboration.
