# Manning Flow and Normal Depth

Manning's equation connects discharge, cross-section geometry, roughness, and friction slope under steady, approximately uniform open-channel flow.
Normal depth is the depth that satisfies that relationship for a specified discharge and channel description.

## Why this topic matters

A downstream slope boundary does not simply "let water out."
It uses an assumed friction slope together with local geometry and roughness to establish a depth-discharge relationship at the boundary.
An engineer must understand that relationship before interpreting a normal-depth scenario, changing a slope, or treating a computed water level as physical evidence.

## Prerequisites

Read [Conservation, Discharge, and Storage](01-conservation-discharge-and-storage.md) and [Energy, Momentum, and Flow Regimes](02-energy-momentum-and-flow-regimes.md).
Retain the distinction among bed elevation, WSE, geometric depth, hydraulic depth, discharge, and friction slope.

## Learning objectives

After this chapter, the reader should be able to:

- define hydraulic area, top width, wetted perimeter, hydraulic radius, conveyance, Manning's n, uniform flow, and normal depth;
- apply Manning's equation in SI units and check its dimensions;
- solve a normal-depth problem by reproducible iteration;
- predict how discharge, roughness, slope, and geometry change normal depth; and
- explain why Manning's n is a calibrated resistance parameter rather than a universal material constant.

## Geometry controls flow capacity

**Scientific foundation:** Hydraulic area \(A\) is the wetted cross-sectional area through which water flows.
The handbook also uses the shorter term wetted area when the cross-section context is clear.

The water-surface top width \(T_w\) is the width of the water surface across the section.
It is used to calculate hydraulic depth for the Froude number.

The wetted perimeter \(P\) is the length of the solid channel boundary in contact with water.
The free water surface is not part of the wetted perimeter.

Hydraulic radius is

\[
R_h=\frac{A}{P}
\]

- \(R_h\) is hydraulic radius in m.
- \(A\) is hydraulic area in m2.
- \(P\) is wetted perimeter in m.

Hydraulic radius measures hydraulic area relative to the resisting boundary.
It is not a geometric radius except for special shapes.
For a rectangular channel of bottom width \(b\) and depth \(y\),

\[
A=by, \qquad T_w=b, \qquad P=b+2y, \qquad R_h=\frac{by}{b+2y}
\]

The two vertical sides contribute to wetted perimeter.
Omitting them overstates hydraulic radius and therefore overstates conveyance.

## Manning's equation in SI form

For steady, approximately uniform flow, Manning's equation in SI form is

\[
Q=\frac{1}{n}AR_h^{2/3}S_f^{1/2}
\]

- \(Q\) is discharge in m3/s.
- \(n\) is Manning's roughness coefficient in s/m^(1/3) in the SI form used here.
- \(A\) is hydraulic area in m2.
- \(R_h\) is hydraulic radius in m.
- \(S_f\) is friction or energy slope in m/m and is dimensionless.

No additional 1.486 coefficient belongs in this SI equation.
The 1.486 coefficient is associated with a commonly used U.S. customary form, so mixing it with SI geometry produces incorrect dimensions and discharge.

The dimensional check is

\[
\left[\frac{1}{n}AR_h^{2/3}S_f^{1/2}\right]
=\left(\frac{\text{m}^{1/3}}{\text{s}}\right)
(\text{m2})(\text{m}^{2/3})(1)
=\text{m3/s}
\]

Define section conveyance as

\[
K=\frac{1}{n}AR_h^{2/3}
\]

so that

\[
Q=KS_f^{1/2}
\]

In this SI definition, \(K\) has units m3/s because slope is dimensionless.
Conveyance combines geometry and resistance at one water level.
It normally changes with depth because both area and hydraulic radius change.

The official HEC-RAS [uniform-flow documentation](https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/latest/stable-channel-design-functions/uniform-flow-computations) likewise treats depth, width, slope, discharge, and roughness as coupled Manning variables.

## Roughness is not a universal material constant

Manning's n is an empirical resistance parameter for a represented channel, floodplain, or model cell under stated conditions.
It can reflect surface texture, vegetation, channel irregularity, alignment, obstructions, bed forms, stage, discharge, seasonal condition, and the scale and simplification of the model.

