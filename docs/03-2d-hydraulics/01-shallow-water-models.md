# Shallow-Water Models

A two-dimensional shallow-water model represents how water depth and depth-averaged horizontal motion change across a domain.
It extends the control-volume continuity and momentum ideas from open-channel flow to a connected set of horizontal cells.

## Why this topic matters

Flood-inundation results depend on which physical processes the governing equations retain, which processes they approximate, and how those equations exchange water and momentum across cell faces.
A completed raster is not interpretable until the reader understands those boundaries.

## Prerequisites

Read [Conservation, Discharge, and Storage](../02-open-channel-flow/01-conservation-discharge-and-storage.md), [Energy, Momentum, and Flow Regimes](../02-open-channel-flow/02-energy-momentum-and-flow-regimes.md), and [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md).
Retain the definitions of terrain elevation, WSE, depth, Manning's n, hydraulic depth, and Froude number.

## Learning objectives

After this chapter, the reader should be able to:

- state the hydrostatic and depth-averaging assumptions behind the shallow-water equations;
- interpret the two-dimensional continuity and momentum terms;
- distinguish conservative variables from velocity variables;
- explain how WSE gradient, terrain slope, inertia, and Manning resistance interact;
- explain cell-face flux without assigning one discretization to every solver; and
- identify important vertical, small-scale, and nonhydrostatic processes omitted from the basic equations.

## From a water column to depth-averaged variables

**Scientific foundation:** The shallow-water approximation treats horizontal length scales as large relative to water depth and assumes pressure is approximately hydrostatic.
Hydrostatic pressure at a point is determined by the weight of water above it rather than by substantial vertical acceleration.
The model integrates the three-dimensional flow through the water column and represents horizontal motion with depth-averaged velocities.

Use the following notation in a horizontal Cartesian coordinate system:

| Symbol | Meaning | SI unit |
| --- | --- | --- |
| \(x,y\) | Horizontal coordinates. | m |
| \(t\) | Time. | s |
| \(z_b(x,y)\) | Terrain or bed elevation relative to a stated vertical datum. | m |
| \(\eta(x,y,t)\) | Water-surface elevation relative to the same datum. | m |
| \(h=\eta-z_b\) | Water depth, with \(h\geq0\) in the represented wet state. | m |
| \(u,v\) | Depth-averaged velocities in the \(x\) and \(y\) directions. | m/s |
| \(hu,hv\) | Depth-integrated volume fluxes per unit transverse width. | m2/s |
| \(q\) | Distributed volume source rate per unit horizontal area, positive into the water column. | m/s |
| \(g\) | Gravitational acceleration. | m/s2 |
| \(n\) | Manning roughness coefficient in SI form. | s/m^(1/3) |

The basic state is often written as the conservative-variable vector

\[
\mathbf{U}=
\begin{bmatrix}
h\\
hu\\
hv
\end{bmatrix}
\]

Depth is the conserved water-volume variable per unit horizontal area.
The quantities \(hu\) and \(hv\) are unit-width fluxes and are proportional to depth-integrated momentum when constant density is restored.
Velocities are recovered as \(u=(hu)/h\) and \(v=(hv)/h\) only where the numerical method treats the cell as wet enough for that division to be meaningful.
The one-dimensional correction coefficients \(\alpha\) and \(\beta\) from the open-channel chapters correct section-scale energy and momentum fluxes for unresolved velocity variation.
Do not insert them unchanged into the generic two-dimensional equations unless the selected formulation defines that use.

## Two-dimensional continuity

The local depth form of volume conservation is

\[
\frac{\partial h}{\partial t}
+\frac{\partial(hu)}{\partial x}
+\frac{\partial(hv)}{\partial y}
=q
\]

The first term is local storage change per unit horizontal area.
The second and third terms are the divergence of horizontal volume flux.
Positive flux divergence means more water leaves a small control area than enters it, so depth falls unless a source offsets the loss.
The source \(q\) can represent a modeled distributed addition or removal such as rainfall, infiltration, exchange, or another process when that process is part of the selected model.
It must not be assumed present merely because the equation can contain it.
When \(q\neq0\), a complete momentum-source convention must account for the velocity change caused by adding or removing mass as well as any horizontal momentum carried by that water.
The displayed velocity form below therefore assumes \(q=0\).

**Dimensional check:** \(\partial h/\partial t\), \(\partial(hu)/\partial x\), \(\partial(hv)/\partial y\), and \(q\) all have units m/s.

Integrated over one cell, the same statement becomes a finite-volume balance:

\[
\frac{dV_i}{dt}
=
-\sum_{f\in i} Q_f
+Q_{source,i}
\]

