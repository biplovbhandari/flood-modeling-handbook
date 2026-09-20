# Discharge, Stage, and Routing

Discharge describes how much water passes a section per unit time, while stage describes water level relative to a stated reference.
Routing explains how the timing, shape, and magnitude of a hydrograph change as water moves through reaches and confluences.

## Why this topic matters

A hydraulic scenario needs both a forcing magnitude and boundary context.
A discharge selected from a hydrograph is not interchangeable with stage, and a steady scenario cannot retain the travel time, attenuation, volume, and confluence timing of the event that supplied it.

## Prerequisites

Read [Water Balance, Runoff, and Hydrographs](02-water-balance-runoff-and-hydrographs.md) and [Quantities, Units, and Datums](../00-orientation/03-quantities-units-and-datums.md).
Retain the orientation rule that stage requires a reference and that WSE requires a vertical datum.

## Learning objectives

After this chapter, the reader should be able to:

- distinguish discharge, stage, WSE, and depth;
- explain what a rating curve can and cannot establish;
- identify travel time, attenuation, lateral inflow, and confluence timing in routed hydrographs;
- explain why routed downstream flow is not generally the unshifted sum of upstream peaks; and
- state what one steady 2D FIM discharge scenario preserves and loses.

## Discharge and stage answer different questions

**Scientific foundation:** Discharge, \(Q\), is the volume of water passing a section or boundary per unit time.
Its SI unit is m3/s.
Stage is water level relative to a stated reference, such as a gauge zero or vertical datum.
Its SI unit is metres when SI units are used.

Discharge answers how much water is moving through a section per unit time.
Stage answers how high the water surface is relative to its reference at a location.
The same discharge can correspond to different stages when channel geometry, roughness, downstream control, vegetation, sediment, ice, or floodplain activation differs.

WSE is an elevation relative to a vertical datum.
Depth is the local difference between compatible WSE and terrain or bed elevation.
A stage observation becomes WSE only when its reference elevation and datum relationship are known.

## Rating curves connect stage and discharge locally

**Scientific foundation:** A stage-discharge rating curve represents the relationship between stage and discharge at a particular site under the conditions used to establish the relation.
USGS develops ratings from paired stage and discharge measurements and applies them to continuous stage records to estimate discharge.

The relationship is site-specific because geometry, slope, and roughness affect the water level associated with a flow.
The relationship can change when the channel, vegetation, debris, ice, or control condition changes.

