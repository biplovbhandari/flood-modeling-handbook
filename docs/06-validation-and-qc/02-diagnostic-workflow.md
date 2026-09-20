# Diagnostic Workflow

Diagnosis should reduce uncertainty about the next decision rather than maximize the number of inspected artifacts.
Begin with competing hypotheses, then choose the next observation most likely to separate them.

## Why this matters

Hydraulic symptoms are rarely unique to one cause.
A high WSE can result from roughness, terrain, a blocked structure, downstream control, inflow error, boundary placement, unresolved transient behavior, or datum mismatch.
Changing a parameter before distinguishing those explanations can hide the original defect and create a compensating error.

## Prerequisites

Complete [Validation Framework](01-validation-framework.md) and review the model-development and scenario-library chapters.
Use the project mappings in [Decision-Code-Artifact Crosswalk](../reference/decision-code-artifact-crosswalk.md) and the unresolved policies in [Conflicts and Open Questions](../reference/conflicts-and-open-questions.md).

## Learning objectives

After this chapter, the reader should be able to:

- follow the complete diagnostic order without skipping authority, provenance, or materialization checks;
- form at least three materially different hypotheses for an ambiguous symptom;
- select a next check by discriminatory value, cost, and reversibility;
- map known project failure signatures to evidence and bounded next checks; and
- stop or escalate without converting an unresolved diagnosis into acceptance.

## 1. Use a hypothesis-driven next-check loop

For each symptom, write a compact hypothesis table before inspecting more evidence.

| Field | Required content |
| --- | --- |
| Symptom | The observed quantity, location, scenario, time, units, and comparison basis. |
| Hypotheses | At least three causes that imply materially different corrective actions. |
| Predictions | The observation expected if each hypothesis is true and the observation expected if it is false. |
| Candidate next checks | Bounded observations that can be obtained without changing the model first. |
| Selection rule | Prefer the check that most cleanly separates the leading hypotheses at acceptable cost and risk. |
| Stop condition | State what evidence supports a bounded diagnosis or readiness verdict. |
| Escalation condition | State which missing authority, data, or policy prevents a responsible conclusion. |

Do not start with an unrestricted review of every map, log, file, and parameter.
Do not tune roughness, expand the domain, alter a boundary, or change terrain until evidence identifies the category that the change is meant to test.

### A simple discrimination example

Suppose WSE is high upstream of a road.
The leading hypotheses are a missing culvert pathway, roughness that is too high, and a downstream boundary that imposes excessive backwater.

- A terrain and connectivity profile through the road can distinguish a blocked structure from the other two causes.
- A longitudinal WSE comparison upstream and downstream of the boundary-influence zone can distinguish a localized blockage from broad downstream control.
- A bounded roughness sensitivity run can assess resistance only after source class, lookup, terrain, structure, and boundary evidence are credible.

The first check should inspect the represented pathway and local head discontinuity because it can rule in or rule out the structure hypothesis without using a parameter change to compensate for missing conveyance.

## 2. Follow the ordered diagnostic workflow

The order is deliberate.
Later stages depend on evidence established by earlier stages.
When a stage fails, record the failure and decide whether later inspection can still add useful bounded evidence without implying readiness.

### Step 1: Question and decision context

State what decision is blocked and what result is suspicious.
Identify the quantity, units, datum, location, time or scenario, intended use, comparison basis, and consequence of error.
Confirm whether the review concerns a model, one scenario, a selected library, a transfer dependency, a composite, or an operational product.

**Discriminating question:** Would the same numerical difference matter for the actual decision, or is the symptom outside the intended use and review domain?

### Step 2: Forcing and provenance

Verify discharge or stage values, units, source version, reach identity, time or frequency basis, bounds, rounding, and transformation history.
Confirm that a planned target, a submitted job, a returned path, a published trial, and a selected library member have not been treated as interchangeable.
Trace immutable or content-based source identity when available.

**Discriminating question:** Does the suspicious response remain after comparing the exact realized forcing and provenance rather than a label or folder name?

### Step 3: Network and source data

Verify directed prepared-network topology, source-to-prepared identifier lineage, upstream and downstream adjacency, lake or coast tags, removed or merged reaches, and source versions.
Check whether the target geometry and related reaches belong to the same prepared-network revision.
Inspect source-data completeness, quality metadata, and changed remote content.

