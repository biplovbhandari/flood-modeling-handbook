# Sensitivity and Uncertainty

Sensitivity analysis asks how a stated output changes when a factor changes.
Uncertainty characterization asks what is not known about inputs, methods, calculations, artifacts, and operations, and how that lack of knowledge limits a decision.
Neither activity supplies validation or acceptance by itself.

## Why this matters

A flood map can be highly sensitive to one factor in one reach class and nearly insensitive to the same factor elsewhere.
Two individually modest uncertainties can also interact and produce a material change that neither one-at-a-time test reveals.
The review therefore needs an explicit taxonomy, a decision-relevant experiment design, interaction tests, and evidence limits.

## Prerequisites

Review [Forcing Sources and Uncertainty](../01-hydrology-for-fim/05-forcing-sources-and-uncertainty.md), [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md), [Domain and Boundary Geometry](../04-model-development/04-domain-and-boundary-geometry.md), [Compositing, Identity, and Provenance](../05-scenario-libraries/05-compositing-identity-and-provenance.md), [Validation Framework](01-validation-framework.md), and [Diagnostic Workflow](02-diagnostic-workflow.md).
Use [Source Authority](../reference/source-authority.md) to distinguish scientific guidance, selected methodology, current implementation, target design, evidence, and open questions.

## Learning objectives

After this chapter, the reader should be able to:

- classify a project concern in the complete uncertainty taxonomy;
- explain why uncertainties interact rather than behaving as independent additive error bars;
- design a sensitivity study around a decision, output, metric, reach class, controls, and evidence limits;
- use the project sensitivity order as a review heuristic without claiming a validated universal ranking; and
- include contract and operational sensitivities without pretending that they are ranked by physical impact.

## 1. Separate the uncertainty classes

The classes below identify different mechanisms and evidence needs.
A concern can belong to more than one class when its mechanism crosses system boundaries.

| Uncertainty class | What varies or is not known | Examples in this system | Decision-relevant evidence |
| --- | --- | --- | --- |
| Forcing | The magnitude, timing, duration, spatial allocation, frequency interpretation, or source version of hydraulic inflow is uncertain. | AEP discharge, `Q_HFT`, hydrograph reduction to a steady discharge, tributary allocation, and adjacent-reach flow dependence. | Source provenance, units, time support, statistical assumptions, alternative forcings, and output response. |
| Terrain | The represented land surface, grid alignment, source revision, resampling, surface treatment, or vertical reference is uncertain. | Continually updated 3DEP source, reprojection, grid snapping, DEM differences, and road or bridge surfaces. | Versioned source, horizontal and vertical references, transformation record, comparison surfaces, and hydraulic response. |
| Bathymetry | The submerged channel form and its merge with land topography are incomplete or uncertain. | Missing below-LiDAR channel geometry, surveyed topobathymetry coverage, and overlap treatment. | Survey provenance, datum and time compatibility, merge method, rating or WSE comparison, and transfer limits. |
| Roughness | Effective resistance, categorical mapping, spatial variation, stage dependence, and calibration transfer are uncertain. | Annual NLCD class assignment, Manning lookup values, channel polygons, and compensating calibration. | Physically plausible alternatives, mapping completeness, independent evaluation, and joint tests with terrain and resolution. |
| Network | Direction, adjacency, segmentation, reach identity, trimming, merging, and lake or coast treatment are uncertain. | Prepared-reach lineage, short-reach merging, waterbody breakpoints, and omitted coastal reaches. | Versioned topology, source-to-prepared lineage, validation checks, and downstream dependency traces. |
| Domain | The spatial extent, active area, clipping rule, and ownership of expansion are uncertain. | Initial rectangle, authored domain, edge contact, wide floodplain, and scenario-driven expansion. | Alternative extents, edge evidence, expected influence area, cost, and response inside the area of interest. |
| Boundary | The type, location, magnitude, geometry, and hydraulic meaning of imposed conditions are uncertain. | Normal-depth slope, known WSE, transferred cell values, `FREE`, outflow spans, and inflow lines. | Alternative boundary realizations, distance-to-area-of-interest response, boundary audit, and regime context. |
| Parameter | A configurable physical or empirical value is uncertain within a selected model structure. | Manning's n, wet-dry threshold, duration, convergence tolerance, and smoothing scale. | Plausible range, source or calibration basis, controlled comparison, and response metric. |
| Numerical | Discretization, time integration, convergence, wetting logic, precision, and solver implementation affect the computed solution. | Grid size, time step, CFL behavior, storage-change termination, and interpolation. | Space-time refinement, conservation, repeated runs where needed, and version-pinned solver evidence. |
| Initial-condition or hot-start | The starting state and compatibility of a reused state with target forcing and boundaries are uncertain. | Dry start, depth-only `startfile`, source scenario selection, and omitted motion state. | Cold-start and defensible hot-start comparison, source identity, target duration, and final-state equivalence metrics. |
| Structural-model | The equations, represented processes, dimensional assumptions, and omitted structures may be inadequate for the intended use. | Local-inertial shallow-water formulation, no explicit culvert or bridge hydraulics, steady scenario abstraction, and no rainfall-runoff simulation. | Alternative model forms or bounded benchmarks, process-specific observations, and explicit domain of applicability. |
| Solver | The exact executable, build, configuration, hardware-sensitive behavior, and failure semantics are uncertain. | Unpinned executable details, LISFLOOD-FP versus documented SFINCS capability, return status, and termination escalation. | Executable identity, settings, environment, regression evidence, logs, and solver-specific verification. |
| Scenario-sampling | A finite scenario library may omit important discharge-stage combinations or misrepresent response between samples. | Adaptive ND trials, discharge grid, KWSE bounds, stage grid, joint-frequency plan, and selected membership. | Coverage rationale, interpolation error, endpoint behavior, membership record, and out-of-sample checks. |
| Identity and reuse | Different scientific objects may collide, or an old object may be reused under incomplete compatibility checks. | Baked Decision Register revision, domain code, rounded boundary values, exact-input short circuit, and missing executable identity. | Collision-resistant identity, compatibility contract, immutable source version, asset re-observation, and lineage. |
| Publication and materialization | A producer may return or publish an incomplete, mixed-generation, or incorrectly described artifact set. | Sequential writes, manifest-last publication, missing STL or Zarr, wrong product CRS label, and unchecked referenced assets. | Observed required assets, checksums, schema and identity match, atomicity or generation markers, and retry behavior. |
| Composite | Combining otherwise valid local results can change values, extent, provenance, or interpretation. | Pixelwise maximum, overlapping reach artifacts, incompatible grids or datums, and source-specific anomalies. | Compatibility checks, source membership, overlap diagnostics, single-domain comparison, and composite provenance. |
| Operational | Scheduling, retries, resource limits, source drift, partial failure, and monitoring can alter or obscure the realized scientific product. | Wall-time termination, retry of non-idempotent publication, source updates, missing dependency observation, and alert gaps. | Attempt identity, idempotency, resource and timing record, drift monitoring, failure retention, and reassessment triggers. |

