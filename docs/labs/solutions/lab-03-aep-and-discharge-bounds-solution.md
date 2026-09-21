# Lab 3 Solution: AEP and Discharge Bounds

This solution gives one supported answer to the packet in [Lab 3](../lab-03-aep-and-discharge-bounds.md).
Use it only after attempting the prompt.

## 1. AEP and recurrence calculations

The conversion uses \(T=1/p\) with AEP expressed as a fraction.

| AEP | AEP fraction | Recurrence interval |
| ---: | ---: | ---: |
| 20 percent | 0.20 | 5 years |
| 4 percent | 0.04 | 25 years |
| 1 percent | 0.01 | 100 years |
| 0.2 percent | 0.002 | 500 years |

For example:

\[
T_{4\%}=\frac{1}{0.04}=25 \text{years}
\]

A 100-year recurrence interval means a 1 percent annual probability of an equal or greater event under the stated probability model.
It neither prevents consecutive exceedances nor predicts a fixed 100-year gap.

## 2. Multi-year probability

Under stationary and independent annual periods:

\[
P(N\geq1)=1-(1-p)^n
\]

\[
P(N\geq1)=1-(1-0.01)^{30}
=1-0.99^{30}
\approx0.2603
\]

The communication value is **about 26 percent**.
Additional decimal places would overstate the meaning of a result that depends on stationarity, independence, and an estimated event magnitude.

The expected number of exceedances is:

\[
E[N]=np=30(0.01)=0.30
\]

The expected count of 0.30 is not a 30 percent probability.
It is a long-run average count across comparable 30-year periods.

## 3. Bound calculation

The lower endpoint is:

\[
Q_{\min}=0.9Q_H
=0.9(220 \text{m3/s})
=198 \text{m3/s}
\]

The upper endpoint is:

\[
Q_{\max}=1.5Q_1
=1.5(760 \text{m3/s})
=1{,}140 \text{m3/s}
\]

Both endpoints are positive whole numbers and \(198<1{,}140\).
No rounding is required for the exercise contract.

**Evidence note:** Correct multiplication establishes only the endpoint arithmetic for the supplied values and factors.
It does not define \(Q_H\), validate either source value, or establish that the factors are suitable elsewhere.

## 4. Candidate comparison

| Candidate | Contract check | Follows supplied rule? | Scientific concern | Provenance or uncertainty concern |
| --- | --- | --- | --- | --- |
| A: 198 to 1,140 m3/s | Passes | Yes | The factors may not match the intended hydraulic or hydrologic use. | The source periods, derivations, crosswalk, and uncertainty are missing. |
| B: 220 to 880 m3/s | Passes | No | A 14-year record can provide weak support for a 1 percent AEP estimate without regional and historical information. | Rating changes, fitting method, confidence limits, and review are absent. |
| C: 310 to 690 m3/s | Passes | No | A conditional adjacent-reach range answers a different question from marginal bounds for `R-200`. | The method, data, uncertainty, and acceptance record are absent. |
| D: 0 to 1,600 m3/s | Fails because the lower endpoint is not positive | No | Width alone does not establish coverage of important hydraulic transitions. | No hydrologic basis or uncertainty treatment is supplied. |

Candidate A is the best-supported choice only for the narrow purpose of following the supplied rule.
That conditional selection does not make Candidate A operationally ready.

Candidate B could become useful evidence after a suitable observed-flow frequency analysis.
Candidate C could inform a separately defined conditional-boundary analysis.
Neither candidate silently replaces the applied rule.
Candidate D would require a hydrologically justified positive lower endpoint, a justified upper endpoint, an intended-use coverage criterion, a candidate grid, sensitivity evidence, source provenance, and uncertainty characterization before consideration.
Correcting its zero endpoint alone would not provide that evidence.

## 5. Source-boundary trace

| Stage | Supplied or calculated content | Evidence required before the next stage |
| --- | --- | --- |
| Source values | \(Q_H=220 \text{m3/s}\) and \(Q_1=760 \text{m3/s}\) with stated limitations | Exact sources, periods, reach mapping, extraction, fitting, and uncertainty |
| Factors | 0.9 and 1.5 | Scientific rationale, intended use, sensitivity, and decision process |
| Point endpoints | 198 and 1,140 m3/s | Independent arithmetic review and units |
| Input contract | Positive whole-number endpoints in increasing order | Explicit rounding rule for non-integer results |
| Scenario plan | Proposed bounds and candidate-selection method | Durable provenance and complete candidate grid |
| Realized library | Attempted and selected scenario records | Evidence that endpoints, density, failures, and membership match the plan |
| Hydraulic validation | Response evidence for the intended use | Numerical verification, observations or benchmarks, uncertainty, criteria, and acceptance |

