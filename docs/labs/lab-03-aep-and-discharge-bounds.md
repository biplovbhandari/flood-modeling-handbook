# Lab 3: AEP and Discharge Bounds

This lab uses a complete synthetic probability and discharge packet to practice annual exceedance probability conversion, multi-year probability, bound arithmetic, candidate comparison, and uncertainty review.
The values describe only the applied example for `R-200`.

## Prerequisites

Complete these chapters before starting:

- [Flood Frequency, AEP, and Discharge Bounds](../01-hydrology-for-fim/04-flood-frequency-aep-and-bounds.md)
- [Forcing Sources and Uncertainty](../01-hydrology-for-fim/05-forcing-sources-and-uncertainty.md)

Read [Lab Conventions](README.md), [Source Authority](../reference/source-authority.md), and [Equations and Units](../reference/equations-and-units.md).

## Learning objectives

After completing this lab, the learner should be able to:

- convert AEP to recurrence interval without describing a forecast schedule;
- calculate the probability of at least one exceedance over several years;
- apply a stated discharge-bound rule with units and endpoint checks;
- compare bound candidates without claiming unsupported precision;
- separate point-estimate arithmetic from uncertainty and provenance; and
- identify evidence that blocks operational use.

## Synthetic probability and bound packet

Assume every discharge has already been converted to m3/s for `R-200`.
The scenario-input contract for this exercise requires positive whole-m3/s lower and upper endpoints with the lower endpoint less than the upper endpoint.

| Item | Supplied value | Supplied limitation |
| --- | ---: | --- |
| High-flow threshold, \(Q_H\) | 220 m3/s | A retrospective hydrologic summary supplied the value, but its event definition, period, extraction rule, and uncertainty are absent. |
| Retrospective 1 percent AEP estimate, \(Q_1\) | 760 m3/s | A log-Pearson Type III summary supplied the value, but the exact period, skew treatment, fitting details, and confidence interval are absent. |
| Short-record 1 percent AEP estimate | 880 m3/s | The estimate uses 14 annual peaks, but rating changes, regional information, record suitability, and confidence limits are unreviewed. |
| Conditional adjacent-reach range | 310 to 690 m3/s | The range is a hypothetical conditional analysis with no method record, uncertainty interval, or acceptance decision. |
| Wide screening range | 0 to 1,600 m3/s | The endpoints were chosen only to be broad and have no stated hydrologic basis. |

The packet does not identify whether the retrospective hydrologic source underlying \(Q_H\) and \(Q_1\) is observed, modeled, or blended.
Treat the retrospective source type as unknown.

Use this applied bound rule:

\[
Q_{\min}=0.9Q_H
\]

\[
Q_{\max}=1.5Q_1
\]

The factors are assumptions supplied by this exercise.
They are not established as suitable for another reach or intended use.

The probability calculations assume stationary and independent annual periods.
The discharge estimates themselves remain uncertain even when the arithmetic is exact.

## Part A: Convert AEP and recurrence interval

**Core inspection:** Complete the table using \(T=1/p\), with \(p\) expressed as a fraction.

| AEP | AEP fraction | Recurrence interval |
| ---: | ---: | ---: |
| 20 percent | ? | ? |
| 4 percent | ? | ? |
| 1 percent | ? | ? |
| ? | ? | 500 years |

For the 1 percent row, explain why a 100-year recurrence interval does not predict one event exactly every 100 years.

## Part B: Calculate multi-year exceedance probability

**Core inspection:** Calculate the probability of at least one 1 percent AEP exceedance during 30 years.

\[
P(N\geq1)=1-(1-p)^n
\]

Show the substitution and result.
Round the communication value to an appropriate percentage.
Explain why additional decimal places would not improve the underlying scientific claim.
Calculate the expected number of exceedances, (np), and distinguish that expectation from the probability of one or more events.

## Part C: Apply the bound rule

**Core inspection:** Calculate \(Q_{\min}\) and \(Q_{\max}\) from the supplied rule.
Show each multiplication with units.
Check the ordering, positivity, and whole-m3/s requirements.
State whether rounding is required.
Explain why correct arithmetic does not resolve the missing definition and derivation of \(Q_H\).

## Part D: Compare candidate bound sets

Evaluate these candidates.