**Discriminating question:** Is the apparent hydraulic anomaly consistent with the intended network relationship, or does it follow a topology, identifier, or source-version error?

### Step 4: Terrain and roughness

Inspect the longitudinal and cross-channel terrain profiles, controlling high and low cells, bathymetry treatment, hydroflattening, drainage enforcement, bridges, culverts, and resampling.
Verify source LULC classes, categorical reprojection, lookup coverage, positive roughness values, spatial patterns, and whether any calibration applies to this setting.

**Discriminating question:** Does the symptom align spatially with a terrain or roughness transition, obstruction, nodata artifact, or changed source realization?

### Step 5: CRS and datums

Verify horizontal CRS, axis order, projected units, affine transform, bounds, resolution, cell registration, vertical datum, vertical units, and any transformation.
Do this before adding depth to terrain, comparing WSE, sampling a transfer raster, or compositing grids.

**Discriminating question:** Does the anomaly disappear when quantities are compared on the same horizontal support and vertical reference, or does a half-cell, index, unit, or datum offset explain it?

### Step 6: Geometry

Inspect the realized domain, active-cell mask, inflow lines, outflow areas, stage-transfer lines, reach endpoints, centerlines, buffers, extra geometries, and rasterized cell mappings.
Check intersections, coverage, connected components, index bounds, and unintended negative-index wrap or out-of-range sampling.

**Discriminating question:** Do the vector features select the intended active cells and hydraulic pathways under the realized grid?

### Step 7: Boundaries

Trace every inflow, slope-based outflow, fixed or transferred stage, closed edge, and initial condition.
Use the actual mathematical and implemented behavior rather than substituting the labels `FREE`, freefall, and normal depth.
Confirm boundary values, geometry, sign, orientation, source scenario, and intended role.

**Discriminating question:** Would the observed WSE, flow direction, or edge behavior follow from the realized boundary even if terrain and roughness were correct?

### Step 8: Solver state

Record executable and build identity, image digest when available, run configuration, hardware when relevant, process exit, watcher action, saved-output schedule, initial state, hot-start source, and any ignored or unsupported input.
Distinguish a solver-declared completion from watcher termination, wall time, edge termination, convergence termination, and post-processing success.

**Discriminating question:** Did the intended solver and configuration evolve the intended initial-boundary problem, or did execution state alter or truncate the experiment?

### Step 9: Convergence and balance

Inspect time histories of decision-relevant local and global quantities.
Include depth, WSE, velocity or flux where available, wet extent, storage, inflow, outflow, sources and sinks, and residuals.
Treat the current volume-convergence ratio as a storage-change proxy, not full mass balance.
Compare cold and compatible alternative hot starts where initial-condition dependence is material.

**Discriminating question:** Is the symptom stationary and conserved enough for the use, or is it a residual filling, draining, oscillation, cancellation, or initial-state effect?

### Step 10: Edge evidence

Inspect every wet perimeter component with side, cell index, terrain, depth, WSE, intended role, connectivity, and boundary assignment.
Record whether the edge check activated, why it was withheld, whether edge water was allowed, and whether a simultaneous event was suppressed by termination priority.
Separate an intended outlet or transfer edge from an artificial clipping edge.

**Discriminating question:** Is water at the perimeter an intended hydraulic connection, an isolated immaterial fringe, a closed-edge pileup, or unintended leakage?

### Step 11: Scenario membership

Identify the full set of simulated and published trials, the selected members, adaptive references and positions, re-judgments, endpoints, skips, and off-grid values.
Verify that a manifest's presence is not being used as proof of selected membership.
Check reuse compatibility and the authority of the selection rule.

**Discriminating question:** Is the suspicious scenario part of the intended library, or only a trial, reused input, proposal state, or unobserved plan target?

### Step 12: Network continuity

Compare adjacent reach models across overlaps and transfer lines using compatible WSE, depth, flux, forcing, datum, grid, and scenario provenance.
Trace downstream-to-upstream dependency direction separately from physical river-flow direction.
Check confluences, divergent paths, lakes, coasts, and source-scenario bindings.

**Discriminating question:** Does the discontinuity originate within one model, at a transfer or topology boundary, or from incomparable scenarios?

