# Applied Evidence Catalog

This catalog contains five synthetic records for practicing evidence classification and review.
Every name, value, observation, and artifact state on this page is constructed teaching material.
The records are self-contained, non-authoritative, and make no claim about an external project or operational system.

## Why this topic matters

A record title does not determine what the record proves.
An experiment plan can be detailed without containing a result.
An issue can describe a symptom without identifying its cause.
An operational observation can prove that a storage control worked without proving that a hydraulic result is valid.

## Prerequisites

Read [Source Authority](../reference/source-authority.md), [Validation Framework](01-validation-framework.md), [Diagnostic Workflow](02-diagnostic-workflow.md), and [Sensitivity and Uncertainty](03-sensitivity-and-uncertainty.md).

## Learning objectives

After this chapter, the reader should be able to:

- distinguish completed evidence from a planned method;
- preserve configuration, metrics, observations, and limits together;
- treat a signature as a symptom until cause evidence exists;
- explain why an inconclusive result remains useful; and
- separate operational observations from scientific acceptance.

## Catalog inventory

| Record | Record type | Status | Primary lesson |
| --- | --- | --- | --- |
| Boundary-distance comparison | Completed case | Completed scoped result | A passing comparison supports only its tested configuration and metric. |
| Roughness and grid interaction | Experiment plan | Plan only | A method record contains no result until observations and provenance are supplied. |
| Unexpected upstream ponding | Issue signature | Unresolved | A symptom supports competing hypotheses rather than a cause claim. |
| Transferred-stage comparison | Inconclusive result | Inconclusive | A comparison cannot isolate method effects when datum compatibility is unknown. |
| Partial generation withheld | Operational observation | Observed control response | Publication evidence does not establish scientific adequacy. |

**Applied example:** All five records use the synthetic reach names `R-100`, `R-200`, and `R-300` where a network context is needed.
`R-100` and `R-300` are immediately upstream of terminal reach `R-200`.
All elevations use metres and synthetic vertical datum `VD-1` unless a record explicitly states that the reference is unresolved.

## Synthetic water-balance residual

The catalog uses one defined balance metric wherever a water-balance percentage appears.
For cumulative volumes over the interval from model time \(t_0=0\) to the final recorded state at \(t_1=6\) h, define:

\[
R_V=100\frac{\Delta S-\left(V_{in}-V_{out}+V_{source}-V_{sink}\right)}{V_{in}}
\]

Here, \(\Delta S=S(t_1)-S(t_0)\), all volume terms are in cubic metres, and \(V_{in}>0\).
Inflow and source volumes are positive additions to the domain, while outflow and sink volumes are positive removals.
A positive \(R_V\) means storage increased more than the recorded net input accounts for, and a negative value means it increased less.
Acceptance comparisons use the absolute percentage \(\lvert R_V\rvert\).

## Completed case: Boundary-distance comparison

### Question and intended use

Does extending the downstream domain reduce boundary influence enough to support screening water-surface elevations within the synthetic `R-200` area of interest at 175 cubic metres per second?
The intended use is only a boundary-placement sensitivity check for one steady synthetic scenario.

### Configuration

Both alternatives use the same synthetic network, terrain, roughness, structures, 10 m grid, initial state, solver build, discharge, downstream slope, wet-dry settings, output times, and post-processing.
The near alternative places the downstream boundary 0.8 km below the area of interest.
The extended alternative places the same boundary treatment 1.8 km below the area of interest.

The predeclared criteria are:

- the 95th percentile of absolute water-surface elevation difference within the area of interest must not exceed 0.05 m;
- the absolute final water-balance residual must not exceed 1 percent in either alternative; and
- no connected wet component may touch an unintended closed edge.

### Observations

The 95th percentile absolute water-surface elevation difference is 0.03 m.
The maximum local absolute difference is 0.07 m at the downstream edge of the area of interest.
The balance inputs and calculated residuals are:

| Alternative | \(\Delta S\) | \(V_{in}\) | \(V_{out}\) | \(V_{source}\) | \(V_{sink}\) | Signed \(R_V\) | Absolute residual |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Near boundary | 705,120 m3 | 3,780,000 m3 | 3,090,000 m3 | 0 m3 | 0 m3 | 0.40% | 0.40% |
| Extended boundary | 818,900 m3 | 3,780,000 m3 | 2,980,000 m3 | 0 m3 | 0 m3 | 0.50% | 0.50% |

No connected wet component touches an unintended closed edge.

### Conclusion and limits

**Evidence note:** The synthetic packet meets its three predeclared criteria for this reach, discharge, grid, boundary treatment, and area of interest.
The packet supports the bounded conclusion that the additional kilometre of downstream domain does not materially change the stated screening metric in this configuration.

The packet contains no independent physical observations.
It does not validate the model, define a universal boundary distance, establish other discharges or boundary regimes, or authorize a production rule.

## Experiment plan: Roughness and grid interaction

### Question and intended use

How do a defensible roughness range and grid resolution interact in their effect on maximum water-surface elevation and inundated area for a synthetic low-gradient `R-200` scenario?
The intended use is to decide whether later validation should treat roughness and grid resolution jointly.

### Planned design

The plan uses four combinations:

| Combination | Roughness multiplier | Grid size |
| --- | ---: | ---: |
| Lower roughness, finer grid | 0.90 | 10 m |
| Higher roughness, finer grid | 1.10 | 10 m |
| Lower roughness, coarser grid | 0.90 | 20 m |
| Higher roughness, coarser grid | 1.10 | 20 m |

All combinations hold the synthetic network, terrain source, domain, forcing, downstream condition, solver build, initial state, run duration, and output processing fixed.
Each output will be compared on the common 20 m support after conservative aggregation appropriate to the reported quantity.

The primary metrics are maximum water-surface elevation at three fixed cross sections, total inundated area above 0.05 m depth, and the final water-balance residual.
The interaction measure is the difference between the roughness effect on the 10 m grid and the roughness effect on the 20 m grid for each primary metric.

The plan requires complete identity and provenance for every realization, retained failed runs, identical acceptance masks, and no calibration against the evaluation cross sections.
The plan will stop without interpretation if grid registration, vertical reference, source identity, or balance evidence is incomplete.

### Status and limits

**Evidence note:** This record is a plan and contains no executed run, observation, response estimate, preferred alternative, or acceptance result.
The plan can be reviewed for design quality but cannot support a sensitivity conclusion.

## Issue signature: Unexpected upstream ponding

### Recorded symptom

A synthetic `R-100` scenario uses 100 cubic metres per second and a 102.5 m downstream stage in `VD-1`.
The final output shows a 0.45 m water-surface elevation step across a road crossing 600 m upstream of the transfer boundary.
The wet area remains connected on both sides of the road.
The supplied summary does not include a terrain profile, structure record, local flux, balance history, or observation referent.

### Competing hypotheses

- The terrain realization blocks a culvert or low opening.
- Roughness is too high in the crossing cells.
- The downstream stage creates backwater through the crossing.
- The scenario is still filling at the recorded final time.

### Next check and limits

The first gate is verification of horizontal and vertical references, transforms, registration, and units for the terrain and water-surface inputs.
If that gate passes, the highest-value next check is a co-registered terrain and water-surface profile through the crossing with represented pathway and recent time history.
That packet can separate a local terrain obstruction from broad downstream control and continuing fill more directly than a roughness change.

**Evidence note:** The signature establishes only that the synthetic symptom was recorded under the stated scenario.
It does not establish which hypothesis caused the ponding.

## Inconclusive result: Transferred-stage comparison

### Question and configuration

