# Energy, Momentum, and Flow Regimes

Continuity constrains how much water enters, leaves, and accumulates, but it does not by itself determine water-surface elevation or velocity.
Energy and momentum relationships add the effects of elevation, pressure, motion, gravity, friction, and other forces, while the Froude number helps identify how hydraulic information can propagate.

## Why this topic matters

Downstream stage can influence upstream water levels in some flow regimes and have little upstream reach in others.
An engineer must understand that influence before interpreting normal-depth and known-WSE boundaries, diagnosing edge effects, or deciding whether a cross-section average is adequate.

## Prerequisites

Read [Conservation, Discharge, and Storage](01-conservation-discharge-and-storage.md).
Retain the definitions of terrain elevation, WSE, geometric depth, velocity, discharge, storage, slope, CRS, and vertical datum from the orientation and hydrology chapters.

This chapter establishes prerequisites for later Manning-flow and backwater chapters.
It does not require their methods and does not teach a complete water-surface-profile calculation.

## Learning objectives

After this chapter, the reader should be able to:

- identify elevation, pressure-depth, velocity-head, and loss terms in an open-channel energy balance;
- explain why energy and momentum balances answer different questions;
- describe how a water-surface gradient and friction contribute to acceleration or steady balance;
- distinguish geometric depth from hydraulic depth;
- calculate and interpret a section-scale Froude number; and
- explain why subcritical flow permits possible downstream influence without assuming that every downstream boundary is appropriate.

## Energy head organizes elevation, pressure, and motion

**Scientific foundation:** For a one-dimensional open-channel section with approximately hydrostatic pressure, total energy head can be represented as

\[
H=z_b+y+\alpha\frac{\bar{V}^{2}}{2g}
\]

- \(H\) is total energy head in m.
- \(z_b\) is channel-bed or section-invert elevation in m relative to a stated vertical datum.
- \(y\) is geometric water depth above that bed or invert in m.
- \(\bar{V}\) is area-averaged velocity normal to the section in m/s.
- \(g\) is gravitational acceleration in m/s2.
- \(\alpha\) is the dimensionless kinetic-energy correction coefficient.
- The sum \(z_b+y\) is WSE, denoted \(\eta\), when the bed and water surface share the same location, units, and vertical datum.

The elevation term represents position relative to a datum.
The depth term represents hydrostatic pressure head for the open-channel section.
The velocity-head term represents kinetic energy per unit weight after correcting for a nonuniform velocity distribution.

Every term has dimensions of length.
For the velocity term,

\[
\left[\frac{\bar{V}^{2}}{2g}\right]
=\frac{\text{m2/s2}}{\text{m/s2}}
=\text{m}
\]

The kinetic-energy correction coefficient is

\[
\alpha=\frac{\int_A u_n^3\,dA}{A\bar{V}^3}
\]

- \(u_n\) is local velocity normal to the section in m/s.
- \(A\) is wetted cross-sectional area in m2.
- \(\bar{V}\) is the area-averaged normal velocity in m/s.
- \(\alpha\) is dimensionless and equals 1 for a perfectly uniform unidirectional velocity distribution.

Because velocity is cubed, fast zones contribute disproportionately to energy flux.
The correction cannot recover the full velocity field, but it prevents a simple average from being treated as though the field were uniform.
The [HEC-RAS mean kinetic-energy documentation](https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.2/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-steady-flow-water-surface-profiles/evaluation-of-the-mean-kinetic-energy-head) shows how a one-dimensional model weights subsection velocity heads when computing one mean cross-section energy.

## Energy between two sections

For steady flow from upstream section 1 to downstream section 2, a useful one-dimensional balance is

\[
z_1+y_1+\alpha_1\frac{\bar{V}_1^2}{2g}
=
z_2+y_2+\alpha_2\frac{\bar{V}_2^2}{2g}+h_L
\]

- Subscripts 1 and 2 identify the upstream and downstream sections.
- \(z\), \(y\), \(\bar{V}\), \(g\), and \(\alpha\) retain the definitions and SI units given above.
- \(h_L\) is nonnegative energy-head loss from section 1 to section 2 in m under the chosen downstream direction.

The loss term can represent boundary friction, turbulence, expansion, contraction, and other dissipative processes included by the selected method.
It must not be used as an unexplained adjustment that hides omitted physics or a datum mismatch.

The [HEC-RAS steady-profile equations](https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.1/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-steady-flow-water-surface-profiles/equations-for-basic-profile-calculations) use this combination of bed elevation, depth, corrected velocity head, and energy loss for section-to-section profile calculations.
That documented application does not make the energy equation a universal solver for every rapidly varied, unsteady, or multidirectional flow.

### Worked energy-head calculation

Assume the following compatible section values for steady downstream flow:

| Quantity | Upstream section 1 | Downstream section 2 |
| --- | ---: | ---: |
| Bed elevation, m | 100.0 | 99.5 |
| Geometric depth, m | 2.0 | 2.4 |
| Area-averaged velocity, m/s | 2.0 | 1.8 |
| Kinetic-energy coefficient | 1.10 | 1.05 |

Using \(g=9.81\) m/s2, the upstream energy head is

\[
H_1=100.0+2.0+1.10\frac{(2.0)^2}{2(9.81)}=102.224\ \text{m}
\]

The downstream energy head is

\[
H_2=99.5+2.4+1.05\frac{(1.8)^2}{2(9.81)}=102.073\ \text{m}
\]

The implied loss is

\[
h_L=H_1-H_2=0.151\ \text{m}
\]

The result is positive under the stated upstream-to-downstream convention.
A negative value would require checking the flow direction, external energy inputs, section data, correction coefficients, loss model, and vertical datums before accepting the calculation.

## Momentum tracks forces and directional motion

Energy is a scalar accounting of work and losses.
Momentum is directional and connects pressure, gravity, friction, structures, and other external forces to changes in motion.

For a fixed control volume with steady, one-dimensional, incompressible, unidirectional flow and one inlet and outlet, take the positive streamwise direction as downstream.
A streamwise momentum balance can then be written as

\[
\sum F_x
=
\rho Q
\left(
\beta_2\bar{V}_2-
\beta_1\bar{V}_1
\right)
\]

- \(\sum F_x\) is the net external force on the water in the positive streamwise direction in N.
- \(\rho\) is water density in kg/m3.
- \(Q\) is discharge in m3/s, assumed equal at the inlet and outlet for this steady no-source form.
- \(\bar{V}_1\) and \(\bar{V}_2\) are signed area-averaged streamwise velocities in m/s.
- \(\beta_1\) and \(\beta_2\) are dimensionless momentum correction coefficients.
- Pressure forces, the streamwise component of water weight, boundary drag, and structure reactions belong in \(\sum F_x\) when present.

The momentum correction coefficient is

\[
\beta=\frac{\int_A u_n^2\,dA}{A\bar{V}^2}
\]

- \(u_n\), \(A\), and \(\bar{V}\) retain their earlier definitions and SI units.
- \(\beta\) is dimensionless and equals 1 for a perfectly uniform unidirectional velocity distribution.

The force dimensions are

\[
[\rho Q\bar{V}]
=\frac{\text{kg}}{\text{m3}}
\frac{\text{m3}}{\text{s}}
\frac{\text{m}}{\text{s}}
=\frac{\text{kg m}}{\text{s2}}
=\text{N}
\]

The [HEC-RAS momentum-equation documentation](https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.6/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-unsteady-flow-hydrodynamics/momentum-equation) develops the control-volume balance using pressure, gravity, boundary drag, momentum flux, and momentum accumulation.
The simplified equation above omits accumulation and multiple flux boundaries, so those terms must be restored for an unsteady or branched control volume.

### Momentum-flux scale from the discharge example

For the preceding chapter's \(Q=52\) m3/s and \(\bar{V}=1.04\) m/s, suppose \(\beta=1.25\) and \(\rho=1{,}000\) kg/m3.
The momentum flux magnitude through the section is

\[
\rho\beta Q\bar{V}
=(1{,}000)(1.25)(52)(1.04)
=67{,}600\ \text{N}
\]

This value is not the net force on a reach by itself.
The net force depends on the difference between inlet and outlet momentum fluxes, momentum accumulation, and every external force included in the control volume.

## Energy and momentum answer different questions

An energy balance is useful for relating elevations, depth, velocity head, and irreversible loss between states.
A momentum balance is useful when pressure and reaction forces, abrupt transitions, flow-direction changes, or momentum exchange control the question.

The correction coefficients also differ because kinetic-energy flux weights local velocity with the third power, while momentum flux uses the second power.
Using \(\alpha\) in a momentum equation or \(\beta\) in an energy equation changes the physical quantity being represented.

Neither relationship replaces continuity.
Neither relationship universally replaces the other.
Neither eliminates the need to solve the governing equations numerically when geometry, storage, friction, wetting, time dependence, or two-dimensional motion vary through the domain.

## Why water responds to a water-surface gradient

Let \(x\) increase downstream and let \(\eta=z_b+y\) be WSE in m.
For gradually varied one-dimensional flow with hydrostatic pressure, no wind stress, and no added force term, a simplified streamwise momentum form is

\[
\frac{\partial \bar{V}}{\partial t}
+\bar{V}\frac{\partial \bar{V}}{\partial x}
=
-g\frac{\partial \eta}{\partial x}
-gS_f
\]

