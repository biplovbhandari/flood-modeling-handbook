# Adaptive Discharge Selection

Adaptive selection can refine a discharge library where adjacent measured scenarios show a large hydraulic response.
The synthetic method in this chapter repeatedly divides the widest unresolved interval on a fixed candidate grid.

## Why this topic matters

Equal discharge increments do not create equal hydraulic changes.
A small increase can cross a levee or fill a side channel, while a larger increase elsewhere can produce little map change.

Interval refinement directs simulations toward measured changes without requiring a fitted response curve.
It also makes stopping behaviour and unresolved intervals explicit.

## Prerequisites

Read [Discharge-Only Scenario Libraries](01-normal-depth-libraries.md), [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md), and [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md).

## Learning objectives

After this chapter, the reader should be able to:

- define a fixed candidate grid and endpoint scenarios;
- calculate a normalized response distance between adjacent measured scenarios;
- identify the widest interval that still requires refinement;
- choose an untried candidate deterministically when the exact midpoint is unavailable;
- choose a compatible warm start from a lower scientifically accepted scenario;
- state the stopping rule and any unresolved residual; and
- specify a durable interval-refinement record.

## Synthetic candidate grid

**Applied example:** The `R-200` candidate grid contains 100, 125, 150, 175, 200, 225, and 250 cubic metres per second.
The lower and upper endpoints at 100 and 250 cubic metres per second are measured first.

Every measured scenario reports maximum depth and flooded area from the same grid, wet threshold, nodata rule, and final-state definition.
Median depth can remain a diagnostic, but it does not control this synthetic selector.

For wet cells \(W\), cell size \(s\), and wet-cell count \(N_w\):

\[
h_{max}=\max_{i\in W}(h_i)
\]

\[
A_f=\frac{N_ws^2}{10^6}
\]

Maximum depth is in metres when depth is in metres.
Flooded area is in square kilometres only when \(s\) is in metres.

## Response distance

For adjacent measured scenarios at discharges \(Q_a<Q_b\), define:

\[
D(a,b)=\max\left(\frac{|h_{max,b}-h_{max,a}|}{0.50\ \text{m}},\frac{|A_{f,b}-A_{f,a}|}{0.10\ \text{km}^2}\right)
\]

An interval meets the response criterion when \(D(a,b)\leq1\).
An interval requires refinement when \(D(a,b)>1\) and at least one untried candidate lies strictly inside it.

The 0.50 m and 0.10 square kilometre scales are synthetic method values.
They do not define a universal resolution requirement.

## Deterministic refinement rule

Apply the following steps after each completed, scientifically accepted scenario.

1. Sort measured scenarios by discharge.
2. Calculate \(D(a,b)\) for every adjacent pair.
3. Keep intervals with \(D(a,b)>1\) and at least one untried interior candidate.
4. Select the widest retained interval.
5. Break equal-width interval ties by choosing the interval with the lower discharge endpoint.
6. Select the untried interior candidate nearest the arithmetic midpoint of the chosen interval.
7. If two candidates are equally near, choose the lower discharge.
8. Warm-start the new scenario from the nearest lower scientifically accepted measured member with compatible model, grid, terrain, datum, boundary family, and state type.

The nearest-untried rule also defines behaviour when the exact midpoint was already tried.
The selector never repeats a candidate and never invents an off-grid discharge.

**Design principle:** Candidate choice, tie breaking, and warm-start choice must be part of the method record rather than implementation accident.

## Stopping rule

Stop when every adjacent measured interval either meets \(D(a,b)\leq1\) or contains no untried interior candidate.
If an interval still has \(D(a,b)>1\) but no untried interior candidate, record it as an unresolved grid residual.

The stopping rule means the selected set can include every scientifically accepted scenario measured by the refinement process.
It does not claim that the fixed grid resolves an unsampled transition narrower than its spacing.

## Worked refinement trace

The synthetic response packet is:

| Discharge | Maximum depth | Flooded area | Measurement order | Warm-start source |
| ---: | ---: | ---: | ---: | --- |
| 100 m3/s | 1.00 m | 0.500 km2 | 1 | Dry start |
| 250 m3/s | 2.30 m | 0.750 km2 | 2 | 100 m3/s |
| 175 m3/s | 1.60 m | 0.595 km2 | 3 | 100 m3/s |
| 125 m3/s | 1.20 m | 0.530 km2 | 4 | 100 m3/s |
| 200 m3/s | 1.78 m | 0.635 km2 | 5 | 175 m3/s |
| 225 m3/s | 2.01 m | 0.680 km2 | 6 | 200 m3/s |

The initial endpoint interval from 100 to 250 m3/s has:

