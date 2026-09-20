# Equations and Units

This reference gives the minimum equation, variable, unit, assumption, and project-use context needed by later chapters.
Symbols are local to each equation unless a section states otherwise.
SI units are the default.

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

Project inputs often use `cms` to mean m3/s.
An elevation, stage, or depth value must retain its vertical datum and reference before values are combined.

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

**Project use:** This equation explains the discharge imposed at an inflow boundary and the relationship among channel geometry, velocity, and flow.
It is a scientific foundation rather than a claim that the current jobs calculate every boundary discharge from area and velocity.

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

**Project use:** Continuity is the scientific basis for diagnosing filling, draining, and mass balance.
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

**Project use:** Energy-head reasoning helps explain water-surface response, velocity-head effects, and loss between sections.
It does not prove that a project boundary value, loss representation, or scenario result is correct.

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

**Project use:** Momentum reasoning helps evaluate directional forces, abrupt transitions, and momentum exchange.
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

**Project use:** The balance explains why water responds to the WSE gradient and why friction must be represented.
It is a conceptual foundation rather than a statement of the complete equation or discretization in either current solver.

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

**Project use:** The Froude number helps interpret subcritical downstream influence, critical conditions, supercritical behavior, and the sensitivity of results to downstream boundaries.
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

**Project use:** Manning's equation explains normal-depth boundaries, roughness sensitivity, and why geometry, slope, and Manning's n jointly control conveyance.

## Manning roughness sensitivity at fixed geometry

For two Manning calculations with the same wetted area, hydraulic radius, and friction slope,

\[
\frac{Q_2}{Q_1}=\frac{n_1}{n_2}
\]

- \(Q_1\) and \(Q_2\) are discharges in m3/s.
- \(n_1\) and \(n_2\) are Manning roughness values in \(\mathrm{s}/\mathrm{m}^{1/3}\).

**Assumptions:** Wetted geometry and friction slope remain fixed, and both calculations satisfy the steady, approximately uniform assumptions of Manning's equation.
This relation does not describe a fixed-discharge simulation in which depth, WSE, wet extent, and flow routing adjust.

**Project use:** The ratio supports transparent roughness sensitivity calculations while preventing a fixed-geometry result from being presented as a complete two-dimensional model response.

## Gradually varied flow concept

Gradually varied flow is steady open-channel flow whose depth changes over a distance long enough for a one-dimensional, approximately hydrostatic treatment to remain useful.
The simplified profile reasoning used by this handbook also assumes gradual variation, prismatic geometry, and constant discharge or no material lateral inflow over the reach being interpreted.
It does not apply unchanged at a confluence, a material lateral inflow, an abrupt geometry transition, a hydraulic jump, a structure with rapidly varied flow, or a strongly two-dimensional flow pattern.

**Project use:** The concept organizes backwater intuition and explains why downstream stage can influence an upstream subcritical profile.
It is not an equation or discretization used to describe the current two-dimensional solver.

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

**Project use:** This equation supplies the common conservation vocabulary for interpreting cell storage, face flux, wetting, and solver behavior.
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

**Project use:** The equations explain conservative variables, inertia, hydrostatic pressure, terrain, WSE gradients, and resistance.
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

**Project use:** The relation supports transparent wetting-transition bookkeeping in Lab 6.
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

**Project use:** The equation connects terrain rasters and modeled WSE to depth artifacts and stage-transfer calculations.

For comparable results at one location, the change relation is

\[
\Delta h=\Delta WSE-\Delta z_b
\]

**Assumptions:** The two results use compatible horizontal locations, vertical datums, units, and definitions of terrain and WSE.
If WSE is held fixed, a positive terrain error produces an equal negative depth error.
When terrain changes the hydraulic solution, both terms can change and the total depth response cannot be inferred from terrain error alone.

**Project use:** The relation separates the direct depth-calculation effect of terrain error from its indirect effect on solved WSE through storage, conveyance, barriers, and pathways.

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

