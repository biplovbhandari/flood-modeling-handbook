# Time Stepping and Stability

A numerical model advances an approximate hydraulic state through discrete time steps.
The time step must be compatible with the grid, represented wave and flow speeds, and the selected numerical method.

## Why this topic matters

A run can finish without obvious numerical failure and still be inaccurate or physically unsuitable.
Conversely, visible oscillation or negative depth can indicate a numerical problem before any scientific interpretation is attempted.

## Prerequisites

Read [Shallow-Water Models](01-shallow-water-models.md) and [Grids, Wetting, and Drying](02-grids-wetting-and-drying.md).
Retain the definitions of depth, velocity, cell spacing, wetting front, and face flux.

## Learning objectives

After this chapter, the reader should be able to:

- calculate shallow-water gravity-wave speed;
- use a directional CFL estimate as a teaching calculation;
- explain why wetting fronts, small cells, deep water, and fast flow can constrain time steps;
- explain what an adaptive time step does and does not prove;
- identify common symptoms of numerical instability; and
- distinguish numerical stability, numerical accuracy, and physical correctness.

## Gravity-wave speed

For hydrostatic shallow water with local depth \(h\), the long-wave gravity speed relative to the water is

\[
c=\sqrt{gh}
\]

- \(c\) is gravity-wave speed in m/s.
- \(g\) is gravitational acceleration in m/s2.
- \(h\) is local water depth in m.

**Dimensional check:** \([c]=\sqrt{(\text{m/s2})(\text{m})}=\text{m/s}\).

In one horizontal direction, information can travel with characteristic speeds related to \(u-c\) and \(u+c\).
The magnitude \(|u|+c\) is therefore a useful conservative estimate of the fastest local signal speed in that direction.
This wave-speed interpretation also connects to the Froude number because \(Fr=|u|/c\) for the idealized local depth representation.

## A practical directional CFL estimate

The Courant-Friedrichs-Lewy concept asks whether a numerical time step is small enough relative to the distance that relevant information can travel across the grid.
For a rectilinear teaching grid, use

\[
C_x=\frac{(|u|+c)\Delta t}{\Delta x},\qquad
C_y=\frac{(|v|+c)\Delta t}{\Delta y}
\]

A practical estimate for a target Courant value \(C_{target}\) is

\[
\Delta t_{est}
=C_{target}
\min\left(
\frac{\Delta x}{|u|+c},
\frac{\Delta y}{|v|+c}
\right)
\]

- \(C_x\) and \(C_y\) are dimensionless directional Courant estimates.
- \(u\) and \(v\) are representative depth-averaged velocity components in m/s.
- \(\Delta x\) and \(\Delta y\) are representative grid spacings in m.
- \(\Delta t\) and \(\Delta t_{est}\) are time steps in s.
- \(C_{target}\) is a dimensionless teaching target.

**Dimensional check:** Each distance divided by speed has units s, and each Courant number is dimensionless.

The estimate is not the exact stability condition for every two-dimensional discretization.
Unstructured cells, diagonal propagation, local face lengths, source terms, explicit or implicit treatment, nonlinear iteration, wetting logic, friction, and solver-specific safety factors can change the operational time-step rule.

## Worked teaching estimate

Consider a synthetic square grid with \(\Delta x=\Delta y=20.0\ \text{m}\), \(h=1.00\ \text{m}\), \(u=1.50\ \text{m/s}\), \(v=0.50\ \text{m/s}\), \(g=9.81\ \text{m/s2}\), and \(C_{target}=0.80\).

The gravity-wave speed is

\[
c=\sqrt{(9.81)(1.00)}=3.132\ \text{m/s}
\]

The directional estimates are

\[
\Delta t_x
=0.80\frac{20.0}{1.50+3.132}
=3.45\ \text{s}
\]

\[
\Delta t_y
=0.80\frac{20.0}{0.50+3.132}
=4.41\ \text{s}
\]

The smaller estimate controls, so \(\Delta t_{est}=3.45\ \text{s}\).
A 3.0 s teaching choice falls below that estimate.
A 4.0 s choice gives \(C_x\approx0.93\), which exceeds the stated target of 0.80 even though it is not proof that a particular solver will fail.

