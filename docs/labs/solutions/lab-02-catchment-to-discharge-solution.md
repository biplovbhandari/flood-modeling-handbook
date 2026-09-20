# Lab 2 Solution: Catchment to Discharge

This solution shows one evidence-disciplined interpretation of the synthetic network and hydrographs.
It does not validate a production forcing source, model, scenario, or reach library.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, and stopping criteria are defined in [Lab 2](../lab-02-catchment-to-discharge.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and keeps synthetic calculations outside project authority.
Stop when the answer satisfies the prompt's competency criteria or records the exact evidence gap that prevents a supported forcing choice.

## 1. Network and mainstem table

| Reach | Immediate upstream reaches | All transitive upstream reaches | Headwater? | Project-specific `upstream_mainstem_reach_id` |
| --- | --- | --- | --- | --- |
| A | None | None | Yes | null |
| B | None | None | Yes | null |
| C | A, B | A, B | No | A |
| D | None | None | Yes | null |
| E | C, D | A, B, C, D | No | C |

A is selected for C because A has the larger drainage area among C's immediate upstream neighbors, 24 km2 compared with 16 km2 for B.
C is selected for E because C has the larger drainage area among E's immediate upstream neighbors, 40 km2 compared with 30 km2 for D.
A, B, and D use null because they are headwaters in the represented network.

**Current implementation:** [`BuildModelInputs`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/build_model.py) defines `upstream_mainstem_reach_id` as the upstream reach with the largest drainage area and null for a headwater.
[`BuildModelJob`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/build_model.py) reads the supplied identifier and queries that reach.
The generated [build-model documentation](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/docs/jobs/build_model/build_model.md) states that the caller supplies `upstream_reach_ids` and `upstream_mainstem_reach_id` because the file is queried by `reach_id` rather than traversed by `reach_to_id` in this job.

The upstream-mainstem result is project-contract reasoning rather than a universal rule for naming a river's mainstem.
It selects one immediate upstream reach for the current model input.

E's hydrologic watershed includes land draining through A, B, C, and D to E's outlet.
A hydraulic model domain for E is the numerical area over which terrain, roughness, water state, and boundaries are represented.
The two areas can overlap but answer different questions and need not share a boundary.

## 2. Peak table

| Reach | Sampled peak discharge, m3/s | Peak time, h after start |
| --- | ---: | ---: |
| A | 30 | 6 |
| B | 25 | 9 |
| C | 48 | 9 |
| D | 28 | 6 |
| E | 68 | 9 |

A and D peak at 6 hours.
B, C, and E peak at 9 hours.

Adding C's peak of 48 m3/s and D's peak of 28 m3/s gives 76 m3/s, but those peaks do not occur at the same time.
At 9 hours, the aligned table gives 48 m3/s for C and 20 m3/s for D.
Their sum is 68 m3/s, which equals the listed E value at that sample, but this equality does not establish the routing mechanism.
The table explicitly allows unlisted storage and lateral inflow, and it provides only five discrete samples.

## 3. Routing interpretation and missing evidence

**Inference from the synthetic table:** The shift from A's 6-hour peak and B's 9-hour peak to C's 9-hour peak is consistent with tributary confluence timing plus travel time and storage between the upstream and downstream sample locations.
The reduction from the aligned C-plus-D total of 70 m3/s at 6 hours to 55 m3/s at E is consistent with attenuation or delay of the earlier combined pulse.
The later E peak could reflect release of stored water as C peaks, and unlisted lateral inflow could also change the result, but the table does not establish either mechanism.

The word inference is necessary because the exercise provides no routing equations, reach-storage record, continuous hydrographs, lateral-inflow time series, downstream-stage record, or travel-time observations.

Useful discriminating observations include:

1. Continuous inflow and outflow hydrographs at C, D, and E on one clock.
2. Reach storage or water-level observations between the C-D confluence and E.
3. A lateral-inflow time series for the local catchment between the sampled sections.
4. Channel geometry, roughness, slope, and downstream-stage observations for a routing or hydraulic analysis.
5. A stated routing model with calibrated or independently supported parameters.

## 4. Selected steady discharge and scenario context

A defensible selection is **68 m3/s for reach E**.
The value comes from E's synthetic hydrograph at 9 hours and is the sampled event peak.
It matches the bounded objective of examining the reach-scale hydraulic response near the sampled peak under one downstream condition.

This selection does not assert that 68 m3/s is a selected production scenario, frequency-based bound, forecast, or validated design flow.
It is only a plausible educational choice for the stated objective.

Before the number becomes a scientifically interpretable hydraulic scenario, the scenario needs at least:

1. The exact reach and model identity for E.
2. The inflow-line geometry, CRS, direction convention, and confirmation that 68 m3/s is applied there as intended.
3. A stated downstream boundary condition, including its value, units, source, and vertical reference when it uses WSE.
4. Terrain, roughness, domain, and grid provenance for the model realization.
5. Initial conditions or hot-start provenance.
6. Solver environment, run settings, termination rule, and output interval.
7. The hydrologic source, time meaning, uncertainty, and reason for selecting the value.
8. Checks for edge effects, convergence evidence, quantity units, and hydraulic plausibility.

**Current implementation:** The current scenario jobs bind one discharge value to the model manifest's inflow line as `QFIX`.
The typed positive value and geometry establish part of the hydraulic input, but they do not establish the hydrologic source or scientific suitability of the selected scenario.

## 5. Information lost in the steady reduction

Reducing E's hydrograph to 68 m3/s loses or alters at least the following information:

1. The 9-hour timing of the sampled peak relative to the event start.
2. The rising-limb sequence from 12 to 27 to 55 to 68 m3/s.
3. The recession from 68 to 37 m3/s within the supplied record.
4. The event duration and the discharge integrated over that duration.
5. The rate of rise and rate of recession between samples.
6. Travel time and attenuation between upstream reaches and E.
7. The relative timing of C and D at their confluence.
8. Any unlisted lateral inflow, withdrawal, or storage exchange.
9. The antecedent hydraulic state produced by earlier discharges.
10. The possibility that 68 m3/s under another downstream stage would produce a different response.

The hydraulic solver can run for many model seconds under a constant 68 m3/s boundary.
That time allows the modeled water state to evolve toward the run's termination condition.
It does not replay the event clock, rising limb, peak arrival, or recession in the synthetic table.

## 6. Evidence-label classification

### Statement 1

> The synthetic table gives E a sampled peak of 68 m3/s at 9 hours.

This is an instructional given outside the project authority hierarchy.
It does not receive one of the six evidence labels and must not be presented as production, experiment, or project-case evidence.
The calculation and inference drawn from it still need explicit reasoning and limits.

### Statement 2

> `BuildModelInputs.upstream_mainstem_reach_id` is defined as the immediate upstream reach with the largest drainage area.

**Current implementation:** The checked-out input model defines the field this way and the job consumes the caller-supplied identifier.

### Statement 3

> DR-013 selects a line inflow on the highest-drainage upstream reach.

**Selected methodology:** DR-013 ALT-A has status Alternate Selected.
The status and scope must remain attached to the claim.

### Statement 4

> The target orchestrator propagates changed downstream hydraulic dependencies upstream.

**Target design:** The system-design material assigns this responsibility to target orchestration.
The statement does not prove deployed behavior.

### Statement 5

> The current jobs route a rainfall-driven hydrograph from A through E.

The claim is unsupported as written, and **Current implementation** evidence establishes the opposite boundary.
An accurate rewrite is:

> **Current implementation:** The current modeling jobs consume prepared steady discharge values and do not simulate rainfall-runoff or route a rainfall-driven discharge hydrograph through the network.

The target orchestrator's upstream dependency propagation does not change this conclusion because dependency propagation is not hydrologic routing.

## Remaining limitations

**Open question:** The exercise contains no real hydrofabric, precipitation record, routing configuration, rating curve, downstream condition, hydraulic model, or validation artifact.
It demonstrates reasoning and evidence boundaries, not operational adequacy.