**Project use:** DR-029 selects recurrence-interval-based discharge bounds from National Water Model retrospective flow with status Alternate Selected.
The probability model, source version, record period, annual-maximum extraction, fitting method, sampling uncertainty, and nonstationarity treatment still affect the estimated discharges.
See [Flood Frequency, AEP, and Discharge Bounds](../01-hydrology-for-fim/04-flood-frequency-aep-and-bounds.md).

## DR-029 discharge-bound rule

\[
Q_{min}=0.9Q_{HFT}, \qquad Q_{max}=1.5Q_{100}
\]

- \(Q_{min}\) is the selected lower discharge bound in m3/s.
- \(Q_{max}\) is the selected upper discharge bound in m3/s.
- \(Q_{HFT}\) is the input that DR-029 calls the reach high-flow-threshold discharge, in m3/s.
- \(Q_{100}\) is the estimated 1 percent AEP discharge in m3/s for the stated source and method.

**Assumptions:** The source values refer to the intended reach and compatible model-network version, and their derivations use a documented period, sample rule, probability model, and stationarity assumption.
The current ND input interface requires whole-m3/s values, so a reproducible workflow also needs an explicit rounding policy when either result is non-integer.

**Open question:** The reviewed project record does not define the event, statistic, dataset variable, time support, or derivation represented by \(Q_{HFT}\).
Its scientific definition cannot be inferred from the symbol or the words "high-flow threshold."

