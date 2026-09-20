# Forcing Sources and Uncertainty

A hydraulic result can only be as interpretable as the forcing, location, time meaning, and provenance supplied to it.
Numerical precision inside a hydraulic solver cannot recover information that the forcing source never contained or that was attached to the wrong reach.

## Why this topic matters

Two discharge values can have identical units and magnitudes while representing different evidence.
One can be an observed estimate from a streamgage rating, another can be an hourly NWM retrospective output, and a third can be a forecast value.
Using any of them as a steady hydraulic boundary requires a clear statement of source, time support, spatial support, uncertainty, and intended decision.

## Prerequisites

Read [Discharge, Stage, and Routing](03-discharge-stage-and-routing.md) and [Flood Frequency, AEP, and Discharge Bounds](04-flood-frequency-aep-and-bounds.md).
Retain the boundary that the current modeling jobs consume steady hydraulic forcing rather than simulate rainfall-runoff.

## Learning objectives

After this chapter, the reader should be able to:

- distinguish observed, modeled retrospective, forecast, and selected design forcing;
- separate observation error, modeled-forcing error, network mismatch, temporal sampling, spatial scale, and nonstationarity;
- explain how each uncertainty can propagate into a 2D FIM result;
- identify why hydraulic numerical precision does not imply forcing accuracy;
- trace NWM retrospective flow through DR-029 without treating it as observed flow; and
- state the provenance needed for a scientifically interpretable scenario.

## Forcing depends on the system boundary

**Scientific foundation:** Forcing is an input that drives a model across its boundary or source terms.
The relevant forcing changes with the model boundary.

Meteorological precipitation, temperature, radiation, humidity, wind, and pressure can force a land-surface or hydrologic model.
The hydrologic model then produces states and flows, including routed streamflow at network reaches.
A hydraulic reach model can consume a selected discharge and downstream condition as boundary forcing.

The current 2D FIM jobs begin at the hydraulic boundary.
They do not convert precipitation into runoff and do not route a time-varying rainfall-driven hydrograph.
For this handbook, the phrase forcing source therefore includes the evidence chain that produced the discharge as well as the immediate steady boundary supplied to the hydraulic job.

## Four common forcing source classes

### Streamgage-derived discharge

**Scientific foundation:** A streamgage commonly measures stage continuously and estimates discharge through a site-specific stage-discharge rating supported by direct measurements.
The resulting discharge is observational evidence, but it still contains uncertainty from measurements, the rating relation, changing controls, extrapolation, timing, and missing data.

A gage represents one physical location.
Transferring that discharge to another reach requires a stated regionalization, drainage-area adjustment, routing method, or other scientific relationship.

### Modeled retrospective discharge

**Scientific foundation:** A retrospective simulation applies one model configuration to historical forcing over a long period.
It provides a spatially consistent modeled record that can support climatology, frequency analysis, comparison, and model evaluation.