| Candidate | Lower bound | Upper bound | Proposed basis |
| --- | ---: | ---: | --- |
| A | Result from Part C | Result from Part C | Apply the supplied factors to \(Q_H\) and \(Q_1\). |
| B | 220 m3/s | 880 m3/s | Use the threshold and short-record estimate without factors. |
| C | 310 m3/s | 690 m3/s | Use the hypothetical adjacent-reach conditional range. |
| D | 0 m3/s | 1,600 m3/s | Use the unsupported wide screening range. |

For each candidate:

1. Check it against the exercise input contract.
2. State whether it follows the supplied bound rule.
3. Identify one scientific concern.
4. Identify one provenance or uncertainty concern.
5. State what additional evidence would be needed before use.

Select the best-supported candidate for the narrow purpose of following the supplied applied-example rule.
Use conditional language and do not call any candidate operationally ready.

## Part E: Build the source-boundary trace

**Core inspection:** Trace Candidate A through these stages:

1. Source values and their stated limitations.
2. The two multiplication factors.
3. Calculated point endpoints.
4. Whole-m3/s contract check.
5. A proposed scenario plan.
6. A realized response library.
7. Hydraulic validation for the intended use.

For each stage, state the evidence required before the next claim.
Explain why a response library can prove which discharges were calculated without proving why the endpoints were scientifically selected.
Use [MX-003](../reference/decision-code-artifact-crosswalk.md#mx-003-discharge-selection) to check the distinction between candidate bounds, attempted scenarios, and selected members.

## Part F: Build an uncertainty register

Create a six-row table with these categories:

1. Forcing observation error.
2. Modeled-forcing error.
3. Network mismatch.
4. Temporal sampling.
5. Spatial scale.
6. Nonstationarity.

For each category, identify one concern supported by the packet and one item that would reduce or characterize the uncertainty.
Do not collapse the six categories into one general uncertainty statement.

## Part G: Identify missing evidence

List at least ten missing items that prevent operational use of Candidate A.
Include the exact retrospective period, reach crosswalk, annual-maximum extraction rule, fitting method, uncertainty interval, stationarity assessment, endpoint rounding policy, durable provenance, realized-library evidence, and hydraulic validation.

Separate missing items that can change a point estimate or submitted endpoint from items that change confidence, interpretation, traceability, realization evidence, or validation evidence.
Identify evidence that could motivate a different bound method without pretending that it changes the arithmetic of the supplied rule.

## Part H: Apply the five evidence labels

Classify each statement as **Scientific foundation**, **Applied example**, **Design principle**, **Evidence note**, or **Open question**.

1. AEP is the probability of an equal or greater event in a specified year under the stated probability model.
2. Candidate A uses factors of 0.9 and 1.5.
3. A scenario plan should preserve the data, method, assumptions, and rounding that produced its endpoints.
4. Candidate A equals 198 to 1,140 m3/s for the supplied values.
5. The suitability of the factors for another basin is unresolved.

## Deliverable

Submit a short answer with these sections:

1. AEP and recurrence calculations.
2. Multi-year probability calculation.
3. Bound calculation and contract checks.
4. Candidate comparison and conditional selection.
5. Source-boundary trace.
6. Six-category uncertainty register.
7. Missing evidence and direct readiness statement.

## Competency criteria

The lab is complete when the answer:

- converts 20, 4, and 1 percent AEP correctly and derives 0.2 percent from a 500-year interval;
- reports about a 26 percent chance of at least one 1 percent AEP exceedance in 30 years under the stated assumptions;
- calculates Candidate A as 198 to 1,140 m3/s;
- conditionally selects Candidate A only for alignment with the supplied rule;
- rejects Candidate D under the positive-endpoint contract;
- distinguishes point arithmetic from uncertainty and provenance;
- separates all six uncertainty categories; and
- concludes that no candidate is ready for operational use from the supplied packet.

After completing the lab, compare the reasoning with [Lab 3 Solution](solutions/lab-03-aep-and-discharge-bounds-solution.md).

## Source notes

- **Scientific foundation:** Flood-frequency methods and limitations are supported by [SCI-009](../reference/bibliography.md#sci-009-bulletin-17c).
- **Scientific foundation:** AEP and recurrence-interval interpretation are supported by [SCI-010](../reference/bibliography.md#sci-010-aep-and-the-100-year-flood).
- **Scientific foundation:** Nonstationarity considerations are supported by [SCI-014](../reference/bibliography.md#sci-014-nonstationary-flood-frequency-analysis).
- **Evidence note:** Every discharge and limitation in the packet is constructed teaching material.
