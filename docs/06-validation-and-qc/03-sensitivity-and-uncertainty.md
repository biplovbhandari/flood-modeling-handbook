# Sensitivity and Uncertainty

Sensitivity analysis asks how a stated output changes when a factor changes.
Uncertainty characterization asks what is not known about inputs, methods, calculations, artifacts, and operation, and how that lack of knowledge limits a decision.
Neither activity supplies validation or acceptance by itself.

## Why this topic matters

A flood map can be highly sensitive to one factor in one hydraulic setting and nearly insensitive to the same factor elsewhere.
Two individually modest uncertainties can also interact and create a material response that one-factor-at-a-time testing does not reveal.
A useful study therefore starts with a decision, names plausible alternatives, tests mechanisms, and preserves its evidence limits.

## Prerequisites

Review [Forcing Sources and Uncertainty](../01-hydrology-for-fim/05-forcing-sources-and-uncertainty.md), [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md), [Compositing, Identity, and Provenance](../05-scenario-libraries/05-compositing-identity-and-provenance.md), [Validation Framework](01-validation-framework.md), and [Diagnostic Workflow](02-diagnostic-workflow.md).

## Learning objectives

After this chapter, the reader should be able to:

- classify uncertainty by mechanism and evidence need;
- explain why uncertainty sources can interact and cannot always be added;
- design a sensitivity study around an intended decision and realistic alternatives;
- distinguish a local sensitivity result from an uncertainty bound or acceptance result; and
- include identity, publication, reuse, and operational uncertainty in scientific review.

## Separate the uncertainty classes

A concern can belong to more than one class when its mechanism crosses system boundaries.

| Uncertainty class | What varies or remains unknown | Decision-relevant evidence |
| --- | --- | --- |
| Forcing | Magnitude, timing, duration, spatial allocation, frequency interpretation, or source version. | Provenance, units, time support, statistical assumptions, alternatives, and output response. |
| Terrain | Surface elevation, source realization, conditioning, transformation, resampling, registration, or vertical reference. | Immutable source identity, references, transformations, comparison surfaces, and hydraulic response. |
| Bathymetry and structures | Submerged channel form, bridge or culvert conveyance, merge method, or coverage. | Survey and structure provenance, datum compatibility, realized geometry, comparison evidence, and limits. |
| Roughness | Effective resistance, categorical mapping, spatial variation, stage dependence, or calibration transfer. | Physically defensible alternatives, mapping completeness, independent evaluation, and interaction studies. |
| Network and topology | Direction, adjacency, segmentation, lineage, confluences, diversions, or waterbody treatment. | Versioned topology, lineage, consistency checks, and dependency traces. |
| Domain | Extent, active area, clipping, expansion, and the location of artificial boundaries. | Alternative extents, edge evidence, influence distance, cost, and response in the area of interest. |
| Boundary | Type, value, geometry, source, direction, spatial support, and hydraulic regime. | Alternative realizations, boundary audits, response with distance, and compatible source scenarios. |
| Parameter | A configurable physical or empirical value within one model structure. | Plausible range, source or calibration basis, controlled comparison, and decision metric. |
| Numerical | Grid, time integration, convergence, conservation, wetting, interpolation, or precision. | Refinement, histories, balance evidence, reference solutions, and version-pinned calculations. |
| Initial state | Starting depth, motion state, stored water, and compatibility with target forcing and boundaries. | Cold-start and compatible alternate-start comparisons with complete source identity. |
| Model form | Equations, dimensional assumptions, represented processes, and omitted processes. | Alternative formulations, process-specific observations, benchmarks, and an explicit applicability domain. |
| Solver realization | Executable build, options, environment, failure semantics, or hardware-sensitive behavior. | Build identity, configuration, regression evidence, logs, and repeatability checks where needed. |
| Scenario sampling | Coverage of discharge, stage, timing, joint conditions, and interpolation space. | Coverage rationale, endpoint behavior, interpolation error, membership, and out-of-sample checks. |
| Identity and reuse | Omitted identity fields, mutable sources, lossy names, incomplete compatibility checks, or stale acceptance. | Complete identity, collision analysis, immutable sources, asset observation, lineage, and invalidation rules. |
| Publication and materialization | Partial writes, mixed generations, missing assets, incorrect metadata, or failed observation. | Required assets, integrity values, atomicity, generation markers, observer evidence, and retry behavior. |
| Composite | Source membership, overlap, grid, datum, nodata, resampling, and combination rule. | Compatibility checks, source provenance, overlap diagnostics, alternative products, and composite records. |
| Operational | Scheduling, retries, resource limits, source drift, partial failure, or monitoring gaps. | Attempt identity, idempotency, resource record, drift monitoring, retained failures, and reassessment triggers. |
| Observation | Measurement error, sampling, representativeness, timing, support, datum, and processing. | Observation provenance, uncertainty model, alignment, quality control, and independence from calibration. |