- \(V_i\) is water stored in cell \(i\) in m3.
- \(Q_f\) is signed discharge through face \(f\) in m3/s, positive outward under this equation's convention.
- \(Q_{source,i}\) is the net modeled source rate inside the cell in m3/s.

Each internal face is shared by two cells.
A conservative method uses equal and opposite flux for those neighbors so that their shared exchange does not create or destroy domain-wide water.
Boundary faces and internal sources or sinks remain in the domain-wide balance.

## Two-dimensional momentum

A common conservative form for clear water over fixed terrain is

\[
\frac{\partial(hu)}{\partial t}
+\frac{\partial}{\partial x}
\left(hu^2+\frac{1}{2}gh^2\right)
+\frac{\partial(huv)}{\partial y}
=-gh\frac{\partial z_b}{\partial x}-ghS_{fx}+R_x
\]

\[
\frac{\partial(hv)}{\partial t}
+\frac{\partial(huv)}{\partial x}
+\frac{\partial}{\partial y}
\left(hv^2+\frac{1}{2}gh^2\right)
=-gh\frac{\partial z_b}{\partial y}-ghS_{fy}+R_y
\]

- \(\partial(hu)/\partial t\) and \(\partial(hv)/\partial t\) are local changes in depth-integrated horizontal motion, in m2/s2.
- The \(hu^2\), \(hv^2\), and \(huv\) terms transport horizontal momentum with the flow.
- The \(gh^2/2\) terms represent the depth-integrated hydrostatic pressure contribution.
- The terrain-gradient terms represent gravity acting along a sloping bed.
- \(S_{fx}\) and \(S_{fy}\) are dimensionless friction-slope components that oppose motion.
- \(R_x\) and \(R_y\) collect any represented additional momentum sources or stresses, in m2/s2.

**Dimensional check:** Every displayed momentum term has units m2/s2 after the spatial or temporal derivative is applied.

The split between pressure flux and terrain slope depends on the mathematical form.
For the displayed velocity form, assume \(q=0\) and \(h>0\), so dividing the conservative equations by depth does not require mass-source coupling terms.
Under that assumption, the hydrostatic-pressure and terrain contributions combine into the WSE gradient:

\[
\frac{\partial u}{\partial t}
+u\frac{\partial u}{\partial x}
+v\frac{\partial u}{\partial y}
=-g\frac{\partial\eta}{\partial x}-gS_{fx}+r_x
\]

\[
\frac{\partial v}{\partial t}
+u\frac{\partial v}{\partial x}
+v\frac{\partial v}{\partial y}
=-g\frac{\partial\eta}{\partial y}-gS_{fy}+r_y
\]

The WSE gradient is the immediate hydrostatic driving gradient in this form.
The terms \(r_x\) and \(r_y\) are any additionally represented horizontal accelerations in m/s2 under the stated zero-\(q\) assumption.
The displayed equations must not be read as equivalent to the conservative equations for an arbitrary nonzero \(q\).
Terrain still matters because \(\eta=z_b+h\), because terrain controls available depth and connectivity, and because the conservative form must balance pressure and bed-slope terms correctly.
Do not add a separate terrain-slope acceleration to the displayed WSE-gradient form unless the chosen derivation requires it, because doing so can count the same gravity effect twice.

## Manning resistance in two horizontal directions

One common depth-based Manning closure for broad shallow flow is

\[
U=\sqrt{u^2+v^2},\qquad
S_{fx}=\frac{n^2uU}{h^{4/3}},\qquad
S_{fy}=\frac{n^2vU}{h^{4/3}}
\]

- \(U\) is depth-averaged speed in m/s.
- \(S_{fx}\) and \(S_{fy}\) have signs that follow the velocity components and are dimensionless.
- The momentum equations apply \(-gS_{fx}\) and \(-gS_{fy}\), so resistance opposes motion.

**Dimensional check:** \(n^2uU/h^{4/3}\) is dimensionless in SI units.

This closure is useful for interpreting roughness sensitivity.
It is not a universal statement of how every solver averages depth, hydraulic radius, or spatially varying roughness at a cell face.
The expression also becomes singular as \(h\rightarrow0\), which is one reason numerical methods require explicit wetting-drying treatment rather than unguarded division by very small depth.

## What each term does

