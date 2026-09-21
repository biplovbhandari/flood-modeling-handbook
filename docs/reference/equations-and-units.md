# Equations and Units

This reference gives the minimum equation, variable, unit, assumption, and interpretation context needed by later chapters.
Symbols are local to each equation unless a section states otherwise.
SI units are the default.

## Source mapping

The equations and assumptions below synthesize the source families listed in the [Bibliography and Source Map](bibliography.md).
Applied-example equations state their constructed values and assumptions locally.

| Equation family | Primary source records |
| --- | --- |
| Discharge, continuity, energy, momentum, hydraulic depth, and Froude number | [SCI-016](bibliography.md#sci-016-hec-ras-continuity-equation) through [SCI-021](bibliography.md#sci-021-usace-subdivision-froude-number) |
| Flow regimes, Manning flow, normal depth, loss, and downstream boundaries | [SCI-022](bibliography.md#sci-022-hec-ras-flow-regime-boundary-guidance) through [SCI-026](bibliography.md#sci-026-hec-ras-2d-external-boundary-conditions) |
| Two-dimensional shallow-water behavior, grids, CFL reasoning, and solver limitations | [SCI-027](bibliography.md#sci-027-hec-ras-2d-unsteady-flow-hydrodynamics) through [SCI-029](bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance), plus [SCI-031](bibliography.md#sci-031-lisflood-fp-local-inertial-formulation) through [SCI-034](bibliography.md#sci-034-sfincs-forcing-documentation) |
| Raster transforms, alignment, reprojection, and resampling | [SCI-030](bibliography.md#sci-030-gdal-geotransform) and [SCI-042](bibliography.md#sci-042-rasterio-reprojection-and-resampling) |
| Verification, validation, uncertainty, and evidence limits | [SCI-043](bibliography.md#sci-043-nasa-standard-for-models-and-simulations) through [SCI-045](bibliography.md#sci-045-epa-environmental-model-guidance) |
| Applied sampling, domain, convergence, and compositing equations | Public sources listed above, the self-contained assumptions in this page, and the [Method-Evidence-Artifact Crosswalk](decision-code-artifact-crosswalk.md) |

## Unit conventions

| Quantity | Symbol | SI unit |
| --- | --- | --- |
| Length, depth, elevation | \(L, h, z\) | m |
| Area | \(A\) | m2 |
| Volume or storage | \(V, S\) | m3 |
| Time | \(t\) | s |
| Velocity | \(u, v\) | m/s |
| Unit-width volume flux | \(hu, hv\) | m2/s |
| Discharge | \(Q\) | m3/s |
| Acceleration | \(g\) | m/s2 |
| Density | \(\rho\) | kg/m3 |
| Force | \(F\) | N, equal to kg m/s2 |
| Slope | \(S_f\) | m/m, dimensionless |
| Manning roughness | \(n\) | \(\mathrm{s}/\mathrm{m}^{1/3}\) in SI form |

Discharge in this handbook is written in m3/s.
An elevation or stage value must retain its vertical datum and reference before values are combined.
A depth value must retain its units, sign convention, location, and surface definition.
Deriving depth from terrain and WSE requires those two elevations to use compatible vertical datums.

## Discharge through a cross-section

\[
Q=\int_A u_n\,dA, \qquad
\bar{V}=\frac{1}{A}\int_A u_n\,dA, \qquad
Q=A\bar{V}
\]

- \(Q\) is signed discharge through the section in m3/s.
- \(A\) is the wetted cross-sectional area in m2.
- \(u_n\) is local velocity normal to the section in m/s.
- \(dA\) is an element of wetted area in m2.
- \(\bar{V}\) is area-averaged velocity normal to the section in m/s.
- The chosen positive unit normal defines positive discharge.

**Dimensional check:** \([Q]=(\text{m/s})(\text{m2})=\text{m3/s}\).

**Assumptions:** The area and velocities refer to the same cross-section and time, and the integral includes the complete velocity distribution represented by that section.
The relation \(Q=A\bar{V}\) is exact for the defined area average but becomes an approximation when \(\bar{V}\) is estimated from a point, subsection, different location, or assumed uniform distribution.

**Interpretation:** This equation explains the discharge imposed at an inflow boundary and the relationship among channel geometry, velocity, and flow.
It is a scientific foundation rather than a claim that every boundary discharge is calculated from area and velocity.

## Storage continuity

\[
\frac{dS}{dt} = \sum Q_{in} - \sum Q_{out} + Q_{source} - Q_{sink}
\]

- \(S\) is water stored in the control volume in m3.
- \(t\) is time in s.
- \(Q_{in}\) and \(Q_{out}\) are boundary inflows and outflows in m3/s.
- \(Q_{source}\) and \(Q_{sink}\) are modeled internal or distributed source and sink rates in m3/s.

The interval form is

\[
S(t_1)-S(t_0)=
\int_{t_0}^{t_1}
\left(
\sum Q_{in}-\sum Q_{out}+Q_{source}-Q_{sink}
\right)dt
\]

- \(t_0\) and \(t_1\) are the interval start and end in s on a common time axis.
- \(S(t_1)-S(t_0)\) is storage change in m3.

**Sign convention:** Inflows and sources add storage, outflows and sinks subtract storage, and positive storage change means filling.

**Dimensional check:** The differential equation has m3/s on both sides, and time integration converts every discharge rate to m3.

**Assumptions:** The control volume and sign convention are defined, and all material fluxes and source or sink terms are included consistently.
For constant-density incompressible water, the volume balance is equivalent to a mass balance after every term is multiplied by density.

**Interpretation:** Continuity is the scientific basis for diagnosing filling, draining, and mass balance.
It also shows why a storage-change measure alone cannot prove that inflow and outflow balance.

## Open-channel energy head

\[
H=z_b+y+\alpha\frac{\bar{V}^2}{2g}
\]

- \(H\) is total energy head in m.
- \(z_b\) is bed or invert elevation in m relative to a stated vertical datum.
- \(y\) is geometric water depth above the bed or invert in m.
- \(\bar{V}\) is area-averaged section velocity in m/s.
- \(g\) is gravitational acceleration in m/s2.
- \(\alpha\) is the dimensionless kinetic-energy correction coefficient.
- \(z_b+y\) is WSE when both terms use the same location, units, and vertical datum.

For steady flow from upstream section 1 to downstream section 2,

\[
z_1+y_1+\alpha_1\frac{\bar{V}_1^2}{2g}
=
z_2+y_2+\alpha_2\frac{\bar{V}_2^2}{2g}+h_L
\]

- \(h_L\) is nonnegative energy-head loss from section 1 to section 2 in m under the stated downstream direction.
- Subscripts 1 and 2 identify compatible upstream and downstream section values.

The correction coefficient is

\[
\alpha=\frac{\int_A u_n^3\,dA}{A\bar{V}^3}
\]

- \(u_n\), \(A\), and \(\bar{V}\) have the definitions and SI units used in the discharge equation.
- \(\alpha=1\) for a perfectly uniform unidirectional velocity distribution.

**Dimensional check:** \(\bar{V}^2/g\) has units m, so every energy-head term has units m and \(\alpha\) is dimensionless.

**Assumptions:** The section representation is one-dimensional, pressure is approximately hydrostatic, section values are compatible, and losses and external energy inputs are represented consistently.
Rapidly varied, unsteady, or strongly multidirectional flow can require a fuller momentum or numerical treatment.

**Interpretation:** Energy-head reasoning helps explain water-surface response, velocity-head effects, and loss between sections.
It does not prove that a boundary value, loss representation, or scenario result is correct.

## Momentum flux and force balance

For steady, one-dimensional, incompressible flow through a fixed control volume with one inlet and outlet and equal discharge,

\[
\sum F_x
=
\rho Q
\left(
\beta_2\bar{V}_2-
\beta_1\bar{V}_1
\right)
\]

- \(\sum F_x\) is net external force on the water in the positive streamwise direction in N.
- \(\rho\) is water density in kg/m3.
- \(Q\) is discharge in m3/s.
- \(\bar{V}_1\) and \(\bar{V}_2\) are signed area-averaged streamwise velocities in m/s.
- \(\beta_1\) and \(\beta_2\) are dimensionless momentum correction coefficients.
- Pressure, gravity, boundary drag, and structure reactions are included in \(\sum F_x\) when present.

The correction coefficient is

\[
\beta=\frac{\int_A u_n^2\,dA}{A\bar{V}^2}
\]

- \(u_n\), \(A\), and \(\bar{V}\) have the definitions and SI units used in the discharge equation.
- \(\beta=1\) for a perfectly uniform unidirectional velocity distribution.

**Dimensional check:** \([\rho Q\bar{V}]=(\text{kg/m3})(\text{m3/s})(\text{m/s})=\text{N}\).

**Assumptions:** The displayed balance is steady, one-dimensional, unidirectional, and limited to one inlet and outlet with no net source or sink.
Unsteady momentum accumulation, multiple boundaries, and lateral momentum inputs require additional terms.

**Interpretation:** Momentum reasoning helps evaluate directional forces, abrupt transitions, and momentum exchange.
It neither replaces continuity nor makes the energy balance universal.

## Water-surface-gradient and friction balance

With \(x\) positive downstream, \(\eta=z_b+y\) as WSE, and friction opposing positive flow, a simplified one-dimensional momentum form is

\[
\frac{\partial \bar{V}}{\partial t}
+\bar{V}\frac{\partial \bar{V}}{\partial x}
=
-g\frac{\partial \eta}{\partial x}
-gS_f
\]

- \(x\) is streamwise distance in m, increasing downstream.
- \(\eta\) is WSE in m relative to a stated vertical datum.
- \(z_b\) is bed elevation in m relative to the same vertical datum.
- \(y\) is geometric water depth in m, so \(\eta=z_b+y\) under the stated hydrostatic section representation.
- \(\partial \bar{V}/\partial t\) is local acceleration in m/s2.
- \(\bar{V}\,\partial \bar{V}/\partial x\) is convective acceleration in m/s2.
- \(\partial\eta/\partial x\) is the dimensionless WSE gradient.
- \(S_f\) is a nonnegative dimensionless friction slope for positive downstream flow.
- \(g\) is gravitational acceleration in m/s2.

Under steady, uniform conditions,

\[
-\frac{d\eta}{dx}=S_f
\]

**Sign convention:** A WSE that falls downstream has \(\partial\eta/\partial x<0\), so the term \(-g\,\partial\eta/\partial x\) acts downstream while \(-gS_f\) acts upstream.

**Dimensional check:** Both gradient and friction slope are dimensionless, so multiplication by \(g\) gives m/s2.

**Assumptions:** The simplified form assumes gradually varied, hydrostatic, one-dimensional flow and omits wind stress, added forces, turbulent diffusion, and lateral momentum exchange.

**Interpretation:** The balance explains why water responds to the WSE gradient and why friction must be represented.
It is a conceptual foundation rather than a statement of the complete equation or discretization in a particular solver.

## Froude number

\[
Fr = \frac{|\bar{V}|}{\sqrt{gD_h}}, \qquad D_h = \frac{A}{T_w}
\]

- \(Fr\) is dimensionless.
- \(\bar{V}\) is section-averaged velocity in m/s.
- \(g\) is gravitational acceleration in m/s2.
- \(D_h\) is hydraulic depth in m.
- \(A\) is wetted cross-sectional area in m2.
- \(T_w\) is water-surface top width in m.

**Dimensional check:** \(D_h=(\text{m2})/(\text{m})=\text{m}\), and \([Fr]=(\text{m/s})/\sqrt{(\text{m/s2})(\text{m})}=1\).

**Assumptions:** The section can be represented by a characteristic velocity and hydraulic depth, and hydrostatic shallow-flow reasoning is appropriate.
Geometric depth equals hydraulic depth only for a rectangular section.
Compound or strongly nonuniform sections can require subsection-sensitive or more complete analysis.

**Interpretation:** The Froude number helps interpret subcritical downstream influence, critical conditions, supercritical behavior, and the sensitivity of results to downstream boundaries.
It does not prove that every part of a cross-section or two-dimensional domain has one flow regime.

## Manning's equation

\[
R_h=\frac{A}{P}, \qquad
K=\frac{1}{n}AR_h^{2/3}, \qquad
Q=KS_f^{1/2}
\]

- \(Q\) is discharge in m3/s.
- \(n\) is Manning roughness in \(\mathrm{s}/\mathrm{m}^{1/3}\) in SI form.
- \(A\) is hydraulic or wetted cross-sectional area in m2.
- \(P\) is wetted perimeter in m and excludes the free water surface.
- \(R_h\) is hydraulic radius in m.
- \(K\) is conveyance in m3/s under this SI definition.
- \(S_f\) is the friction or energy slope in m/m.

For a rectangular channel with bottom width \(b\) and normal depth \(y_n\),

\[
A=by_n, \qquad P=b+2y_n, \qquad
Q=\frac{1}{n}(by_n)
\left(\frac{by_n}{b+2y_n}\right)^{2/3}S_f^{1/2}
\]

**Dimensional check:** \([1/n]=\text{m}^{1/3}/\text{s}\), so \([K]=(\text{m}^{1/3}/\text{s})(\text{m2})(\text{m}^{2/3})=\text{m3/s}\).
The slope factor is dimensionless, leaving \(Q\) in m3/s.

**SI coefficient:** The coefficient is 1 in the stated SI form.
Do not insert the 1.486 coefficient used in a common U.S. customary form into an SI calculation.

**Assumptions:** Flow is steady and approximately uniform, roughness represents the section, geometry is represented at the evaluated depth, and the energy slope is represented by the chosen slope.
These assumptions limit its use in backwater, rapidly varied, or strongly two-dimensional conditions.

**Interpretation:** Manning's equation explains normal-depth boundaries, roughness sensitivity, and why geometry, slope, and Manning's n jointly control conveyance.

## Manning roughness sensitivity at fixed geometry

For two Manning calculations with the same wetted area, hydraulic radius, and friction slope,

\[
\frac{Q_2}{Q_1}=\frac{n_1}{n_2}
\]

- \(Q_1\) and \(Q_2\) are discharges in m3/s.
- \(n_1\) and \(n_2\) are Manning roughness values in \(\mathrm{s}/\mathrm{m}^{1/3}\).

**Assumptions:** Wetted geometry and friction slope remain fixed, and both calculations satisfy the steady, approximately uniform assumptions of Manning's equation.
This relation does not describe a fixed-discharge simulation in which depth, WSE, wet extent, and flow routing adjust.

**Interpretation:** The ratio supports transparent roughness sensitivity calculations while preventing a fixed-geometry result from being presented as a complete two-dimensional model response.

## Gradually varied flow concept

Gradually varied flow is steady open-channel flow whose depth changes over a distance long enough for a one-dimensional, approximately hydrostatic treatment to remain useful.
The simplified profile reasoning used by this handbook also assumes gradual variation, prismatic geometry, and constant discharge or no material lateral inflow over the reach being interpreted.
It does not apply unchanged at a confluence, a material lateral inflow, an abrupt geometry transition, a hydraulic jump, a structure with rapidly varied flow, or a strongly two-dimensional flow pattern.

**Interpretation:** The concept organizes backwater intuition and explains why downstream stage can influence an upstream subcritical profile.
It is not an equation or discretization for a particular two-dimensional solver.

## Depth-averaged two-dimensional continuity

With terrain elevation \(z_b\), WSE \(\eta\), and depth \(h=\eta-z_b\), the local volume-conservation equation is

\[
\frac{\partial h}{\partial t}
+\frac{\partial(hu)}{\partial x}
+\frac{\partial(hv)}{\partial y}
=q
\]

- \(h\) is water depth in m.
- \(u\) and \(v\) are depth-averaged horizontal velocity components in m/s.
- \(hu\) and \(hv\) are unit-width volume-flux components in m2/s.
- \(x\) and \(y\) are horizontal coordinates in m.
- \(t\) is time in s.
- \(q\) is a distributed volume source rate per unit horizontal area in m/s and is positive into the water column.

**Dimensional check:** Every term has units m/s.

**Assumptions:** Water is incompressible with approximately constant density, and all represented sources and sinks use the stated sign and area basis.
The equation expresses continuous conservation but does not by itself prove that a discrete solver, boundary treatment, or artifact closes a water-volume balance.

**Interpretation:** This equation supplies the common conservation vocabulary for interpreting cell storage, face flux, wetting, and solver behavior.
It is not a claim that every solver uses the same discrete variables or source terms.

## Depth-averaged two-dimensional momentum

A common conservative clear-water form over fixed terrain is

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

- \(z_b\) is terrain or bed elevation in m relative to the WSE vertical datum.
- \(g\) is gravitational acceleration in m/s2.
- \(S_{fx}\) and \(S_{fy}\) are signed dimensionless friction-slope components.
- \(R_x\) and \(R_y\) are any additionally represented depth-integrated momentum source or stress terms in m2/s2.
- All other symbols have the continuity-equation meanings and units.

For the velocity-form summary used by this handbook, assume \(q=0\) and \(h>0\).
Under that assumption, the hydrostatic pressure and terrain contributions can be combined as the WSE-gradient accelerations \(-g\,\partial\eta/\partial x\) and \(-g\,\partial\eta/\partial y\).
For an arbitrary nonzero \(q\), the transformed velocity equations also require mass-source coupling and any momentum carried by the added or removed water under a complete source convention.
Do not add terrain slope a second time to that WSE-gradient form without a derivation that requires it.

A common broad, shallow-flow Manning closure is

\[
U=\sqrt{u^2+v^2},\qquad
S_{fx}=\frac{n^2uU}{h^{4/3}},\qquad
S_{fy}=\frac{n^2vU}{h^{4/3}}
\]

- \(U\) is depth-averaged speed in m/s.
- \(n\) is Manning roughness in \(\mathrm{s}/\mathrm{m}^{1/3}\).

**Dimensional check:** Every conservative momentum term has units m2/s2, and each Manning slope component is dimensionless.

**Assumptions:** The equations are depth-averaged, hydrostatic, and appropriate where vertical acceleration and vertical structure are not controlling.
The basic form omits unresolved vertical shear, nonhydrostatic pressure, breaking surface waves, air entrainment, movable-bed effects, structures, and any external process not represented in \(R_x\), \(R_y\), or separate model contracts.

**Interpretation:** The equations explain conservative variables, inertia, hydrostatic pressure, terrain, WSE gradients, and resistance.
They do not assign one momentum discretization, roughness average, or near-dry regularization to LISFLOOD-FP, SFINCS, or another solver.

## Cell face-flux volume update

For a cell with constant horizontal area over one teaching step,

\[
\Delta h_i
=
\frac{\Delta t}{A_i}
\left(
\sum Q_{in}-\sum Q_{out}+Q_{source,i}
\right)
\]

- \(\Delta h_i\) is cell-depth change in m.
- \(\Delta t\) is the teaching interval in s.
- \(A_i\) is cell horizontal area in m2.
- \(Q_{in}\), \(Q_{out}\), and \(Q_{source,i}\) are rates in m3/s.

**Dimensional check:** \((\text{s}/\text{m2})(\text{m3/s})=\text{m}\).

**Assumptions:** The cell uses a constant horizontal area for the step and every face and source term uses a consistent sign convention.
A solver with an elevation-volume curve converts stored volume through that relationship instead of assuming one constant plan area.

**Interpretation:** The relation supports transparent wetting-transition bookkeeping in Lab 6.
It does not reproduce a solver's face-flux or wetting algorithm.

## Water depth from elevations

\[
h = WSE - z_b
\]

- \(h\) is water depth in m.
- \(WSE\) is water-surface elevation in m relative to a stated vertical datum.
- \(z_b\) is terrain or bed elevation in m relative to the same vertical datum.

**Assumptions:** Both elevations use compatible vertical datums, units, horizontal locations, and representations of the bed or terrain.
Negative values normally indicate dry terrain, a mismatch, or an error that requires interpretation.

**Interpretation:** The equation connects terrain rasters and modeled WSE to depth artifacts and stage-transfer calculations.

For comparable results at one location, the change relation is

\[
\Delta h=\Delta WSE-\Delta z_b
\]

**Assumptions:** The two results use compatible horizontal locations, vertical datums, units, and definitions of terrain and WSE.
If WSE is held fixed, a positive terrain error produces an equal negative depth error.
When terrain changes the hydraulic solution, both terms can change and the total depth response cannot be inferred from terrain error alone.

**Interpretation:** The relation separates the direct depth-calculation effect of terrain error from its indirect effect on solved WSE through storage, conveyance, barriers, and pathways.

## Annual exceedance probability and recurrence interval

\[
T = \frac{1}{p}, \qquad p = \frac{1}{T}
\]

- \(p\) is annual exceedance probability as a dimensionless fraction.
- \(T\) is recurrence interval in years.

When AEP is reported as a percentage, convert it to a fraction before using the reciprocal.
For example, \(p=1\%=0.01\) corresponds to \(T=100\) years.

**Assumptions:** The one-year conversion assumes a probability model whose annual exceedance probability applies to the stated year.
The conversion does not state that an event occurs on a fixed schedule.

For independent annual periods with a stationary annual exceedance probability, the probability of at least one exceedance in \(n\) years is:

\[
P(N \geq 1)=1-(1-p)^n
\]

- \(N\) is the number of exceedances in the \(n\)-year period.
- \(n\) is the number of annual periods in years.

**Assumptions:** The same probability \(p\) applies to every annual period and annual exceedance occurrences are independent for this calculation.
These assumptions can be unsuitable when the flood-generating process is nonstationary or materially dependent across years.

**Interpretation:** Recurrence-interval-based discharge estimates depend on the probability model, source version, record period, annual-maximum extraction, fitting method, sampling uncertainty, and nonstationarity treatment.
See [Flood Frequency, AEP, and Discharge Bounds](../01-hydrology-for-fim/04-flood-frequency-aep-and-bounds.md).

## Applied discharge-bound calculation

**Applied example:** For a separate synthetic frequency calculation, assume a 10 percent AEP discharge of \(Q_{10}=312.5\ \text{m3/s}\), a 1 percent AEP discharge of \(Q_{100}=800\ \text{m3/s}\), a lower factor of 0.80, and an upper factor of 1.25.

\[
Q_{min}=0.80Q_{10}=250\ \text{m3/s}
\]

\[
Q_{max}=1.25Q_{100}=1000\ \text{m3/s}
\]

- \(Q_{min}\) is the instructional lower discharge bound in m3/s.
- \(Q_{max}\) is the instructional upper discharge bound in m3/s.
- \(Q_{10}\) and \(Q_{100}\) are synthetic frequency estimates in m3/s.
- The factors 0.80 and 1.25 are constructed teaching assumptions rather than universal hydrologic constants.

**Dimensional check:** Each dimensionless factor multiplies a discharge in m3/s, so each bound has units m3/s.

**Assumptions:** The source estimates refer to one synthetic reach, use one documented probability model and record period, and share a declared stationarity assumption.
A real workflow also needs uncertainty estimates, source lineage, network correspondence, and a rounding policy when the scenario interface requires discrete values.

**Evidence note:** The arithmetic is exact for the stated values, but it does not establish suitable probability levels, factors, or bounds for another reach or intended use.
This separate calculation does not define the `R-200` candidate grid used by the interval-refinement example below.
See [Flood Frequency, AEP, and Discharge Bounds](../01-hydrology-for-fim/04-flood-frequency-aep-and-bounds.md).

## Applied interval-refinement metrics

For a final depth raster, define the wet-cell set as:

\[
W=\{i:h_i>0\}
\]

The applied example uses maximum depth and flooded area:

\[
h_{max}=\max_{i\in W}(h_i)
\]

\[
A_f=\frac{N_ws^2}{10^6}
\]

- \(h_i\) is saved water depth in cell \(i\), in m.
- \(h_{max}\) is maximum depth over wet cells, in m.
- \(N_w\) is the number of wet cells.
- \(s\) is square-cell size in m.
- \(A_f\) is flooded area in km2.

**Dimensional check:** \(N_w\) is dimensionless and \(s^2\) has units m2, so division by \(10^6\ \text{m2/km2}\) gives km2.

**Assumptions:** Every compared scenario uses the same grid, wet threshold, nodata rule, final-state definition, quantity definitions, and compatible identity.
An empty wet set requires an explicit policy because \(h_{max}\) is otherwise undefined.

The complete synthetic response packet is:

| Discharge | Maximum depth | Flooded area | Measurement order | Warm-start source |
| ---: | ---: | ---: | ---: | --- |
| 100 m3/s | 1.00 m | 0.500 km2 | 1 | Dry start |
| 250 m3/s | 2.30 m | 0.750 km2 | 2 | 100 m3/s |
| 175 m3/s | 1.60 m | 0.595 km2 | 3 | 100 m3/s |
| 125 m3/s | 1.20 m | 0.530 km2 | 4 | 100 m3/s |
| 200 m3/s | 1.78 m | 0.635 km2 | 5 | 175 m3/s |
| 225 m3/s | 2.01 m | 0.680 km2 | 6 | 200 m3/s |

## Applied interval-refinement selection

For adjacent accepted scenarios at \(Q_a<Q_b\), the synthetic selector defines a normalized response distance:

\[
D(a,b)=\max\left(
\frac{|h_{max,b}-h_{max,a}|}{0.50\ \text{m}},
\frac{|A_{f,b}-A_{f,a}|}{0.10\ \text{km}^2}
\right)
\]

- \(D(a,b)\) is dimensionless.
- \(h_{max,a}\) and \(h_{max,b}\) are maximum depths in m.
- \(A_{f,a}\) and \(A_{f,b}\) are flooded areas in km2.
- The scales 0.50 m and 0.10 km2 are constructed method values for this example.

**Dimensional check:** Each numerator and denominator share units, so both ratios and their maximum are dimensionless.

An interval meets the response criterion when \(D(a,b)\le1\).
An interval requires refinement when \(D(a,b)>1\) and at least one untried candidate lies strictly inside it.

**Applied example:** The `R-200` candidate grid is \(\{100,125,150,175,200,225,250\}\ \text{m3/s}\).
The method selects the widest interval requiring refinement, breaks interval ties by lower endpoint, selects the untried candidate nearest the arithmetic midpoint, and breaks candidate ties by lower discharge.
It stops when every adjacent measured interval meets the criterion or has no untried interior candidate.

The endpoint calculation is:

\[
D(100,250)=\max\left(\frac{1.30}{0.50},\frac{0.250}{0.10}\right)=\max(2.60,2.50)=2.60
\]

The only eligible interval is 150 m3/s wide, and its exact midpoint is 175 m3/s, so 175 m3/s is measured third.
The two new interval calculations are:

\[
D(100,175)=\max\left(\frac{0.60}{0.50},\frac{0.095}{0.10}\right)=\max(1.20,0.95)=1.20
\]

\[
D(175,250)=\max\left(\frac{0.70}{0.50},\frac{0.155}{0.10}\right)=\max(1.40,1.55)=1.55
\]

Both intervals are 75 m3/s wide and require refinement, so the lower-endpoint tie rule selects the 100 to 175 m3/s interval.
Its midpoint is 137.5 m3/s, and the untried candidates 125 and 150 m3/s are each 12.5 m3/s away, so the lower-candidate tie rule selects 125 m3/s.
After that measurement:

\[
D(100,125)=\max\left(\frac{0.20}{0.50},\frac{0.030}{0.10}\right)=\max(0.40,0.30)=0.40
\]

\[
D(125,175)=\max\left(\frac{0.40}{0.50},\frac{0.065}{0.10}\right)=\max(0.80,0.65)=0.80
\]

Those two intervals meet the criterion, while \(D(175,250)=1.55\) still requires refinement.
The midpoint of 175 to 250 m3/s is 212.5 m3/s, and the untried candidates 200 and 225 m3/s are each 12.5 m3/s away, so the lower-candidate tie rule selects 200 m3/s.
After that measurement:

\[
D(175,200)=\max\left(\frac{0.18}{0.50},\frac{0.040}{0.10}\right)=\max(0.36,0.40)=0.40
\]

\[
D(200,250)=\max\left(\frac{0.52}{0.50},\frac{0.115}{0.10}\right)=\max(1.04,1.15)=1.15
\]

Only the 200 to 250 m3/s interval still requires refinement, and its exact midpoint is 225 m3/s, so 225 m3/s is measured sixth.
The final two affected intervals are:

\[
D(200,225)=\max\left(\frac{0.23}{0.50},\frac{0.045}{0.10}\right)=\max(0.46,0.45)=0.46
\]

\[
D(225,250)=\max\left(\frac{0.29}{0.50},\frac{0.070}{0.10}\right)=\max(0.58,0.70)=0.70
\]

The final adjacent distances are 0.40, 0.80, 0.40, 0.46, and 0.70 for the intervals 100 to 125, 125 to 175, 175 to 200, 200 to 225, and 225 to 250 m3/s.
Every value is at most one, so the stopping rule is satisfied with no unresolved grid residual.
The measurement order is 100, 250, 175, 125, 200, and 225 m3/s, and the selected set contains those six values in ascending order.
The 150 m3/s candidate remains untried because it lies inside the 125 to 175 m3/s interval whose distance is 0.80.

**Assumptions:** Only completed, scientifically accepted, compatible scenarios can divide intervals or provide warm starts.
The response scales, candidate grid, tie rules, and stopping rule are method assumptions rather than universal standards.

**Evidence note:** The calculation establishes only the selection arithmetic for the supplied synthetic metrics.
It does not establish convergence, domain adequacy, initial-state independence, or transferability of the response scales.
See [Adaptive Discharge Selection](../05-scenario-libraries/02-adaptive-discharge-selection.md) and [MX-003](decision-code-artifact-crosswalk.md#mx-003-discharge-selection).

## Applied cell-specific stage transfer

For a compatible downstream source cell \(i\), calculate:

\[
WSE_{ds,i}=h_{ds,i}+z_{ds,i}
\]

- \(WSE_{ds,i}\) is sampled downstream WSE in m relative to a stated vertical datum.
- \(h_{ds,i}\) is final downstream depth in m with a stated sign convention, location, and terrain-surface definition.
- \(z_{ds,i}\) is downstream terrain elevation in m relative to the same datum.

**Dimensional check:** Both terms on the right are lengths, so the result is a length in m.

**Assumptions:** Depth and terrain share grid support, length units, horizontal location, and nodata treatment.
The depth is defined as the vertical distance above the represented terrain surface, while the terrain elevation retains the vertical datum inherited by the calculated WSE.
Applying the result upstream requires compatible downstream and upstream elevation datums, transfer geometry, coordinate-mapping rule, and intended transfer location.

**Applied example:** For an `R-200` source cell with terrain elevation \(z_{ds}=100.85\ \text{m}\) in datum `VD-1` and depth \(h_{ds}=1.15\ \text{m}\) above that represented terrain surface, the transferred WSE is \(102.00\ \text{m}\) in `VD-1`.
The synthetic transfer method emits an exact-source value only where terrain and depth are valid and depth is strictly positive.

For the 102.5 m nominal target, the interpolation coordinate is the scalar transfer-support stage at the confluence in `VD-1`.
The lower source is the 175 m3/s `R-200` scenario at \(s_{175}=102.4\ \text{m}\), and the upper source is the 200 m3/s scenario at \(s_{200}=102.7\ \text{m}\).
The target coordinate is \(s^*=102.5\ \text{m}\), so the upper-source weight is:

\[
w=\frac{s^*-s_{175}}{s_{200}-s_{175}}
=\frac{102.5-102.4}{102.7-102.4}
=\frac{1}{3}
\]

At each common-wet-support point \(i\), calculate the two source WSE fields as:

\[
\eta_{175,i}=h_{175,i}+z_{175,i},
\qquad
\eta_{200,i}=h_{200,i}+z_{200,i}
\]

Then interpolate pointwise along the scalar stage coordinate:

\[
\eta_i^*=(1-w)\eta_{175,i}+w\eta_{200,i}
=\frac{2}{3}\eta_{175,i}+\frac{1}{3}\eta_{200,i}
\]

The common wet support contains only intended-interface points where both source depths and terrain values are valid and both depths are strictly positive.
For intended-interface set \(I\) and valid common-wet set \(M\), the coverage fraction is:

\[
C=\frac{|M|}{|I|}
\]

The synthetic method requires \(C\ge0.90\).
It rejects the scenario when coverage is below 90 percent and emits no value outside \(M\) when coverage passes.
The transfer record retains both source scenario identities, the 102.4 and 102.7 m interpolation coordinates, the 102.5 m target, weights \(2/3\) and \(1/3\), source depth and terrain artifact identities, transforms, datum metadata for the elevation fields, common-wet mask, intended interface, invalid-point reasons, coverage result, and realized interpolated field.

**Evidence note:** A nominal stage organizes the family but does not replace the spatial water-surface field.
See [Downstream-Stage-Aware Libraries and Stage Transfer](../05-scenario-libraries/03-downstream-stage-and-transfer.md), [MX-004](decision-code-artifact-crosswalk.md#mx-004-stage-transfer), and [CQ-003](conflicts-and-open-questions.md#cq-003-incompatible-datums).

## Applied nominal stage grid

For a regular grid with base stage \(z_0\) and increment \(\Delta z>0\), nominal targets can be written as:

\[
z_k=z_0+k\Delta z, \qquad k\in\{0,1,\ldots,n\}
\]

- \(z_k\) is a nominal stage target in m relative to a stated vertical datum.
- \(z_0\) is the lower nominal stage in m relative to the same datum.
- \(\Delta z\) is the stage increment in m.
- \(k\) is a dimensionless integer.

**Dimensional check:** \(k\Delta z\) and \(z_0\) both have units m, so \(z_k\) has units m.

**Applied example:** The upstream `R-100` and `R-300` families use \(z_0=102.0\ \text{m}\), \(\Delta z=0.5\ \text{m}\), and \(k\in\{0,1,2\}\) in datum `VD-1`.
The resulting nominal targets are 102.0, 102.5, and 103.0 m.

**Assumptions:** A planning record defines the datum, units, endpoints, target-to-source binding, interpolation rule, and out-of-range behavior.
The nominal grid does not prescribe a uniform value at every transfer point.

## Pixelwise-maximum depth compositing

For compatible depth rasters at one output location:

\[
h_{comp}(x,y)=\max_{r\in R(x,y)}h_r(x,y)
\]

- \(h_{comp}(x,y)\) is composite depth in m.
- \(h_r(x,y)\) is depth in m contributed by source reach scenario \(r\).
- \(R(x,y)\) is the set of valid, compatible contributors at the location.

**Assumptions:** Every contributor represents the same physical quantity and units on a compatible grid under an authorized scenario-selection, nodata, and resampling contract.
Terrain and vertical-datum compatibility remain material when depth is derived from WSE or network continuity is assessed.

**Interpretation:** The maximum rule is deterministic, but it cannot make incompatible source scenarios scientifically compatible.
The source membership, joint hydrologic condition, quantity, grid, datum, nodata, and transformation rules must be validated before the calculation.
See [MX-012](decision-code-artifact-crosswalk.md#mx-012-compositing).

## Applied characteristic-width relation

An illustrative power relation can be written as:

\[
W_c=aA_d^b
\]

- \(W_c\) is an estimated characteristic width in m.
- \(A_d\) is drainage area in km2 for this example.
- \(a\) carries the unit convention needed to produce metres.
- \(b\) is dimensionless.

**Applied example:** For `R-200`, let \(A_d=142\ \text{km}^2\), \(a=3.0\ \text{m}/(\text{km}^2)^{0.35}\), and \(b=0.35\).
The resulting teaching estimate is approximately \(W_c=17.0\ \text{m}\).

**Dimensional check:** The units encoded by \(a\) cancel \((\text{km}^2)^b\) and leave metres.

**Assumptions:** The coefficients, calibration population, applicability range, and uncertainty are stated before the relation is used.
A width estimate can guide a buffer or geometry proposal, but it is not an observation of channel or floodplain width and does not prove domain adequacy.

## Computed domain grid and snapping

For unsnapped geometry bounds \((x_{min}^*,y_{min}^*,x_{max}^*,y_{max}^*)\), nonnegative domain buffer \(b\), and grid resolution \(r>0\), an outward-snapping rule is:

\[
x_{min}=r\left\lfloor\frac{x_{min}^*-b}{r}\right\rfloor,
\qquad
y_{min}=r\left\lfloor\frac{y_{min}^*-b}{r}\right\rfloor
\]

\[
x_{max}=r\left\lceil\frac{x_{max}^*+b}{r}\right\rceil,
\qquad
y_{max}=r\left\lceil\frac{y_{max}^*+b}{r}\right\rceil
\]

The grid dimensions are:

\[
n_{col}=\frac{x_{max}-x_{min}}{r},
\qquad
n_{row}=\frac{y_{max}-y_{min}}{r}
\]

- \(x_{min}\), \(y_{min}\), \(x_{max}\), and \(y_{max}\) are realized domain coordinates in the horizontal CRS units.
- \(b\) is the domain buffer in the same horizontal units.
- \(r\) is the grid resolution in the same horizontal units.
- \(n_{col}\) and \(n_{row}\) are dimensionless cell counts.

**Dimensional check:** Each coordinate, buffer, and resolution uses the same horizontal length unit, and each grid dimension is dimensionless.

**Assumptions:** The horizontal coordinate reference is projected with metre linear units unless explicit conversions are applied, all construction geometries use that reference, the grid is unrotated, and the resulting widths are divisible by \(r\).
An authored extent that is already grid aligned can bypass the buffer and snapping calculation when the method treats that extent as final.

**Interpretation:** Snapping guarantees a full-cell rectangle that contains the supplied construction bounds.
It does not prove that the rectangle contains the relevant floodplain, outflow region, transfer geometry, or largest intended scenario.
See [MX-007](decision-code-artifact-crosswalk.md#mx-007-domain-adequacy) and [CQ-004](conflicts-and-open-questions.md#cq-004-domain-clipping).

## Applied endpoint terrain-slope estimate

A synthetic normal-depth outflow example calculates:

\[
S_{proxy}=\max\left(\frac{|z_1-z_2|}{L},S_{min}\right)
\]

- \(S_{proxy}\) is a nonnegative slope magnitude in m/m when elevations and length are in m.
- \(z_1\) and \(z_2\) are terrain elevations sampled at stated endpoints in m relative to one stated vertical datum.
- \(L\) is the reach length in m.
- \(S_{min}\) is a stated lower bound for the teaching method.

**Dimensional check:** The elevation difference and length both have units m, so their ratio and \(S_{proxy}\) are dimensionless.

**Assumptions and limitation:** The equation assumes that endpoint terrain difference over total reach length is a useful boundary-slope proxy.
Its m/m interpretation also assumes metre elevation and metre horizontal length or an explicit consistent conversion.
The absolute value removes direction, so a downstream endpoint higher than the upstream endpoint still produces a positive magnitude.
A positive result therefore does not prove correct reach direction, downhill bed slope, water-surface slope, vertical-datum compatibility, or boundary adequacy.

**Evidence note:** The proxy must not be called freefall or observed energy slope without evidence for that different behavior.

## CFL stability estimate

\[
c=\sqrt{gh}
\]

For a rectilinear teaching grid,

\[
C_x=\frac{(|u|+c)\Delta t}{\Delta x},\qquad
C_y=\frac{(|v|+c)\Delta t}{\Delta y}
\]

\[
\Delta t_{est}
=C_{target}\min\left(
\frac{\Delta x}{|u|+c},
\frac{\Delta y}{|v|+c}
\right)
\]

- \(c\) is shallow-water gravity-wave speed in m/s.
- \(C_x\) and \(C_y\) are dimensionless directional Courant estimates.
- \(u\) and \(v\) are representative depth-averaged velocity components in m/s.
- \(g\) is gravitational acceleration in m/s2.
- \(h\) is water depth in m.
- \(\Delta t\) is the numerical time step in s.
- \(\Delta x\) and \(\Delta y\) are representative grid spacings in m.
- \(C_{target}\) is a dimensionless teaching target rather than a universal stability limit.
- \(\Delta t_{est}\) is the resulting teaching estimate in s.

**Assumptions:** The estimate uses hydrostatic shallow-water wave speed, representative local values, and a rectilinear directional distance.
Actual solver restrictions can depend on face geometry, diagonal propagation, variable placement, explicit or implicit treatment, source terms, wetting logic, nonlinear iteration, and solver-specific safety factors.

**Interpretation:** The estimate explains why grid resolution, depth, velocity, and time step interact in stability and runtime decisions.
It is not a verified statement of the exact adaptive time-step rule used by LISFLOOD-FP or SFINCS.

## Applied storage-change convergence ratio

\[
C_V = \frac{|V_t - V_{t-\Delta t_s}|}{Q_{in}\Delta t_s}
\]

- \(C_V\) is the dimensionless storage-change convergence ratio.
- \(V_t\) and \(V_{t-\Delta t_s}\) are modeled water volumes in m3 at consecutive saved outputs.
- \(Q_{in}\) is the imposed inflow discharge in m3/s.
- \(\Delta t_s\) is the saved-output interval in s.

For square cells, estimate stored volume from valid positive depths as:

\[
V_t=(\Delta x)^2\sum_{i\in P_t}h_{i,t}
\]

- \(\Delta x\) is raster cell width in m.
- \(P_t\) is the set of cells whose saved depth is positive at time \(t\).
- \(h_{i,t}\) is saved water depth in cell \(i\) at time \(t\), in m.

**Dimensional check:** \([V_t]=(\text{m})^2(\text{m})=\text{m3}\), \([Q_{in}\Delta t_s]=(\text{m3/s})(\text{s})=\text{m3}\), and therefore \([C_V]=1\).

**Assumptions:** Consecutive rasters are compatible, the projected horizontal reference uses metre linear units so squared cell width represents m2, inflow is positive and constant over the interval, nodata is excluded, and storage change is a useful proxy for the stated diagnostic purpose.

**Applied example:** Let \(\Delta x=20\ \text{m}\), \(Q_{in}=50\ \text{m3/s}\), \(\Delta t_s=900\ \text{s}\), and consecutive positive-depth sums be 328.5000 m and 328.5900 m.
The stored volumes are 131,400 m3 and 131,436 m3, so:

\[
C_V=\frac{36\ \text{m3}}{(50\ \text{m3/s})(900\ \text{s})}=0.0008
\]

The synthetic rule declares quasi-steady storage only when \(C_V<10^{-3}\) for three consecutive saved intervals.
A value equal to the tolerance does not satisfy the strict inequality.

**Evidence note:** This diagnostic does not calculate outflow and must not be described as complete inflow-outflow mass-balance closure.
The full ratio history, local hydraulic quantities, boundary fluxes, termination evidence, and artifact inventory remain separate evidence.
See [MX-002](decision-code-artifact-crosswalk.md#mx-002-convergence) and [CQ-005](conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence).