Parameter uncertainty and model-form uncertainty must remain separate.
Changing roughness within one formulation does not test whether omitted conveyance or a steady-forcing abstraction is adequate.
Numerical uncertainty and solver-realization uncertainty must also remain separate.
A grid refinement study on one build does not identify behavior changes introduced by a different executable.

## Treat interactions as mechanisms

Do not add every uncertainty as though each were an independent symmetric error bar.
Hydraulic response is nonlinear, input errors can be correlated, and one factor can change which process controls the answer.

Important interaction patterns include:

- downstream condition with domain extent because a nearby artificial boundary can dominate the area of interest;
- terrain and bathymetry with roughness because adjusted resistance can compensate for missing conveyance;
- grid size with terrain, structures, roughness, and wet-dry settings because resolution changes represented pathways and classes;
- inflow distribution with domain and topology because source placement can create local water-surface artifacts;
- convergence control with initial state, forcing, and wetting because storage history can change when a stopping rule is met;
- stage transfer with grid registration, datum, nodata, and source scenario because a correct nominal stage does not repair an incompatible field;
- scenario sampling with downstream-stage uncertainty because an omitted discharge-stage pair cannot be recovered from an unrelated response branch;
- identity with source-version drift because equal declared inputs can refer to different bytes;
- publication with retries because separate attempts can create a mixed generation; and
- compositing with source anomalies because a maximum rule can preserve the higher artifact rather than cancel it.

**Design principle:** Test an interaction when a credible mechanism predicts that the effect of one factor depends on another factor.
A full factorial study is not automatically required, but a one-factor-at-a-time design is insufficient when the interaction can change the decision.

## Define the decision before the design

**Scientific foundation:** [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) and [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) tie sensitivity and uncertainty work to intended use and model evaluation.
[SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity) distinguishes physical-parameter sensitivity from numerical sensitivity and supports realistic ranges.

Every sensitivity design should record these fields:

1. **Decision question.**
State which choice the study will inform and what evidence could change that choice.
2. **Factor or interaction.**
Name the controlled factor or explicit combination of factors.
3. **Plausible alternatives.**
Use sourced physical ranges, candidate methods, exact source versions, or discrete contract alternatives.
4. **Controls.**
Identify what remains fixed and how identity and provenance verify that claim.
5. **Outputs.**
Name decision-relevant quantities such as water-surface elevation, depth, extent, velocity, storage, balance residual, selected membership, completeness, or compute cost.
6. **Metric.**
Define units, spatial and temporal support, datum, mask, aggregation, and sign convention.
7. **Hydraulic setting.**
State the tested forcing, boundary regime, topology, terrain class, and structure context.
8. **Sampling and replication.**
State how locations, scenarios, repeats, held-out observations, and interactions are selected.
9. **Decision rule.**
Name the authorized criterion or state that no criterion exists.
10. **Evidence limits.**
State what the study cannot establish and which changes require reassessment.

## Choose a design that matches the question

### Screening

Use a deliberately diverse set of factors to identify changes large enough to affect the decision.
Screening directs later work but does not estimate a complete uncertainty distribution.

### One-factor-at-a-time comparison

Use this design when one factor has a clear independent interpretation and interaction risk is low or separately tested.
Keep every named control fixed and verify the controls through identity and provenance.

### Paired or factorial interaction design

Use paired or factorial combinations when a mechanism predicts dependence, such as terrain with roughness or domain with downstream condition.
Interpret the result as a conditional response rather than a universal correction.

### Ensemble or sample-based propagation

Use an ensemble when probability distributions or weighted alternatives are defensible and the decision needs an output distribution.
Do not assign a distribution merely because a factor is uncertain.
Represent correlations and shared source errors or state that they are excluded.

### Contract perturbation

Use exact alternative objects or deliberately invalid fixtures for identity, registration, reuse, publication, and materialization questions.
The result is often a detection or rejection capability rather than a hydraulic response curve.

## Prioritize by the decision mechanism

A useful initial review order is:

