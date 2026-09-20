# Lab 6: Grid Stability and Wetting

This lab combines a transparent CFL estimate, one cell-volume wetting transition, and a terrain-resolution pathway critique.
All numerical inputs and terrain values are synthetic instructional givens outside the project authority hierarchy.
They are not project observations, selected methodology, current implementation, target design, solver settings, or validation evidence.

## Prerequisites

Complete these chapters before starting:

- [Shallow-Water Models](../03-2d-hydraulics/01-shallow-water-models.md)
- [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md)
- [Time Stepping and Stability](../03-2d-hydraulics/03-time-stepping-and-stability.md)

Read [Source Authority](../reference/source-authority.md) and [Lab Conventions](README.md).
Use [Equations and Units](../reference/equations-and-units.md) for the shallow-water, cell-volume, and CFL relationships.

## Execution boundary

All required steps are **Core inspection**.
No command or production access is required, and any independent tooling remains optional and user-run without changing the evidence boundary.

## Learning objectives

After completing this lab, the learner should be able to:

- estimate gravity-wave speed and a directional CFL-limited teaching step;
- distinguish a target Courant estimate from an exact solver rule;
- account for the volume that changes a dry active cell to a wet state;
- explain how mean, minimum, subgrid, and alignment choices change a represented pathway; and
- state missing evidence and a defensible readiness limit.

## Synthetic instructional givens

### CFL state

Use:

| Quantity | Symbol | Value |
| --- | --- | ---: |
| Grid spacing in x | \(\Delta x\) | 20.0 m |
| Grid spacing in y | \(\Delta y\) | 20.0 m |
| Water depth | \(h\) | 1.00 m |
| x velocity | \(u\) | 1.50 m/s |
| y velocity | \(v\) | 0.50 m/s |
| Gravitational acceleration | \(g\) | 9.81 m/s2 |
| Teaching Courant target | \(C_{target}\) | 0.80 |

Compare candidate time steps of 3.0 s and 4.0 s.

### Wetting transition

Use one 20.0 m by 20.0 m active cell with representative terrain elevation \(z_b=100.25\ \text{m SYN-2}\).
The cell begins dry with zero represented water volume.
Its western neighbor has WSE \(100.30\ \text{m SYN-2}\), and the shared face has an illustrative controlling elevation of \(100.20\ \text{m SYN-2}\).

For one 5.0 s teaching interval, accept the supplied eastward face inflow \(Q_{in}=0.80\ \text{m3/s}\), zero outflow, and zero internal source or sink.
Do not derive the flux from head difference.
Use an illustrative wetting threshold \(h_{on}=0.005\ \text{m}\).

### Resolution pathway

A 30 m wide coarse face contains three 10 m terrain samples:

| Sample | Elevation |
| --- | ---: |
| Left ridge | 100.60 m SYN-2 |
| Notch | 100.05 m SYN-2 |
| Right ridge | 100.60 m SYN-2 |

Use a trial WSE of \(100.30\ \text{m SYN-2}\).
Compare a one-value coarse face formed by the arithmetic mean, a one-value coarse face formed by the minimum, and a hypothetical supported subgrid relationship that retains the three sampled widths.

## Part A: Estimate the time step

**Core inspection:** Calculate:

1. \(c=\sqrt{gh}\).
2. The x-direction signal-speed estimate \(|u|+c\).
3. The y-direction signal-speed estimate \(|v|+c\).
4. \(\Delta t_x=C_{target}\Delta x/(|u|+c)\).
5. \(\Delta t_y=C_{target}\Delta y/(|v|+c)\).
6. The controlling teaching estimate \(\Delta t_{est}=\min(\Delta t_x,\Delta t_y)\).
7. \(C_x\) and \(C_y\) for both candidate time steps.

Identify which candidate satisfies the stated target in both directions.
State why neither result is an exact LISFLOOD-FP or SFINCS time-step rule.

## Part B: Trace one wetting transition

**Core inspection:** Calculate:

1. Cell area in m2.
2. Inflow volume during the 5.0 s interval in m3.
3. Resulting depth under the constant-area teaching assumption.
4. Resulting WSE in `SYN-2`.
5. Whether the cell exceeds the illustrative \(h_{on}\).

Draw or describe the sign of the shared face flux for both neighboring cells.
Explain why the shared exchange conserves water across the pair.
Then repeat only the wet-state decision for \(h_{on}=0.020\ \text{m}\) and explain why the stored-volume handling after that comparison remains solver-specific.

## Part C: Evaluate the terrain pathway

**Core inspection:** Calculate the mean face elevation.
At the trial WSE, state whether the mean and minimum one-value representations appear open or closed.

For each representation, explain the likely pathway error:

- mean elevation;
- minimum elevation; and
- supported subgrid width-elevation relationship.

Then explain how shifting the grid origin by 10 m could change which source samples contribute to a face even when the nominal resolution remains 30 m.
State why a reach centerline crossing the coarse cell does not prove hydraulic connectivity.

## Part D: Apply evidence labels

**Core inspection:** Label each statement as **Scientific foundation**, **Current implementation**, **Target design**, or **Open question**, or classify it as a **Synthetic instructional calculation** or **Unsupported conclusion**:

1. The equation \(c=\sqrt{gh}\) is a shallow-water gravity-wave relation.
2. The 3.0 s candidate satisfies this lab's stated directional target.
3. The reviewed current scenario path invokes LISFLOOD-FP.
4. A SFINCS reference in an interface proves that the current checkout executes SFINCS.
5. A universal wetting threshold of 0.005 m should be used for every project grid.

Support project-specific labels with [JOB-003](../reference/bibliography.md#job-003-current-implementation-locations) or [CONF-003](../reference/conflicts-and-open-questions.md#conf-003-sfincs-documentation-and-current-support).

## Part E: Missing evidence and readiness

**Core inspection:** List at least ten items required before these calculations could support a production hydraulic decision.
Include:

- the actual computational grid and face geometry;
- affine transform and horizontal CRS;
- vertical datum and terrain provenance;
- terrain and roughness sampling rules;
- active-mask and boundary semantics;
- solver version and numerical formulation;
- actual internal time-step rule and diagnostics;
- wetting and drying controls;
- forcing and boundary-condition evidence;
- mass or volume accounting; and
- spatial, temporal, and observational sensitivity evidence.

Classify each item as a missing input, implementation check, numerical-validation result, physical-validation result, or unresolved project decision.

## Deliverable

Submit a short answer with these sections:

1. CFL estimate and candidate comparison.
2. Wetting-transition volume account.
3. Resolution-pathway reasoning.
4. Evidence-label table.
5. Missing evidence and readiness statement.

## Competency criteria

The lab is complete when the answer:

- calculates \(c\approx3.132\ \text{m/s}\) and \(\Delta t_{est}\approx3.45\ \text{s}\);
- shows that 3.0 s satisfies the stated target while 4.0 s exceeds it in x;
- calculates 4.0 m3 of inflow, 0.010 m of depth, and WSE \(100.260\ \text{m SYN-2}\);
- treats the 0.005 m and 0.020 m thresholds as illustrative rather than universal;
- calculates a mean face elevation of approximately \(100.42\ \text{m SYN-2}\) and explains both blocking and excess-opening errors;
- preserves the current LISFLOOD-FP and unresolved SFINCS authority boundary; and
- states that the synthetic result is not ready for a production solver setting, pathway decision, or hydraulic validation claim.

After completing the lab, compare the reasoning with [Lab 6 Solution](solutions/lab-06-grid-stability-and-wetting-solution.md).