The calculation uses representative values.
A real domain requires the controlling local depth, velocity, geometry, and solver rule rather than a domain-average value that can hide a small limiting region.

## What changes the estimate

For the same target and method:

- smaller cells reduce the estimated allowable time step;
- faster velocity reduces the estimated allowable time step;
- deeper water increases \(c=\sqrt{gh}\) and reduces the estimated allowable time step;
- a small face or highly refined local region can control a global step; and
- rapid wetting can require additional care because the active signal-speed field changes as cells become wet.

Halving \(\Delta x\) while holding the represented speeds and target fixed halves the one-direction teaching estimate.
In a two-dimensional domain, refining the grid also increases the number of cells and face calculations.
Runtime can therefore grow for both spatial and temporal reasons.

## Fixed and adaptive time steps

A fixed-step method uses one selected \(\Delta t\) throughout the modeled interval or until the user changes it between runs.
Its review must consider the worst relevant combination of grid spacing, wet depth, speed, and event change.

An adaptive method recomputes an allowable step from the current numerical state and configured limits.
It can shorten the step as velocities rise, water deepens, or a limiting region appears, then lengthen it when conditions permit.
An adaptive step can improve efficiency and protect a numerical criterion, but it does not validate the terrain, roughness, boundary conditions, forcing, or physical approximation.

The reported model-output interval is not necessarily the internal computational time step.
Several internal steps can occur between saved rasters.
A convergence or storage diagnostic evaluated at output times must not be mistaken for the internal stability rule.

## Wetting fronts are a demanding transition

A wetting front moves into cells whose prior depth was zero or below the method's dry-state condition.
The method must add a physically and numerically admissible water volume, avoid negative depth in the donating cell, establish new fluxes, and regularize velocity and friction near zero depth.

A stable-looking wetting front can still advance too quickly, too slowly, or through the wrong pathway.
The time step, wetting threshold, grid resolution, face terrain, roughness, and flux method all affect the represented transition.
This is why a wetting-front review needs both numerical diagnostics and spatial evidence.

## Numerical instability and its symptoms

Numerical instability is growth of computational error or oscillation caused by the discrete method, settings, or poorly represented state rather than by the intended physical solution.
Possible symptoms include:

- negative or non-finite depth, WSE, velocity, or flux;
- alternating high and low WSE between neighboring cells without a physical cause;
- rapidly growing velocities in thin water;
- repeated wet-dry switching at one front;
- sawtooth hydrographs or cell histories tied to the time step;
- failure of a nonlinear iteration or repeated time-step reduction;
- isolated spikes near small faces, steep terrain changes, or boundaries; and
- material changes when only the time step is reduced under otherwise identical assumptions.

Not every abrupt result is numerical instability.
A dam-break wave, hydraulic jump, boundary discontinuity, or sudden terrain control can create a real sharp transition.
The diagnosis must compare the location, timing, conservation behavior, grid, forcing, and sensitivity.

## Stability, accuracy, and physical correctness

| Question | Meaning | Evidence that helps | What it does not prove |
| --- | --- | --- | --- |
| Is the run numerically stable? | The discrete calculation avoids uncontrolled error growth under the tested settings. | Finite states, bounded diagnostics, solver logs, and time-step sensitivity. | That the answer is spatially accurate or physically correct. |
| Is the run numerically accurate enough? | Grid, time, and discrete approximations resolve the quantities needed for the decision within a stated tolerance. | Space-time sensitivity, conservation checks, and comparison with a more resolved or otherwise credible numerical reference. | That inputs, forcing, and represented physics are correct. |
| Is the model physically correct enough for the use? | The equations, terrain, roughness, boundaries, forcing, and structures reproduce relevant observed behavior within accepted uncertainty. | Calibration and independent validation data, uncertainty analysis, and process-specific evidence. | Universal validity outside the tested events, sites, and decision scope. |

A stable run can be smoothly wrong because a coarse grid blocks a channel, a datum shifts WSE, or a boundary imposes the wrong control.
An accurate solution of the selected equations can still be physically inadequate when those equations omit a controlling process.
Physical agreement at one observation can also result from compensating errors.