- \(\partial \bar{V}/\partial t\) is local acceleration in m/s2.
- \(\bar{V}\,\partial \bar{V}/\partial x\) is convective acceleration in m/s2.
- \(x\) is streamwise distance in m and increases downstream.
- \(\partial \eta/\partial x\) is the dimensionless WSE gradient, positive when WSE rises in the positive \(x\) direction.
- \(S_f\) is a nonnegative dimensionless friction slope opposing positive downstream flow.
- \(g\) is gravitational acceleration in m/s2.

If WSE falls downstream, \(\partial\eta/\partial x<0\), so the term \(-g\,\partial\eta/\partial x\) accelerates water downstream.
Friction acts in the opposite direction and removes mechanical energy.
Under steady, uniform conditions the acceleration terms vanish and the magnitude of the falling WSE gradient balances friction:

\[
-\frac{d\eta}{dx}=S_f
\]

The bed slope matters because it helps shape depth and WSE, but water does not respond to bed elevation alone.
A sloping bed beneath a horizontal water surface does not supply the same downstream pressure-gradient drive as a falling water surface.
The [HEC-RAS one-dimensional finite-volume equations](https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.6/theoretical-basis-for-one-dimensional-and-two-dimensional-hydrodynamic-calculations/1d-unsteady-flow-hydrodynamics/semi-implicit-finite-volume-scheme/hydraulic-equations-1d-fv) likewise place the WSE gradient, friction, and acceleration in the momentum balance and identify bottom friction as energy loss from drag.

## Flow regime compares flow speed with shallow-water wave speed

For a cross-section, define hydraulic depth as

\[
D_h=\frac{A}{T_w}
\]

- \(D_h\) is hydraulic depth in m.
- \(A\) is wetted cross-sectional area in m2.
- \(T_w\) is water-surface top width in m.

Hydraulic depth is not generally the same as geometric depth \(y\).
For a rectangular channel, \(A=T_w y\), so \(D_h=y\).
For triangular, trapezoidal, compound, or irregular sections, geometric depth and hydraulic depth can differ materially.

The section-scale Froude number is

\[
Fr=\frac{|\bar{V}|}{\sqrt{gD_h}}
\]

- \(Fr\) is dimensionless.
- \(|\bar{V}|\) is the magnitude of area-averaged section velocity in m/s.
- \(g\) is gravitational acceleration in m/s2.
- \(D_h\) is hydraulic depth in m.
- \(c=\sqrt{gD_h}\) is the long shallow-water wave celerity relative to the water under the assumptions of this section-scale approximation, in m/s.

The dimensions cancel:

\[
[Fr]=\frac{\text{m/s}}{\sqrt{(\text{m/s2})(\text{m})}}=1
\]

The conventional interpretations are:

- \(Fr<1\) is subcritical flow, where the mean flow is slower than the shallow-water wave celerity;
- \(Fr=1\) is critical flow in the idealized section-scale relation; and
- \(Fr>1\) is supercritical flow, where the mean flow is faster than the shallow-water wave celerity.

