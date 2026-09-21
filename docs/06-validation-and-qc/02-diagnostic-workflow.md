# Diagnostic Workflow

Diagnosis should reduce uncertainty about the next decision rather than maximize the number of inspected artifacts.
Begin with competing hypotheses, then choose the next observation most likely to separate them.

## Why this topic matters

Hydraulic symptoms rarely identify one cause.
High water-surface elevation can result from terrain, roughness, a blocked pathway, downstream control, forcing, boundary placement, unresolved transients, or datum mismatch.
Changing a parameter before distinguishing those explanations can hide the original defect and create a compensating error.

## Prerequisites

Complete [Validation Framework](01-validation-framework.md) and review the model-development and scenario-library chapters.
Use [Applied Evidence Catalog](04-case-issue-and-experiment-catalog.md) for bounded synthetic practice records.

## Learning objectives

After this chapter, the reader should be able to:

- follow an ordered diagnostic process without skipping provenance or materialization;
- form at least three materially different hypotheses for an ambiguous symptom;
- choose a next check by discriminatory value, directness, cost, and reversibility;
- distinguish a symptom record from evidence of cause; and
- stop or escalate without turning an unresolved diagnosis into acceptance.

## Use a hypothesis-driven loop

Write a compact hypothesis table before inspecting more evidence.

| Field | Required content |
| --- | --- |
| Symptom | State the observed quantity, location, scenario, time, units, datum, and comparison basis. |
| Competing hypotheses | Include at least three causes that imply different corrective actions. |
| Predictions | State the observation expected if each hypothesis is true and if it is false. |
| Candidate checks | Prefer bounded observations that do not change the model first. |
| Selection rule | Choose the check that best separates the leading hypotheses at acceptable cost and risk. |
| Stop condition | State what evidence supports a bounded diagnosis. |
| Escalation condition | State which missing data, contract, or decision prevents a responsible conclusion. |

**Design principle:** Observe before changing.
Do not tune roughness, expand the domain, alter terrain, or replace a boundary until the change is tied to a testable hypothesis.

## Follow the dependency order

The order below prevents a downstream symptom from hiding an earlier identity, input, or geometry error.
When a stage fails, record the failure before deciding whether later inspection can still add bounded evidence.

### 1. Define the question and decision context

State the blocked decision and the suspicious result.
Identify the quantity, units, datum, location, time or scenario, intended use, referent, and consequence of error.

Ask whether the observed difference matters within the actual decision domain.
A striking anomaly outside the area or quantity of interest may require documentation without controlling the immediate decision.

### 2. Confirm object identity and provenance

Verify model and scenario identity, source versions, transformation history, method version, solver build, and artifact generation.
Distinguish requested values from realized values and a planned object from a materialized object.

Ask whether the symptom remains after comparing the exact realized objects rather than labels, filenames, or rounded addresses.

### 3. Check coordinate reference systems and datums

Verify horizontal reference, axis order, projected units, transform, bounds, resolution, cell registration, vertical datum, vertical units, epoch when relevant, and transformation uncertainty.
Complete this gate before any spatial profile, sampling, resampling, nodata comparison, cross-source geometry comparison, elevation arithmetic, or raster combination.

Ask whether a horizontal shift, vertical offset, unit conversion, or out-of-range index explains the difference.

### 4. Check forcing and source data

Verify discharge, stage, timing, duration, units, spatial allocation, frequency interpretation, and source identity.
After the reference gate passes, check whether target geometry, topology, terrain, roughness, and observations come from compatible source realizations.

Ask whether the apparent anomaly follows a forcing, topology, lineage, or source-version mismatch.

### 5. Check terrain, bathymetry, structures, and roughness

Inspect longitudinal and cross-channel profiles, controlling cells, submerged-channel treatment, surface conditioning, bridges, culverts, resampling, and nodata only after reference compatibility is established.
Verify categorical roughness conversion, lookup coverage, positive values, spatial patterns, and the scope of any calibration.

Ask whether the symptom aligns with a terrain barrier, class boundary, omitted pathway, nodata artifact, or changed source.

### 6. Check geometry and boundaries