Parameter uncertainty and structural-model uncertainty must remain separate.
Changing Manning's n within one model structure does not test whether the omitted culvert relation or steady-scenario abstraction is adequate.
Numerical uncertainty and solver uncertainty must also remain separate.
A grid refinement study on one executable does not identify behavior changes caused by a different build or implementation.

## 2. Treat interactions as mechanisms

Do not add every uncertainty as if each were an independent symmetric error bar.
The model is nonlinear, many factors are correlated, and several factors change which process controls the answer.

Important interaction patterns include:

- downstream condition and domain extent, because a boundary placed inside its hydraulic influence zone can dominate an otherwise adequate interior solution;
- terrain and bathymetry with roughness, because an adjusted Manning value can compensate for missing conveyance and then fail when transferred;
- grid resolution with terrain, structures, roughness, and wet-dry settings, because refinement changes which pathways, barriers, classes, and shallow connections are represented;
- inflow distribution with domain and network geometry, because a source placed too close to the area of interest can create a local WSE artifact that compositing preserves;
- convergence control with initial state, forcing, and wetting, because a depth-only hot start or slowly filling floodplain can change when the termination proxy crosses its threshold;
- stage-transfer geometry with raster registration, datum, nodata, and downstream scenario choice, because a correct nominal stage does not repair a misregistered cell-specific transfer;
- scenario sampling with downstream-stage uncertainty, because an omitted discharge-stage combination cannot be recovered by interpolating an unrelated response branch;
- identity and source-version uncertainty with reuse, because equal declared inputs can still reference revised or missing assets;
- publication with retries and address collisions, because separate generations can mix even when every individual file is valid; and
- composite rules with upstream inflow anomalies, DEM differences, and overlap membership, because pixelwise maximum can preserve the higher artifact rather than cancel it.

An interaction test should be selected when a causal mechanism predicts that the effect of one factor depends on another factor.
Full factorial testing is not automatically necessary, but one-factor-at-a-time testing is not sufficient when such a mechanism is plausible.

## 3. Define the decision before the sensitivity design

[SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) and [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) tie sensitivity and uncertainty work to intended use and model evaluation.
[SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity) distinguishes physical-parameter sensitivity from numerical sensitivity and recommends realistic ranges.

Every sensitivity design should record all of these fields:

1. **Decision or acceptance question.**
State what choice the study will inform and what evidence would change that choice.
2. **Factor.**
Name one controlled factor or an explicit interaction among factors.
3. **Plausible range or alternatives.**
Give sourced physical ranges, candidate methods, exact versions, or discrete contract alternatives rather than arbitrary percentages.
4. **Controlled variables.**
Identify what remains fixed, including source versions, model identity, forcing, grid, solver build, run settings, initial state, domain, boundary, and output processing.
5. **Outputs.**
Name quantities such as WSE, depth, extent, velocity, storage, outflow, STL values, selected membership, artifact completeness, or compute cost.
6. **Metric.**
Define the comparison statistic, units, spatial and temporal support, datum, masking, and sign convention.
7. **Scenario or reach class.**
State the tested discharge-stage condition and the hydraulic setting, such as low-gradient backwater, steep confined reach, lake outlet, coast, confluence, structure, wide floodplain, or divergent pathway.
8. **Replication or sample design.**
State reach selection, flow and stage coverage, repeats where stochastic or hardware variability matters, interaction combinations, and held-out cases.
9. **Acceptance or decision rule.**
Name the authorized criterion or state that no authorized threshold exists.
10. **Evidence limits.**
State what the study cannot establish, including transfer to untested reaches, regimes, sources, solver versions, or product uses.

### Compact design template

| Field | Required entry |
| --- | --- |
| Question | Which decision will this evidence inform? |
| Factor | What varies, alone or in a defined interaction? |
| Range or alternatives | Why are these values or methods plausible? |
| Controls | What stays identical and how is that verified? |
| Outputs and metric | Which quantity, units, support, datum, mask, and statistic are compared? |
| Scenario or reach class | Where and under which forcing and boundary regime is the result applicable? |
| Replication or sampling | How are reaches, scenarios, repeats, and interactions selected? |
| Decision rule | What evidence changes the decision, and who owns that rule? |
| Limits | What remains uncertain after the study? |

## 4. Use the project sensitivity order as a review heuristic

The following order begins with factors likely to change hydraulic control or represented storage over broad areas, then moves toward numerical and termination details.
It is a review heuristic for allocating attention, not a project-validated universal ranking.
Project evidence supports concern in several categories, but it does not estimate comparable effect distributions across the full reach population.

1. **Downstream condition and domain.**
Vary boundary type, magnitude, slope, location, edge span, domain extent, and the distance between the boundary and the decision area.
2. **Terrain and topobathymetry.**
Vary source versions or defensible surfaces, submerged-channel treatment, structure representation, resampling, and datum assumptions.
3. **Roughness and resolution.**
Vary defensible Manning mappings together with grid sizes that alter terrain, class, pathway, and wetting representation.
4. **Inflow distribution.**
Vary line or point geometry, location, width, active-cell intersection, tributary allocation, and warm-up distance.
5. **Wet-dry settings.**
Vary solver-supported thresholds and related time-step settings while examining extent, thin-water storage, connectivity, and stability.
6. **Convergence controls.**
Vary output interval, tolerance, required persistence, duration, and stopping logic while retaining full mass-balance and local-response evidence where available.

This order should be revised for a stated decision when a known mechanism, observation, or completed project experiment supports a different priority.
For example, a structure-dominated site can require terrain and structure representation before a general downstream-boundary study.
The order remains a review heuristic unless project evidence measures comparable sensitivities over representative reach and scenario classes.

## 5. Add high-risk contract sensitivities without forcing them into the physical ranking

Some factors can invalidate interpretation even when their effect cannot be ordered by metres of WSE or hectares of inundation.
Test these as contract pass-fail or alternative-realization questions:

- vertical datum compatibility and transformations;
- CRS and horizontal-unit compatibility;
- transfer-point index bounds, raster shape, transform, bounds, registration, and nodata handling;
- identity collisions and omitted identity inputs;
- immutable source version and source drift;
- cold-start versus depth-only hot-start state;
- selected scenario membership and reuse compatibility;
- publication completeness, generation mixing, checksum integrity, and observed materialization; and
- composite source membership, quantity, grid, datum, resampling, nodata, and provenance compatibility.

These checks are high risk because failure can make the compared objects scientifically different or incomplete.
They are not claimed to outrank a boundary or terrain factor by physical effect.

## 6. Choose a study design that matches the question

### Screening

Use a small, deliberately diverse factor set to find changes large enough to affect the decision.
Screening is useful for allocating later work, but it cannot estimate a complete uncertainty distribution.

### One-factor-at-a-time comparison

Use this design when one factor has a clear independent interpretation and interaction risk is low or separately tested.
Keep every named control fixed and verify the control through identities and artifact provenance.

