# Lab 10 Solution: Follow Deterministic Discharge Selection

This solution derives every selection decision from [Lab 10](../lab-10-follow-adaptive-selection.md).
It follows the fixed-grid method in [MX-003](../../reference/decision-code-artifact-crosswalk.md#mx-003-discharge-selection).

## 1. Endpoint calculation

The endpoint changes are:

\[
\Delta h_{max}=2.30-1.00=1.30\ \text{m}
\]

\[
\Delta A_f=0.750-0.500=0.250\ \text{km}^2
\]

The response distance is:

\[
D(100,250)=\max\left(\frac{1.30}{0.50},\frac{0.250}{0.10}\right)
=\max(2.60,2.50)
=2.60
\]

The interval requires refinement because \(2.60>1\) and untried candidates lie inside it.
Its arithmetic midpoint is 175 m3/s, which is on the candidate grid.
The first refinement candidate is therefore 175 m3/s.
The nearest lower accepted compatible measurement is 100 m3/s, so it supplies the warm start.

## 2. Tie decisions

After 175 m3/s is accepted:

\[
D(100,175)
=\max\left(\frac{1.60-1.00}{0.50},\frac{0.595-0.500}{0.10}\right)
=\max(1.20,0.95)
=1.20
\]

\[
D(175,250)
=\max\left(\frac{2.30-1.60}{0.50},\frac{0.750-0.595}{0.10}\right)
=\max(1.40,1.55)
=1.55
\]

Both eligible intervals are 75 m3/s wide.
The lower-endpoint tie rule selects the 100 to 175 m3/s interval.

Its midpoint is:

\[
\frac{100+175}{2}=137.5\ \text{m3/s}
\]

Candidates 125 and 150 m3/s are both 12.5 m3/s from that midpoint.
The lower-candidate tie rule selects 125 m3/s.
Its warm start is 100 m3/s.

After 125 m3/s is accepted:

\[
D(100,125)
=\max\left(\frac{0.20}{0.50},\frac{0.030}{0.10}\right)
=0.40
\]

\[
D(125,175)
=\max\left(\frac{0.40}{0.50},\frac{0.065}{0.10}\right)
=0.80
\]

Those intervals meet the criterion.
The 175 to 250 m3/s interval remains at 1.55 and is the only eligible interval.
Its midpoint is 212.5 m3/s.
Candidates 200 and 225 m3/s are equally near, so the lower-candidate tie selects 200 m3/s.
The nearest lower accepted compatible measurement is 175 m3/s.

## 3. Rejected-attempt handling

Event 5a uses R-200-M0 rather than the required R-200-M1 generation.
It also has a connected wet component at an unassigned edge.
Either condition is sufficient to reject it from the scientifically accepted measured set.

The rejected result cannot divide the 175 to 250 m3/s interval.
It cannot become a selected member or a warm-start source.
Its maximum depth and flooded area also cannot be mixed into the accepted sequence because they describe an incompatible and edge-rejected object.

Under the method, selection stops at that point.
The prompt then supplies a separately authorized recovery that repeats the same 200 m3/s candidate against the required generation.
The selector does not silently advance to 225 m3/s.
The quarantine observation preserves the failed attempt as evidence without granting it membership or reuse authority.

## 4. Recovered refinement calculations

Event 5b passes the stated identity, edge, artifact, and integrity screens and is admitted at 200 m3/s.

The lower interval distance is:

\[
D(175,200)
=\max\left(\frac{1.78-1.60}{0.50},\frac{0.635-0.595}{0.10}\right)
=\max(0.36,0.40)
=0.40
\]

The upper interval distance is:

\[
D(200,250)
=\max\left(\frac{2.30-1.78}{0.50},\frac{0.750-0.635}{0.10}\right)
=\max(1.04,1.15)
=1.15
\]

Only the 200 to 250 m3/s interval requires refinement.
Its exact midpoint is 225 m3/s, so event 6 is selected.
The nearest lower accepted compatible measurement is 200 m3/s, so it supplies the warm start.

After 225 m3/s is accepted:

\[
D(200,225)
=\max\left(\frac{2.01-1.78}{0.50},\frac{0.680-0.635}{0.10}\right)
=\max(0.46,0.45)
=0.46
\]

\[
D(225,250)
=\max\left(\frac{2.30-2.01}{0.50},\frac{0.750-0.680}{0.10}\right)
=\max(0.58,0.70)
=0.70
\]

Both new intervals meet the criterion.

## 5. Final set and stopping result

| Final adjacent interval | Response distance | Result |
| --- | ---: | --- |
| 100 to 125 m3/s | 0.40 | Meets criterion |
| 125 to 175 m3/s | 0.80 | Meets criterion |
| 175 to 200 m3/s | 0.40 | Meets criterion |
| 200 to 225 m3/s | 0.46 | Meets criterion |
| 225 to 250 m3/s | 0.70 | Meets criterion |

Every final interval has \(D\leq1\).
The selected set is 100, 125, 175, 200, 225, and 250 m3/s.
Candidate 150 m3/s remains untried because it lies inside the accepted 125 to 175 m3/s interval.
No interval has \(D>1\) without an untried interior candidate, so no unresolved grid residual remains.

The accepted warm-start sequence is 100 from dry, 250 from 100, 175 from 100, 125 from 100, recovered 200 from 175, and 225 from 200 m3/s.
Event 5a is absent from that sequence.

## 6. Direct evidence verdict

**SELECTION TRACE COMPLETE; SCIENTIFIC READINESS NOT ESTABLISHED.**

The packet supports the candidate arithmetic, deterministic tie decisions, rejection handling, recovery sequence, accepted membership, final stopping result, and observation of required roles for the accepted synthetic runs.
It also demonstrates that the edge-rejected wrong-generation result was preserved without contaminating selector state.

The packet does not establish that the fixed grid resolves every material hydraulic transition.
It supplies no grid or time-step refinement, complete balance, local hydraulic history, initial-state sensitivity, independent validation, or authorized intended-use acceptance criteria.
The 0.50 m and 0.10 km2 scales are method inputs for this example rather than universal adequacy thresholds.

Another review would require numerical and hydraulic evidence for the intended use, representative scenario coverage, sensitivity to the fixed grid and warm starts, complete provenance, and an authorized acceptance decision.