Inspect the realized domain, active mask, inflow and outflow geometry, transfer support, model edges, and rasterized cell mappings.
Trace every inflow, slope-based outflow, specified stage, closed edge, and initial condition using mathematical meaning rather than a short label.

Ask whether the selected cells and realized boundary values represent the intended hydraulic connection.

### 7. Check solver state and numerical evidence

Record the executable identity, settings, initial state, saved-output schedule, process status, termination reason, and any ignored input.
Inspect local and global time histories for depth, water-surface elevation, wet extent, storage, inflow, outflow, sources, sinks, and residuals.

Ask whether the suspicious state is stable and conserved enough for the stated use or remains affected by filling, draining, oscillation, cancellation, or initialization.

### 8. Check perimeter evidence

Inspect each connected wet perimeter component with terrain, depth, water-surface elevation, assigned boundary, flux, and intended role.
Separate an intended outlet or transfer edge from artificial clipping or leakage.

Ask whether perimeter water represents a valid connection, an immaterial fringe, a closed-edge pileup, or unintended loss.

### 9. Check scenario membership and dependencies

Identify simulated trials, selected members, source scenarios, interpolation or selection rules, skips, and superseded objects.
Verify that storage presence is not being used as proof of membership.

For connected models, trace upstream and downstream dependencies, confluence assumptions, transfer direction, and source bindings.
Ask whether the suspicious object belongs to the intended family or only to an old trial, proposal, or incompatible dependency.

### 10. Check compositing and derived products

Verify source membership, quantity, units, datum, grid, resampling, nodata, overlap, and combination rule.
Inspect source results before the composite so that a source anomaly is not misdiagnosed as a combination defect.

Ask whether the symptom already exists in a source or is created by selection, registration, overlap, or the combination rule.

### 11. Check publication, materialization, and reuse

Verify the manifest, every required artifact, schema, identity, checksum, generation consistency, and compatibility at the final address.
Distinguish complete publication from independent observation and scientific acceptance.

Ask whether a partial, stale, mixed, or incompatible generation was adopted through an incomplete reuse rule.

### 12. Check uncertainty and method limits

List forcing, terrain, parameter, model-form, numerical, observation, transfer, composite, and operational uncertainties that can affect the decision.
Compare the requested interpretation with the equations, grid scale, source resolution, structure treatment, forcing abstraction, boundary method, and demonstrated validation domain.

Ask whether more evidence can resolve the symptom or whether the method cannot support the requested claim.

## Choose the next check deliberately

Rank each candidate check using four questions:

1. **Discrimination.**
Will opposite outcomes favor different hypotheses?
2. **Directness.**
Does the check observe the suspected mechanism instead of a distant proxy?
3. **Cost and reversibility.**
Can the check preserve the current packet and avoid creating new ambiguity?
4. **Decision value.**
Can the result change the immediate disposition, or would another missing decision still block it?

A cheap check with no discriminatory value is not the best first check.
A costly controlled experiment can be justified when it is the only way to separate high-consequence explanations.
When two checks have similar value, prefer the one that changes nothing and preserves the evidence.

## Map common signatures to competing causes

Every spatial check in this table assumes that horizontal and vertical references, transforms, registration, and units passed the reference gate first.

| Signature | Competing hypotheses | High-value first check |
| --- | --- | --- |
| High upstream water-surface elevation at a road | Missing conveyance, terrain bias, high roughness, downstream backwater, or unresolved filling. | Inspect a co-registered terrain and water-surface profile through the crossing with pathway and time-history evidence. |
| Low downstream water-surface elevation | Excessive drainage, weak downstream control, low terrain, low roughness, forcing error, datum mismatch, or continued draining. | Compare the realized boundary, perimeter flux, terrain, datum, forcing, and recent local water-surface history in one aligned packet. |
| Abrupt inundation at a model edge | Domain clipping, active-mask error, source-coverage gap, boundary error, or output crop. | Overlay connected wet components, domain, mask, source coverage, and output extent on one grid. |
| Confluence discontinuity | Incompatible flows, topology error, transferred-stage mismatch, grid mismatch, or source-selection error. | Trace both tributary and downstream scenario identities, flow accounting, stages, topology, and overlap compatibility. |
| Different terrain from the same source name | Mutable source bytes, retrieval timing, transformation, resampling, nodata, or grid-snap difference. | Compare immutable source identities and complete transformation provenance before interpreting hydraulic residuals. |
| Apparent steady state with continuing local change | Insensitive global metric, cancellation, slow storage exchange, oscillation, or incompatible hot start. | Inspect local histories, full balance terms, perimeter flux, and an appropriate alternate initial state. |
| Missing artifact after a successful response | Partial publication, stale manifest, incorrect address, failed copy, or incomplete observer contract. | Observe the expected generation and verify every required artifact and integrity value independently. |