### Factorial or paired interaction design

Use paired or factorial combinations when the mechanism predicts dependence, such as terrain with roughness, grid with wet-dry settings, or domain with downstream condition.
Interpret an interaction as a conditional response, not as a new universal correction factor.

### Ensemble or sample-based propagation

Use an ensemble when distributions or weighted alternatives are defensible and the decision needs an output distribution.
Do not assign a probability distribution merely because a factor is uncertain.
Correlated factors, shared source errors, and scenario dependence must be represented or explicitly excluded.

### Contract perturbation

Use exact alternative objects or deliberately invalid fixtures for identity, registration, source-version, reuse, publication, and materialization questions.
The result is often a detection or rejection capability rather than a hydraulic response curve.

## 7. Interpret the result without false precision

Report the realized comparison before generalizing.
Name the factor values, controls, outputs, metric, reach class, scenario, model and source identities, solver evidence, and failed checks.
Separate these conclusion types:

- **local sensitivity result**, which describes the tested case;
- **screening priority**, which directs further study;
- **uncertainty bound**, which requires justified coverage and combination assumptions;
- **diagnostic clue**, which narrows competing causes; and
- **acceptance evidence**, which requires an authorized intended-use criterion.

A small change in one metric does not prove insensitivity in all quantities.
A large change does not identify which alternative is more accurate without an appropriate referent.
A stable output under parameter changes does not eliminate structural, identity, publication, or operational uncertainty.

## 8. Project evidence boundaries

**Evidence or experiment:** Case-001, Case-002, Case-007, Case-015, Case-018, and Case-019 show bounded sensitivity or comparison evidence for downstream conditions, domains, bathymetry, compositing, convergence metrics, or normal-depth slope.
The complete scope and limitations are indexed in [Case, Issue, and Experiment Catalog](04-case-issue-and-experiment-catalog.md).

**Evidence or experiment:** Case-018 contains the completed observations conducted under the EXP-013 and EXP-014 headings.
The standalone EXP-013 and EXP-014 records describe methods and do not independently contain those results.

**Open question:** No reviewed project record establishes a representative cross-reach sensitivity ranking, joint uncertainty distribution, universal uncertainty allowance, or authorized sensitivity threshold.
The six-part order in this chapter is therefore repeatedly and explicitly a review heuristic.

**Current implementation:** [XW-020](../reference/decision-code-artifact-crosswalk.md#xw-020-sensitivity-and-uncertainty-evidence) maps the exact reviewed schemas, constants, jobs, utilities, and solver modules that expose terrain, roughness, domain, boundary, grid, wetting, convergence, hot-start, identity, reuse, publication, and artifact factors.
Those reviewed paths do not themselves provide evidence that a systematic sensitivity study was run or accepted.
Whether another component owns the missing sensitivity, uncertainty, or acceptance capabilities remains an Open question.

## Common misconceptions

### "Sensitivity is uncertainty"

Sensitivity measures response to specified changes.
Uncertainty also requires a defensible account of what values or alternatives are plausible and how they relate.

### "The largest local effect is the universal priority"

One reach and one scenario do not establish a population ranking.
The project order is a review heuristic unless representative evidence supports a stronger claim.

### "Independent ranges can be added"

Inputs can be correlated, responses can be nonlinear, and one factor can change the controlling mechanism.
An additive calculation requires evidence for its combination assumptions.

### "A reused result removes uncertainty"

Reuse can reduce compute, but it adds compatibility, identity, source-version, asset-integrity, and materialization questions.

## Competency check

1. Classify a downstream-stage error, a wrong vertical datum, a depth-only hot start, and a missing STL asset in the taxonomy.
2. Explain one mechanism by which terrain, roughness, and grid resolution interact.
3. Design a sensitivity study with all ten required fields for a low-gradient reach whose downstream boundary may influence the area of interest.
4. Explain why the six-part sensitivity order is a review heuristic rather than a validated ranking.
5. Name three contract sensitivities that can invalidate interpretation without being ranked by physical impact.

## Further reading and source notes

- [SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity) provides official USACE guidance on physical and numerical sensitivity.
- [SCI-029](../reference/bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance) provides official USACE guidance on interaction among grid, terrain, time step, wetting fronts, and represented hydraulics.
- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) defines uncertainty characterization and sensitivity-analysis expectations within intended-use modeling practice.
- [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) supports separation of numerical approximation, verification, validation, and bounded validity.
- [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) supplies decision-specific environmental-model sensitivity and uncertainty guidance.
- [SDR-004](../reference/bibliography.md#sdr-004-cases-issues-and-experiments) identifies the bounded local project evidence cataloged in the next chapter.
