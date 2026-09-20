# Flood Frequency, AEP, and Discharge Bounds

Flood-frequency analysis relates flood magnitude to the probability that an equal or larger annual peak will occur.
The 2D FIM workflow uses that statistical context to define a discharge range for each reach, but a probability-based bound is an estimate rather than a forecast schedule or a proof of hydraulic adequacy.

## Why this topic matters

A scenario library can only represent the hydraulic responses inside its discharge range.
A range that is too narrow can omit important floods, while a range that is unnecessarily wide can spend computation on poorly supported extremes.
The choice depends on the flow source, record period, probability model, uncertainty, and assumption about whether flood behavior is stable through time.

## Prerequisites

Read [Water Balance, Runoff, and Hydrographs](02-water-balance-runoff-and-hydrographs.md) and [Discharge, Stage, and Routing](03-discharge-stage-and-routing.md).
Use [Equations and Units](../reference/equations-and-units.md) for the probability and project-bound equations.

## Learning objectives

After this chapter, the reader should be able to:

- define annual exceedance probability, recurrence interval, annual maximum series, record length, sampling uncertainty, and stationarity;
- convert between annual exceedance probability and recurrence interval without treating recurrence as a schedule;
- explain why the selected probability model and record period affect an estimated design discharge;
- derive the bounds selected by DR-029 from stated inputs;
- distinguish DR-029 from the proposed joint-frequency work in EXP-015; and
- trace the selected bounds to current job inputs and target planning responsibility.

## Annual exceedance probability describes one annual chance

**Scientific foundation:** Annual exceedance probability, or AEP, is the probability that a specified magnitude will be equaled or exceeded during one year.
An AEP of 0.01 is a 1 percent annual chance.
It does not mean that exactly 1 percent of every year's flow record exceeds the magnitude.

The recurrence interval, \(T\), is the reciprocal expression of AEP, \(p\):

\[
T = \frac{1}{p}, \qquad p = \frac{1}{T}
\]

A 1 percent AEP corresponds to a recurrence interval of 100 years.
The recurrence interval is a long-run average under the assumed probability model, not a countdown or appointment.
Two 1 percent AEP floods can occur in consecutive years, and one century can pass without one.