The official HEC-RAS [energy-loss documentation](https://www.hec.usace.army.mil/confluence/rasdocs/ras1dtechref/6.2/basic-data-requirements/geometric-data/energy-loss-coefficients) identifies this variability and recommends calibration to observed WSE information when suitable evidence exists.
A lookup value based on land-cover class can be a starting estimate.
It is not proof that the value is correct at every depth, season, flow direction, or spatial resolution.

Increasing \(n\) reduces conveyance at a fixed depth.
If discharge, slope, and geometry remain fixed, the normal depth must therefore increase until the larger area and hydraulic radius restore the required conveyance.

## Uniform flow and normal depth

Uniform flow is an idealized condition in which depth, hydraulic area, and mean velocity do not change along the channel segment.
For steady uniform flow in a prismatic channel, the bed, water surface, and energy grade line are parallel, and the friction slope equals their common positive downstream fall in the adopted convention.

Normal depth \(y_n\) is the depth that satisfies Manning's equation for a specified discharge, geometry, roughness, and friction slope.
For a rectangle, it is the positive solution of

\[
Q=\frac{1}{n}(by_n)
\left(\frac{by_n}{b+2y_n}\right)^{2/3}
S_f^{1/2}
\]

This equation is nonlinear in \(y_n\).
A transparent solution can bracket the root with trial depths and then narrow the bracket until the discharge residual is acceptable.
A numerical solver uses the same physical relationship even when it applies a more efficient root-finding algorithm.

Normal depth is a reference hydraulic state.
It does not mean that every point in a natural reach actually has that depth.
Changes in geometry, slope, roughness, discharge, downstream stage, structures, confluences, and multidirectional floodplain flow can all produce nonuniform conditions.

## Worked normal-depth and sensitivity example

Consider a synthetic rectangular channel with these instructional givens:

- bottom width \(b=12.0\ \text{m}\);
- discharge \(Q=50.0\ \text{m3/s}\);
- Manning's \(n=0.035\ \text{s/m}^{1/3}\); and
- friction slope \(S_f=0.0010\ \text{m/m}\).

These values are synthetic.
They do not describe a real reach or recommend an operational roughness or slope.

At a trial depth of \(y=2.90\ \text{m}\),

\[
A=(12.0)(2.90)=34.80\ \text{m2}
\]

\[
P=12.0+2(2.90)=17.80\ \text{m}
\]

\[
R_h=\frac{34.80}{17.80}=1.955\ \text{m}
\]

\[
Q_{calc}=\frac{1}{0.035}(34.80)(1.955)^{2/3}(0.0010)^{1/2}
\approx49.1\ \text{m3/s}
\]

At \(y=3.00\ \text{m}\),

\[
A=36.00\ \text{m2}, \qquad
P=18.00\ \text{m}, \qquad
R_h=2.000\ \text{m}
\]

\[
Q_{calc}=\frac{1}{0.035}(36.00)(2.000)^{2/3}(0.0010)^{1/2}
\approx51.6\ \text{m3/s}
\]

The target discharge lies between the two calculated discharges, so \(2.90<y_n<3.00\ \text{m}\).
Further bisection or another stated root-finding method gives

\[
y_n\approx2.934\ \text{m}
\]

Substituting that depth gives \(Q_{calc}\approx50.0\ \text{m3/s}\), subject to the displayed rounding.

Repeat the same calculation after changing one input at a time.

| Case | Changed input | Calculated normal depth | Direction and reason |
| --- | --- | ---: | --- |
| Baseline | None | 2.934 m | Reference state. |
| Greater discharge | \(Q=75.0\ \text{m3/s}\) | 3.899 m | Depth rises because greater area and hydraulic radius are required. |
| Greater roughness | \(n=0.045\ \text{s/m}^{1/3}\) | 3.496 m | Depth rises because conveyance at a fixed depth falls. |
| Flatter slope | \(S_f=0.0005\ \text{m/m}\) | 3.739 m | Depth rises because the slope factor falls. |
| Wider rectangle | \(b=20.0\ \text{m}\) | 1.980 m | Depth falls because the wider section supplies more area and conveyance at a given depth. |

These directions assume that the other listed inputs and the rectangular geometry remain fixed.
Real geometry changes can alter wetted perimeter, compound-section activation, and local roughness at the same time, so "wider means shallower" is not a universal rule without a defined geometry change.

## Applying normal depth at a model boundary

**Applied example:** A synthetic model derives a candidate boundary-slope parameter from a 2.0 m terrain-elevation fall over a 4,000 m reach.
The terrain-slope estimate is \(S_{terrain}=0.0005\ \text{m/m}\).
The model record stores the estimate, the two sampled elevations, the reach length, the vertical datum, and any lower bound applied to the candidate parameter.
Only after site evidence supports the uniform-flow assumption may the model adopt this value as the friction slope \(S_f\) for a normal-depth outflow.

**Design principle:** Store descriptive boundary meaning and the evidence used to derive its value.
A generic label such as "free" is insufficient because normal-depth outflow and physical freefall are different hydraulic conditions.

**Open question:** Does the endpoint terrain slope represent the friction slope at the boundary, and is the boundary far enough downstream that plausible alternatives do not affect the interpretation area?
The question requires sensitivity and site evidence rather than a terminology decision.

**Scientific foundation:** A normal-depth calculation supplies a boundary relationship only under its assumptions.
It does not prove that a natural reach is uniform, that the estimated slope equals friction slope, that roughness is calibrated, or that the boundary is far enough from the study area.

## Common misconceptions

### Hydraulic radius is the channel's geometric radius

Hydraulic radius is area divided by wetted perimeter.
It can be computed for rectangular, trapezoidal, irregular, and compound sections that have no geometric radius.

### Manning's n belongs permanently to a land-cover class

Land cover can inform an initial spatial estimate, but effective resistance also depends on stage, geometry, vegetation condition, obstructions, scale, and calibration evidence.

### Bed slope always equals friction slope

The equality is part of the steady uniform-flow idealization.
Backwater, acceleration, geometry changes, and local controls can make the energy slope differ from the local bed slope.

### A normal-depth boundary proves the modeled reach is at normal depth

The boundary imposes or derives a local relationship at the model edge.
The interior can remain nonuniform and can be affected by inflow adjustment, geometry, roughness variation, and other controls.

### A steeper slope value means a more realistic outflow

A larger slope increases boundary conveyance for the same depth.
Its realism depends on evidence for the intended physical control, not on its ability to remove water quickly.

## Competency check

A rectangular channel is 8.0 m wide and carries 24.0 m3/s with \(n=0.032\ \text{s/m}^{1/3}\) and \(S_f=0.0015\ \text{m/m}\).
Write the area, wetted-perimeter, hydraulic-radius, conveyance, and Manning equations needed to solve for normal depth.
Then predict the direction of normal-depth change if \(Q\) rises, \(n\) rises, \(S_f\) rises, or the width rises while every other listed input remains fixed.

Your answer should also state why the direction for a real geometry change must be checked with the complete area and wetted-perimeter relationship.

## Practice

Complete [Lab 4: Flow Regime and Normal Depth](../labs/lab-04-flow-regime-and-normal-depth.md) to combine section geometry, Froude number, normal-depth iteration, sensitivity, and missing-evidence review.

## Source notes

- **Scientific foundation:** Manning flow, conveyance, and normal-depth calculations are supported by [SCI-023](../reference/bibliography.md#sci-023-hec-ras-uniform-flow-computations) and [SCI-024](../reference/bibliography.md#sci-024-hec-ras-downstream-boundary-conditions).
- **Scientific foundation:** Roughness variability and calibration context are supported by [SCI-025](../reference/bibliography.md#sci-025-hec-ras-energy-loss-coefficients).
- **Applied example:** The terrain-slope estimate is synthetic and demonstrates the provenance needed before adopting a friction slope for a normal-depth outflow.
- **Open question:** A derived terrain slope remains a hypothesis for friction slope until boundary sensitivity and site evidence support it.
