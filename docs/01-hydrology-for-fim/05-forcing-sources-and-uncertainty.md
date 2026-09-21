# Forcing Sources and Uncertainty

A hydraulic result can be interpreted only as well as the forcing, location, time meaning, and provenance supplied to it.
Numerical precision inside a hydraulic solver cannot recover information that the forcing source never contained or that was attached to the wrong reach.

## Why this topic matters

Two discharge values can have identical units and magnitudes while representing different evidence.
One can be an observed estimate from a streamgage rating, another can be a modeled retrospective output, and a third can be a forecast value.
Using any of them as a hydraulic boundary requires a clear statement of source, time support, spatial support, uncertainty, and intended decision.

## Prerequisites

Read [Discharge, Stage, and Routing](03-discharge-stage-and-routing.md) and [Flood Frequency, AEP, and Discharge Bounds](04-flood-frequency-aep-and-bounds.md).
Retain the distinction between hydrologic forcing and hydraulic response.

## Learning objectives

After this chapter, the reader should be able to:

- distinguish observed, modeled retrospective, forecast, and selected scenario forcing;
- separate observation error, modeled-forcing error, network mismatch, temporal sampling, spatial scale, and nonstationarity;
- explain how each uncertainty can propagate into a flood-inundation result;
- identify why hydraulic numerical precision does not imply forcing accuracy; and
- state the provenance needed for a scientifically interpretable scenario.

## Forcing depends on the system boundary

**Scientific foundation:** Forcing is an input that drives a model across its boundary or through source terms.
The relevant forcing changes with the model boundary.

Meteorological precipitation, temperature, radiation, humidity, wind, and pressure can force a land-surface or hydrologic model.
The hydrologic model can then produce states and flows, including routed streamflow at network reaches.
A hydraulic reach model can consume selected discharge as hydrologic forcing and a downstream water level as a hydraulic boundary control.
The broader set of hydraulic boundary conditions includes hydrologic water inputs and controls such as a specified water level.
A specified water level is not an imposed mass input, although the modeled solution may calculate inflow or outflow across that boundary.

This handbook uses the phrase forcing source for the evidence chain that produced the discharge as well as the immediate boundary value supplied to a hydraulic scenario.
Keeping the full chain visible prevents a modeled or selected value from being mistaken for an observation.

## Four common forcing source classes

### Streamgage-derived discharge

**Scientific foundation:** A streamgage commonly measures stage continuously and estimates discharge through a site-specific stage-discharge rating supported by direct measurements.
The resulting discharge is observational evidence, but it still contains uncertainty from measurements, the rating relation, changing controls, extrapolation, timing, and missing data.

A gage represents one physical location.
Transferring that discharge to another reach requires a stated regionalization, drainage-area adjustment, routing method, or other scientific relationship.

### Modeled retrospective discharge

**Scientific foundation:** A retrospective simulation applies a documented model configuration to historical forcing for a stated period.
Claims about spatial consistency require evidence that the selected outputs share a compatible model version, network, configuration, variables, units, and period.