Does a spatially varying transferred-stage boundary change the `R-100` result relative to a uniform 102.5 m boundary under the same 100 cubic metres per second discharge?
Both alternatives use the same network, terrain file, roughness, grid, domain, solver build, initial state, duration, and output processing.

The uniform alternative declares `VD-1`.
The transferred field is recorded in metres, but its source record does not identify a vertical datum or transformation.

### Observations

The median absolute water-surface elevation difference within the area of interest is 0.08 m.
The maximum local difference is 0.22 m near the downstream boundary.
The inundated area differs by 3.1 percent on the common grid.
The supplied balance values are:

| Alternative | \(\Delta S\) | \(V_{in}\) | \(V_{out}\) | \(V_{source}\) | \(V_{sink}\) | Signed \(R_V\) | Absolute residual |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Uniform stage | 516,480 m3 | 2,160,000 m3 | 1,650,000 m3 | 0 m3 | 0 m3 | 0.30% | 0.30% |
| Transferred stage | 500,800 m3 | 2,160,000 m3 | 1,670,000 m3 | 0 m3 | 0 m3 | 0.50% | 0.50% |

### Conclusion and limits

**Evidence note:** The outputs disagree under the supplied processing, and the largest difference is near the changed boundary.
The result cannot isolate the hydraulic effect of spatial transfer because vertical-reference compatibility is unknown.

The proper conclusion is inconclusive rather than passed or failed.
Resolving the datum and rerunning or transforming the boundary under a documented contract is required before method attribution.

## Operational observation: Partial generation withheld

### Intended publication

A synthetic scenario generation requires a scenario record, depth raster, inundation raster, transfer-support raster, diagnostic history, and integrity values for every asset.
The producer stages the generation outside the consumer-visible location and requests promotion only after verification.

### Observed event

The staging observer finds the scenario record, depth raster, transfer-support raster, and diagnostic history.
The inundation raster is absent.
The observer records the generation as incomplete, rejects promotion, and leaves the pointer to the previous complete generation unchanged.
No consumer-visible address is updated.

### Conclusion and limits

**Evidence note:** The observation supports the claim that this publication attempt was detected as incomplete and withheld under the stated contract.
It does not establish that every failure mode is detected, that the previous generation is scientifically accepted, or that the staged hydraulic values are adequate.

## How to use a catalog record

A defensible evidence statement should name:

1. the record and its type;
2. the exact configuration and provenance supplied;
3. the observed quantities, units, support, metric, and referent;
4. the conclusion status;
5. the decision the record can inform;
6. missing evidence and competing explanations; and
7. prohibited generalizations.

**Design principle:** Cite the record that contains the observation rather than a related plan or issue title.

## Common misconceptions

### A detailed plan contains evidence of effect

A plan can define factors, controls, and metrics without containing any observation.
Execution evidence and provenance are required before interpreting an effect.

### A completed case establishes a universal rule

A completed result remains bounded by its configuration, metric, evidence, and intended use.
Transfer to other settings requires a justified sampling and inference argument.

### An issue name identifies the cause

An issue signature records a symptom.
Use competing hypotheses and discriminatory checks before assigning cause.

### An operational control proves scientific quality

A publication control can preserve artifact consistency while the underlying calculation remains invalid or unvalidated.
Operational and scientific conclusions must remain separate.

## Competency check

1. Which record contains completed observations, and what is its strongest supported conclusion?
2. Why can the roughness and grid record not support a sensitivity ranking?
3. Which check best separates a local obstruction from broad downstream control in the ponding signature?
4. Why is the transferred-stage result inconclusive even though both calculations completed?
5. What does the withheld generation observation prove, and what does it leave unknown?

## Further reading and source notes

- [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations) supports bounded verification, validation, uncertainty, provenance, and acceptance reasoning.
- [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability) supports distinctions among software correctness, numerical accuracy, validation, and regions of validity.
- [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance) supports decision-specific evaluation, calibration separation, sensitivity, uncertainty, and evidence limits.
