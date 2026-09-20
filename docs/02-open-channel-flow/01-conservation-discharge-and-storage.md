# Conservation, Discharge, and Storage

Open-channel hydraulics begins with a strict accounting rule: water can cross a boundary, accumulate inside it, or leave it, but a model cannot create or remove water without representing a source or sink.
Discharge expresses the rate of water crossing a section, while continuity connects those crossings to storage change.

## Why this topic matters

A model can show plausible depths and still have an unacceptable water balance.
An engineer therefore needs to distinguish imposed discharge, spatially distributed velocity, storage change, and complete continuity before judging a result.

## Prerequisites

Read [Discharge, Stage, and Routing](../01-hydrology-for-fim/03-discharge-stage-and-routing.md) and [Quantities, Units, and Datums](../00-orientation/03-quantities-units-and-datums.md).
Retain the hydrology chapters' distinction between a time-varying hydrograph and one steady discharge selected from it.

## Learning objectives

After this chapter, the reader should be able to:

- define a control volume and assign consistent flux signs;
- calculate discharge from a distributed velocity field and explain when \(Q=A\bar{V}\) is valid;
- distinguish local velocity from area-averaged velocity;
- apply differential and interval forms of storage continuity;
- perform dimensional checks on discharge and storage equations; and
- explain why a small storage change does not by itself prove mass-balance closure or hydraulic adequacy.

## Start with a control volume

**Scientific foundation:** A control volume is a chosen region in space used to account for water and the fluxes crossing its boundary.
The region can be a short channel reach, an entire floodplain domain, one numerical cell, or another clearly bounded part of the system.

The control surface is the boundary of that volume.
Water crossing inward is an inflow, water crossing outward is an outflow, and water retained within the boundary contributes to storage.
Rainfall, infiltration, evaporation, pumps, tributaries, and other processes belong in the accounting only when the model represents them as boundary fluxes or internal sources and sinks.

This chapter uses the following sign convention for continuity:

- inflows and internal sources are positive contributions to storage;
- outflows and internal sinks are positive magnitudes that are subtracted; and
- a positive storage change means that the control volume is filling.

The convention is not the only valid one.
Any other convention must be stated and applied consistently to every term.

The [HEC-RAS continuity-equation documentation](https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.6/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-unsteady-flow-hydrodynamics/continuity-equation) likewise develops continuity from inflow, outflow, lateral inflow, and storage in a channel control volume.

## Discharge is an integral of velocity

Velocity usually varies across a wetted cross-section.
It tends toward zero at solid boundaries because of drag, can be greater in the deeper main channel than on a shallow floodplain, and can include local directions that are not exactly normal to the section.

The discharge through a cross-section is

\[
Q = \int_A u_n\,dA
\]

- \(Q\) is signed discharge through the section in m3/s.
- \(A\) is the wetted cross-sectional area in m2.
- \(u_n\) is the local component of velocity normal to the section in m/s.
- \(dA\) is an infinitesimal element of wetted area in m2.
- The positive unit normal defines the positive direction through the section.

The dimensions are

\[
[Q] = [u_n][A] = \frac{\text{m}}{\text{s}}\text{m2} = \text{m3/s}
\]

The integral includes the spatial distribution that a single velocity value cannot show.
A small fast-flowing part and a large slow-flowing part can contribute comparable amounts of discharge.
Local reverse flow contributes negative discharge when the positive normal points the other way.

## Why \(Q=A\bar{V}\) works

Define the area-averaged normal velocity as

\[
\bar{V}=\frac{1}{A}\int_A u_n\,dA
\]

Substitution gives

\[
Q=A\bar{V}
\]

- \(\bar{V}\) is area-averaged velocity normal to the section in m/s.
- \(A\), \(Q\), \(u_n\), and the section orientation have the definitions above.

With this definition, \(Q=A\bar{V}\) is an exact restatement of the discharge integral for the same section and time.
It becomes an approximation when \(\bar{V}\) is estimated from incomplete measurements, a point velocity, a simplified subsection, a model output sampled at another location, or an assumed uniform distribution.

Do not replace \(\bar{V}\) with speed unless direction is irrelevant and every local velocity crosses the section in the positive direction.
Speed is nonnegative, while normal velocity is signed.

### Worked discharge calculation

Suppose a cross-section is represented by two zones at one time.
The main channel has area \(A_1=20\) m2 and representative normal velocity \(V_1=2.0\) m/s.
The inundated floodplain has area \(A_2=30\) m2 and representative normal velocity \(V_2=0.4\) m/s.
Both velocities are positive downstream.

