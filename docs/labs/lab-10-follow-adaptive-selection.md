# Lab 10: Follow Deterministic Discharge Selection

This lab reconstructs the synthetic fixed-grid interval-refinement method used for R-200.
The packet includes accepted measurements, a rejected wrong-generation edge result, an authorized recovery, and the final stopping state.

## Prerequisites

Complete [Discharge-Only Scenario Libraries](../05-scenario-libraries/01-normal-depth-libraries.md) and [Adaptive Discharge Selection](../05-scenario-libraries/02-adaptive-discharge-selection.md).
Read [MX-003](../reference/decision-code-artifact-crosswalk.md#mx-003-discharge-selection), [MX-008](../reference/decision-code-artifact-crosswalk.md#mx-008-identity), [CQ-004](../reference/conflicts-and-open-questions.md#cq-004-domain-clipping), and [CQ-005](../reference/conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence).

## Execution boundary

Complete the trace from the tables and equations below.
No solver run or external data is required.
Optional tools may be used only for generic arithmetic.

## Learning objectives

After completing this lab, the learner should be able to:

- calculate the normalized response distance for adjacent measured scenarios;
- reproduce deterministic interval, midpoint, and tie decisions;
- distinguish a completed attempt from a scientifically accepted measurement;
- handle a rejected edge result without allowing it to divide an interval;
- identify the compatible warm-start source;
- apply the fixed-grid stopping rule; and
- distinguish selection arithmetic from hydraulic readiness.

## Method packet

The fixed candidate grid is:

\[
\{100,125,150,175,200,225,250\}\ \text{m3/s}
\]

The 100 and 250 m3/s endpoints are measured first.
Every admissible measurement uses model generation R-200-M1, grid G-10, terrain T-200-A, datum VD-1, one boundary family, one wet threshold, one nodata rule, and one final-state definition.

For adjacent scientifically accepted measurements at \(Q_a<Q_b\), use:

\[
D(a,b)=\max\left(
\frac{|h_{max,b}-h_{max,a}|}{0.50\ \text{m}},
\frac{|A_{f,b}-A_{f,a}|}{0.10\ \text{km}^2}
\right)
\]

An interval meets the response criterion when \(D(a,b)\leq1\).
An interval requires refinement when \(D(a,b)>1\) and at least one untried grid candidate lies strictly inside it.

Apply these deterministic rules:

1. Sort scientifically accepted measurements by discharge.
2. Keep every adjacent interval that requires refinement.
3. Choose the widest retained interval.
4. Break an equal-width interval tie by the lower discharge endpoint.
5. Choose the untried interior candidate nearest the arithmetic midpoint.
6. Break an equal-distance candidate tie by the lower discharge.
7. Warm-start from the nearest lower scientifically accepted compatible measurement.
8. Do not let a failed or scientifically rejected result divide an interval or supply a warm start.
9. Stop when every adjacent interval meets the criterion or contains no untried interior candidate.
10. Record an interval with \(D>1\) and no untried interior candidate as an unresolved grid residual.

## Recovery rule

If the next candidate returns with the wrong model generation, incompatible identity, or an unintended connected wet-edge result, quarantine that attempt and stop selection.
A separately authorized recovery may resubmit the same candidate against the required generation.
The selector must not skip to another candidate and must not use metrics from the rejected attempt.

For this synthetic trace, the recovery for event 5a is authorized.
The required generation remains R-200-M1, so prior accepted measurements remain compatible.

## Complete trial and observation table

Events are listed in observation order.
Maximum depth and flooded area control selection.

| Event | Discharge | Model generation | Maximum depth | Flooded area | Edge and identity screen | Observation disposition | Warm-start source |
| --- | ---: | --- | ---: | ---: | --- | --- | --- |
| 1 | 100 m3/s | R-200-M1 | 1.00 m | 0.500 km2 | Pass | Accepted lower endpoint | Dry start |
| 2 | 250 m3/s | R-200-M1 | 2.30 m | 0.750 km2 | Pass | Accepted upper endpoint | 100 m3/s |
| 3 | 175 m3/s | R-200-M1 | 1.60 m | 0.595 km2 | Pass | Accepted refinement measurement | 100 m3/s |
| 4 | 125 m3/s | R-200-M1 | 1.20 m | 0.530 km2 | Pass | Accepted refinement measurement | 100 m3/s |
| 5a | 200 m3/s | R-200-M0 | 1.90 m | 0.650 km2 | Fail: wrong generation and connected wet component at an unassigned edge | Rejected and quarantined; selection stops pending the stated recovery | Not eligible |
| 5b | 200 m3/s | R-200-M1 | 1.78 m | 0.635 km2 | Pass | Accepted recovery measurement | 175 m3/s |
| 6 | 225 m3/s | R-200-M1 | 2.01 m | 0.680 km2 | Pass | Accepted refinement measurement | 200 m3/s |

For events 1, 2, 3, 4, 5b, and 6, an observer found the scenario record, depth, inundation, and diagnostic roles in one generation with matching integrity values.
The same observer confirmed that the edge screen executed and found no connected wet component at an unintended edge.
The observer found event 5a only in a quarantine generation that is excluded from selected membership and reuse.

The table supplies no grid-refinement study, full balance, local hydraulic history, independent validation, or authorized real-world acceptance threshold.

## Part A: Reproduce endpoint refinement

Calculate \(D(100,250)\).
Identify the first candidate and warm-start source.

## Part B: Reproduce the interval and candidate ties

After 175 m3/s is accepted, calculate \(D(100,175)\) and \(D(175,250)\).
Apply the interval-width tie rule.
Show why the midpoint rule selects 125 rather than 150 m3/s.

After 125 m3/s is accepted, calculate the new adjacent distances.
Identify the next interval, midpoint, candidate, and warm-start source.

## Part C: Handle the rejected edge attempt

State why event 5a cannot divide the interval, become a member, or supply a warm start.
State what the method does at the rejection before applying the separately authorized recovery.
Explain why the event 5a metrics must not be compared with the accepted response sequence.

## Part D: Finish the recovered trace

After event 5b is accepted, calculate the distances on both sides of 200 m3/s.
Identify the final candidate and warm-start source.
Calculate the two final distances created by 225 m3/s.

## Part E: Apply the stopping rule

List every final adjacent interval and response distance.
State the selected discharge set, the status of 150 m3/s, and whether an unresolved grid residual remains.

## Part F: Issue an evidence verdict

Choose exactly one verdict.

- SELECTION TRACE COMPLETE; SCIENTIFIC READINESS NOT ESTABLISHED
- DISCHARGE LIBRARY READY FOR UNRESTRICTED USE

State the verdict first.
Then state what the arithmetic, edge screen, observation record, and missing evidence can and cannot establish.

## Deliverable

Submit a short answer with these sections:

1. Endpoint calculation.
2. Tie decisions.
3. Rejected-attempt handling.
4. Recovered refinement calculations.
5. Final set and stopping result.
6. Direct evidence verdict.

## Competency criteria

The lab is complete when the answer:

- calculates \(D(100,250)=2.60\) and selects 175 m3/s;
- applies the lower-endpoint tie and then the lower-candidate tie to select 125 m3/s;
- selects 200 m3/s next;
- excludes event 5a from every selector state and stops until the authorized recovery;
- accepts event 5b and then selects 225 m3/s;
- derives final distances 0.40, 0.80, 0.40, 0.46, and 0.70;
- retains 150 m3/s as untried;
- reports no unresolved grid residual; and
- issues SELECTION TRACE COMPLETE; SCIENTIFIC READINESS NOT ESTABLISHED.

After completing the lab, compare the reasoning with [Lab 10 Solution](solutions/lab-10-follow-adaptive-selection-solution.md).