## A bounded diagnostic sequence

When a time-step or stability problem is suspected, ask these questions in order:

1. Are depth, WSE, velocity, flux, and time-step diagnostics finite and within defensible ranges?
2. Where and when does the first suspicious behavior appear?
3. Does that location coincide with a small cell or face, wetting front, terrain discontinuity, boundary, structure, or extreme roughness transition?
4. Is the internal time step consistent with the documented solver rule and the local state?
5. Does a smaller step materially change the symptom under the same grid and physical inputs?
6. Does the water-volume account remain coherent across the affected control volume?
7. After numerical behavior is acceptable, do grid, boundary, parameter, and observation evidence support physical interpretation?

This sequence separates an initial numerical diagnosis from later convergence and validation decisions.

## Solver-specific limits and this chapter's boundary

Official HEC-RAS guidance publishes equation-set-specific Courant suggestions and emphasizes joint cell-size and time-step testing.
Official SFINCS documentation describes a global adaptive CFL-limited step and identifies cells that constrain it.
The primary LISFLOOD-FP local-inertial paper documents a formulation and numerical approach specific to that model family.

Those sources demonstrate why the generic estimate is not an operational rule for every solver.
This chapter does not transfer a HEC-RAS Courant recommendation to LISFLOOD-FP or SFINCS, and it does not infer the current project executable's exact rule from a paper or another solver's manual.

[LISFLOOD-FP and SFINCS](05-lisflood-fp-and-sfincs.md) addresses solver-specific equations, settings, diagnostics, and comparison evidence.
[Convergence, Mass Balance, and Hot Starts](04-convergence-mass-balance-and-hot-starts.md) addresses convergence, mass-balance interpretation, and initialization evidence in its project context.
This chapter does not decide those questions.

## Common misconceptions

### A Courant number below one guarantees a correct run

A Courant value is one numerical diagnostic under a stated definition.
It does not validate the grid, forcing, boundaries, physical approximation, or solver implementation.

### A completed run was stable

A solver can finish while carrying oscillation, excessive diffusion, repeated limiting, or locally unacceptable errors.
Completion status is not a stability assessment.

### A smaller time step always improves every error

A smaller step can reduce some temporal errors and improve stability while leaving spatial, input, and structural errors unchanged.
Some schemes also have more complex error behavior than monotonic improvement with smaller steps.

### The output interval is the time step

Saved outputs can be much farther apart than internal computational updates.
The two intervals must be checked separately.

### Adaptive stepping proves efficiency and adequacy

Adaptive stepping responds to its configured numerical criterion.
It does not prove that the criterion, minimum and maximum bounds, or physical model are adequate for the intended use.

## Competency check

For a 10 m square grid cell with \(h=0.50\ \text{m}\), \(u=1.0\ \text{m/s}\), \(v=0.2\ \text{m/s}\), and \(C_{target}=0.70\), calculate \(c\), \(\Delta t_x\), \(\Delta t_y\), and the controlling teaching estimate.
Then explain why that value cannot be inserted as an exact LISFLOOD-FP or SFINCS setting without solver-specific evidence.
Finally, give one example each of a stable but inaccurate result and a numerically accurate solution of a physically unsuitable model.

## Practice

Complete [Lab 6: Grid Stability and Wetting](../labs/lab-06-grid-stability-and-wetting.md) to combine a CFL estimate, a wetting transition, and a resolution-pathway critique.

## Source notes

- **Scientific foundation:** Shallow-water gravity-wave and numerical-method context is supported by [SCI-027](../reference/bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics) and the primary research in [SCI-031](../reference/bibliography.md#sci-031-lisflood-fp-local-inertial-formulation).
- **Scientific foundation with solver-specific scope:** HEC-RAS grid and time-step guidance is recorded in [SCI-029](../reference/bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance).
- **Scientific foundation with solver-specific scope:** SFINCS adaptive time-step diagnostics and numerical controls are recorded in [SCI-032](../reference/bibliography.md#sci-032-sfincs-user-manual).
- **Open question:** Current project solver-support boundaries remain recorded in [CONF-003](../reference/conflicts-and-open-questions.md#conf-003-sfincs-documentation-and-current-support).