## Applied diagnostic packet

**Applied example:** The [unexpected upstream ponding signature](04-case-issue-and-experiment-catalog.md#issue-signature-unexpected-upstream-ponding) reports a 0.45 m water-surface step across a synthetic road crossing during one scenario.
The packet gives four competing explanations but no established cause.

First verify horizontal and vertical references, transforms, registration, and units for the terrain and water-surface inputs.
After that gate passes, inspect a co-registered terrain and water-surface profile through the road because it directly tests whether represented conveyance is blocked.
Include recent water-surface history so continuing fill is not mistaken for a structure effect.

**Evidence note:** The issue signature establishes that the symptom was recorded in the synthetic packet.
It does not establish that a missing culvert caused the symptom.

If the profile shows a continuous terrain barrier and a localized head step at the same cells, the missing-pathway hypothesis gains support.
If the terrain pathway is open and the elevated profile extends through the downstream influence zone, the downstream-boundary hypothesis gains support instead.
If the final local water-surface series is still rising, a cause conclusion should wait for numerical evidence.

## Stop and escalate at explicit boundaries

Stop the diagnostic loop when one of these conditions applies:

- evidence supports one bounded explanation and remaining alternatives would not change the immediate action;
- the result meets or fails predeclared criteria for the stated use;
- a missing source, contract, observation, responsibility, or decision prevents meaningful discrimination;
- the requested use exceeds the demonstrated method domain; or
- the next step is a controlled experiment, software change, or methodology decision outside diagnosis.

Escalate with the blocked decision, evidence labels, hypotheses, completed checks, unresolved observations, decision gap, and exact evidence that could change the disposition.
Do not send an unstructured collection of maps and logs without stating which hypothesis each item supports or challenges.

## Separate diagnosis from acceptance

A diagnosis can state that a represented terrain barrier is the leading explanation for ponding.
The result can still fail acceptance because structure treatment, validation, uncertainty, or artifact integrity remains unresolved.

Use **Insufficient evidence to assess** when the packet cannot distinguish the leading explanations.
Use **Does not meet the stated criteria** when a material required condition is contradicted or missing.
Use an accepting conclusion only under the criteria and responsibility described in [Validation Framework](01-validation-framework.md).

## Common misconceptions

### Start with the most visible anomaly

The most visible feature can be downstream of an earlier identity, forcing, datum, or geometry error.
Follow the dependency order.

### Try parameters until the map looks better

Unstructured tuning can create equifinality, where different erroneous parameter combinations match the same observation.
Test the suspected mechanism and protect independent evaluation evidence.

### Every signature has a standard correction

The same symptom can arise from different mechanisms in different settings.
A signature routes investigation but does not prescribe a universal fix.

### More evidence always reduces uncertainty

Incomparable, untraceable, or redundant evidence can increase review burden without separating hypotheses.
Choose each next check for expected decision value.

## Competency check

1. Place datum compatibility, scenario membership, numerical histories, compositing, and materialization in diagnostic order.
2. Give three competing causes of high downstream water-surface elevation and one check that separates at least two.
3. Explain why inspecting a composite before its source identities can produce a false diagnosis.
4. State one diagnostic stop condition and one escalation condition.
5. Explain why a complete artifact set can still fail scientific acceptance.

## Further reading and source notes

- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) supports intended-use, provenance, uncertainty, verification, and validation distinctions.
- [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) supports the separation of numerical accuracy, software correctness, and physical validation.
- [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) supports decision-specific evaluation, corroboration, sensitivity, and uncertainty review.