**Project use:** The factors 0.9 and 1.5 are selected project methodology from DR-029 ALT-A, not universal hydrologic constants.
The current ND job consumes caller-supplied `min_upstream_inflow` and `max_upstream_inflow` values but does not derive either bound.
The source and consumer mapping is recorded in [XW-006](decision-code-artifact-crosswalk.md#xw-006-scenario-library-bounds-and-sampling).

## Current adaptive ND metrics and verdict

For a final saved depth raster, define the current wet-cell set as:

\[
W=\{i:h_i>0\}
\]

The current final-state metrics are:

\[
h_{max}=\max_{i\in W}(h_i)
\]

\[
h_{med}=\operatorname{median}_{i\in W}(h_i)
\]

\[
A_f=\frac{|W|r^2}{10^6}
\]

- \(h_i\) is saved water depth in cell \(i\), in m under the current depth contract.
- \(h_{max}\) is maximum depth over wet cells, in m.
- \(h_{med}\) is median depth over wet cells, in m.
- \(|W|\) is the number of wet cells.
- \(r\) is raster resolution in horizontal CRS units.
- \(A_f\) is flooded area in km2 only when \(r\) is in m.

If \(W\) is empty, current code returns zero for all three metrics.
Zero-depth and negative-depth cells are not in \(W\).

For trial \(t\) and reference \(r_f\), the measured changes are:

\[
\Delta h_{max}=h_{max,t}-h_{max,r_f}
\]

\[
\Delta h_{med}=h_{med,t}-h_{med,r_f}
\]

\[
\Delta A_f=100\frac{A_{f,t}-A_{f,r_f}}{A_{f,r_f}}
\]

The depth changes are in m, and \(\Delta A_f\) is a percentage.
When \(A_{f,r_f}=0\), current code defines \(\Delta A_f=0.0\) rather than evaluating the fraction.
This zero makes the area criterion neutral in the measured verdict.
It does not make the area curve neutral in proposal construction.

For criterion floors \(L_j\) and ceilings \(U_j\), current code applies:

\[
\text{verdict}=
\begin{cases}
\texttt{reject\_high}, & \exists j:\Delta_j>U_j\\
\texttt{accept}, & \left(\forall j:\Delta_j\le U_j\right)\land\left(\exists j:\Delta_j\ge L_j\right)\\
\texttt{reject\_low}, & \forall j:\Delta_j<L_j
\end{cases}
\]

The current default bands are 0.75 through 1.25 m for maximum depth, 0.25 through 0.50 m for median depth, and 10 through 15 percent for flooded area.
These current quantities and defaults differ from DR-030's monitor-point stage descriptions and its median-stage and extent bands.

## Current adaptive ND proposal curves

For metric values ordered by increasing simulated discharge, current code uses a running maximum:

\[
\widetilde y_i=\max(y_1,\ldots,y_i)
\]

This construction enforces a nondecreasing proposal curve but does not replace the raw values used for measured verdicts.

For a target response \(y^*\) between two increasing scenario points \((Q_i,\widetilde y_i)\) and \((Q_{i+1},\widetilde y_{i+1})\), the crossing discharge is:

\[
Q^*=Q_i+(Q_{i+1}-Q_i)
\frac{y^*-\widetilde y_i}{\widetilde y_{i+1}-\widetilde y_i}
\]

Above the last point, current code uses the final positive segment slope:

\[
m_{last}=\frac{\widetilde y_n-\widetilde y_{n-1}}{Q_n-Q_{n-1}}
\]

\[
Q^*=Q_n+\frac{y^*-\widetilde y_n}{m_{last}}
\]

No extrapolated crossing exists when there are fewer than two points or \(m_{last}\le0\).

For an absolute depth metric with reference response \(y_{r_f}\), its target responses are \(y_{r_f}+L_j\) and \(y_{r_f}+U_j\).
For flooded area, its target responses are \(y_{r_f}(1+L_j/100)\) and \(y_{r_f}(1+U_j/100)\).
When the reference flooded-area response is zero, both relative targets collapse to zero:

\[
A_{f,floor}=0\left(1+\frac{L_A}{100}\right)=0,
\qquad
A_{f,ceiling}=0\left(1+\frac{U_A}{100}\right)=0
\]

If a later monotone flooded-area point is positive, the current crossing test can return the lower endpoint of the first rising segment for both zero targets.
That endpoint can be the reference or a later zero-area point, and the identical crossings can create a degenerate or otherwise influential combined proposal window.
If the monotone area curve remains zero, the crossing function returns no area crossing.

The combined predicted acceptance window is:

\[
Q_{open}=\min_j Q_{j,floor},
\qquad
Q_{close}=\min_j Q_{j,ceiling}
\]

Only existing crossings enter each minimum.
No floor crossings produce no window, while floor crossings with no ceiling crossing produce an infinite close.
Both cases send the proposal to the maximum discharge in current code.

For a finite window and grid spacing \(g\), current code calculates:

\[
Q_{lowest}=\max\left(g\left\lceil\frac{Q_{open}}{g}\right\rceil,Q_{ref}+g\right)
\]

\[
Q_{highest}=g\left\lfloor\frac{Q_{close}}{g}\right\rfloor
\]

If \(Q_{lowest}\le Q_{highest}\), the proposal is the grid-rounded window midpoint clamped to that inclusive range.
If no grid value lies inside, current code first aims at the largest grid value strictly below the opening and then raises it to one grid step above the position when needed.
The first candidate is zero-grid aligned, but the replacement is:

\[
Q_{fallback}=Q_{position}+g
\]

If \(Q_{position}\bmod g\ne0\), then \(Q_{fallback}\bmod g\ne0\).
The fallback therefore preserves an off-grid residue from an adopted or opening-trial position.
No-window, infinite-close, and at-or-above-maximum branches return \(Q_{max}\) directly, which can also be off-grid.

**Project use:** These equations describe current code, not an independently validated sampling rule.
Finite-window midpoint proposals and the first below-window candidate use the zero-anchored grid.
The grid is not enforced for adopted scenarios, endpoints, the opening authored step, an off-grid position-relative fallback, or a direct maximum return.
See [Adaptive Discharge Selection](../05-scenario-libraries/02-adaptive-discharge-selection.md) and [CONF-009](conflicts-and-open-questions.md#conf-009-scenario-publication-membership-reuse-and-discharge-grid-authority).

## Current cell-specific stage transfer

For downstream source cell \(i\), current transfer preprocessing calculates:

\[
WSE_{ds,i}=h_{ds,i}+z_{ds,i}
\]

- \(WSE_{ds,i}\) is sampled downstream WSE in m relative to a stated vertical datum.
- \(h_{ds,i}\) is final downstream depth in m.
- \(z_{ds,i}\) is downstream terrain elevation in m relative to the same datum.

**Dimensional check:** Both terms on the right are lengths, so the result is a length in m.

**Assumptions:** Depth and terrain share grid support, units, horizontal location, and vertical reference.
Applying the result upstream also requires compatibility with the upstream terrain datum and intended transfer location.

**Current code rule:** A sampled value becomes point `HFIX` only when \(WSE_{ds,i}>0\).
The current rule does not separately require \(h_{ds,i}>0\), so a dry positive-terrain cell can produce a transfer point and a wet cell at nonpositive WSE is omitted.
The nominal scenario `bc_value` labels the scenario but does not replace \(WSE_{ds,i}\).

## DR-033 zero-anchored stage grid

For selected stage increment \(\Delta z\), stage targets lie on:

\[
z_k=k\Delta z, \qquad k\in\mathbb{Z}
\]

- \(z_k\) is a nominal stage-grid target in m relative to the applicable vertical datum.
- \(\Delta z\) is the per-reach stage increment in m.
- \(k\) is a dimensionless integer.

DR-033 ALT-B selects \(\Delta z\in\{0.25,0.5,1,2,5\}\) m and anchors the grid at zero rather than at one reach's bound.
The selected method rounds bounds to the nearest grid value and binds a target only when a downstream scenario's nominal achieved stage lies within \(\Delta z/2\).

**Project use:** The stage grid defines planning coordinates, not a uniform cell-by-cell boundary.
Current `run_kwse_scenarios` consumes an already authored scenario list and does not construct this grid.

## Selected pixelwise-maximum depth compositing

For compatible depth rasters at one output location:

\[
h_{comp}(x,y)=\max_{r\in R(x,y)}h_r(x,y)
\]

- \(h_{comp}(x,y)\) is composite depth in m.
- \(h_r(x,y)\) is depth in m contributed by source reach scenario \(r\).
- \(R(x,y)\) is the set of valid, compatible contributors at the location.

**Assumptions:** Every contributor represents the same physical quantity and units on a compatible grid under an authorized scenario-selection, nodata, and resampling contract.
Terrain and vertical-datum compatibility remain material when depth is derived from WSE or network continuity is assessed.

**Project use:** DR-004 ALT-D selects pixelwise maximum for composite maps.
No current `twod-fim-jobs` entry point performs this cross-reach compositing.
The unpinned current local `twod-fim-deployment/orchestrator/scripts/f2f.py` path requests Flows2FIM VRT output and rewrites each VRT band with `PixelFunctionType` set to `max`, outside the modeling jobs.

## Current bankfull-width estimate

The current model builder estimates bankfull width as:

\[
W_{bf}=2.7A_d^{0.352}
\]

- \(W_{bf}\) is estimated bankfull width in m under the current code relation.
- \(A_d\) is the target reach's `total_da_sqkm` drainage-area value in km2.

**Dimensional interpretation:** The numeric coefficient and exponent encode the unit convention used by the implemented empirical relation.
They must not be applied to drainage area expressed in another unit without a corresponding conversion or refit.

**Project use:** The current `build_model` job multiplies \(W_{bf}\) by `bankfull_width_multiplier` to set inflow-line length and by `centerline_buffer_bankfull_multiplier` to set computed-domain centerline buffer distance.
The unmultiplied estimate is recorded in manifest properties.
This is current implementation, not proof that the estimate matches observed channel or floodplain width for a particular reach or flow.

## Computed domain grid and snapping

For unsnapped geometry bounds \((x_{min}^*,y_{min}^*,x_{max}^*,y_{max}^*)\), nonnegative domain buffer \(b\), and grid resolution \(r>0\), the current computed-domain rule is:

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
- \(b\) is `domain_buffer` in the same horizontal units.
- \(r\) is `grid_resolution` in the same horizontal units.
- \(n_{col}\) and \(n_{row}\) are dimensionless cell counts.

**Assumptions:** The horizontal CRS is projected with metre linear units unless explicit conversions are applied, all construction geometries already use that CRS, the current grid is unrotated, and the resulting widths are divisible by \(r\).
The metre-based contract is also required when the bankfull-width estimate is used as geometry distance, reach length is used for slope, discharge is divided by raster resolution for per-unit-width `QFIX`, resolution is squared for cell area and volume, or wet-cell area is converted to km2.
Current input validation requires only a positive EPSG integer and does not enforce projected coordinates or metre linear units.
The current authored-domain path bypasses these buffer and outward-snap equations because validation requires the supplied bbox to be grid aligned already.

**Project use:** Snapping guarantees a full-cell rectangle that contains the supplied computed-domain construction bounds.
It does not prove that the rectangle contains the relevant floodplain, outflow region, STL, or largest intended scenario.

## Current endpoint terrain-slope estimate

The current ND helper calculates:

\[
S_{code}=\max\left(\frac{|z_1-z_2|}{L},S_{min}\right)
\]

- \(S_{code}\) is the nonnegative slope magnitude passed to the current `FREE` boundary interface, in m/m when elevations and length are in m.
- \(z_1\) and \(z_2\) are terrain raster values sampled at the two target-reach endpoints, in m under the unstated terrain vertical reference.
- \(L\) is the manifest reach length in m.
- \(S_{min}\) is the configured minimum, whose checked-in fallback is \(10^{-4}\).

**Assumptions and limitation:** The equation assumes that endpoint terrain difference over total reach length is a useful boundary-slope proxy.
Its m/m interpretation also assumes metre elevation and metre horizontal length or an explicit consistent conversion.
The absolute value removes direction, so a downstream endpoint higher than the upstream endpoint still produces a positive magnitude.
A positive result therefore does not prove correct reach direction, downhill bed slope, water-surface slope, vertical-datum compatibility, or boundary adequacy.

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

**Project use:** The estimate explains why grid resolution, depth, velocity, and time step interact in stability and runtime decisions.
It is not a verified statement of the exact adaptive time-step rule used by LISFLOOD-FP or SFINCS.

## Project storage-change convergence ratio

\[
C_V = \frac{|V_t - V_{t-\Delta t}|}{Q_{in}\Delta t}
\]

- \(C_V\) is the dimensionless storage-change convergence ratio.
- \(V_t\) and \(V_{t-\Delta t}\) are modeled water volumes in m3 at consecutive saved outputs.
- \(Q_{in}\) is the imposed inflow discharge in m3/s.
- \(\Delta t\) is the saved-output interval in s.

For a raster of positive cell depths, `calculate_volume_convergence` in the current [solver run code](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/hydraulic_solvers/run.py) estimates each volume as the sum of positive depths multiplied by cell area.

For the current square-cell calculation,

\[
V_t=(\Delta x)^2\sum_{i\in P_t}h_{i,t}
\]

- \(\Delta x\) is raster cell width in m.
- \(P_t\) is the set of cells whose saved depth is positive at time \(t\).
- \(h_{i,t}\) is saved water depth in cell \(i\) at time \(t\), in m.

**Dimensional check:** \([V_t]=(\text{m})^2(\text{m})=\text{m3}\), \([Q_{in}\Delta t]=(\text{m3/s})(\text{s})=\text{m3}\), and therefore \([C_V]=1\).

**Assumptions:** Consecutive rasters are compatible, the projected horizontal CRS uses metre linear units so squared grid resolution represents m2, inflow is positive and constant over the interval, and storage change is a useful proxy for the selected termination purpose.

**Project use:** The reviewed Decision Register selects DR-022 ALT-G volume convergence and DR-028 ALT-A threshold \(10^{-3}\), both with status Alternate Selected.
The standalone DR-022 file also marks ALT-J as `#current`, so that file-marker conflict remains an Open question while the registered selection controls methodology within its status and scope.
The current [solver run code](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/hydraulic_solvers/run.py) terminates when the absolute ratio is below the configured tolerance, and [the current constants](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/consts.py) set the default to `0.001`.
The project mapping and validation question are recorded in [XW-002](decision-code-artifact-crosswalk.md#xw-002-quasi-steady-termination).
This diagnostic does not calculate outflow and must not be described as a complete inflow-outflow mass-balance closure.