1. downstream condition and domain;
2. terrain, bathymetry, and structures;
3. roughness and grid resolution;
4. forcing and inflow distribution;
5. wet-dry and time-step behavior; and
6. convergence and stopping controls.

This order is a review heuristic, not a universal ranking.
Revise it when observations or mechanism knowledge identify another likely control.
A structure-dominated site can require conveyance representation before a general downstream-boundary study.

Test high-risk contract factors in parallel with physical factors:

- horizontal and vertical reference compatibility;
- grid registration, bounds, masks, nodata, and index validity;
- identity completeness and address collision;
- immutable source versions and transformation provenance;
- initial-state compatibility;
- selected scenario membership and dependency compatibility;
- publication completeness and one-generation integrity; and
- reuse, materialization, and acceptance state.

These factors can invalidate interpretation even when their effect cannot be ranked in metres or hectares.

## Interpret results without false precision

Report the realized alternatives, controls, outputs, metric, hydraulic setting, source identities, solver evidence, and failed checks before generalizing.
Separate these conclusion types:

- a **local sensitivity result** describes the tested packet;
- a **screening priority** directs further study;
- an **uncertainty bound** requires justified coverage and combination assumptions;
- a **diagnostic clue** narrows competing explanations; and
- **acceptance evidence** requires a stated use and authorized criterion.

A small change in one metric does not prove insensitivity in every quantity.
A large change does not identify which alternative is more accurate without an appropriate referent.
A stable output under parameter changes does not remove model-form, identity, publication, or operational uncertainty.

## Applied examples

**Applied example:** The [roughness and grid interaction plan](04-case-issue-and-experiment-catalog.md#experiment-plan-roughness-and-grid-interaction) proposes four controlled combinations for a synthetic reach.
It states its factors, controls, metrics, sampling, and stop conditions but contains no observations.

**Evidence note:** The plan demonstrates a reviewable experiment design.
It cannot support a response magnitude, preferred setting, uncertainty bound, or acceptance conclusion until the experiment is executed and its provenance is supplied.

**Applied example:** The [inconclusive transferred-stage result](04-case-issue-and-experiment-catalog.md#inconclusive-result-transferred-stage-comparison) contains model differences but lacks verified vertical-reference compatibility for one source.

**Evidence note:** The reported differences establish that the two supplied outputs disagree under the packet's processing.
They cannot establish a hydraulic effect of the transfer method because a datum offset remains a competing explanation.

## Uncertainty in calibration and validation

Calibration should record parameter bounds, objective functions, observation uncertainty, nonuniqueness, and the domain over which parameters were estimated.
Validation should use independent evidence where practical and should retain observation uncertainty and comparability limits.

**Scientific foundation:** [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) emphasizes decision-specific data quality, sensitivity, uncertainty, calibration, and corroboration.

**Open question:** The acceptable treatment of correlated observation and model errors depends on the intended decision and available evidence.
Do not assume independence only because it simplifies aggregation.

## Common misconceptions

### Sensitivity is uncertainty

Sensitivity measures response to specified changes.
Uncertainty also requires a defensible account of which values or alternatives are plausible and how they relate.

### The largest local effect is the universal priority

One location and scenario do not establish a population ranking.
Transfer requires representative sampling and a stated inference model.

### Independent ranges can simply be added

Inputs can be correlated, responses can be nonlinear, and one factor can change the controlling process.
An additive calculation requires evidence for its combination assumptions.

### Reuse removes uncertainty

Reuse reduces computation but introduces compatibility, identity, source-version, artifact-integrity, and acceptance questions.

## Competency check

1. Classify a wrong vertical datum, an omitted culvert, a depth-only initial state, and a missing artifact in the taxonomy.
2. Explain one mechanism by which terrain, roughness, and grid resolution interact.
3. Design a sensitivity study using all ten required fields for a low-gradient reach affected by downstream control.
4. Explain why the suggested review order is a heuristic rather than a validated ranking.
5. Name three contract uncertainties that can invalidate interpretation without a measurable hydraulic effect.

## Further reading and source notes

- [SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity) provides public guidance on physical and numerical hydraulic sensitivity.
- [SCI-029](../reference/bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance) discusses interactions among grid, terrain, time step, wetting fronts, and represented hydraulics.
- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) defines uncertainty characterization and sensitivity-analysis expectations for intended-use modeling.
- [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) supports separation of numerical approximation, verification, validation, and bounded validity.
- [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) supplies decision-specific environmental-model sensitivity and uncertainty guidance.