The [USGS Streamgaging Basics](https://www.usgs.gov/mission-areas/water-resources/science/streamgaging-basics) page explains how stage-discharge ratings are developed, maintained, and applied.
A rating curve does not make stage and discharge the same quantity.
It supplies a conditional relationship at a stated location.

## Routing changes a hydrograph in space and time

**Scientific foundation:** Routing predicts or calculates how a hydrograph changes as a flood wave moves through a river reach, reservoir, or network.
The [National Weather Service routing glossary](https://forecast.weather.gov/glossary.php?word=routing) defines flood routing in terms of downstream changes in timing, shape, and amplitude.

Travel time is the elapsed time associated with movement of a flow feature or water response between locations under a stated definition.
It can be described by the lag between corresponding hydrograph features, but peak-to-peak lag is not automatically equal to the travel time of an individual parcel of water.

Attenuation is a reduction and spreading of a hydrograph feature, commonly the peak, as storage and hydraulic processes redistribute flow over time.
Attenuation does not mean that water volume disappears.
A volume change requires boundary fluxes, sources, sinks, or a different accounting interval.

Lateral inflow is water entering a routed reach between its upstream and downstream endpoints.
It can come from tributaries, local catchment runoff, groundwater, drainage systems, or other represented sources.
Withdrawal, loss, or diversion terms must be named separately when they matter.

## Confluence timing controls the combined peak

At a confluence, a downstream hydrograph reflects the timing and shape of all contributing flows plus routing and local lateral inflow.
If two tributary peaks arrive together, their combined peak can be larger than if one arrives after the other has receded.
If one tributary's lower but broad hydrograph overlaps another's narrow peak, the combined result can have a different peak time and duration from either input.

The downstream discharge at time \(t\) can be expressed conceptually as the result of routing all upstream hydrographs, distributed lateral inflow, and the initial reach storage together:

\[
Q_{down}(t) =
\mathcal{R}\!\left(
\{Q_i(t)\}_{i=1}^{n},
q_{\ell}(x,t),
S(t_0);
\theta
\right)
\]

- \(Q_{down}(t)\) is downstream discharge in m3/s.
- \(Q_i(t)\) is the hydrograph from upstream input \(i\) in m3/s, and \(n\) is the number of upstream inputs.
- \(q_{\ell}(x,t)\) is distributed lateral inflow per unit reach length at location \(x\) and time \(t\), in m3/s per m.
- \(S(t_0)\) is water stored in the routed reach at the initial time \(t_0\), in m3.
- \(\theta\) represents the routing method's geometry, parameters, downstream controls, and other required conditions.
- \(\mathcal{R}\) represents the selected routing method that applies travel, storage, and attenuation to the supplied inputs.

The corresponding interval water balance is

\[
S(t_1)-S(t_0) =
\sum_{i=1}^{n}\int_{t_0}^{t_1} Q_i(t)\,dt
+ \int_{t_0}^{t_1}\int_{0}^{L} q_{\ell}(x,t)\,dx\,dt
- \int_{t_0}^{t_1} Q_{down}(t)\,dt
- V_{loss}
\]

- \(S(t_1)-S(t_0)\) is the change in reach storage over the interval, in m3.
- \(t_1\) is the end of the accounting interval, in s on the same time axis as \(t_0\).
- \(L\) is routed reach length in m.
- The first integral sum is total upstream inflow volume in m3.
- The double integral is total distributed lateral-inflow volume in m3.
- The downstream integral is outflow volume in m3.
- \(V_{loss}\) is any separately represented withdrawal, infiltration, evaporation, or other sink volume over the interval, in m3.

Upstream and lateral inflow volumes can differ from downstream outflow volume over the same interval because water can enter or leave reach storage and because separately represented sinks can remove water.
Attenuation can reduce and spread a peak while the interval balance still accounts for the associated water volume.

These expressions are conceptual rather than a complete routing algorithm.
Different routing methods impose different conservation, storage, momentum, and parameter assumptions.

## Worked timing example

Two tributaries join immediately upstream of a routed reach.
Tributary A peaks at 30 m3/s at 6 hours.
Tributary B peaks at 25 m3/s at 9 hours.

Adding the two peak values gives 55 m3/s, but that number is not necessarily the combined peak because the peaks occur at different times.
The combined inflow at 6 hours requires B's discharge at 6 hours.
The combined inflow at 9 hours requires A's discharge at 9 hours.
The routed downstream peak then depends on reach storage, travel time, attenuation, lateral inflow, and downstream hydraulic conditions.

The correct diagnostic is to align the full hydrographs on a common time reference before combining or routing them.
Peak values without time series are insufficient.

## From a hydrograph to a steady hydraulic scenario

**Scientific foundation:** Selecting one discharge from a hydrograph preserves one flow-rate magnitude and its stated context.
It does not preserve the event's sequence, duration, integrated volume, rate of rise, recession, or the hydraulic state created by earlier flows.

A steady scenario can answer a bounded question such as:

> What quasi-steady hydraulic response does this model produce when 250 m3/s is imposed at this inflow geometry under these downstream and initial conditions?

It cannot by itself answer:

> How will this complete flood event evolve through time at this reach?

The same numerical discharge on a rising limb and recession can encounter different downstream stage, floodplain storage, tributary timing, and antecedent hydraulic state.
Representing those differences requires a suitable unsteady forcing and boundary design, not merely the repeated use of the same steady magnitude.

## Current project boundary

**Current implementation:** [`QFixBC`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/solvers.py) carries one integer discharge value in m3/s and one boundary vector.
The current ND and KWSE jobs construct that boundary from the scenario discharge and the model manifest's inflow line.
No current scenario input accepts a discharge hydrograph.

**Current implementation:** The ND job samples multiple steady discharge magnitudes within caller-provided bounds and evaluates changes in hydraulic response metrics between runs.
The sequence is a scenario-library sampling procedure, not a routed event hydrograph.

**Current implementation:** The KWSE job executes caller-provided pairs of upstream discharge and downstream water-surface condition.
Its downstream transfer captures hydraulic boundary influence from a selected downstream scenario, but it does not route a time-varying catchment hydrograph through the reach network.

**Target design:** The target orchestrator propagates changed downstream hydraulic dependencies upstream through network topology and boundary-condition provenance.
That is scenario-dependency propagation, not hydrologic flood-wave routing.
The distinction matters because both processes use a reach network but update different scientific objects.

**Open question:** A project-wide contract that converts a sourced time-varying hydrograph into the present steady scenario libraries is not established by the current job inputs inspected here.
Later chapters address discharge bounds, scenario selection, and uncertainty without assuming that this missing conversion is already implemented.

## Common misconceptions

### A stage increase implies the same proportional discharge increase

The rating relationship is generally nonlinear and site-specific.
Floodplain activation or a changed control can alter the relationship further.

### Routing only shifts a hydrograph in time

Routing can change timing, shape, and peak magnitude.
Lateral inflow and confluence timing can also increase or reshape downstream flow.

### Attenuation proves water was lost

A lower peak can result from temporary storage and a broader hydrograph.
Volume must be checked over compatible intervals before inferring loss.

### Running several steady discharges creates an event simulation

A set of independent steady scenarios samples a response surface.
It does not preserve temporal state transitions unless an explicit method and compatible initial conditions connect the runs.

### Network propagation is hydrologic routing

The target orchestrator propagates scenario dependencies and changed downstream boundary information.
Hydrologic routing propagates a time-varying flow response.
One does not prove the other.

## Competency check

Suppose an upstream hydrograph reaches 250 m3/s twice, once on the rising limb and once on the recession.
For each occurrence, list the additional evidence needed to decide whether one steady hydraulic scenario can represent the intended use.
Include downstream stage, initial hydraulic state, hydrograph timing, reach identity, inflow geometry, and the decision the result must support.

Then explain why the following three statements are different:

1. A streamgage rating estimates 250 m3/s from a measured stage at one site.
2. Hydrologic routing predicts 250 m3/s at a downstream reach and time.
3. A current 2D FIM scenario applies 250 m3/s as a steady `QFIX` boundary.

## Source notes

- **Scientific foundation:** Rating-curve behavior is supported by [SCI-007](../reference/bibliography.md#sci-007-streamgaging-and-stage-discharge-ratings), and routing terminology is supported by [SCI-008](../reference/bibliography.md#sci-008-national-weather-service-routing-glossary).
- **Current implementation:** Constant discharge boundaries and scenario input shapes are mapped under [JOB-004](../reference/bibliography.md#job-004-reach-topology-and-steady-forcing-contracts).
- **Target design:** Upstream scenario-dependency propagation is mapped under [SYS-002](../reference/bibliography.md#sys-002-upstream-scenario-propagation).
- **Open question:** No current inspected job contract accepts a time-varying discharge hydrograph, and no target-design statement cited here is treated as deployed hydrologic routing.
