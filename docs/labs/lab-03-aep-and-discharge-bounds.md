# Lab 3: AEP and Discharge Bounds

This lab uses synthetic probability estimates and bound candidates to practice AEP conversion, DR-029 arithmetic, authority classification, and uncertainty review.
The values do not describe a production reach and do not validate a production scenario library.
They are instructional givens outside the project authority hierarchy and do not receive one of the six evidence labels.

## Prerequisites

Complete these chapters before starting:

- [Flood Frequency, AEP, and Discharge Bounds](../01-hydrology-for-fim/04-flood-frequency-aep-and-bounds.md)
- [Forcing Sources and Uncertainty](../01-hydrology-for-fim/05-forcing-sources-and-uncertainty.md)

Read [Source Authority](../reference/source-authority.md) and follow the evidence-label rules in [Lab Conventions](README.md).
Use [Equations and Units](../reference/equations-and-units.md) for the required equations.

## Execution boundary

All required steps are **Core inspection**.
No command or production access is required, and any independent tooling remains optional and user-run without changing the evidence boundary.

## Learning objectives

After completing this lab, the learner should be able to:

- convert AEP to recurrence interval without describing a forecast schedule;
- calculate the probability of at least one exceedance over several years;
- apply the exact DR-029 lower- and upper-bound rule;
- distinguish a selected bound method from observed-flow and experimental alternatives;
- select the best-supported candidate without claiming false precision; and
- identify missing evidence that blocks production use.

## Synthetic instructional givens

Assume all quantities below have already been converted to m3/s for a synthetic reach named `R-1042`.
No source files, confidence intervals, or crosswalk artifacts are provided beyond the table.

| Item | Value | Stated source and limitation |
| --- | ---: | --- |
| High-flow-threshold input, \(Q_{HFT}\) | 220 m3/s | Value attributed to an NWM version 3.0 retrospective derivative, but its scientific definition and derivation metadata are absent. |
| Retrospective 1 percent AEP discharge, \(Q_{100}\) | 760 m3/s | Value attributed to an LP3 fit of retrospective annual maxima, but the exact period, fitting implementation, and confidence interval are absent. |
| Short-gage-record 1 percent AEP discharge | 880 m3/s | Value attributed to 14 years of observed annual peaks, but rating changes, regional skew, record suitability, and confidence interval are unreviewed. |
| Joint conditional downstream range | 310 to 690 m3/s | Hypothetical output patterned after the proposed EXP-015 question, with no completed experiment or adopted decision. |

Assume the current ND job interface requires whole-m3/s bounds.

## Part A: Convert AEP and recurrence interval

**Core inspection:** Complete the table.

| AEP, percent | AEP, fraction | Recurrence interval, years |
| ---: | ---: | ---: |
| 20 | ? | ? |
| 4 | ? | ? |
| 1 | ? | ? |
| ? | ? | 500 |

For the 1 percent AEP row, explain in two sentences why a 100-year recurrence interval does not predict an event only once every 100 years.

## Part B: Calculate multi-year exceedance probability

**Core inspection:** Under stationary and independent annual periods, calculate the probability of at least one 1 percent AEP exceedance during 30 years.

Show the equation, substitution, and result.
Round the communication value to an appropriate percentage and explain why extra decimal places would not improve the scientific claim.

State how this probability differs from the expected number of exceedances, \(np\), over the same period.

## Part C: Apply DR-029

**Core inspection:** Use DR-029 ALT-A to calculate:

\[
Q_{min}=0.9Q_{HFT}
\]

\[
Q_{max}=1.5Q_{100}
\]

Show each multiplication with units.
State whether rounding is required for the current ND input contract in this synthetic case.
State why applying the selected arithmetic does not resolve the missing scientific definition of \(Q_{HFT}\).

## Part D: Compare candidate bound sets

**Core inspection:** Evaluate these candidates.