The subsection discharges are

\[
Q_1=A_1V_1=(20)(2.0)=40\ \text{m3/s}
\]

and

\[
Q_2=A_2V_2=(30)(0.4)=12\ \text{m3/s}
\]

The total discharge and total wetted area are

\[
Q=Q_1+Q_2=52\ \text{m3/s}, \qquad A=20+30=50\ \text{m2}
\]

The corresponding area-averaged velocity is

\[
\bar{V}=\frac{Q}{A}=\frac{52}{50}=1.04\ \text{m/s}
\]

The dimensional check is

\[
(50\ \text{m2})(1.04\ \text{m/s})=52\ \text{m3/s}
\]

The 1.04 m/s average reproduces total discharge, but it does not describe either zone's actual representative velocity.
Using the main-channel velocity over the entire 50 m2 would incorrectly give 100 m3/s.

## What a cross-section average hides

One value of \(\bar{V}\) preserves discharge when paired with its wetted area, but it discards material information about the distribution.
It does not identify high-velocity corridors, slow floodplain storage, recirculation, shear between zones, or the direction of local flow.

The loss matters because energy flux depends on velocity cubed and momentum flux depends on velocity squared.
Two sections can have the same \(Q\), \(A\), and \(\bar{V}\) but different energy, momentum, erosion potential, and local hazard because their velocity distributions differ.
The next chapter introduces correction coefficients for these nonlinear effects.

In two-dimensional modeling, the depth-averaged velocity in a cell also hides vertical variation.
The horizontal vector components preserve direction better than one cross-section average, but they still do not resolve the full three-dimensional velocity field.

## Continuity connects fluxes to storage

For a fixed control volume, volume conservation can be written as

\[
\frac{dS}{dt}=\sum Q_{in}-\sum Q_{out}+Q_{source}-Q_{sink}
\]

- \(S\) is water storage inside the control volume in m3.
- \(t\) is time in s.
- \(Q_{in}\) is each boundary inflow magnitude in m3/s.
- \(Q_{out}\) is each boundary outflow magnitude in m3/s.
- \(Q_{source}\) is the combined represented internal source rate in m3/s.
- \(Q_{sink}\) is the combined represented internal sink rate in m3/s.

The left side has dimensions m3/s because storage volume is differentiated with respect to time.
Every term on the right must therefore also have dimensions m3/s.

For incompressible water of constant density, conserving volume is equivalent to conserving mass after multiplying every term by the same density.
If density variation matters, a mass balance must retain density explicitly.

Over the interval from \(t_0\) to \(t_1\), continuity becomes

\[
S(t_1)-S(t_0)=
\int_{t_0}^{t_1}
\left(
\sum Q_{in}-\sum Q_{out}+Q_{source}-Q_{sink}
\right)dt
\]

- \(t_0\) and \(t_1\) are the start and end times in s on the same time axis.
- \(S(t_1)-S(t_0)\) is storage change in m3.
- All flux terms retain the signs and units defined above.

Integration converts each discharge rate into a volume.
A positive result means net filling, while a negative result means net draining.

### Worked storage calculation

Assume a control volume has the following constant rates for 15 minutes:

- boundary inflow of 120 m3/s;
- boundary outflow of 100 m3/s;
- represented lateral source of 5 m3/s; and
- represented sink of 1 m3/s.

The net filling rate is

\[
\frac{dS}{dt}=120-100+5-1=24\ \text{m3/s}
\]

The interval is

\[
\Delta t=(15\ \text{min})(60\ \text{s/min})=900\ \text{s}
\]

The storage change is

\[
\Delta S=(24\ \text{m3/s})(900\ \text{s})=21{,}600\ \text{m3}
\]

The seconds cancel, leaving m3 as required.
If measured storage increased by a materially different amount, at least one boundary flux, source, sink, storage estimate, time alignment, or numerical residual would need investigation.

## Steady discharge does not require zero storage change everywhere

A constant imposed inflow is steady forcing, but the hydraulic state can remain unsteady while the domain fills, drains, wets new cells, or adjusts to downstream control.
A reach-level storage rate can also be small while local depths and velocities continue to change in offsetting ways.

At exact steady state for a fixed control volume, \(dS/dt=0\).
Continuity then requires total represented inflow plus sources to equal total represented outflow plus sinks.
This equality is necessary, but it does not prove that the velocity, depth, energy, or momentum solution is physically adequate.