### Step 13: Compositing

Verify the producing component, scenario selection, source manifests, quantity, units, datum, grid, resampling, nodata, overlap, and pixel rule.
DR-004 ALT-D selects pixelwise maximum, but the selected rule does not establish source compatibility or product validation.

**Discriminating question:** Is the anomaly present in each source model, or is it created by scenario selection, registration, overlap, nodata, or the compositing rule?

### Step 14: Uncertainty

List input, parameter, model-form, numerical, observation, transfer, and compositing uncertainties that can affect the decision-relevant output.
Use sensitivity evidence to identify influential sources without treating sensitivity as uncertainty quantification.
State when no quantitative or justified qualitative uncertainty estimate is available.

**Discriminating question:** Is the observed difference larger than the combined, decision-relevant uncertainty under comparable support?

### Step 15: Method limitations

Compare the intended use with the shallow-water assumptions, grid scale, source resolution, missing structures, steady-forcing abstraction, boundary method, reach decomposition, transfer method, and validation domain.
Identify where the requested interpretation exceeds the evidence or the model's permissible use.

**Discriminating question:** Would better data or settings solve the problem, or is the method itself unable to support the requested claim?

## 3. Map project failure signatures to next checks

The issue records below are **Evidence or experiment**.
Most state a symptom without establishing a cause.
The diagnostic mapping therefore preserves competing explanations and asks for the next observation rather than prescribing a universal fix.

| Failure signature | Project evidence | Competing hypotheses | High-value next check |
| --- | --- | --- | --- |
| Low downstream WSE | ISU-001 | Excessive drainage at an edge, weak downstream control, wrong datum, low terrain, low roughness, forcing mismatch, or continued draining. | Use the bounded longitudinal evidence bundle below to compare realized boundary values, edge flux, terrain, datum, roughness, forcing, balance, and recent WSE change through the overlap. |
| High downstream WSE | ISU-004 and ISU-010 | Excessive downstream control, closed-edge pileup, obstruction, high roughness, terrain bias, forcing mismatch, or residual filling. | Use the bounded longitudinal evidence bundle below to compare boundary influence, wet-edge roles, local conveyance, terrain, roughness, forcing, complete balance, recent WSE change, and benchmark comparability. |
| Confluence narrowing | ISU-002 | Missing tributary forcing, wrong topology, incompatible stages, clipped lateral extent, transfer mismatch, or composite selection. | Trace both tributary and mainstem scenarios, topology, forcing, WSE, wet-component continuity, and source membership across the confluence. |
| Unintended edge leakage | ISU-003 | Incorrectly open edge, boundary span bridging dry gaps, wrong outflow geometry, divergent path, or grid registration error. | Persist cell-level edge roles and fluxes, then trace each leaking component to the intended outlet and boundary construction. |
| Divergent flow path | ISU-005 | Real bifurcation, wrong network direction, terrain saddle, culvert or structure omission, inflow placement, or domain overlap. | Compare prepared topology with terrain-controlled connectivity and face-level flow direction through the split. |
| Arbitrary edge cutoff | ISU-006 | Domain clipping, invalid active mask, dry-cell threshold, missing source coverage, or post-processing crop. | Overlay connected wet components, domain and active mask, source coverage, terrain, and output crop on the same grid. |
| Culvert blockage | ISU-007 | Missing or raised terrain pathway, absent structure relation, debris or capacity assumption, grid under-resolution, or wrong datum. | Inspect a longitudinal terrain and WSE profile through the crossing with represented connectivity and structure evidence. |
| WSE anomalies | ISU-008 | Inflow artifacts, mismatched DEMs, boundary transition, transfer smoothing, compositing maximum, or unresolved transient. | Determine whether the anomaly exists in source WSE grids before transfer and compositing and whether it aligns with an inflow, grid, or source transition. |
| Oversized domains | ISU-009 | Excessive buffer, topology or length error, unrelated extra geometry, unit mismatch, or deliberate floodplain coverage. | Decompose the final bbox contribution by each input geometry and verify units, snapping, and intended source area. |
| DEM differences | ISU-011 and Case-018 | Mutable remote bytes, retrieval timing, reprojection, resampling, nodata, or transform differences. | Compare immutable source versions or checksums and transformation provenance before attributing depth residuals to hydraulics. |
| Failure to reach quasi-steady state | ISU-012 | Insufficient duration, slow storage exchange, boundary reflection, hot-start dependence, oscillation, or metric insensitivity. | Inspect local and global time histories, inflow-outflow balance, storage, edge flux, and alternative initial conditions beyond the reported trigger. |
| Transfer misregistration | XW-009 and CONF-016 | Half-cell shift, CRS mismatch, vertical-datum mismatch, shape-only array addition, wrong index, negative-index wrap, or partial STL coverage. | Compare manifests and rasters cell by cell for shape, transform, bounds, CRS, units, datum, masks, registration, and bounded indices. |
| Identity collision | XW-015, XW-017, CONF-013, and CONF-016 | Omitted output-affecting inputs, lossy boundary naming, truncated identity, mutable source address, or same-address rebuild. | Reconstruct identity and target address from complete realized inputs, then compare stored manifest and asset generation metadata. |
| Missing materialization evidence | XW-015, XW-017, CONF-013, and CONF-016 | Returned path mistaken for observation, partial sequential publication, missing asset, stale manifest, checksum mismatch, or undeployed observer. | Independently observe the manifest and every required asset at the expected address and verify schema, identity, checksum, and one-generation consistency. |

