# Lab 6: Grid Stability and Wetting

This lab combines a directional CFL estimate, one cell-volume wetting transition, and a terrain-resolution pathway critique.
All numerical inputs and terrain values are synthetic applied-example values.

## Prerequisites

Complete these chapters before starting:

- [Shallow-Water Models](../03-2d-hydraulics/01-shallow-water-models.md)
- [Grids, Wetting, and Drying](../03-2d-hydraulics/02-grids-wetting-and-drying.md)
- [Time Stepping and Stability](../03-2d-hydraulics/03-time-stepping-and-stability.md)

Read [Lab Conventions](README.md), [Source Authority](../reference/source-authority.md), and [Equations and Units](../reference/equations-and-units.md).

## Learning objectives

After completing this lab, the learner should be able to:

- estimate gravity-wave speed and a directional CFL-limited teaching step;
- distinguish a teaching target from an exact solver rule;
- account for the volume that changes a dry active cell to a wet state;
- explain how mean, minimum, subgrid, and alignment choices change a represented pathway; and
- state the evidence needed before selecting an operational grid, time step, or wetting control.

## Synthetic CFL packet

Use:

| Quantity | Symbol | Value |
| --- | --- | ---: |
| Grid spacing in x | \(\Delta x\) | 20.0 m |
| Grid spacing in y | \(\Delta y\) | 20.0 m |
| Water depth | \(h\) | 1.00 m |
| x velocity | (u) | 1.50 m/s |
| y velocity | (v) | 0.50 m/s |
| Gravitational acceleration | \(g\) | 9.81 m/s2 |
| Teaching Courant target | \(C_{target}\) | 0.80 |

Compare candidate time steps of 3.0 s and 4.0 s.
The directional teaching estimate uses \(c=\sqrt{gh}\) and \(C=(|V|+c)\Delta t/\Delta s\).

## Synthetic wetting packet

Use one 20.0 m by 20.0 m active cell with representative terrain elevation \(z_b=100.25 \text{m VD-2}\).
The cell begins dry with zero represented water volume.
Its western neighbour has \(WSE=100.30 \text{m VD-2}\).
The shared face has an illustrative controlling elevation of \(100.20 \text{m VD-2}\).

For one 5.0 s teaching interval, accept the supplied eastward face inflow \(Q_{in}=0.80 \text{m3/s}\), zero outflow, and zero internal source or sink.
Do not derive the flux from head difference.
Use an illustrative wetting threshold \(h_{on}=0.005 \text{m}\).
Repeat only the wet-state comparison for \(h_{on}=0.020 \text{m}\).

## Synthetic resolution packet

A 30 m wide coarse face contains three 10 m terrain samples:

| Sample | Elevation |
| --- | ---: |
| Left ridge | 100.60 m VD-2 |
| Notch | 100.05 m VD-2 |
| Right ridge | 100.60 m VD-2 |

Use a trial \(WSE=100.30 \text{m VD-2}\).
Compare a one-value coarse face formed by the arithmetic mean, a one-value coarse face formed by the minimum, and a supported subgrid relationship that retains the three sampled widths.

## Solver-evidence boundary

No solver executable, configuration, time-step history, convergence record, mass-balance record, or validation result is supplied for this lab.
The CFL and wetting results are hand calculations for the stated packets.
The public solver sources in the source notes describe documented methods and capabilities within their own versions.
They do not make this teaching rule an exact rule for LISFLOOD-FP, SFINCS, or another solver.

## Part A: Estimate the time step

**Core inspection:** Calculate:

1. \(c=\sqrt{gh}\).
2. The x-direction signal-speed estimate \(|u|+c\).
3. The y-direction signal-speed estimate \(|v|+c\).
4. \(\Delta t_x=C_{target}\Delta x/(|u|+c)\).
5. \(\Delta t_y=C_{target}\Delta y/(|v|+c)\).
6. \(\Delta t_{est}=\min(\Delta t_x,\Delta t_y)\).
7. \(C_x\) and \(C_y\) for both candidate time steps.

Identify which candidate satisfies the stated target in both directions.
State why neither result is an exact operational solver rule.

## Part B: Trace one wetting transition

**Core inspection:** Calculate:

1. Cell area in m2.
2. Inflow volume during the 5.0 s interval in m3.
3. Resulting depth under the constant-area assumption.
4. Resulting WSE in `VD-2`.
5. Whether the cell exceeds \(h_{on}=0.005 \text{m}\).

