# Lab 7 Solution: Convergence and Solver Evidence

This solution gives one evidence-bounded assessment of the packet in [Lab 7](../lab-07-convergence-and-solver-evidence.md).
Use it only after attempting the prompt.

## 1. Storage-change history

The square-cell area is:

\[
A_c=(20.0 \text{m})^2=400 \text{m2}
\]

The inflow volume during every saved interval is:

\[
V_{in}=Q_{in}\Delta t_s
=(50.0 \text{m3/s})(900 \text{s})
=45{,}000 \text{m3}
\]

The complete calculation is:

| Time | Positive-depth sum | Storage | Absolute storage change | \(C_V\) | \(C_V<0.001\)? |
| ---: | ---: | ---: | ---: | ---: | --- |
| 900 s | 225.0000 m | 90,000 m3 | Not available | 1.0 sentinel | No |
| 1,800 s | 281.2500 m | 112,500 m3 | 22,500 m3 | 0.5 | No |
| 2,700 s | 315.0000 m | 126,000 m3 | 13,500 m3 | 0.3 | No |
| 3,600 s | 326.2500 m | 130,500 m3 | 4,500 m3 | 0.1 | No |
| 4,500 s | 328.5000 m | 131,400 m3 | 900 m3 | 0.02 | No |
| 5,400 s | 328.5900 m | 131,436 m3 | 36 m3 | 0.0008 | Yes |

For the final saved interval:

\[
C_V=\frac{|131{,}436-131{,}400| \text{m3}}{45{,}000 \text{m3}}
=0.0008
\]

The 5,400 s output is the first and only supplied output below the threshold.
The controller's one-interval stop rule is therefore satisfied.
The review requirement for three consecutive outputs is not satisfied.

The 900 s value is a sentinel because no preceding grid exists for a two-grid calculation.
It is not a ratio derived from 900 s of storage change.

## 2. Termination, exit, and convergence

The controller requested a stop because the final storage-change ratio was below 0.001.
The process acknowledged the request within 0.4 s, wrote final outputs, and returned exit code 0.
Those facts support controlled process completion under the supplied log.

The exit code does not establish mass balance, local steady behavior, acceptable edges, numerical verification, or physical validity.
The stored termination reason records why the controller stopped the process.
It does not override the separate three-interval review criterion.

**Evidence note:** Termination succeeded, but the stated quasi-steady review criterion failed because only one consecutive ratio is below the threshold.

## 3. Mass balance

For the final interval:

\[
\Delta V_s=131{,}436-131{,}400=36 \text{m3}
\]

\[
R=45{,}000-44{,}900-36=64 \text{m3}
\]

\[
\frac{|R|}{V_{in}}\times100
=\frac{64}{45{,}000}\times100
\approx0.142\%
\]

For the cumulative run:

\[
\Delta V_s=131{,}436-0=131{,}436 \text{m3}
\]

\[
R=270{,}000-138{,}100-131{,}436
=464 \text{m3}
\]

\[
\frac{|R|}{V_{in}}\times100
=\frac{464}{270{,}000}\times100
\approx0.172\%
\]

Both percentages are below the supplied 0.5 percent criterion.
The final-interval and cumulative mass-balance criteria therefore pass for the supplied control volume and terms.

That bounded pass does not establish local steady state.
It also does not establish grid convergence, acceptable edges, or agreement with physical evidence.

## 4. Stability, local state, edges, and numerical verification

The local WSE changes are:

| Point | 3,600 to 4,500 s | 4,500 to 5,400 s | Criterion result |
| --- | ---: | ---: | --- |
| A | \(102.404-102.400=0.004 \text{m}\) | \(102.405-102.404=0.001 \text{m}\) | Passes both intervals |
| B | \(101.835-101.800=0.035 \text{m}\) | \(101.860-101.835=0.025 \text{m}\) | Fails both intervals |

The evidence-separation table is:

| Category | What the packet supports | Failure or missing evidence | Result |
| --- | --- | --- | --- |
| Numerical stability summary | Maximum recorded Courant value is 0.76 against a 0.80 target, with no rejected steps or non-finite values. | Complete internal histories and the significance of 0.60 m3 of corrections are not supplied. | Bounded summary only |
| Local transient behavior | Point A changes are small under the stated criterion. | Point B WSE and speed continue to increase, and both WSE intervals fail. | Fail |
| Edge behavior | Intended outlet cells become wet and checks complete after 900 s. | One unintended perimeter cell remains wet from 3,600 through 5,400 s. | Fail |
| Numerical verification | No comparison is supplied. | A grid or time-step comparison is required. | Missing |

A decreasing domain-storage ratio can coexist with local change because positive and negative changes can be small in the domain total while remaining material at a point.
The storage ratio also contains no spatial information about the perimeter.
An unintended edge can therefore remain wet even when total storage change is small.

This distinction follows [MX-002](../../reference/decision-code-artifact-crosswalk.md#mx-002-convergence) and [CQ-005](../../reference/conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence).

## 5. Proposed alternate hot-start assessment

The reviewed run starts dry with zero initial storage.
That dry start is the initial state used by the reviewed cumulative balance and its 464 m3 residual.

The separate proposed alternate packet supplies one initial-state component: a prior final water-depth raster.
The proposed packet does not supply velocity, momentum, face flux, or a complete numerical restart state.

The proposed compatibility evidence includes:

1. Matching grid dimensions.
2. Matching alignment.
3. Matching terrain identity.
4. Matching vertical-reference metadata.
5. A source inflow of 45.0 m3/s near the target inflow of 50.0 m3/s.

Missing proposed-source evidence includes downstream condition, convergence history, velocity, flux, mass balance, and hydraulic acceptance.
Missing alternate-run evidence includes every resulting storage, local-state, balance, edge, termination, and final-field observation.

The prior depth field can plausibly reduce initial filling and wetting-front adjustment.
That efficiency inference does not prove complete-state continuation or final-state independence.

A discriminating comparison would run the proposed alternate with the same target forcing and boundaries as the reviewed dry-start run.
The comparison should use the same duration limits and review criteria.
It should compare the proposed alternate results with the supplied reviewed-run storage history, local WSE and speed, wet extent, balance, edge results, termination, and final fields.

## 6. Public-source boundaries

| Statement | Support classification | Evidence label and reason |
| --- | --- | --- |
| The LISFLOOD-FP manual describes its documented initial-depth input. | Supported by [SCI-033](../../reference/bibliography.md#sci-033-lisflood-fp-user-manual) within the stated release. | **Scientific foundation** for that documented capability |
| The SFINCS manual describes initial water levels and restart files for its documented version. | Supported by [SCI-032](../../reference/bibliography.md#sci-032-sfincs-user-manual) within the stated release. | **Scientific foundation** for that documented capability |
| Either manual proves which engine produced this unnamed packet. | Unsupported by both the prompt and sources. | **Open question** because engine identity is not supplied |
| The exit code and stop log establish controlled process completion. | Supported only by the synthetic packet. | **Evidence note** limited to the recorded run |
| Documentation for one solver proves adequacy for another solver or scenario. | Unsupported extension. | **Design principle** that capability and adequacy must remain separate |

Public documentation describes the named release and its stated scope.
It does not identify or validate the unnamed synthetic calculation.

## 7. Conclusion: DOES NOT MEET THE STATED CRITERIA

**DOES NOT MEET THE STATED CRITERIA**

The packet supports controlled process completion and passes the supplied final-interval and cumulative mass-balance thresholds.
It does not satisfy the three-interval storage criterion.
It also fails the Point B local-state criterion and the zero-unintended-edge criterion.
Numerical verification and hydraulic validation are absent.

The smallest additional evidence set that could change the conclusion includes:

1. At least two more consecutive below-threshold saved intervals without changing the stated review rule.
2. Continued Point B histories showing every final interval within 0.005 m or a justified revised criterion.
3. Correction or justified acceptance of the unintended wet perimeter cell.
4. A grid or time-step comparison within predeclared output tolerances.
5. Execution of the proposed alternate hot start and comparison with the supplied reviewed dry-start run.
6. Observation or accepted-benchmark evidence for the intended hydraulic quantities.
7. Boundary and parameter sensitivity material to the use.
8. A complete acceptance record for the stated intended use.

The successful stop and bounded mass-balance pass remain useful evidence.
They are not substitutes for the failed and missing categories.