| Candidate | Lower bound, m3/s | Upper bound, m3/s | Proposed rationale |
| --- | ---: | ---: | --- |
| A | Result from Part C | Result from Part C | Apply DR-029 ALT-A to the stated retrospective values. |
| B | 220 | 880 | Use the high-flow threshold and the short-gage-record estimate without DR-029 multipliers. |
| C | 310 | 690 | Use the hypothetical adjacent-reach conditional range. |
| D | 0 | 1600 | Use a wide range to avoid missing anything. |

For each candidate, state:

1. Which value is only an instructional given and which real project, code, target-design, experiment, or open-question claim receives an evidence label.
2. Whether it follows current selected methodology.
3. One scientific or contract concern.
4. Whether the current ND input model would accept both endpoint values.

Select the best-supported candidate for a scenario plan intended to follow the current selected methodology.
Use conditional language and do not call any candidate production-ready.

## Part E: Trace decision, experiment, code, and target design

**Core inspection:** Complete a source-boundary trace for the selected candidate.

Include:

- DR-029's selected alternative and status;
- the exact purpose of EXP-015 and why it does not replace DR-029;
- the current `min_upstream_inflow` and `max_upstream_inflow` inputs;
- the calculations that the current ND job does not perform;
- the target design distinction between authored `q_*_bound` intent and an emergent ND `q_set`; and
- the artifact needed to prove how the bounds were derived and supplied.

Use these sources:

- [DR-029](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-decision-record/02_Decisions/DR-029%20-%20What%20Should%20be%20the%20Lower%20and%20Upper%20Discharge%20Bounds%20for%20Each%20Reach.md)
- [EXP-015](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-decision-record/03_Experiments/EXP-015%20-%20Characterize%20Joint%20Frequency%20Distribution%20of%20Adjacent%20Reach%20Flows.md)
- [`RunNDScenariosInputs`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_nd_scenarios.py)
- [System-design guide](https://github.com/NGWPC/twod-fim-knowledge-base/blob/6ca2d8471676eb4d6b4a86ef3c4683e029ef017e/system-design/guide.md)

## Part F: Build an uncertainty register

**Core inspection:** Create a six-row table with these categories:

1. Forcing observation error.
2. Modeled-forcing error.
3. Network mismatch.
4. Temporal sampling.
5. Spatial scale.
6. Nonstationarity.

For each category, identify one plausible concern for the synthetic evidence and one item that would reduce or characterize the uncertainty.
Do not collapse the six categories into one general uncertainty statement.

## Part G: Identify missing evidence

**Core inspection:** List at least eight missing items that prevent production use of Candidate A.
Your list must include the exact retrospective period, reach crosswalk, AMS extraction rule, fitting method, uncertainty interval, stationarity assessment, rounding policy, and durable planning or provenance artifact.

Under the fixed DR-029 formula, separate missing items that directly change a point input or submitted endpoint from items that change confidence, interpretation, traceability, realization evidence, or validation evidence.
Identify any evidence that could motivate a different authorized analysis without describing it as a change to the current fixed-input arithmetic.

## Deliverable

Submit a short answer with these sections:

1. AEP and recurrence calculations.
2. Multi-year probability calculation.
3. DR-029 bound calculation.
4. Candidate comparison and conditional selection.
5. Project source-boundary trace.
6. Six-category uncertainty register.
7. Missing evidence and readiness statement.

## Competency criteria

The lab is complete when the answer:

- converts 20, 4, and 1 percent AEP correctly and derives 0.2 percent from a 500-year interval;
- reports about a 26 percent chance of at least one 1 percent AEP exceedance in 30 years under the stated assumptions;
- calculates Candidate A as 198 to 1140 m3/s;
- selects Candidate A only conditionally for alignment with DR-029;
- rejects EXP-015 as implemented or adopted replacement behavior;
- identifies that current jobs consume rather than derive the bounds;
- separates all six uncertainty categories; and
- states that every candidate remains unready for production without the missing evidence.

After completing the lab, compare the reasoning with [Lab 3 Solution](solutions/lab-03-aep-and-discharge-bounds-solution.md).