A response library can establish which discharges were calculated and selected.
It cannot by itself establish why its lower and upper bounds are hydrologically defensible.
That separation is central to [MX-003](../../reference/decision-code-artifact-crosswalk.md#mx-003-discharge-selection).

## 6. Uncertainty register

| Category | Concern supported by the packet | Evidence that would reduce or characterize it |
| --- | --- | --- |
| Forcing observation error | The 14-year estimate may be affected by rating changes and sparse high-flow measurements. | Measurement history, rating revisions, flags, and discharge uncertainty. |
| Modeled-forcing error | If the retrospective source is modeled or includes modeled values, it can inherit forcing, parameter, routing, and regulation error. | Identify the source type, then compare modeled values with quality-controlled observations across relevant events and magnitudes when applicable. |
| Network mismatch | The source reach used for the estimates may not match `R-200`. | Preserved network versions and a checked geometry, topology, and drainage-area crosswalk. |
| Temporal sampling | The sampling interval or aggregation rule may miss short peaks or alter annual maxima. | Native time support, missing-data audit, and sensitivity to finer data where justified. |
| Spatial scale | Basin-integrated estimates may not represent flow at the exact hydraulic inflow location. | Source-support comparison, drainage-area review, and local evidence. |
| Nonstationarity | The unknown period may span changes in climate, land cover, regulation, or channel condition. | Exact period, basin history, exploratory change analysis, and period sensitivity. |

Keeping the categories separate makes the needed evidence more specific.
It does not imply that the categories are independent.

## 7. Missing evidence and readiness

At least these items are missing:

1. The exact retrospective release and record period.
2. The source reach and checked crosswalk to `R-200`.
3. Geometry, topology, and drainage-area consistency checks.
4. The event definition and derivation of \(Q_H\).
5. The water-year and annual-maximum extraction rules.
6. Missing-data and time-support conventions.
7. The probability distribution and fitting implementation.
8. Skew, outlier, and historical-information treatment.
9. Confidence intervals or another uncertainty summary.
10. A stationarity assessment and decision horizon.
11. Bias or performance evidence for the retrospective source.
12. A rounding policy for non-integer endpoints.
13. Durable provenance connecting source values, factors, arithmetic, and plan.
14. Evidence that the realized library spans the planned bounds.
15. Numerical and hydraulic validation over the intended scenario range.
16. Classification of the retrospective source as observed, modeled, or blended, with supporting provenance.

Items 1 through 8 can change a source value or point-estimation method and therefore change the calculated endpoints.
Item 12 can change the submitted endpoint when arithmetic produces a non-integer value.

Items 9 through 11 affect confidence and interpretation and can motivate a different authorized analysis.
They do not change the arithmetic of the currently supplied point inputs by themselves.

Item 13 affects traceability.
Item 14 affects realization evidence.
Item 15 affects suitability for the intended hydraulic use.

**NOT READY for operational use.**
Candidate A follows the packet's rule, but the packet lacks source, method, uncertainty, crosswalk, provenance, realization, and validation evidence.

## 8. Evidence labels

| Statement | Label | Reason |
| --- | --- | --- |
| AEP is an annual probability under the stated probability model. | **Scientific foundation** | The concept is supported by [SCI-010](../../reference/bibliography.md#sci-010-aep-and-the-100-year-flood). |
| Candidate A uses factors of 0.9 and 1.5. | **Applied example** | The factors are supplied assumptions for this exercise. |
| A scenario plan should preserve data, method, assumptions, and rounding. | **Design principle** | Those records make the bounds reproducible and reviewable. |
| Candidate A equals 198 to 1,140 m3/s for the supplied values. | **Evidence note** | The statement records the bounded calculation and no broader validity. |
| Suitability of the factors for another basin is unresolved. | **Open question** | The prompt provides no transferability evidence. |
