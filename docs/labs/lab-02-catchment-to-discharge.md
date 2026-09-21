# Lab 2: Catchment to Discharge

This lab uses the shared synthetic reach network and aligned discharge hydrographs to connect catchment topology, confluence timing, uncertainty, and selection of one steady hydraulic forcing.
The exercise does not represent a real watershed or validate a response library.

## Prerequisites

Complete these chapters before starting:

- [Watersheds, Reaches, and Hydrofabrics](../01-hydrology-for-fim/01-watersheds-reaches-and-hydrofabrics.md)
- [Water Balance, Runoff, and Hydrographs](../01-hydrology-for-fim/02-water-balance-runoff-and-hydrographs.md)
- [Discharge, Stage, and Routing](../01-hydrology-for-fim/03-discharge-stage-and-routing.md)

Read [Lab Conventions](README.md), [Source Authority](../reference/source-authority.md), and [Glossary](../reference/glossary.md).

## Learning objectives

After completing this lab, the learner should be able to:

- identify immediate and transitive upstream relationships;
- apply a stated mainstem-selection rule without treating it as a universal river-naming rule;
- identify peak magnitude and timing from aligned hydrographs;
- distinguish supplied values from routing inferences;
- select one steady discharge for a bounded hydraulic question; and
- state the uncertainty and information loss created by that reduction.

## Synthetic catchment and network packet

The represented network is directed from the two headwaters toward terminal reach `R-200`.

```text
R-100 --\
         >-- R-200 --> outlet
R-300 --/
```

| Reach | Immediate downstream reach | Cumulative drainage area | Strahler order | Role at the confluence |
| --- | --- | ---: | ---: | --- |
| `R-100` | `R-200` | 84 km2 | 1 | Mainstem under the stated largest-area rule |
| `R-300` | `R-200` | 51 km2 | 1 | Tributary |
| `R-200` | Outlet | 142 km2 | 2 | Terminal reach |

The two upstream cumulative areas total 135 km2.
The remaining 7 km2 is the local incremental drainage area between the two upstream boundaries and the `R-200` outlet.
Assume no flow split, lake, or hidden reach changes the represented topology.

## Synthetic discharge packet

The following values use one event clock and represent flow at the downstream end of each listed reach.
The local column is a catchment-scale contribution estimate for the incremental area rather than a boundary measurement.

| Time after event start | `R-100` discharge | `R-300` discharge | Local contribution | `R-200` discharge |
| ---: | ---: | ---: | ---: | ---: |
| 0 h | 24 m3/s | 12 m3/s | 3 m3/s | 40 m3/s |
| 3 h | 92 m3/s | 44 m3/s | 6 m3/s | 130 m3/s |
| 6 h | 160 m3/s | 72 m3/s | 10 m3/s | 215 m3/s |
| 9 h | 148 m3/s | 90 m3/s | 12 m3/s | 250 m3/s |
| 12 h | 76 m3/s | 64 m3/s | 9 m3/s | 170 m3/s |

Do not assume that the four columns must satisfy an instantaneous sum at every row.
Travel time, channel and floodplain storage, attenuation, different sampling locations, and estimation error can separate upstream contributions from downstream discharge.

The uncertainty packet supplies:

| Quantity | Supplied uncertainty description |
| --- | --- |
| `R-100` hydrograph | Illustrative relative uncertainty of plus or minus 12 percent |
| `R-300` hydrograph | Illustrative relative uncertainty of plus or minus 15 percent |
| Local contribution | Illustrative relative uncertainty of plus or minus 50 percent |
| `R-200` sampled peak | Illustrative interval of 225 to 275 m3/s |
| Dependence | Error correlations among the four series are unknown |
| Missing evidence | No routing model, storage history, continuous hydrograph, observation method, or calibration record is supplied |

The percentages and interval are independent givens for this exercise.
They are not sufficient to reconstruct a joint uncertainty distribution.

## Part A: Identify network relationships

**Core inspection:** Record the immediate upstream reaches of `R-200`.
Record every transitive upstream reach of `R-200`.
Identify the headwater and terminal reaches in the represented network.
Explain why the 142 km2 hydrologic catchment and a hydraulic model domain for `R-200` are not interchangeable.

## Part B: Apply the stated mainstem rule

**Core inspection:** Apply this rule:

> At a confluence, select the immediate upstream reach with the largest cumulative drainage area as the main inflow path.

Identify the selected reach and show the area comparison.
Calculate the local incremental drainage area.
Explain why the rule chooses an inflow role for this model context rather than declaring a universal named-river mainstem.

## Part C: Identify peak magnitude and timing

**Core inspection:** Record the sampled peak discharge and peak time for `R-100`, `R-300`, and `R-200`.
Calculate the aligned sum of the two upstream values and local contribution at 6 h and 9 h.
Compare each sum with the `R-200` value at the same time.
Explain why adding separate peak magnitudes without their time coordinates would be misleading.

## Part D: Interpret routing and confluence timing

**Core inspection:** Write one plausible interpretation that uses travel time, attenuation, storage, lateral inflow, and confluence timing correctly.
Label the interpretation as inference.
Name at least four additional observations that would discriminate among possible explanations for the 6 h and 9 h differences.

## Part E: Select one steady model discharge

**Core inspection:** Use this bounded objective:

> Examine the `R-200` reach-scale hydraulic response near the sampled event peak under one stated downstream condition.

Select one discharge from the `R-200` hydrograph for a fixed-discharge scenario.
State the value, unit, reach, source row, event-time context, supplied uncertainty interval, and reason for the choice.
List at least six additional inputs or checks needed before the value defines an interpretable hydraulic scenario.
Include inflow allocation, inflow geometry, downstream condition, initial state, and model identity.

## Part F: State what the steady reduction loses

**Core inspection:** List at least eight pieces of information lost or altered when the `R-200` hydrograph is reduced to one steady discharge.
Address peak timing, rising and recession history, duration, event volume, travel time, attenuation, confluence timing, local inflow, uncertainty dependence, and antecedent hydraulic state.
Explain why simulation time under constant forcing is not a substitute for the event clock.

## Part G: Review the uncertainty boundary

**Core inspection:** Explain why the three component percentages cannot be added directly to reproduce the supplied `R-200` interval.
State what covariance, routing, and measurement information would be needed for a quantitative propagation.
Describe one sensitivity comparison that is possible with the supplied 225 to 275 m3/s interval without calling it a probability distribution.

## Part H: Apply the five evidence labels

Classify each statement as **Scientific foundation**, **Applied example**, **Design principle**, **Evidence note**, or **Open question**.

1. A hydrograph relates discharge to time at a stated location.
2. The supplied table gives `R-200` a sampled peak of 250 m3/s at 9 h.
3. A forcing record should preserve reach, time support, units, source, and uncertainty.
4. The smaller 6 h value at `R-200` proves that channel storage caused the difference.
5. The correlation among the supplied uncertainty descriptions is unknown.

Rewrite statement 4 so that its evidence scope is accurate.

## Deliverable

Submit a short answer with these sections:

1. Network and drainage-area table.
2. Peak and aligned-sum table.
3. Routing interpretation and missing evidence.
4. Selected steady discharge and scenario context.
5. Lost information and uncertainty review.
6. Evidence-label classification.

## Competency criteria

The lab is complete when the answer:

- identifies `R-100` and `R-300` as immediate upstream reaches of `R-200`;
- identifies both upstream reaches as headwaters and `R-200` as terminal;
- calculates a 7 km2 local incremental area;
- selects `R-100` under the stated largest-area rule;
- records the `R-200` sampled peak as 250 m3/s at 9 h;
- distinguishes supplied values from routing inference;
- selects a defensible steady discharge for the stated objective; and
- preserves the limits of the supplied uncertainty information.

After completing the lab, compare the reasoning with [Lab 2 Solution](solutions/lab-02-catchment-to-discharge-solution.md).

## Source notes

- **Scientific foundation:** Watershed and drainage-network concepts are supported by [SCI-003](../reference/bibliography.md#sci-003-watersheds-and-drainage-basins).
- **Scientific foundation:** Hydrograph and catchment-response concepts are supported by [SCI-006](../reference/bibliography.md#sci-006-surface-runoff-and-catchment-response).
- **Scientific foundation:** Routing terminology is supported by [SCI-008](../reference/bibliography.md#sci-008-national-weather-service-routing-glossary).
- **Evidence note:** The network, discharges, and uncertainty descriptions are constructed teaching material.