The [USGS explanation of the 100-year flood](https://www.usgs.gov/water-science-school/science/100-year-flood) emphasizes the annual chance and warns against interpreting recurrence terminology as a schedule.

## Multi-year risk is not the recurrence interval divided by time

If annual exceedances are independent and the annual probability remains \(p\), the probability of at least one exceedance in \(n\) years is:

\[
P(N \geq 1) = 1 - (1-p)^n
\]

For a 1 percent AEP flood over 30 years:

\[
P(N \geq 1) = 1 - (1-0.01)^{30} \approx 0.260
\]

The result is about a 26 percent chance of at least one exceedance, not a 30 percent chance and not a guarantee.
This calculation inherits the independence and stationarity assumptions.

## An annual maximum series selects one peak per year

**Scientific foundation:** An annual maximum series, or AMS, contains the largest qualifying discharge in each defined year, commonly a water year.
Forty complete years normally contribute 40 annual maxima, not every hourly value above a threshold.
The year definition, missing periods, regulation, measurement changes, and duplicate-event handling must be recorded.

The AMS supports an annual-probability interpretation because each comparable year contributes one maximum.
A partial-duration or peaks-over-threshold series uses a different sampling rule and can include more than one event per year.
The two series require different probability treatment and must not be mixed without an explicit method.

## Record length limits what the sample can show

Record length is the number of comparable annual periods represented by the analysis after accounting for missing, censored, historical, or otherwise qualified information.
A 40-year record can support estimation beyond the largest observed annual peak only through a fitted probability model and its assumptions.
It does not directly observe a 100-year recurrence-interval discharge.

Sampling uncertainty is the variation in an estimate that arises because the available years are only one finite sample from the underlying flood process.
Another 40-year period could contain a different combination of wet years, dry years, and extremes and therefore produce a different fitted 1 percent AEP discharge.
Longer relevant records generally reduce sampling uncertainty, but length alone cannot correct biased forcing, a changed basin, a poor distribution, or incompatible data.

Confidence intervals or another explicit uncertainty summary should accompany a flood-frequency estimate when the decision depends on how precise the estimate is.
A single fitted discharge conceals that uncertainty.

## The probability model is a scientific choice

**Scientific foundation:** A flood-frequency workflow typically inspects the data, defines the sample, fits or estimates a probability distribution, and reports discharges associated with selected AEPs.
USGS [Bulletin 17C](https://pubs.usgs.gov/publication/tm4B5) provides federal guidelines for flood-frequency analysis based on annual peak-flow information and a log-Pearson Type III framework with methods for interval data, influential low floods, skew, and confidence intervals.

Log-Pearson Type III, abbreviated LP3, fits a Pearson Type III distribution to logarithms of peak discharge.
Naming LP3 alone does not specify the complete analysis.
The fitting method, skew treatment, thresholds, historical information, low-flow treatment, record qualification, and confidence-interval method can all affect the estimate.

The fitted curve also depends on the source series.
An analysis of observed streamgage peaks characterizes the available observations and rating methods at that site.
An analysis of National Water Model retrospective streamflow characterizes a model realization driven by its forcing data and model structure at a model reach.
Those sources are useful for different reasons and do not become interchangeable because the same statistical distribution is fitted to them.

## Stationarity is an assumption about the generating process

Stationarity means that the probability distribution used for the analysis is treated as invariant through the period to which the estimate is applied.
For flood frequency, that commonly means that the distribution of annual peak flows is assumed not to change systematically with time.

Bulletin 17C describes annual peak-flow estimates as a sample from an assumed stationary process, subject to exploratory analysis and physical knowledge of the system.
Observed trends can arise from sampling variation, while real nonstationarity can result from climate, land cover, regulation, channel change, urbanization, or other basin changes.

A stationarity assumption should therefore be stated, not hidden.
If the record combines materially different regimes, one stationary fitted curve may not represent either regime well.
If a nonstationary model is considered, its covariates, extrapolation behavior, validation, and intended time horizon require separate evidence.

## From a retrospective series to per-reach bounds

A defensible bound derivation needs a reproducible chain:

1. Identify the reach network, reach identifier, and flow source version.
2. Record the exact period, time step, units, missing values, and quality controls.
3. Derive one annual maximum for each complete and comparable year under a stated year definition.
4. Inspect the series for data problems, regulation, changes, outliers, and plausible nonstationarity.
5. Select and document the frequency method, parameters, skew treatment, and uncertainty method.
6. Estimate the required AEP discharges and retain confidence limits or another uncertainty summary.
7. Apply the project's bound rule with an explicit rounding policy for the consuming interface.
8. Store the source, method, version, assumptions, and calculations in a planning or provenance artifact.
9. Check that the resulting scenario library spans the authored range.

Retrospective flow can support national, per-reach processing because it provides a spatially consistent modeled series across the model network.
That consistency does not eliminate forcing error, model structural error, reach mismatch, record-length limits, or nonstationarity.

## Project-selected bound rule

**Selected methodology:** DR-029 ALT-A, `Fixed Recurrence Interval Bounds from NWM Retrospective`, has status Alternate Selected in the current Decision Register.
It defines the lower and upper bounds as:

\[
Q_{min} = 0.9 Q_{HFT}, \qquad Q_{max} = 1.5 Q_{100}
\]

- \(Q_{min}\) is the selected lower discharge bound in m3/s.
- \(Q_{max}\) is the selected upper discharge bound in m3/s.
- \(Q_{HFT}\) is the input that DR-029 calls the reach's high-flow-threshold discharge, in m3/s.
- \(Q_{100}\) is the fitted 1 percent AEP discharge in m3/s under the stated retrospective analysis.

DR-029 describes fitting a flood-frequency distribution, for example LP3, to NWM retrospective flow for each reach.
The phrase "for example" does not establish one fully specified fitting contract.
**Open question:** The reviewed project record does not define the event, statistic, dataset variable, time support, or derivation that makes a discharge \(Q_{HFT}\).
Its scientific meaning cannot be inferred from the symbol or the words "high-flow threshold."
Until an authorized source supplies that definition, \(Q_{HFT}\) is a required but scientifically unresolved input to the selected arithmetic.
The record also does not by itself resolve the exact retrospective version and period, AMS extraction rules, LP3 implementation, confidence treatment, stationarity check, or rounding policy.

### Worked bound calculation

As an instructional given outside the project authority hierarchy, assume a synthetic reach has \(Q_{HFT}=210\ \text{m3/s}\) and an estimated \(Q_{100}=740\ \text{m3/s}\).
Applying DR-029 gives:

\[
Q_{min}=0.9(210)=189\ \text{m3/s}
\]

\[
Q_{max}=1.5(740)=1110\ \text{m3/s}
\]

The arithmetic is exact for the stated synthetic inputs.
The scientific bounds are not exact because the source values and fitted \(Q_{100}\) retain uncertainty.
The factors 0.9 and 1.5 are selected project rules, not universal flood-frequency constants.

## EXP-015 addresses a different question

**Evidence or experiment:** EXP-015 proposes an analysis of joint flow frequency for adjacent NWM reaches.
Its methodology proposes 40 years of NWM version 3.0 retrospective flow, annual maximum series, LP3 fits, recurrence-interval normalization, a two-dimensional empirical distribution, and possible bivariate or copula models.
Its intended use is to characterize conditional downstream flow ranges for KWSE scenario planning.

The official NWM version 3.0 dataset description identifies a 44-year simulation from February 1979 through January 2023.
EXP-015 does not identify which 40-year subset it intends to use, so the exact experimental record period remains unresolved.

EXP-015 is not a completed validation of DR-029's per-reach lower and upper discharge bounds.
It is not current job behavior.
Its proposed 5th and 95th conditional percentiles must not replace DR-029 bounds unless an authorized decision adopts that method and implementation evidence shows a consumer.

The experiment also notes a clipping simplification and the importance of climate-region stratification.
Those notes are evidence of open methodological work, not proof that the proposed distribution is ready for CONUS use.

## Current job and target-design boundaries

**Current implementation:** [`RunNDScenariosInputs`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_nd_scenarios.py) requires positive whole-m3/s `min_upstream_inflow` and a whole-m3/s `max_upstream_inflow`.
The ND job samples steady scenarios inside the caller-provided interval and ensures the endpoints participate in the library workflow.
It does not read NWM retrospective data, extract annual maxima, fit LP3, compute \(Q_{HFT}\), estimate \(Q_{100}\), or apply the DR-029 multipliers.

**Current implementation:** [`RunKWSEScenariosInputs`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_kwse_scenarios.py) consumes a caller-provided list of scenario pairs.
It does not derive network-wide discharge bounds or implement the EXP-015 joint-frequency proposal.

**Target design:** The [system-design guide](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/guide.md) treats discharge bounds such as `q_*_bound` as authored intent and the adaptive ND discharge set as emergent output.
The target materialization check is intended to confirm that the emergent library spans the authored range and satisfies density rules.
This is a target ownership and validation contract, not evidence that a deployed component currently computes DR-029 bounds.

## Common misconceptions

### A 100-year flood occurs once every 100 years

The term describes a 1 percent annual exceedance probability under the assumed model.
It does not impose spacing between exceedances.

### A 40-year retrospective directly contains a 100-year flood

The 1 percent AEP discharge is estimated by fitting or otherwise extending a probability model beyond the finite sample.
The result depends on the sample and method.

### Modeled retrospective flow is an observation

NWM retrospective streamflow is model output driven by retrospective meteorological forcing.
The official dataset notes that the retrospective simulations do not assimilate streamflow observations.

### A safety multiplier removes statistical uncertainty

Multiplying \(Q_{100}\) by 1.5 expands the selected upper bound.
It does not quantify or eliminate uncertainty in the source, model, record, or fitted estimate.

### EXP-015 is the implemented scenario planner

EXP-015 is a proposed experiment.
Current jobs do not implement its joint distribution or conditional percentiles.

## Competency check

A reach has a 4 percent AEP discharge of 360 m3/s.
State the corresponding recurrence interval and explain why the result does not predict the next occurrence year.

Then treat \(Q_{HFT}=230\ \text{m3/s}\) and \(Q_{100}=810\ \text{m3/s}\) as instructional givens, not as project evidence.
Calculate the DR-029 bounds, state their units, name the evidence label for the multipliers, and list four missing items required before using the bounds in a production scenario plan.

## Source notes

- **Scientific foundation:** Flood-frequency procedure, data assumptions, stationarity, and confidence treatment are supported by [SCI-009](../reference/bibliography.md#sci-009-bulletin-17c).
- **Scientific foundation:** AEP and recurrence-interval interpretation are supported by [SCI-010](../reference/bibliography.md#sci-010-aep-and-the-100-year-flood).
- **Scientific foundation:** The official NWM version 3.0 retrospective period, forcing, and no-assimilation boundary are supported by [SCI-011](../reference/bibliography.md#sci-011-national-water-model-retrospective-dataset).
- **Selected methodology:** DR-029 is mapped under [SDR-006](../reference/bibliography.md#sdr-006-discharge-bounds-adaptive-selection-and-joint-frequency).
- **Evidence or experiment:** EXP-015 is mapped under [SDR-006](../reference/bibliography.md#sdr-006-discharge-bounds-adaptive-selection-and-joint-frequency) and remains experimental.
- **Current implementation:** Caller-supplied discharge bounds are mapped under [JOB-005](../reference/bibliography.md#job-005-discharge-bound-consumer-contracts).
- **Target design:** Authored bounds and emergent discharge sets are mapped under [SYS-003](../reference/bibliography.md#sys-003-authored-discharge-bounds-and-emergent-scenario-sets).
