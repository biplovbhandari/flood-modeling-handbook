# Lab 2: Catchment to Discharge

This lab uses a synthetic reach network and discharge hydrographs to connect catchment topology, confluence timing, and selection of one steady hydraulic forcing.
The exercise does not represent a production watershed or validate a project scenario library.

## Prerequisites

Complete these chapters before starting:

- [Watersheds, Reaches, and Hydrofabrics](../01-hydrology-for-fim/01-watersheds-reaches-and-hydrofabrics.md)
- [Water Balance, Runoff, and Hydrographs](../01-hydrology-for-fim/02-water-balance-runoff-and-hydrographs.md)
- [Discharge, Stage, and Routing](../01-hydrology-for-fim/03-discharge-stage-and-routing.md)

Read [Source Authority](../reference/source-authority.md) and follow the evidence-label rules in [Lab Conventions](README.md).

## Execution boundary

All required steps are **Core inspection**.
No command or production access is required, and any independent tooling remains optional and user-run without changing the evidence boundary.

## Learning objectives

After completing this lab, the learner should be able to:

- identify immediate and transitive upstream relationships;
- apply the current project's upstream-mainstem input definition without treating it as a universal mainstem definition;
- identify peak magnitude and timing from aligned hydrographs;
- select and justify one plausible steady discharge for a bounded hydraulic question; and
- state what information is lost when a hydrograph is reduced to one steady scenario.

## Synthetic network

The synthetic network is directed from left to right.

```text
A --\
     >-- C --\
B --/         >-- E --> outlet
D ------------/
```

| Reach | Immediate downstream reach | Total drainage area, km2 | Strahler order |
| --- | --- | ---: | ---: |
| A | C | 24 | 1 |
| B | C | 16 | 1 |
| C | E | 40 | 2 |
| D | E | 30 | 1 |
| E | outlet | 70 | 2 |

Assume the drainage areas are cumulative at the downstream end of each reach.
Assume no flow split or lake modifies the represented topology.

## Synthetic discharge hydrographs

The following values are aligned to one common event clock.
They are synthetic observations or model outputs at the downstream end of each listed reach.
They are not intended to satisfy an instantaneous equation such as \(Q_C(t)=Q_A(t)+Q_B(t)\) because routing, storage, and unlisted local inflow may affect each location.

| Time after event start, h | A, m3/s | B, m3/s | C, m3/s | D, m3/s | E, m3/s |
| ---: | ---: | ---: | ---: | ---: | ---: |
| 0 | 4 | 3 | 8 | 5 | 12 |
| 3 | 16 | 10 | 20 | 12 | 27 |
| 6 | 30 | 22 | 42 | 28 | 55 |
| 9 | 18 | 25 | 48 | 20 | 68 |
| 12 | 8 | 12 | 26 | 10 | 37 |

## Part A: Identify the network relationships

**Core inspection:** Record the immediate upstream reaches of C and E.
Then record every transitive upstream reach of E.

Identify the headwater reaches in the represented network.
Explain why E's hydrologic watershed and a hydraulic model domain for E would not be interchangeable.

## Part B: Apply the current upstream-mainstem contract

**Core inspection:** For C and E, choose the value that would be supplied as `upstream_mainstem_reach_id` under the current `BuildModelInputs` definition.
Use the largest total drainage area among each reach's immediate upstream neighbors.

State the value for A, B, and D under the same contract.
Explain why the selected identifier is a caller-supplied project input rather than a value that the current `build_model` job derives by traversing the network.

Use these sources:

- [`BuildModelInputs`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/build_model.py)
- [`BuildModelJob`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/build_model.py)
- [Current build-model documentation](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/docs/jobs/build_model/build_model.md)

## Part C: Identify peak magnitude and timing

**Core inspection:** For A, B, C, D, and E, record the sampled peak discharge and peak time.

Identify the reaches whose peaks occur at 6 hours and those whose peaks occur at 9 hours.
Explain why adding the peak values of C and D does not establish E's peak without their time alignment and routing context.

## Part D: Interpret routing and confluence timing

**Core inspection:** Compare the timing of A and B with C, then compare C and D with E.

Write one plausible routing interpretation that uses the terms travel time, attenuation, lateral inflow, and confluence timing correctly.
Label the interpretation as an inference because the table does not provide a routing model, lateral-inflow record, storage record, or continuous hydrograph.

Identify at least two additional observations that would discriminate among possible explanations for E's 9-hour peak.

## Part E: Select one steady model discharge

**Core inspection:** Assume the bounded modeling objective is:

> Examine E's reach-scale hydraulic response near the sampled event peak under one stated downstream condition.

Select one discharge from E's hydrograph as a plausible steady `QFIX` scenario.
State its value and units, the reach, the source table, the event-time context, and why it fits the objective.

Then state at least four additional inputs or checks needed before the number becomes a scientifically interpretable hydraulic scenario.
Include inflow geometry and downstream condition in the answer.

## Part F: State what the steady reduction loses

**Core inspection:** List at least six pieces of information lost or altered when E's hydrograph is reduced to the selected steady discharge.

Your list must address:

- peak timing;
- rising and recession history;
- duration and event volume;
- travel time or routing behavior;
- confluence timing or lateral inflow; and
- antecedent or initial hydraulic state.

Explain why solver time under constant `QFIX` forcing is not a substitute for event time in the table.

## Part G: Preserve the project evidence boundaries

**Core inspection:** Classify each statement with one evidence label.

1. The synthetic table gives E a sampled peak of 68 m3/s at 9 hours.
2. `BuildModelInputs.upstream_mainstem_reach_id` is defined as the immediate upstream reach with the largest drainage area.
3. DR-013 selects a line inflow on the highest-drainage upstream reach.
4. The target orchestrator propagates changed downstream hydraulic dependencies upstream.
5. The current jobs route a rainfall-driven hydrograph from A through E.

For statement 5, state whether the available evidence supports the claim.
If it does not, rewrite the statement accurately.

## Deliverable

Submit a short answer with these sections:

1. Network and mainstem table.
2. Peak table.
3. Routing interpretation and missing evidence.
4. Selected steady discharge and required scenario context.
5. Lost information.
6. Evidence-label classification.

## Competency criteria

The lab is complete when the answer:

- identifies A and B upstream of C, and C and D upstream of E;
- identifies A, B, and D as headwaters in the represented network;
- selects A as C's project-specific upstream mainstem and C as E's;
- records E's sampled peak as 68 m3/s at 9 hours;
- selects a defensible steady discharge for the stated objective;
- distinguishes an inference from observed synthetic values and current code; and
- explains why the current steady forcing does not reproduce the complete hydrograph.

After completing the lab, compare the reasoning with [Lab 2 Solution](solutions/lab-02-catchment-to-discharge-solution.md).
