# Flood Frequency, AEP, and Discharge Bounds

Flood-frequency analysis relates flood magnitude to the probability that an equal or larger annual peak will occur.
That statistical context can help define a discharge range for a response library, but a probability-based bound is an estimate rather than a forecast schedule or proof of hydraulic adequacy.

## Why this topic matters

A response library can represent only the hydraulic conditions inside its discharge range and other boundary dimensions.
A range that is too narrow can omit important floods, while an unnecessarily wide range can spend computation on poorly supported extremes.
The choice depends on the flow source, record period, probability model, uncertainty, intended use, and assumptions about change through time.

## Prerequisites

Read [Water Balance, Runoff, and Hydrographs](02-water-balance-runoff-and-hydrographs.md) and [Discharge, Stage, and Routing](03-discharge-stage-and-routing.md).
Use [Equations and Units](../reference/equations-and-units.md) for the probability equations.

## Learning objectives

After this chapter, the reader should be able to:

- define annual exceedance probability, recurrence interval, annual maximum series, record length, sampling uncertainty, and stationarity;
- convert between annual exceedance probability and recurrence interval without treating recurrence as a schedule;
- explain why the probability model and record period affect an estimated discharge;
- describe a general method for choosing discharge bounds;
- perform a synthetic bound calculation with explicit assumptions; and
- identify the evidence needed before a bound is used in a response-library plan.

## Annual exceedance probability describes one annual chance

**Scientific foundation:** Annual exceedance probability, or AEP, is the probability that a specified magnitude will be equaled or exceeded during one year.
An AEP of 0.01 is a 1 percent annual chance.
It does not mean that exactly 1 percent of every year's flow record exceeds the magnitude.

The recurrence interval, \(T\), is the reciprocal expression of AEP, \(p\):

\[
T = \frac{1}{p}, \qquad p = \frac{1}{T}
\]

A 1 percent AEP corresponds to a recurrence interval of 100 years.
The recurrence interval is a long-run average under the assumed probability model rather than a countdown or appointment.
Two 1 percent AEP floods can occur in consecutive years, and a century can pass without one.