Near \(Fr=1\), small changes in geometry, depth, discharge, or losses can change the diagnosed regime.
Irregular compound sections, strong lateral variation, rapidly varied flow, and two-dimensional cells require more care than one section-wide value can provide.
The original USACE technical paper recorded as [SCI-021](../reference/bibliography.md#sci-021-usace-subdivision-froude-number) specifically examines why nonuniform compound sections need more than the simplest uniform-velocity Froude definition.

### Worked Froude calculation

Suppose a section carries \(Q=18\) m3/s with wetted area \(A=12\) m2 and top width \(T_w=8\) m.
Its geometric maximum depth is 2.0 m, but the Froude calculation uses hydraulic depth:

\[
D_h=\frac{12}{8}=1.5\ \text{m}
\]

The area-averaged velocity and wave celerity are

\[
\bar{V}=\frac{18}{12}=1.5\ \text{m/s}
\]

and

\[
c=\sqrt{(9.81)(1.5)}=3.84\ \text{m/s}
\]

Therefore,

\[
Fr=\frac{1.5}{3.84}=0.39
\]

The section-scale result is subcritical.
Substituting the 2.0 m geometric maximum depth would give a different number and would not follow the general-section definition.

## What subcritical downstream influence means

For positive downstream flow, shallow-water disturbances have characteristic speeds approximately \(\bar{V}+c\) and \(\bar{V}-c\) relative to the ground.
When \(Fr<1\), \(\bar{V}-c<0\), so one disturbance direction can travel upstream.
A downstream stage, constriction, confluence, or other control can therefore influence an upstream water-surface profile.

When \(Fr>1\), both characteristic speeds are downstream in this idealized one-dimensional interpretation.
An upstream condition then governs the entering information until a transition such as a hydraulic jump changes the regime.

This is why one-dimensional subcritical profile calculations begin from a downstream condition, while supercritical calculations begin from an upstream condition in [HEC-RAS boundary-condition guidance](https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.0/basic-data-requirements/steady-flow-data/boundary-conditions0).
It does not mean that every subcritical reach is controlled equally by every downstream boundary.
Distance, friction, geometry, tributaries, structures, regime transitions, and boundary placement determine how much influence reaches the area of interest.

A backwater effect is an upstream WSE response to downstream hydraulic control.
Calculating its complete profile requires the next-stage methods for resistance, normal depth, boundary conditions, and spatial integration.
This chapter establishes only the physical direction of possible influence.

## Current project relevance

**Current implementation:** Current scenarios pair an upstream `QFIX` discharge with downstream boundary handling.
ND scenarios use a slope-based downstream condition, while KWSE scenarios use transferred or fixed WSE information together with their documented edge handling.

**Scientific foundation:** Energy, momentum, and Froude reasoning explain why both upstream discharge and downstream hydraulic conditions can matter.
They do not prove that a selected slope, transferred WSE, domain extent, or edge treatment is scientifically adequate for a particular reach.

**Open question:** The terms `FREE`, freefall, and normal depth have unresolved relationships in project sources.
As recorded in [CONF-001](../reference/conflicts-and-open-questions.md#conf-001-boundary-condition-terminology-and-behavior), a code token or steep slope must not be interpreted as proof of a particular physical regime.

**Scientific foundation:** A section-wide Froude number is a diagnostic abstraction.
It should not be presented as proof that every cell or subsection in a two-dimensional model shares one regime or one direction of influence.

## Common misconceptions

### Energy conservation means energy loss is zero

Mechanical energy can be converted to heat and unresolved turbulence while total energy remains conserved at a broader thermodynamic level.
Open-channel energy equations represent that mechanical dissipation with loss terms.

### The energy equation replaces momentum conservation

Energy and momentum balances preserve different physical information.
Abrupt transitions, forces, and direction changes can require momentum reasoning even when an energy relation is also used.

### Water flows downhill according to bed slope alone

The WSE gradient supplies the gravity-pressure drive in the simplified momentum equation.
Bed elevation and depth combine to form WSE.

### Depth always equals hydraulic depth

The equality holds for a rectangular section.
General sections require \(D_h=A/T_w\) for the section-scale Froude relation.

### Subcritical means slow in an everyday sense

Subcritical compares mean flow speed with shallow-water wave celerity.
A hazardous high velocity can still be subcritical in sufficiently deep water.

### Subcritical flow proves a downstream boundary is correct

Subcritical flow permits downstream influence.
It does not validate the boundary value, its location, or its representation of the real control.

## Competency check

For a section with \(A=24\) m2, \(T_w=12\) m, and \(Q=48\) m3/s, calculate \(D_h\), \(\bar{V}\), wave celerity, and \(Fr\) using \(g=9.81\) m/s2.
State the diagnosed section-scale regime and the direction from which a boundary condition could influence the idealized profile.

Then explain why the result cannot establish any of the following claims by itself:

1. every part of the cross-section has the same velocity;
2. every two-dimensional cell has the same flow regime;
3. a current project boundary condition is physically correct; or
4. continuity, energy, momentum, and numerical convergence have all been satisfied.

## Source notes

- **Scientific foundation:** Energy-head terms and losses are supported by [SCI-017](../reference/bibliography.md#sci-017-hec-ras-energy-equation), and velocity-head correction is supported by [SCI-018](../reference/bibliography.md#sci-018-hec-ras-mean-kinetic-energy-head).
- **Scientific foundation:** Momentum, pressure, gravity, and boundary drag are supported by [SCI-019](../reference/bibliography.md#sci-019-hec-ras-momentum-equation), while the WSE-gradient and friction form is supported by [SCI-020](../reference/bibliography.md#sci-020-hec-ras-one-dimensional-hydraulic-equations).
- **Scientific foundation:** The simple and subsection-sensitive Froude interpretations are supported by [SCI-021](../reference/bibliography.md#sci-021-usace-subdivision-froude-number), while boundary-direction implications are supported by [SCI-022](../reference/bibliography.md#sci-022-hec-ras-flow-regime-boundary-guidance).
- **Current implementation and Open question:** Current boundary contracts are mapped under [JOB-004](../reference/bibliography.md#job-004-reach-topology-and-steady-forcing-contracts), and unresolved terminology is preserved in CONF-001.
- **Supporting reference:** *Open-Channel Hydraulics* remains supporting reading under SCI-001, but it was not directly inspected and no chapter or page citation is asserted.