The official [National Water Model retrospective dataset](../reference/bibliography.md#sci-011-national-water-model-retrospective-dataset) describes available versions, periods, forcing context, and assimilation boundaries.
The [configuration overview](../reference/bibliography.md#sci-012-national-water-model-configurations-and-forcing) and [output description](../reference/bibliography.md#sci-013-national-water-model-output-and-forcing-fields) distinguish forcing fields, analysis configurations, forecasts, and stream-routing outputs.
Within a documented National Water Model retrospective release and compatible output set, the modeled records can support climatology, frequency analysis, comparison, and model evaluation.
The modeled flow is not a continuous observed record, even where a model reach is near a gage.

### Forecast discharge

A forecast discharge represents a future model trajectory under forecast meteorological forcing, initial conditions, assimilation choices, and forecast-system assumptions.
Its uncertainty changes with lead time, meteorological predictability, hydrologic state, observations, and model behavior.

A retrospective frequency estimate and an operational forecast can share a model name without sharing the same time meaning, forcing, or assimilation contract.
Dataset name alone is therefore insufficient provenance.

### Selected scenario discharge

A scenario discharge is chosen to answer a bounded question or populate a response library.
It can be derived from observations, retrospective flow, a frequency estimate, a policy threshold, or another documented method.
Its authority comes from the selection method and evidence rather than from the numerical value alone.

## A practical uncertainty taxonomy

Uncertainty categories should remain separate long enough to identify a useful check or mitigation.
Calling every discrepancy model uncertainty hides where evidence can improve the result.

### 1. Observation error

Observation error concerns the measurements and transformations used to estimate the source quantity.
Examples include stage-sensor error, discharge-measurement error, rating-curve uncertainty, changing channel control, missing observations, and extrapolation beyond measured flows.

This category applies to streamgage-derived discharge and to observations used to evaluate a modeled forcing product.
It should not be assigned directly to model output merely because observations exist nearby.

### 2. Modeled-forcing error

Modeled-forcing error concerns the meteorological and hydrologic model chain that produces a discharge.
It includes meteorological input error, hydrologic model structure, parameters, initial states, reservoir representation, routing, and accumulated bias.

A retrospective discharge inherits uncertainty from both its forcing data and its hydrologic model configuration.
An open-loop simulation is a model realization rather than a record repeatedly corrected toward observed discharge.

### 3. Network mismatch

Network mismatch occurs when the discharge is attached to a different hydrologic object than the hydraulic model represents.
Examples include stale feature identifiers, changed network versions, divergent reach segmentation, confluence offsets, lake or coast breakpoints, and mapping a gage to the wrong model reach.

The numerical discharge can be correct for its source reach and still be wrong for the target reach.
Drainage area, upstream topology, network version, and spatial location are primary checks.

### 4. Temporal sampling

Temporal sampling concerns how often and over what interval the source is recorded or reported.
An hourly instantaneous or averaged value can miss a shorter peak.
A daily value can smooth a rapidly rising event.
An AMS derived from hourly output characterizes the maximum of the sampled series rather than necessarily the continuous-time maximum.

Time zones, water-year boundaries, missing intervals, duplicate timestamps, and aggregation conventions can also change the selected annual maximum or scenario discharge.

### 5. Spatial scale

Spatial-scale uncertainty arises when the source and target summarize different areas or resolutions.
Meteorological forcing on a grid may not resolve a local convective storm or terrain effect.
A reach flow integrates processes across an upstream drainage area, while a two-dimensional hydraulic domain represents a local reach and floodplain.

Transferring information across scales requires a method that preserves the intended quantity and location.
Increasing hydraulic grid resolution cannot restore rainfall variability or upstream runoff processes absent from the discharge source.

### 6. Climate and other nonstationarity

Nonstationarity concerns changes in the process through time.
The USGS [nonstationary flood-frequency study](../reference/bibliography.md#sci-014-nonstationary-flood-frequency-analysis) evaluates trends, change points, climate, land use, and regulation evidence in a regional analysis.
That study supports evaluating those drivers rather than assuming that one driver or trend applies to every reach.

A long retrospective can still span a trend, change point, or altered basin regime.
A stationary frequency fit assumes one distribution remains suitable for the intended period.
If stationarity is doubtful, the analysis should state the evidence, decision horizon, and method rather than silently selecting a trend model.

## Useful evidence by category

| Uncertainty category | Useful evidence or check |
| --- | --- |
| Observation error | Measurement quality, rating shifts, direct high-flow measurements, and uncertainty intervals. |
| Modeled-forcing error | Gage comparison where appropriate, water-balance checks, event and seasonal bias, and model-version evaluation. |
| Network mismatch | Feature crosswalk, geometry overlay, drainage area, topology, and network-version provenance. |
| Temporal sampling | Native time step, aggregation rule, missing-interval audit, and sensitivity to sampling. |
| Spatial scale | Source and target support, drainage-area comparison, forcing-grid resolution, and local-event diagnostics. |
| Nonstationarity | Change-point or trend exploration, physical basin history, period sensitivity, and stated decision horizon. |

No single check closes every category.
Separating categories does not imply statistical independence.

## Uncertainty propagates rather than disappearing

The routing relation in [Discharge, Stage, and Routing](03-discharge-stage-and-routing.md) represents downstream discharge as a function of upstream hydrographs, lateral inflow, initial storage, and routing conditions.
If an input is uncertain, more than one downstream hydrograph can be consistent with the evidence.
The direction and magnitude of the downstream difference require a stated routing method and sensitivity or uncertainty analysis.

A hydraulic scenario maps boundary inputs and model properties to WSE, depth, velocity, and inundation extent.
If more than one inflow discharge is plausible, running only one discharge cannot show how that input range affects those outputs.
The response can be nonlinear and reach-specific, so its shape must be demonstrated rather than assumed.

Downstream conditions, terrain, bathymetry, roughness, domain extent, and numerical settings add other uncertainty sources.
Official [HEC-RAS sensitivity guidance](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity) separates numerical sensitivity from physical-parameter sensitivity and recommends comparisons across realistic ranges.

## Hydraulic precision does not establish forcing accuracy

A solver can report discharge, WSE, or depth with many decimal places.
Those digits describe numerical representation rather than the accuracy of the full evidence chain.

**Design principle:** Compare forcing uncertainty and numerical sensitivity using the same decision-relevant output and stated scenarios.
A small change under one numerical-refinement check does not bound error in the discharge source, reach mapping, terrain, roughness, or downstream condition.
The relative importance of these sources must be demonstrated for the intended use.

## Applied uncertainty trace for R-200

**Applied example:** A synthetic scenario applies \(Q=250\ \text{m3/s}\) to `R-200`.
The value was selected from a modeled retrospective frequency analysis and rounded to the nearest whole m3/s for the scenario record.

The provenance packet records the modeled dataset version, source feature, period, hourly time support, network crosswalk, sample definition, fitting method, uncertainty interval, stationarity assumption, rounding rule, and target reach.
The packet also identifies a plausible discharge interval of 225 to 280 m3/s for sensitivity analysis.

Three hydraulic calculations at 225, 250, and 280 m3/s would provide output evidence at those three tested inflows under fixed model and downstream conditions.
They would not isolate all uncertainty because terrain, roughness, downstream stage, and numerical settings would remain fixed.

**Evidence note:** Agreement among the three synthetic calculations would characterize sensitivity only at the tested inflow values under the shared assumptions.
The three results would not bound behavior between those values unless interpolation, continuity, monotonicity, or denser sampling were independently justified.
It would not prove that 250 m3/s is accurate, that the reach crosswalk is correct, or that the hydraulic model is valid.

## Minimum provenance for a discharge bound or scenario

A useful provenance record should include:

1. Target network version and reach identifier.
2. Source organization, dataset, version, variable, and source feature identifier.
3. Units, native time step, aggregation, and exact record period.
4. Network crosswalk method and drainage-area or topology checks.
5. Missing-data and quality-control rules.
6. AMS or other sample-definition rule when frequency analysis is used.
7. Probability model, fitting implementation, parameters, and uncertainty summary.
8. Stationarity assumption, physical change assessment, and decision horizon.
9. Scenario-selection method and intended use.
10. Bound calculations, factors, and rounding policy.
11. Creation time, software or method version, and upstream dependencies.
12. Consumer inputs and evidence that the realized library spans the intended range.

Without this chain, a reviewer may reproduce the arithmetic but not the scientific result.

## Common misconceptions

### Modeled retrospective streamflow is gage observation

It is model output from a retrospective simulation.
Public documentation must be checked for the forcing and assimilation context of the selected version.

### A long record removes uncertainty

A longer record can reduce sampling uncertainty.
It does not remove bias, structural error, network mismatch, scale mismatch, or nonstationarity.

### Matching units prove compatibility

Two values in m3/s can refer to different locations, intervals, network versions, processes, or evidence classes.
Units are necessary but not sufficient.

### A finer hydraulic grid corrects a coarse or biased forcing source

Grid refinement changes hydraulic representation.
It does not reconstruct omitted upstream meteorology or hydrology.

### A completed calculation validates the forcing

Completion shows that the input was processed under the calculation conditions.
It does not prove that the discharge was correctly derived, mapped, or suitable for the intended decision.

## Competency check

For the synthetic 250 m3/s scenario, write a six-row uncertainty table using the categories in this chapter.
For each row, state one plausible failure, one piece of evidence, and one check that could change the decision.

Then explain which uncertainty categories remain if the hydraulic calculation is numerically converged to a very small tolerance.
State why those categories can still control scientific interpretation.

## Source notes

- **Scientific foundation:** Streamgage and rating context is supported by [SCI-007](../reference/bibliography.md#sci-007-streamgaging-and-stage-discharge-ratings).
- **Scientific foundation:** The National Water Model discussion is supported only by [SCI-011](../reference/bibliography.md#sci-011-national-water-model-retrospective-dataset), [SCI-012](../reference/bibliography.md#sci-012-national-water-model-configurations-and-forcing), and [SCI-013](../reference/bibliography.md#sci-013-national-water-model-output-and-forcing-fields).
- **Scientific foundation:** Stationarity limits are supported by [SCI-009](../reference/bibliography.md#sci-009-bulletin-17c) and [SCI-014](../reference/bibliography.md#sci-014-nonstationary-flood-frequency-analysis).
- **Scientific foundation:** Separation of numerical and physical-parameter sensitivity is supported by [SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity).
- **Evidence note:** The `R-200` source packet, uncertainty interval, and sensitivity calculations are synthetic teaching material.
