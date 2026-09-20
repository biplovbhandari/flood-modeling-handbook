# Lab 3 Solution: AEP and Discharge Bounds

This solution demonstrates one evidence-disciplined answer to the synthetic exercise.
It does not validate the numerical values, data sources, probability fit, or production suitability of any candidate.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, and stopping criteria are defined in [Lab 3](../lab-03-aep-and-discharge-bounds.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and keeps synthetic calculations outside project authority.
Stop when the answer satisfies the prompt's competency criteria or records the exact evidence gap that blocks production use.

## 1. AEP and recurrence calculations

The conversion uses \(T=1/p\) with AEP expressed as a fraction.

| AEP, percent | AEP, fraction | Recurrence interval, years |
| ---: | ---: | ---: |
| 20 | 0.20 | 5 |
| 4 | 0.04 | 25 |
| 1 | 0.01 | 100 |
| 0.2 | 0.002 | 500 |

For example:

\[
T_{4\%}=\frac{1}{0.04}=25\ \text{years}
\]

A 100-year recurrence interval means a 1 percent chance of an equal or greater flood in each year under the assumed model.
It does not prevent consecutive exceedances and does not predict a 100-year gap between them.

## 2. Multi-year probability calculation

Under stationary and independent annual periods:

\[
P(N\geq1)=1-(1-p)^n
\]

Substituting \(p=0.01\) and \(n=30\):

\[
P(N\geq1)=1-(1-0.01)^{30}=1-0.99^{30}\approx0.2603
\]

The communication value is **about 26 percent**.
Reporting many more digits would overstate the meaning of a calculation that assumes constant annual probability and independence and is applied to an estimated AEP magnitude.

The expected number of exceedances is:

\[
E[N]=np=30(0.01)=0.30
\]

The expected count of 0.30 is not a 30 percent probability.
It is the long-run average number of exceedances across many comparable 30-year periods, while 26 percent is the probability of one or more exceedances in a period.

## 3. DR-029 bound calculation

The synthetic values \(Q_{HFT}=220\ \text{m3/s}\) and \(Q_{100}=760\ \text{m3/s}\) are instructional givens outside the project authority hierarchy.

The lower bound is:

\[
Q_{min}=0.9Q_{HFT}=0.9(220\ \text{m3/s})=198\ \text{m3/s}
\]

The upper bound is:

\[
Q_{max}=1.5Q_{100}=1.5(760\ \text{m3/s})=1140\ \text{m3/s}
\]

Both results are already whole m3/s, so no rounding is required for the current ND input contract in this exercise.
That interface compatibility does not make the bounds scientifically exact.
The source values, fitted \(Q_{100}\), and selected factors retain their stated limitations.
**Open question:** DR-029 does not define the event, statistic, dataset variable, time support, or derivation represented by \(Q_{HFT}\), and the exercise's supplied number does not resolve that gap.

## 4. Candidate comparison and conditional selection

| Candidate | Authority treatment and method status | Contract check | Material concern |
| --- | --- | --- | --- |
| A: 198 to 1140 m3/s | The endpoint values are instructional results. **Selected methodology:** The DR-029 ALT-A rule has Alternate Selected status. | Both positive whole-m3/s endpoints fit the field types. | The \(Q_{HFT}\) definition, source period, feature crosswalk, frequency implementation, uncertainty, and provenance are missing. |
| B: 220 to 880 m3/s | The values and short-record attribution are instructional givens. **Open question:** No inspected project record establishes this candidate as selected methodology or verified evidence. | Both endpoints fit the field types. | Fourteen years and unreviewed rating changes provide weak support for a 1 percent AEP estimate, and the candidate omits the DR-029 multipliers. |
| C: 310 to 690 m3/s | The range is an instructional given. **Evidence or experiment:** EXP-015 is a real proposed experiment, but it does not supply or validate this hypothetical result. | Both endpoints fit the field types. | EXP-015 is proposed, the result answers a conditional downstream question, and the candidate does not implement the selected per-reach bound rule. |
| D: 0 to 1600 m3/s | The range is an instructional candidate. **Open question:** No scientific or selected-methodology source supports using it. | The lower endpoint violates `min_upstream_inflow > 0`. | A wide arbitrary range can waste computation, include unsupported conditions, and still provide no proof that important hydraulic transitions are sampled adequately. |

**Conditional selection:** Candidate A is the best-supported choice if the immediate goal is to follow the currently selected DR-029 methodology.
The selection is conditional because the exercise supplies claims about the two source values but omits the evidence needed to verify them.
Candidate A is not production-ready.

Candidate B could be relevant independent evidence after a proper observed-flow frequency analysis and site-to-reach mapping.
It does not become the selected project bound merely because it uses observations.

Candidate C could inform future adjacent-reach KWSE planning if EXP-015 is completed, reviewed, and adopted through an authorized decision.
It is not a present replacement for the DR-029 range.

## 5. Project source-boundary trace

### Decision

**Selected methodology:** DR-029 ALT-A, `Fixed Recurrence Interval Bounds from NWM Retrospective`, has status Alternate Selected.
It specifies \(0.9Q_{HFT}\) as the lower bound and \(1.5Q_{100}\) as the upper bound.
**Open question:** The reviewed decision record names \(Q_{HFT}\) but does not define its event, statistic, dataset variable, time support, or derivation.
The symbol alone cannot supply that scientific definition.

### Experiment

**Evidence or experiment:** EXP-015 proposes characterizing joint flow frequency between adjacent NWM reaches.
It proposes AMS extraction, LP3 fitting, recurrence normalization, empirical joint distributions, and possible bivariate or copula fitting.
Its conditional downstream percentiles address KWSE scenario planning and do not supersede DR-029.

### Current consumer

**Current implementation:** [`RunNDScenariosInputs`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_nd_scenarios.py) accepts `min_upstream_inflow` and `max_upstream_inflow` as whole-m3/s values, with the minimum constrained to be greater than zero.
The ND workflow uses the interval to bound its adaptive steady-scenario sampling.

The current ND job does not:

- fetch NWM retrospective data;
- crosswalk network reaches;
- extract annual maxima;
- fit LP3 or another frequency distribution;
- calculate \(Q_{HFT}\) or \(Q_{100}\);
- apply the 0.9 or 1.5 factors; or
- decide how uncertain non-integer bounds should be rounded.

### Target ownership

**Target design:** The [system-design guide](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/guide.md) treats `q_*_bound` values as authored intent.
It treats the adaptive ND `q_set` as emergent when the job selects the interior points.
The target materialization check should confirm that the realized library spans the authored range and satisfies density intent.

The target statement does not prove that a deployed planner currently performs the DR-029 derivation.

### Required artifact

**Open question:** A durable scenario-plan or desired-state provenance record should connect the source dataset and reach, exact period, AMS extraction, fit, uncertainty, stationarity assessment, DR-029 arithmetic, rounding policy, authored bounds, and current job request.
Scenario manifests can prove which discharges were realized, but they cannot alone prove why the range was scientifically selected.

## 6. Six-category uncertainty register

| Category | Plausible concern in the synthetic evidence | Evidence or check needed |
| --- | --- | --- |
| Forcing observation error | The 14-year gage estimate may reflect rating shifts, sparse high-flow measurements, or extrapolation. | Review measurement history, rating changes, high-flow measurements, flags, and discharge uncertainty. |
| Modeled-forcing error | The retrospective values can inherit AORC precipitation error and NWM structural, parameter, routing, or reservoir error. | Compare appropriate retrospective outputs with quality-controlled gage records and inspect event, seasonal, and magnitude-dependent bias. |
| Network mismatch | `R-1042` may not correspond to the NWM feature used for the threshold and \(Q_{100}\). | Preserve network versions and a feature crosswalk, then compare geometry, topology, and drainage area. |
| Temporal sampling | Hourly retrospective output can miss a shorter peak or use an aggregation convention that changes the AMS. | Record the native time support, audit missing intervals, and test peak sensitivity to available finer data where justified. |
| Spatial scale | Gridded meteorological forcing and reach-integrated flow may not represent a local storm or the precise hydraulic inflow location. | Compare source and target support, upstream drainage area, forcing resolution, and localized event evidence. |
| Nonstationarity | The actual retrospective period used for the synthetic threshold and fitted discharge is unknown. If a selected long period spans changing climate, land-cover, regulation, or channel regimes, one stationary distribution may not represent them all. | Identify the exact analysis period, then perform exploratory change and period-sensitivity analysis with basin history and state the decision horizon. |

These concerns can interact.
Keeping them separate makes it possible to select evidence that discriminates among them.

## 7. Missing evidence and readiness statement

At least the following evidence is missing for Candidate A:

1. The exact NWM retrospective version, release, and record period used for both source values.
2. The source feature identifier and verified crosswalk to `R-1042`.
3. Network-version, geometry, topology, and drainage-area checks.
4. The definition and derivation of the high-flow threshold.
5. The water-year definition, AMS extraction code, missing-data policy, and time-support convention.
6. The probability distribution and exact fitting implementation.
7. LP3 skew treatment, threshold handling, outlier treatment, and any historical-information treatment.
8. Confidence intervals or another uncertainty summary for \(Q_{100}\) and the derived bounds.
9. Exploratory stationarity assessment, basin-change evidence, and intended decision horizon.
10. Bias or performance evidence for the retrospective at relevant flows and locations.
11. The policy for converting uncertain non-integer results to whole-m3/s job inputs.
12. A durable planning or provenance artifact connecting the calculations to the submitted job inputs.
13. Evidence that the completed library spans the authored endpoints and satisfies scenario-density intent.
14. Hydraulic validation showing that the resulting range covers decision-relevant transitions without unacceptable edge or boundary behavior.

Under the fixed DR-029 formula, items 1 through 7 can directly change the source feature, high-flow threshold, \(Q_{100}\), or their point-estimation method and can therefore change the computed point bounds.
Item 11 can change the submitted whole-m3/s endpoints when a computed point bound is not an integer.

Item 8 characterizes uncertainty around the point estimate and derived bounds.
It can change confidence or a decision without changing the fixed point estimate or the arithmetic result.

Items 9 and 10 can change interpretation and can show that the stationary retrospective estimate is unsuitable for the intended decision.
They can motivate an authorized change in record, model, bias treatment, or frequency method, which would produce a new point estimate, but they do not alter the current fixed-input calculation by themselves.

Item 12 controls reproducibility, traceability, and authority.
Item 13 shows whether the selected endpoints and density were realized.
Item 14 addresses whether the realized range is hydraulically useful for the intended decision.

**Readiness:** Candidate A is the only candidate aligned with current selected methodology, but no candidate is ready for production use from the supplied evidence.
The missing source, method, uncertainty, crosswalk, provenance, realization, and validation evidence must be resolved or explicitly accepted by project authority.