| Term or relationship | Physical role | Diagnostic question |
| --- | --- | --- |
| Storage \(\partial h/\partial t\) | Fills or drains a location. | Does the depth change match net face flux and sources? |
| Flux divergence | Moves water among neighboring control volumes. | Are face signs, active cells, and boundary fluxes consistent? |
| Local momentum change | Represents acceleration at a fixed location. | Is a rapidly changing forcing or wetting front present? |
| Momentum transport | Carries horizontal motion through the domain. | Could strong directional flow, a bend, or an abrupt transition make advection important? |
| WSE or pressure gradient | Accelerates water from higher hydraulic head toward lower head under the hydrostatic approximation. | Is the gradient physical, or could terrain or datum misalignment create it? |
| Terrain | Controls depth, storage, barriers, openings, and the balance of pressure against gravity. | Does the grid represent the controlling high and low features? |
| Manning friction | Dissipates horizontal motion through a resistance closure. | Is roughness defensible at the modeled scale and depth? |
| Additional stress or source | Represents only processes included by the selected formulation. | Which forces or exchanges are actually enabled and supported? |

## Cell-face flux is the connection between physics and the grid

A control-volume method stores state for a cell and computes an exchange across each face.
The face flux depends on the states on both sides, face geometry, wetting state, terrain or subgrid properties, boundary conditions, and the selected numerical method.
Some methods reconstruct values at a face, some use staggered variables, and some retain subgrid elevation-volume or conveyance relationships.

The common concept is conservation through shared interfaces.
The handbook does not claim that LISFLOOD-FP, SFINCS, HEC-RAS, or another solver uses one common flux formula, variable placement, friction treatment, or time integrator.

## Assumptions and omitted processes

The basic equations rely on these assumptions:

- water is treated as incompressible with approximately constant density;
- pressure is hydrostatic through the represented water column;
- vertical acceleration is small enough for the hydrostatic approximation;
- horizontal velocity is represented by a depth average;
- terrain is fixed during the calculation unless a separate process changes it; and
- represented source, resistance, turbulence, and boundary terms are explicitly identified.

The basic depth-averaged equations do not resolve vertical velocity structure, vertical shear profiles, nonhydrostatic pressure, overturning, breaking surface waves, air entrainment, or three-dimensional circulation.
They also do not automatically represent culverts, bridges, buildings, sewer systems, sediment transport, erosion, infiltration, rainfall, wind, Coriolis effects, or turbulence unless the selected model adds an appropriate term, parameterization, or structure contract.
Sub-cell channels and barriers are omitted when neither the grid nor a supported subgrid representation retains them.

The shallow-water approximation can remain useful without resolving every omitted process.
Its adequacy depends on the site, forcing, intended decision, grid, parameterization, solver, and validation evidence.

## Solver interpretation boundary

**Evidence note:** The generic equations in this chapter are not a claim that any named solver uses every displayed term, variable placement, closure, or numerical method exactly as written.
The [LISFLOOD-FP and SFINCS](05-lisflood-fp-and-sfincs.md) comparison bounds solver statements to the LISFLOOD-FP 5.9.6 manual, the SFINCS 2.0.6 documentation, and the cited LISFLOOD-FP research paper.

**Design principle:** Separate documented external capability from implementation support.
A solver manual can establish behavior for its documented release, but it cannot establish that another workflow has implemented, configured, or validated that capability.

This chapter supplies only the common physical vocabulary needed for that comparison.

## Common misconceptions

### Two-dimensional means the model resolves the vertical water column

The represented velocities are depth averages in two horizontal directions.
Vertical structure remains unresolved by the basic shallow-water equations.

### Terrain slope alone drives the water

The hydrostatic velocity form responds to WSE gradient.
A sloping bed under a parallel sloping water surface can support flow, while a sloping bed under a level water surface does not create the same downstream WSE gradient.

### A conservative equation guarantees a conservative implementation

The continuous equations express conservation.
Discrete conservation also depends on matched face fluxes, boundary treatment, source integration, wetting-drying logic, and numerical tolerances.

### Manning's n is a force added in the flow direction

The friction term opposes the velocity vector.
Its magnitude and averaging require solver-specific treatment, especially in thin water.

### Omitting a process means it is unimportant everywhere

An omitted process can be negligible for one application and controlling for another.
Adequacy requires evidence at the intended scale and decision.

## Competency check

For one cell, explain how positive inflow across the west face, smaller outflow across the east face, and no other exchange change the stored depth.
Then identify the equation terms that can accelerate the depth-averaged flow eastward when WSE falls to the east.
State why terrain slope must not be added a second time to a velocity equation already written with WSE gradient.
Finally, name four physical processes that the basic depth-averaged equations do not resolve.

## Source notes

- **Scientific foundation:** The assumptions, depth-averaged variables, conservation laws, WSE relation, and finite-volume context are supported by [SCI-027](../reference/bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics).
- **Scientific foundation:** Local-inertial flood-model context and an efficient two-dimensional formulation are supported by the primary research in [SCI-031](../reference/bibliography.md#sci-031-lisflood-fp-local-inertial-formulation).
- **Evidence note:** Solver-specific capability claims require the exact release, equation set, configuration, and documentation source.