The official [NOAA National Water Model retrospective dataset](https://registry.opendata.aws/nwm-archive/) describes NWM version 3.0 as a 44-year simulation from February 1979 through January 2023.
It states that version 3.0 used AORC version 1.1 meteorological forcing and that the retrospective simulations did not assimilate streamflow observations.
The modeled flow is therefore not a continuous observed record, even where a model reach is near a gage.

### Forecast discharge

A forecast discharge represents a future model trajectory under forecast meteorological forcing, initial conditions, and forecast-system assumptions.
Its uncertainty changes with lead time, meteorological predictability, data assimilation, hydrologic state, and model behavior.

The [NOAA National Water Model overview](https://water.noaa.gov/about/nwm) distinguishes operational analysis, open-loop analysis, and forecast configurations and identifies different forcing and assimilation behavior among them.
A retrospective frequency estimate and an operational forecast can share a model name without sharing the same time meaning, forcing, or assimilation contract.

### Selected design or scenario discharge

A design or scenario discharge is chosen to answer a bounded question or populate a response library.
It can be derived from observations, retrospective flow, a frequency estimate, a policy threshold, or another authorized method.
Its authority comes from the documented selection method and evidence, not from the numerical value alone.

## A practical uncertainty taxonomy

Uncertainty categories should remain separate long enough to identify a useful check or mitigation.
Calling every discrepancy "model uncertainty" hides where evidence can improve the result.

### 1. Forcing observation error

Observation error concerns the measurements and transformations used to estimate the source quantity.
Examples include stage sensor error, discharge-measurement error, rating-curve uncertainty, changing channel control, missing observations, and extrapolation beyond measured flows.

This category applies to streamgage-derived discharge and to observations used to calibrate or evaluate a forcing product.
It should not be assigned to NWM output merely because observations exist nearby.

### 2. Modeled-forcing error

Modeled-forcing error concerns the meteorological and hydrologic model chain that produces a discharge.
It includes precipitation and other meteorological input error, hydrologic model structure, parameters, initial states, reservoir representation, routing, and accumulated bias.

NWM retrospective discharge inherits uncertainty from AORC forcing and from the NWM configuration.
The absence of streamflow assimilation in the retrospective means that the resulting series is an open-loop model realization rather than a record repeatedly corrected toward gage discharge.

### 3. Network mismatch

Network mismatch occurs when the discharge is attached to a different hydrologic object than the hydraulic model represents.
Examples include stale feature identifiers, changed network versions, divergent reach segmentation, confluence offsets, lake or coast breakpoints, and mapping a gage to the wrong model reach.

The numerical discharge can be correct for its source reach and still be wrong for the target reach.
Drainage area, upstream topology, network version, and spatial location are primary checks.

### 4. Temporal sampling

Temporal sampling concerns how often and over what interval the source is recorded or reported.
An hourly instantaneous or averaged value can miss a shorter peak.
A daily value can smooth a rapidly rising event.
An AMS derived from hourly output characterizes the maximum of the sampled series, not necessarily the continuous-time maximum.

Time zones, water-year boundaries, missing intervals, duplicate timestamps, and aggregation conventions can also change the selected annual maximum or scenario discharge.

### 5. Spatial scale

Spatial-scale uncertainty arises when the source and target summarize different areas or resolutions.
Meteorological forcing on a grid may not resolve a local convective storm or terrain effect.
A reach flow integrates processes across an upstream drainage area, while a 2D hydraulic domain represents a local reach and floodplain.

Transferring information across these scales requires a method that preserves the intended quantity and location.
Increasing hydraulic grid resolution cannot restore rainfall variability or upstream runoff processes absent from the discharge source.

### 6. Climate and other nonstationarity

Nonstationarity concerns changes in the process through time.
The USGS [nonstationary flood-frequency study](../reference/bibliography.md#sci-014-nonstationary-flood-frequency-analysis) identifies trends and change points in annual peak flow and evaluates time, climate, land-use, and regulation evidence when selecting a nonstationary analysis.
That study is regional and method-specific, so it supports evaluating those drivers rather than assuming that one driver or trend applies to every reach.

A long retrospective provides more annual values but can still span a trend, change point, or altered basin regime.
A stationary frequency fit assumes that one distribution remains suitable for the intended period.
Bulletin 17C provides the stationary framework used here, while the USGS nonstationary study notes that Bulletin 17C does not prescribe a nonstationary replacement method.
If stationarity is doubtful, the analysis must state the evidence, decision horizon, and authorized method rather than silently selecting a trend model.

## These categories interact but are not interchangeable

One flood estimate can contain several categories at once.
For example, an NWM retrospective annual maximum can inherit AORC precipitation error, NWM routing or parameter error, hourly temporal-sampling limits, model-network mismatch with the hydraulic reach, and a stationary fit applied across a changing basin.

Separating the categories does not imply statistical independence.
It supports a clearer evidence plan:

| Uncertainty category | Useful evidence or check |
| --- | --- |
| Observation error | Measurement quality, rating shifts, direct high-flow measurements, and uncertainty intervals. |
| Modeled-forcing error | Gage comparison where appropriate, water-balance checks, event and seasonal bias, and model-version evaluation. |
| Network mismatch | Feature-ID crosswalk, geometry overlay, drainage area, topology, and network-version provenance. |
| Temporal sampling | Native time step, aggregation rule, missing-interval audit, and sensitivity to coarser or finer sampling. |
| Spatial scale | Source and target support, drainage-area comparison, forcing-grid resolution, and local-event diagnostics. |
| Nonstationarity | Change-point or trend exploration, physical basin history, period sensitivity, and stated decision horizon. |

No single check closes every category.

## Downstream propagation carries uncertainty rather than removing it

**Inference from the routing relation:** [Discharge, Stage, and Routing](03-discharge-stage-and-routing.md) represents downstream discharge as a function of upstream hydrographs, lateral inflow, initial storage, and routing conditions.
If any input is uncertain, more than one downstream hydrograph can be consistent with the available evidence.
The direction and magnitude of the downstream difference require a stated routing method and sensitivity or uncertainty analysis and cannot be inferred from the uncertainty label alone.
The current 2D FIM jobs do not perform that time-varying routing.

**Target design:** The [target propagation design](../reference/bibliography.md#sys-002-upstream-scenario-propagation) propagates hydraulic scenario dependencies through the reach network.
A downstream scenario can provide stage-transfer information to an upstream KWSE scenario.
**Inference:** Because the transferred condition is derived from that downstream scenario, an uncertainty review must inspect both the downstream source scenario and the transferred condition.
The target design does not quantify the resulting uncertainty and does not establish deployed behavior.
Target dependency propagation is distinct from downstream hydrologic routing and must not be used as evidence that hydrographs were routed.

In both cases, successful consumption proves neither source accuracy nor correct reach mapping.

## Uncertainty propagates into hydraulic results

A hydraulic scenario maps boundary inputs and model properties to outputs such as WSE, depth, velocity, and inundation extent.
**Inference:** If more than one inflow discharge is plausible, running only one discharge cannot show how that input range affects WSE, depth, velocity, or inundation extent.
The direction, size, and shape of the response are model- and reach-specific questions.
The handbook does not assume proportional, nonlinear, or threshold behavior without a sensitivity result for the case under review.

Downstream conditions, terrain, bathymetry, roughness, domain extent, and numerical settings are additional inputs or assumptions whose contributions require separate evidence.
Official [HEC-RAS sensitivity guidance](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity) separates numerical sensitivity from physical-parameter sensitivity and recommends comparison across realistic parameter or statistical-event ranges.
For this project, an analogous comparison must state which inflow, boundary, model, and numerical values changed and which decision-relevant outputs were compared.

## Hydraulic precision does not establish forcing accuracy

A solver can report discharge, WSE, or depth with many decimal places.
Those digits describe numerical representation, not the accuracy of the full evidence chain.

**Inference:** A small change under one numerical-refinement check does not bound error in the discharge source, reach mapping, terrain, roughness, or downstream condition.
Forcing uncertainty is larger than numerical sensitivity for a stated decision only if runs across defensible forcing values change the decision-relevant output more than the documented numerical-sensitivity range.
The opposite result is also possible.
The comparison must be demonstrated for the reach, scenarios, outputs, and decision rather than asserted from decimal precision.

## Project forcing and provenance boundary

**Selected methodology:** DR-029 ALT-A selects NWM retrospective flow as the basis for per-reach frequency bounds and applies project multipliers to the high-flow threshold and 1 percent AEP discharge.
The selection does not convert NWM output into observed flow or eliminate the uncertainty categories above.

**Evidence or experiment:** EXP-015 proposes using NWM version 3.0 retrospective flow to study joint behavior of adjacent reaches.
Its annual-maxima, LP3, recurrence normalization, empirical-distribution, and possible copula steps remain an experimental plan.
They are not current forcing behavior or an adopted replacement for DR-029.

**Current implementation:** The ND job consumes `min_upstream_inflow` and `max_upstream_inflow` in whole m3/s and produces steady `QFIX` scenarios within that range.
The KWSE job consumes caller-authored scenario discharges and downstream scenario manifests.
Neither job records how an external hydrologic workflow derived the bounds unless that provenance is carried in an upstream planning or desired-state artifact.

**Target design:** The system-design guide treats the discharge bounds as authored intent and the adaptive ND discharge set as emergent.
It also treats scenario discharge as part of a run's realization.
The target design requires materialization checks against intent, but it does not by itself establish the scientific derivation or deployed provenance of a bound.

**Open question:** The inspected current job contracts do not define a complete, durable record connecting NWM source version, source period, feature crosswalk, AMS extraction, frequency fit, uncertainty, DR-029 calculation, rounding, and the supplied bound values.
The producing component and authoritative artifact for that chain require project confirmation.

## Minimum provenance for a discharge bound or scenario

A useful provenance record should include:

1. Target hydrofabric or model-network version and reach identifier.
2. Source organization, dataset, version, variable, and source feature identifier.
3. Units, datum when relevant, native time step, aggregation, and exact record period.
4. Network crosswalk method and drainage-area or topology checks.
5. Missing-data and quality-control rules.
6. AMS or other sample-definition rule.
7. Probability model, fitting implementation, parameters, and uncertainty summary.
8. Stationarity assumption, physical change assessment, and decision horizon.
9. Selected methodology record and status.
10. Bound calculations, safety factors, and rounding policy.
11. Planning artifact, creation time, software version, and upstream dependencies.
12. Consumer inputs and evidence that the realized library spans the intended range.

Without this chain, a later reviewer may be able to reproduce the arithmetic but not the scientific result.

## Common misconceptions

### NWM retrospective streamflow is gage observation

It is model output from a retrospective simulation.
The official dataset states that the retrospective simulations do not assimilate streamflow observations.

### A long record removes uncertainty

A longer record can reduce sampling uncertainty.
It does not remove bias, structural error, network mismatch, scale mismatch, or nonstationarity.

### Matching units proves compatibility

Two values in m3/s can refer to different locations, intervals, network versions, processes, or evidence classes.
Units are necessary but not sufficient.

### A finer hydraulic grid corrects a coarse or biased forcing source

Grid refinement changes hydraulic representation.
It does not reconstruct omitted upstream meteorology or hydrology.

### A completed solver run validates the forcing

Completion shows that the solver accepted and processed the input under its run conditions.
It does not prove that the discharge was correctly derived, mapped, or suitable for the intended decision.

## Competency check

For one proposed 1 percent AEP scenario, write a six-row uncertainty table using the categories in this chapter.
For each row, state one plausible failure, one piece of evidence, and one check that could change the decision.

Then explain which uncertainty categories remain if the hydraulic solver is numerically converged to a very small tolerance.
State why those categories can still control the scientific interpretation.

## Source notes

- **Scientific foundation:** Streamgage and rating uncertainty context is supported by [SCI-007](../reference/bibliography.md#sci-007-streamgaging-and-stage-discharge-ratings).
- **Scientific foundation:** NWM retrospective period, forcing, output, and no-assimilation boundaries are supported by [SCI-011](../reference/bibliography.md#sci-011-national-water-model-retrospective-dataset).
- **Scientific foundation:** Operational NWM forcing and assimilation differences are supported by [SCI-012](../reference/bibliography.md#sci-012-national-water-model-configurations-and-forcing).
- **Scientific foundation:** NWM forcing variables and units are supported by [SCI-013](../reference/bibliography.md#sci-013-national-water-model-output-and-forcing-fields).
- **Scientific foundation:** Stationarity limits and evidence for time-, climate-, land-use-, and regulation-related nonstationarity are supported by [SCI-009](../reference/bibliography.md#sci-009-bulletin-17c) and [SCI-014](../reference/bibliography.md#sci-014-nonstationary-flood-frequency-analysis).
- **Scientific foundation:** The need to separate numerical and physical-parameter sensitivity is supported by [SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity).
- **Selected methodology and Evidence or experiment:** DR-029 and EXP-015 are mapped under [SDR-006](../reference/bibliography.md#sdr-006-discharge-bounds-adaptive-selection-and-joint-frequency).
- **Current implementation:** Current bound and scenario inputs are mapped under [JOB-005](../reference/bibliography.md#job-005-discharge-bound-consumer-contracts).
- **Target design:** Authored bounds and emergent discharge sets are mapped under [SYS-003](../reference/bibliography.md#sys-003-authored-discharge-bounds-and-emergent-scenario-sets).
