# Lab 2 Solution: Catchment to Discharge

This solution gives one supported interpretation of the packet in [Lab 2](../lab-02-catchment-to-discharge.md).
Use it only after attempting the prompt.

## 1. Network and drainage areas

| Reach | Immediate upstream reaches | Transitive upstream reaches | Headwater? | Terminal? |
| --- | --- | --- | --- | --- |
| `R-100` | None | None | Yes | No |
| `R-300` | None | None | Yes | No |
| `R-200` | `R-100`, `R-300` | `R-100`, `R-300` | No | Yes |

The stated rule selects `R-100` as the main inflow path because \(84 \text{km2}>51 \text{km2}\).
The rule applies to this confluence role.
It does not rename an entire river or establish a universal mainstem definition.

The local incremental area is:

\[
A_{local}=142-(84+51)=7 \text{km2}
\]

The 142 km2 catchment represents land contributing to the `R-200` outlet under the supplied network delineation.
A hydraulic model domain represents the area over which terrain, roughness, state, and boundaries are calculated.
The two areas answer different questions and need not share a boundary.

## 2. Peaks and aligned sums

| Reach | Sampled peak | Peak time |
| --- | ---: | ---: |
| `R-100` | 160 m3/s | 6 h |
| `R-300` | 90 m3/s | 9 h |
| `R-200` | 250 m3/s | 9 h |

The aligned sums are:

| Time | Upstream plus local sum | `R-200` discharge | Downstream minus aligned sum |
| ---: | ---: | ---: | ---: |
| 0 h | \(24+12+3=39 \text{m3/s}\) | 40 m3/s | +1 m3/s |
| 3 h | \(92+44+6=142 \text{m3/s}\) | 130 m3/s | -12 m3/s |
| 6 h | \(160+72+10=242 \text{m3/s}\) | 215 m3/s | -27 m3/s |
| 9 h | \(148+90+12=250 \text{m3/s}\) | 250 m3/s | 0 m3/s |
| 12 h | \(76+64+9=149 \text{m3/s}\) | 170 m3/s | +21 m3/s |

Adding the separate peak magnitudes gives \(160+90=250 \text{m3/s}\), but those peaks occur at different times.
That number therefore does not represent an aligned upstream state.
At 9 h, the aligned contributions including the local estimate equal the supplied `R-200` value, but one matching row does not establish the routing mechanism.

## 3. Routing interpretation

**Inference:** The lower `R-200` value relative to the aligned 6 h sum is consistent with travel time, attenuation, or temporary storage between the upstream locations and the outlet.
The 9 h peak is consistent with confluence timing that combines a still-high `R-100` flow, the `R-300` peak, and local inflow.
The 12 h downstream excess over the same-time sum is consistent with delayed release from storage or travel time.

These interpretations are not observations of cause.
The table also permits measurement error, unrepresented inflow, or different time support.

Useful discriminating evidence includes:

1. Continuous hydrographs on one clock at all three reach locations.
2. Water-level or storage histories between the confluence and outlet.
3. A time-varying local-inflow record.
4. Channel geometry, roughness, and downstream-stage observations.
5. A stated routing model with supported parameters.
6. Observation methods and uncertainty covariance across the series.

## 4. Selected steady discharge

A defensible selection for the bounded objective is **250 m3/s for `R-200`**.
It is the supplied sampled peak at 9 h.
The prompt gives an illustrative interval of 225 to 275 m3/s around that value.

The selection does not make 250 m3/s a frequency estimate, forecast, or accepted design flow.
It is one applied-example forcing for the stated near-peak comparison.

An interpretable hydraulic scenario also needs:

1. Complete model identity.
2. Allocation of the total among the two upstream inflow lines and local geometry.
3. Boundary geometry, sign convention, and unit checks.
4. A stated downstream condition with value, units, source, and datum where relevant.
5. An initial state or hot-start record.
6. Terrain, roughness, domain, and grid provenance.
7. Numerical settings and output intervals.
8. Termination, balance, edge, and sensitivity evidence.
9. The hydrologic source method, time support, and uncertainty treatment.

## 5. Information lost in the steady reduction

Reducing the hydrograph to 250 m3/s loses or changes:

1. The 9 h event-time coordinate.
2. The rising sequence before the peak.
3. The recession after the peak.
4. Event duration.
5. Hydrograph volume.
6. Rate of rise and recession.
7. Travel time and attenuation.
8. Confluence timing.
9. The time-varying local contribution.
10. Dependence among source uncertainties.
11. The antecedent water state created by earlier flows.
12. The interaction between discharge history and downstream stage.

Simulation time under constant forcing describes evolution of the hydraulic calculation.
It does not replay the event clock or its changing discharges.

## 6. Uncertainty review

The relative component uncertainties cannot be added directly because the quantities have different magnitudes and their errors may be correlated.
Routing, time alignment, local contribution, and measurement methods also affect the relationship between upstream and downstream estimates.

A quantitative propagation would need probability distributions or defensible bounds for each input, covariance or dependence information, a routing relationship, common time support, and a stated output metric.
The prompt explicitly says that the correlations are unknown.

A bounded sensitivity comparison can calculate otherwise identical scenarios at 225, 250, and 275 m3/s.
That comparison would show response sensitivity across the supplied interval.
It would not assign probabilities to the three results.

## 7. Evidence labels

| Statement | Label | Reason |
| --- | --- | --- |
| A hydrograph relates discharge to time at a stated location. | **Scientific foundation** | This is the general definition supported by the handbook and [SCI-006](../../reference/bibliography.md#sci-006-surface-runoff-and-catchment-response). |
| The table gives `R-200` a sampled peak of 250 m3/s at 9 h. | **Applied example** and **Evidence note** | The value is supplied synthetic evidence and supports only the bounded table claim. |
| A forcing record should preserve reach, time support, units, source, and uncertainty. | **Design principle** | The rule makes the input interpretable and traceable. |
| The smaller 6 h value at `R-200` proves that channel storage caused the difference. | **Evidence note** rejecting the claimed scope | The table shows a difference but does not identify its cause. |
| Error correlation among the uncertainty descriptions is unknown. | **Open question** | The prompt states that the dependence information is absent. |

An accurate rewrite of statement 4 is:

> **Inference from the applied example:** The 6 h difference is consistent with travel time, attenuation, or storage, but the supplied packet does not discriminate among those explanations or measurement error.

## Readiness statement

**NOT READY for operational forcing selection.**
The exercise supports network arithmetic, peak identification, one conditional steady value, and a sensitivity interval.
It lacks the source, routing, dependence, model, boundary, numerical, and validation evidence needed for operational use.