Conversely, a nonzero storage change is not automatically an error.
It can be the correct response during a rising flood wave, initial filling, floodplain activation, or draining period.
The diagnostic question is whether the change agrees with all represented fluxes and the intended simulation state.

## Current project relevance

**Current implementation:** The current `QFIX` boundary value and ND discharge fields are whole-number integers expressed in m3/s, called `cms` in the input descriptions.
In [`RunNDScenariosInputs`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/run_nd_scenarios.py), `min_upstream_inflow` and `delta_upstream_inflow` are each validated as greater than zero.
The `max_upstream_inflow` field has no equivalent positivity check and no cross-field validation that it exceeds `min_upstream_inflow` in the reviewed checkout.
The [current ND adaptive run path](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/run_nd_scenarios.py) can pass the accepted maximum to `_run_scenario`, which constructs the `QFIX` boundary on the model manifest's inflow line.

**Open question:** An intended valid hydraulic scenario requires a physically meaningful positive discharge, but current ND input validation does not fully enforce that requirement for the maximum or its ordering relative to the minimum.
This statement identifies a static model-and-run-path contract gap in the reviewed checkout and does not assert that an invalid ND scenario has been observed in an operational run.
A `QFIX` input prescribes a total boundary discharge, but it does not by itself specify a measured cross-section velocity distribution or prove that the solver's local distribution is correct.

**Current implementation:** The current solver-run path estimates stored volume from positive raster depths and cell area at consecutive saved outputs.
It computes a dimensionless storage-change ratio by dividing the absolute volume change by imposed inflow volume over the saved-output interval.

**Open question:** As recorded in [CONF-002](../reference/conflicts-and-open-questions.md#conf-002-volume-convergence-and-mass-balance), this project ratio does not measure outflow or every source and sink.
Crossing its selected threshold can support a termination decision, but it does not close the control-volume balance and does not prove hydraulic adequacy.

**Scientific foundation:** Continuity is a required physical constraint.
It is not evidence that any particular boundary geometry, roughness field, solver configuration, scenario, or project artifact satisfies that constraint accurately.
Those claims require separate implementation and validation evidence.

## Common misconceptions

### \(Q=A\bar{V}\) means velocity is uniform

The equation uses an area average.
The local field can be highly nonuniform even when the average reproduces discharge exactly.

### A large area always carries most of the discharge

Discharge depends on both area and normal velocity.
A smaller fast zone can carry more water than a larger slow zone.

### Equal inflow and outflow prove the model is correct

Equal fluxes imply zero net storage change only when every source and sink is included consistently.
They do not prove that local depths, velocities, forces, or boundary effects are realistic.

### Small storage change proves mass balance

Small storage change can coexist with equally large and incorrect inflow and outflow errors, omitted fluxes, or local changes that cancel in the aggregate.
A complete check accounts for every modeled flux over the same boundary and interval.

### Constant inflow means the model is already steady

Constant forcing can drive a transient adjustment for a long time.
The hydraulic state, not just the boundary value, determines whether a stated steady or quasi-steady criterion has been met.

## Competency check

A 60 m2 cross-section carries 78 m3/s.
Explain what \(\bar{V}=78/60=1.3\) m/s does and does not tell you about local velocity.
Then define a reach control volume and list the evidence needed to determine whether storage should rise, fall, or remain constant during a 10-minute interval.

Your answer should name the section orientation, wetted area, normal velocity distribution, all boundary inflows and outflows, represented sources and sinks, the accounting interval, and two compatible storage estimates.
It should also explain why a storage-only convergence ratio cannot supply the missing outflow evidence.

## Source notes

- **Scientific foundation:** Control-volume continuity is supported by [SCI-016](../reference/bibliography.md#sci-016-hec-ras-continuity-equation).
- **Current implementation:** Steady discharge boundaries and the project storage-change calculation are mapped under [JOB-004](../reference/bibliography.md#job-004-reach-topology-and-steady-forcing-contracts) and [JOB-003](../reference/bibliography.md#job-003-current-implementation-locations).
- **Open question:** The distinction between storage convergence and complete mass balance is preserved in [CONF-002](../reference/conflicts-and-open-questions.md#conf-002-volume-convergence-and-mass-balance).
- **Supporting reference:** *Open-Channel Hydraulics* remains supporting reading under SCI-001, but it was not directly inspected and no chapter or page citation is asserted.