### Low downstream-WSE prediction bundle

Use one aligned longitudinal evidence packet so the observations remain comparable rather than opening seven unrelated investigations.

- **Excessive edge drainage is supported** when unintended perimeter cells carry outward flux connected to the low-WSE reach segment, and it is weakened when every outward flux belongs to an intended boundary and no unintended connected loss exists.
- **Weak downstream control is supported** when the realized downstream stage or slope is lower or more freely draining than the intended comparable condition and the low profile lies within its influence, and it is weakened when the correct control is realized and the profile remains insensitive outside its influence.
- **Wrong datum is supported** when a consistent vertical offset appears between otherwise matching elevations or when metadata show incompatible references, and it is weakened when terrain, transferred WSE, and comparison data share a verified datum and transformation.
- **Low terrain is supported** when the realized bed or floodplain is lower than an immutable, co-registered source or survey in the same locations, and it is weakened when source checksums, transformations, and profiles agree within their stated uncertainty.
- **Low roughness is supported** when verified coefficients are lower than the authorized realization in the affected conveyance and a bounded sensitivity raises WSE in the expected zone, and it is weakened when the realized coefficients are correct and the response is materially insensitive.
- **Forcing mismatch is supported** when realized inflow or tributary contribution is lower than the intended scenario after units, reach, and time or frequency basis are reconciled, and it is weakened when provenance shows exact comparable forcing at every inlet.
- **Continued draining is supported** when local WSE and storage continue to fall or outflow exceeds inflow over the final intervals, and it is weakened when local state is stable and the complete balance closes under the authorized criterion.

### High downstream-WSE prediction bundle

Use the same aligned profile, edge, balance, and source packet so that a local obstruction can be separated from broad backwater or unresolved filling.

- **Excessive downstream control is supported** when the realized stage is higher or the slope condition is more restrictive than the intended comparable condition and the upstream profile shows a coherent backwater response, and it is weakened when the control is correct and the high band lies outside its influence.
- **Closed-edge pileup is supported** when a connected wet component reaches an unintended closed perimeter with rising local storage and little outward flux, and it is weakened when cell-level roles show intended discharge paths and no connected accumulation at a closed edge.
- **Obstruction is supported** when the WSE rise and head difference localize across a represented terrain barrier, missing culvert, bridge, or constriction, and it is weakened when verified conveyance and structure evidence show an open pathway without a localized head discontinuity.
- **High roughness is supported** when verified coefficients exceed the authorized realization in the affected conveyance and a bounded coefficient reduction lowers WSE in the expected zone, and it is weakened when coefficients are correct and the result is materially insensitive.
- **Terrain bias is supported** when co-registered immutable source or survey evidence shows the realized bed, bank, or barrier is too high, and it is weakened when checksums, transformations, profiles, and stated uncertainty agree.
- **Forcing mismatch is supported** when realized inflow or tributary contribution is higher than the intended comparable scenario after units, reach, and time or frequency basis are reconciled, and it is weakened when provenance shows exact forcing at every inlet.
- **Residual filling is supported** when local WSE and storage continue to rise or inflow exceeds outflow over the final intervals, and it is weakened when local state is stable and the complete balance closes under the authorized criterion.
- **Benchmark incomparability remains a separate explanation for ISU-010** when DEM bytes, roughness, boundaries, grids, initial state, or comparison time differ, and it is weakened only when those dimensions and their uncertainty are demonstrably comparable.