\[
D(100,250)=\max\left(\frac{1.30}{0.50},\frac{0.250}{0.10}\right)=2.60
\]

Its midpoint is 175 m3/s, which is an untried candidate and becomes the third measurement.

After 175 m3/s is measured, both intervals are 75 m3/s wide and exceed the criterion.
The lower-endpoint tie rule selects the 100 to 175 m3/s interval.
Its midpoint is 137.5 m3/s, so 125 and 150 m3/s are equally near and the lower-candidate tie rule selects 125 m3/s.

The new 100 to 125 and 125 to 175 m3/s intervals both meet the response criterion.
The unresolved 175 to 250 m3/s interval selects 200 m3/s because 200 and 225 m3/s are equally near its midpoint of 212.5 m3/s.

After 200 m3/s is measured, only the 200 to 250 m3/s interval exceeds the criterion.
Its exact midpoint is 225 m3/s, so 225 m3/s is measured.

The final adjacent distances are:

| Interval | Response distance | Result |
| --- | ---: | --- |
| 100 to 125 m3/s | 0.40 | Meets criterion |
| 125 to 175 m3/s | 0.80 | Meets criterion |
| 175 to 200 m3/s | 0.40 | Meets criterion |
| 200 to 225 m3/s | 0.46 | Meets criterion |
| 225 to 250 m3/s | 0.70 | Meets criterion |

The selected discharge set is therefore 100, 125, 175, 200, 225, and 250 cubic metres per second.
The 150 m3/s candidate remains untried because both intervals beside it meet the criterion after 125 and 175 m3/s are measured.

**Evidence note:** The trace follows the stated interval, midpoint, tie, and stopping rules exactly.
It proves only the selection arithmetic for the supplied synthetic metrics.
It does not prove convergence, domain adequacy, warm-start independence, or suitability of the response scales for another reach.

## Warm-start compatibility

The method selects the nearest lower scientifically accepted measured member, not merely the nearest lower file in storage.
Compatibility requires the same model generation, grid, terrain, vertical reference, boundary family, wetting convention, and solver-state type.

Depth-only initialization does not preserve velocity, momentum, face flux, or a complete checkpoint.
Dry-start and alternate-start sensitivity remain necessary where initial conditions can affect the interpreted result.

## Incomplete and failed trials

A failed or scientifically rejected scenario does not divide an interval and does not become a warm-start source.
Its artifacts can remain in an execution inventory, but the refinement record must distinguish them from scientifically accepted scenarios.

If the next candidate fails, record the failure and stop or apply a separately authorized recovery policy.
Do not silently skip to another candidate because that would change the synthetic method.

## Durable refinement record

The record should include:

- candidate grid and required endpoints;
- metric definitions, wet threshold, units, and nodata rule;
- response-distance equation and scale values;
- measured scenarios and complete identities;
- interval distances after each measurement;
- width and midpoint used for each choice;
- tie-breaking decisions;
- warm-start source and compatibility evidence;
- scientifically rejected or failed trials; and
- final selected set and unresolved grid residuals.

## Limitations

### Fixed-grid resolution

The method cannot resolve a transition narrower than the candidate spacing without changing the authored grid.

### Sequential execution

Each new interval and warm start depends on prior measured results.

### Metric selection

Maximum depth and total flooded area can miss a localized change that matters for a particular asset or pathway.

### Scale dependence

The 0.50 m and 0.10 square kilometre scales do not represent equal importance for every reach size or application.

### Scientifically rejected scenarios

A numerically complete scenario can fail scientific acceptance and must not guide refinement until the failure is resolved.

## Common misconceptions

### The widest discharge interval is always refined

Only intervals that exceed the response criterion and contain an untried interior candidate are eligible.

### The exact midpoint must exist on the grid

The method selects the nearest untried interior candidate and uses a lower-discharge tie break.

### Every candidate is simulated

The 150 m3/s candidate remains untried because surrounding measured intervals already meet the criterion.

### A completed run is automatically a refinement member

The method requires scientific acceptance and complete observation before a scenario can divide an interval.

## Competency check

1. Which endpoints initialize the synthetic selector?
2. What two quantities define the response distance?
3. Why is 125 m3/s selected before 150 m3/s?
4. What happens when the exact midpoint was already tried?
5. Which scenario warm-starts the 225 m3/s run?
6. Why does 150 m3/s remain untried?
7. What condition creates an unresolved grid residual?

## Further reading and source notes

- **Scientific foundation:** Hydraulic sensitivity concepts are supported by [SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity).
- **Scientific foundation:** Grid and wetting considerations are supported by [SCI-028](../reference/bibliography.md#sci-028-hec-ras-2d-computational-mesh) and [SCI-029](../reference/bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance).