The [USGS explanation of the 100-year flood](https://www.usgs.gov/water-science-school/science/100-year-flood) emphasizes the annual chance and warns against interpreting recurrence terminology as a schedule.

## Multi-year risk

If annual exceedances are independent and the annual probability remains \(p\), the probability of at least one exceedance in \(n\) years is

\[
P(N \geq 1) = 1 - (1-p)^n
\]

For a 1 percent AEP flood over 30 years,

\[
P(N \geq 1) = 1 - (1-0.01)^{30} \approx 0.260
\]

The result is about a 26 percent chance of at least one exceedance.
This calculation inherits the independence and stationarity assumptions.

## An annual maximum series selects one peak per year

**Scientific foundation:** An annual maximum series, or AMS, contains the largest qualifying discharge in each defined year, commonly a water year.
Forty complete years normally contribute 40 annual maxima rather than every value above a threshold.
The year definition, missing periods, regulation, measurement changes, and duplicate-event handling must be recorded.

The AMS supports an annual-probability interpretation because each comparable year contributes one maximum.
A partial-duration or peaks-over-threshold series uses a different sampling rule and can include more than one event per year.
The two series require different probability treatment and must not be mixed without an explicit method.

## Record length limits what the sample can show

Record length is the number of comparable annual periods represented by the analysis after missing, censored, historical, or otherwise qualified information is handled.
A 40-year record can support estimation beyond the largest observed annual peak only through a fitted probability model and its assumptions.
It does not directly observe a 100-year recurrence-interval discharge.

Sampling uncertainty arises because the available years are only one finite sample from the underlying flood process.
Another 40-year period could produce a different fitted 1 percent AEP discharge.
Longer relevant records generally reduce sampling uncertainty, but length alone cannot correct biased forcing, basin change, an unsuitable distribution, or incompatible data.

Confidence intervals or another explicit uncertainty summary should accompany an estimate when the decision depends on its precision.
A single fitted discharge conceals that uncertainty.

## The probability model is a scientific choice

**Scientific foundation:** A flood-frequency workflow typically inspects the data, defines the sample, fits or estimates a probability distribution, and reports discharges associated with selected AEPs.
USGS [Bulletin 17C](https://pubs.usgs.gov/publication/tm4B5) provides federal guidelines based on annual peak-flow information and a log-Pearson Type III framework with methods for interval data, influential low floods, skew, historical information, and confidence intervals.

Log-Pearson Type III, abbreviated LP3, fits a Pearson Type III distribution to logarithms of peak discharge.
Naming LP3 alone does not specify the complete analysis.
The fitting method, skew treatment, thresholds, historical information, record qualification, and confidence-interval method can all affect the estimate.

The fitted curve also depends on the source series.
An analysis of observed streamgage peaks characterizes the available observations and rating methods at that site.
An analysis of modeled retrospective streamflow characterizes a model realization driven by its forcing data and model structure at a model reach.
Those sources do not become interchangeable because the same statistical distribution is fitted to them.

## Stationarity is an assumption

Stationarity means that the probability distribution used for the analysis is treated as invariant through the period to which the estimate is applied.
For flood frequency, that commonly means the distribution of annual peak flows is assumed not to change systematically with time.

Observed trends can arise from sampling variation, while real change can result from climate, land cover, regulation, channel change, urbanization, or other basin changes.
A stationarity assumption should therefore be stated rather than hidden.

If the record combines materially different regimes, one stationary fitted curve may not represent either regime well.
If a nonstationary model is considered, its covariates, extrapolation behavior, validation, and intended time horizon require separate evidence.

## Public retrospective data as one possible source

The National Water Model retrospective dataset is one public example of modeled streamflow that can support exploratory frequency analysis across a model network.
The official description identifies model versions, simulation periods, forcing context, output cadence, and the absence of streamflow assimilation in the retrospective simulations.
The [dataset record](../reference/bibliography.md#sci-011-national-water-model-retrospective-dataset), [configuration overview](../reference/bibliography.md#sci-012-national-water-model-configurations-and-forcing), and [output description](../reference/bibliography.md#sci-013-national-water-model-output-and-forcing-fields) should be read together before selecting a variable or period.

Spatial consistency is useful for per-reach processing, but it does not eliminate meteorological forcing error, hydrologic model error, reach mismatch, record-length limits, or nonstationarity.
Modeled retrospective flow must not be described as observed streamgage flow.

## A general method for choosing discharge bounds

**Design principle:** Choose bounds from the intended use and defensible hydrologic evidence rather than from a universal multiplier.

A reproducible process should:

1. Identify the target network version, reach, and intended hydraulic use.
2. Record the flow source, version, variable, period, time step, units, missing values, and quality controls.
3. Define the sample, such as an AMS from complete and comparable years.
4. Inspect data quality, regulation, basin change, outliers, and plausible nonstationarity.
5. Select the frequency method, parameters, skew treatment, and uncertainty method.
6. Estimate discharges for probability levels relevant to the intended use.
7. Select lower and upper bounds that cover the intended conditions and account for uncertainty without implying unsupported precision.
8. Apply a declared rounding policy that matches the scenario interface.
9. Preserve the source, calculations, assumptions, and review record.
10. Verify that the realized response library spans the authored bounds and does not silently omit critical intervals.

Bounds may come directly from estimated AEP discharges, from confidence limits, from observed or modeled operating thresholds, or from combinations of these.
The method should explain why the lower bound is low enough, why the upper bound is high enough, and what remains outside the library.

## Synthetic bound calculation for R-200

**Applied example:** Assume a teaching analysis for `R-200` estimates \(Q_{10}=312.5\ \text{m3/s}\) for the 10 percent AEP and \(Q_{100}=800\ \text{m3/s}\) for the 1 percent AEP.
Assume the instructional policy chooses a lower bound at 80 percent of \(Q_{10}\) and an upper bound at 125 percent of \(Q_{100}\).

\[
Q_{min}=0.80Q_{10}=0.80(312.5)=250\ \text{m3/s}
\]

\[
Q_{max}=1.25Q_{100}=1.25(800)=1000\ \text{m3/s}
\]

The arithmetic is exact for the stated synthetic values.
The scientific bounds are not exact because the source series, frequency estimates, stationarity assumption, and choice of factors remain uncertain.
The factors 0.80 and 1.25 are assumptions created for this example rather than universal flood-frequency constants.

**Evidence note:** This calculation demonstrates traceable arithmetic only.
It does not establish that the factors, probability levels, or range are suitable for a real reach or intended use.

**Open question:** A real analysis must decide whether the intended use requires other probability levels, confidence limits, observed thresholds, nonstationary treatment, or a broader downstream-stage dimension.

## Common misconceptions

### A 100-year flood occurs once every 100 years

The term describes a 1 percent annual exceedance probability under the assumed model.
It does not impose spacing between exceedances.

### A 40-year retrospective directly contains a 100-year flood

The 1 percent AEP discharge is estimated by fitting or otherwise extending a probability model beyond the finite sample.
The result depends on the sample and method.

### Modeled retrospective flow is an observation

Retrospective streamflow is model output driven by retrospective forcing.
The public dataset documentation states whether and how observations were assimilated.

### A multiplier removes statistical uncertainty

Multiplying an estimated discharge expands or contracts a selected range.
It does not quantify or eliminate uncertainty in the source, model, record, or fitted estimate.

### Wider bounds always make a better library

Very wide bounds can include poorly supported conditions and still miss important variation in downstream controls.
Coverage must be judged against intended use and all material scenario dimensions.

## Competency check

A reach has a 4 percent AEP discharge of 360 m3/s.
State the corresponding recurrence interval and explain why the result does not predict the next occurrence year.

Then use the synthetic `R-200` assumptions in this chapter to calculate the lower and upper bounds.
State their units, identify which values are statistical estimates and which are instructional assumptions, and list four missing items required before using the bounds in a real scenario plan.

## Source notes

- **Scientific foundation:** Flood-frequency procedure, data assumptions, stationarity, and confidence treatment are supported by [SCI-009](../reference/bibliography.md#sci-009-bulletin-17c).
- **Scientific foundation:** AEP and recurrence-interval interpretation are supported by [SCI-010](../reference/bibliography.md#sci-010-aep-and-the-100-year-flood).
- **Scientific foundation:** The public retrospective-data example is supported only by [SCI-011](../reference/bibliography.md#sci-011-national-water-model-retrospective-dataset), [SCI-012](../reference/bibliography.md#sci-012-national-water-model-configurations-and-forcing), and [SCI-013](../reference/bibliography.md#sci-013-national-water-model-output-and-forcing-fields).
- **Evidence note:** The `R-200` frequency estimates, factors, and bounds are synthetic teaching values and provide no evidence about an external methodology.