## 4. Choose among candidate checks

Rank each candidate check using four questions:

1. **Discrimination:** Will opposite outcomes favor different hypotheses?
2. **Directness:** Does it observe the suspected mechanism instead of a distant proxy?
3. **Cost and risk:** Can it be performed without a broad rerun, irreversible change, or new ambiguity?
4. **Authority:** Can its result answer the decision, or would a missing policy still block interpretation?

A cheap check with no discriminatory value is not the best first check.
A costly experiment can be justified when it is the only way to distinguish high-consequence explanations.
When two checks have similar value, prefer the one that preserves the current evidence and changes nothing.

## 5. Stopping and escalation

Stop the diagnostic loop when one of these conditions is met:

- evidence supports one bounded explanation and the remaining alternatives would not change the immediate action;
- the result meets or fails predeclared criteria under the authorized intended use;
- a missing source, policy, observation, or owner prevents meaningful discrimination;
- the requested use exceeds the method's demonstrated domain; or
- the next required action is a controlled experiment, code change, or methodology decision outside the diagnostic scope.

Escalate with a compact record containing the decision blocked, evidence labels, hypotheses, completed discriminatory checks, unresolved observations, authority gap, and exact evidence that could change the verdict.
Do not escalate a folder of unprioritized screenshots and logs without explaining which hypothesis each item supports or challenges.

## 6. Readiness verdict after diagnosis

A diagnosis and a readiness verdict answer different questions.
The diagnosis can state, "Terrain obstruction is the leading explanation for upstream ponding."
The readiness verdict can still be **NOT READY** because the structure treatment, validation evidence, and acceptance authority remain unresolved.

Use **INSUFFICIENT EVIDENCE TO ASSESS** when the packet cannot distinguish the leading hypotheses.
Use **NOT READY** when a material required condition is contradicted or missing for the stated use.
Use a ready verdict only under the criteria and authority described in [Validation Framework](01-validation-framework.md).

## Practical exercise

[Lab 12: Validate and Triage](../labs/lab-12-validate-and-triage.md) provides a bounded synthetic packet with competing terrain, transfer, and materialization hypotheses.
The exercise requires one immediate next check, predicted discriminatory observations, a stopping or escalation condition, evidence labels, and a direct readiness verdict.

## Common misconceptions

### "Start with the most visible anomaly"

The most visible map feature may be downstream of an earlier forcing, identity, datum, or geometry error.
Follow the ordered dependency chain.

### "Try a few parameters and see what looks better"

Unstructured tuning can create equifinality, where different erroneous parameter combinations fit the same observation.
Test the suspected mechanism and preserve independent evaluation evidence.

### "Every issue has a standard fix"

The project issue records name signatures, not universal causes or remedies.
The same signature can require different action under different topology, terrain, boundaries, and intended uses.

### "More evidence always reduces uncertainty"

Incomparable, untraceable, or redundant evidence can increase review burden without separating hypotheses.
Choose the next check for its expected discriminatory value.

## Competency check

1. Place transfer-raster registration, scenario membership, edge evidence, and uncertainty in the correct diagnostic order.
2. Give three competing causes of high downstream WSE and one check that separates at least two of them.
3. Explain why checking the composite before source scenario identity can lead to a false diagnosis.
4. State when diagnosis should stop and policy escalation should begin.
5. Explain why an independently observed complete artifact set can still be scientifically not ready.

## Further reading and source notes

- [Validation Framework](01-validation-framework.md) defines the evidence lanes and readiness terms used here.
- [SDR-004](../reference/bibliography.md#sdr-004-cases-issues-and-experiments) registers the project cases, issue signatures, and experiment plans.
- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) supports intended-use, provenance, uncertainty, verification, and validation distinctions.
- [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) supports decision-specific model evaluation, corroboration, sensitivity, and uncertainty review.