Describe the sign of the shared face flux for both neighbouring cells.
Explain why applying equal magnitude with opposite signs conserves water across the pair.
Repeat the wet-state decision for \(h_{on}=0.020 \text{m}\).
Explain why storage treatment after the threshold comparison remains solver-specific.

## Part C: Evaluate the terrain pathway

**Core inspection:** Calculate the mean face elevation.
At the trial WSE, state whether the mean and minimum one-value representations appear open or closed.

For each representation, explain the likely pathway error:

- arithmetic-mean elevation;
- minimum elevation; and
- supported subgrid width-elevation relationship.

Explain how shifting the grid origin by 10 m could change which source samples contribute to a face while nominal resolution remains 30 m.
State why a reach centerline crossing the coarse cell does not prove hydraulic connectivity.

## Part D: Apply the five evidence labels

Classify each statement as **Scientific foundation**, **Applied example**, **Design principle**, **Evidence note**, or **Open question**.

1. \(c=\sqrt{gh}\) is a shallow-water gravity-wave relation under the stated assumptions.
2. The 3.0 s candidate satisfies this lab's directional target.
3. Grid and time-step sensitivity should be evaluated before a numerical-resolution decision.
4. This prompt supplies no solver run or mass-balance evidence.
5. A universal wetting threshold of 0.005 m should be used by every solver and grid.

For statement 5, explain why the proposed conclusion is unsupported and identify the evidence needed to resolve the question.

## Part E: Distinguish public capability from applied evidence

**Core inspection:** Complete a source-scope table for [SCI-031](../reference/bibliography.md#sci-031-lisflood-fp-local-inertial-formulation), [SCI-032](../reference/bibliography.md#sci-032-sfincs-user-manual), [SCI-033](../reference/bibliography.md#sci-033-lisflood-fp-user-manual), and [SCI-034](../reference/bibliography.md#sci-034-sfincs-forcing-documentation).
For each source, state what documented concept it can support and why it cannot establish the behavior, settings, or acceptance of an unnamed calculation.

## Part F: Missing evidence and readiness

List at least twelve items required before these calculations could support an operational hydraulic decision.
Include:

- actual computational cells and face geometry;
- affine transform and horizontal reference;
- vertical datum and terrain provenance;
- terrain and roughness sampling;
- active-mask and boundary semantics;
- solver identity, version, and numerical formulation;
- actual internal time-step rule and diagnostic history;
- wetting and drying controls;
- forcing and boundary evidence;
- volume accounting;
- grid and time-step sensitivity; and
- observation or benchmark evidence.

Classify each item as a missing input, implementation record, numerical-verification result, physical-validation result, or acceptance criterion.
Use [MX-002](../reference/decision-code-artifact-crosswalk.md#mx-002-convergence) and [CQ-005](../reference/conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence) to keep a teaching estimate separate from convergence and acceptance evidence.

## Deliverable

Submit a short answer with these sections:

1. CFL estimate and candidate comparison.
2. Wetting-transition volume account.
3. Resolution-pathway reasoning.
4. Evidence-label and public-source tables.
5. Missing evidence and readiness statement.

## Competency criteria

The lab is complete when the answer:

- calculates \(c\approx3.132 \text{m/s}\) and \(\Delta t_{est}\approx3.45 \text{s}\);
- shows that 3.0 s satisfies the stated target while 4.0 s exceeds it in x;
- calculates 4.0 m3 of inflow, 0.010 m of depth, and \(WSE=100.260 \text{m VD-2}\);
- treats both wetting thresholds as illustrative;
- calculates a mean face elevation of approximately \(100.42 \text{m VD-2}\);
- explains both blocking and excess-opening errors; and
- states that the supplied calculations cannot select an operational time step, wetting threshold, or terrain pathway.

After completing the lab, compare the reasoning with [Lab 6 Solution](solutions/lab-06-grid-stability-and-wetting-solution.md).

## Source notes

- **Scientific foundation:** Two-dimensional hydraulic and continuity concepts are supported by [SCI-027](../reference/bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics).
- **Scientific foundation:** Grid-size and time-step guidance is supported by [SCI-029](../reference/bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance).
- **Scientific foundation:** Solver-specific formulations and capabilities are documented by [SCI-031](../reference/bibliography.md#sci-031-lisflood-fp-local-inertial-formulation) through [SCI-034](../reference/bibliography.md#sci-034-sfincs-forcing-documentation).
- **Evidence note:** The grid, flux, threshold, and terrain packets are constructed teaching material.
