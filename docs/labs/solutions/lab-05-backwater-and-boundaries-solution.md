# Lab 5 Solution: Backwater and Boundaries

This solution gives one evidence-bounded interpretation of the profiles in [Lab 5](../lab-05-backwater-and-boundaries.md).
Use it only after attempting the prompt.

## 1. Reference calculations

The normal-depth WSE is:

\[
WSE_n=z_b+y_n
=100.00 \text{m VD-1}+2.934 \text{m}
=102.934 \text{m VD-1}
\]

The specified stage is higher by:

\[
\Delta WSE=103.80-102.934=0.866 \text{m}
\]

The hydraulic area is:

\[
A=by=(12.0 \text{m})(2.934 \text{m})
=35.208 \text{m2}
\]

The mean velocity is:

\[
\bar{V}=\frac{50.0 \text{m3/s}}{35.208 \text{m2}}
=1.420 \text{m/s}
\]

The hydraulic depth is:

\[
D_h=\frac{35.208 \text{m2}}{12.0 \text{m}}
=2.934 \text{m}
\]

The Froude number is:

\[
Fr=\frac{1.420 \text{m/s}}{\sqrt{(9.81 \text{m/s2})(2.934 \text{m})}}
\approx0.265
\]

The section-average result is subcritical because \(Fr<1\).
Under the stated steady and gradually varied assumptions, the higher downstream stage should deepen the downstream profile and can influence WSE upstream.

## 2. Profile comparison

| Distance upstream | Specified-stage minus normal-depth WSE |
| ---: | ---: |
| 0 m | \(103.800-102.934=0.866 \text{m}\) |
| 250 m | \(103.880-103.184=0.696 \text{m}\) |
| 500 m | \(104.000-103.434=0.566 \text{m}\) |
| 1,000 m | \(104.280-103.934=0.346 \text{m}\) |
| 2,000 m | \(105.060-104.934=0.126 \text{m}\) |
| 4,000 m | \(106.950-106.934=0.016 \text{m}\) |

The largest supplied difference is 0.866 m at the downstream boundary.
The smallest is 0.016 m at 4,000 m.
The difference decreases with distance upstream in the supplied profile.

The farthest supplied station above a 0.10 m difference is 2,000 m upstream.
The 4,000 m difference is below 0.10 m.
The packet supports upstream influence across the supplied profile but cannot establish what happens beyond 4,000 m.

## 3. Numerical evidence

| Evidence category | What the packet supports | What remains missing |
| --- | --- | --- |
| Termination | Each profile reached its stated numerical profile tolerance. | The exact tolerance definition and independent reproduction are not supplied. |
| Last-iteration WSE change | The recorded maxima are 0.0007 m and 0.0009 m. | No intended-use acceptance threshold is supplied beyond the termination statement. |
| Discharge residual | The recorded maxima are 0.12 and 0.15 percent. | No spatial residual history or independent balance check is supplied. |
| Edge observation | No unexpected wet boundary location was reported. | The inspected geometry, complete edge map, and sensitivity to boundary placement are absent. |
| Numerical sensitivity | Nothing is supplied. | Geometry, roughness, step, discretization, and boundary sensitivity remain untested. |
| Physical validation | Nothing is supplied. | No observation or accepted benchmark is available. |

The two profiles are numerically completed under their supplied conditions.
That result does not choose which downstream condition represents a physical reach.
It also does not establish that either profile is insensitive to geometry, roughness, or boundary placement.

This separation follows [CQ-005](../../reference/conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence).

## 4. Boundary concepts

| Concept | Prescribed quantity or relationship | Continues through calculation? | Can directly control downstream state? | Failure if misapplied |
| --- | --- | --- | --- | --- |
| Imposed discharge | A steady or time-varying flux | Yes | It controls flux but does not independently prescribe stage. | Wrong magnitude, sign, timing, or distribution breaks the water budget or forces an incompatible response. |
| Specified stage | WSE relative to a stated datum | Yes | Yes | Datum, timing, location, or physical-control mismatch can impose false backwater. |
| Normal-depth outflow | A uniform-flow relationship using slope, geometry, roughness, and discharge | Yes | Yes | Unsuitable slope or placement can make the boundary too deep or too shallow. |
| Closed edge | Zero normal flux | Yes | Yes | A real outlet can pool or reflect water if closed. |
| Transferred stage | A spatial stage field derived from compatible downstream evidence | Yes | Yes | Incompatible scenario, datum, terrain, grid, or mapping can impose the wrong field. |
| Initial condition | Water state at the calculation start | No | No | It can be mistaken for continuing control, or it can create long adjustment transients. |

Free outflow is incomplete because it does not specify critical depth, normal depth, stage, extrapolation, overfall physics, or another mathematical relationship.
An initial condition sets the starting state.
A downstream boundary continues to constrain flux or water level as the calculation proceeds.

## 5. Context decisions

### Ordinary river reach

A normal-depth outflow is the best-supported instructional candidate because no observed stage is supplied and the boundary can be placed below the interpretation area.
The choice remains conditional because geometry continuity, energy slope, roughness calibration, and sensitivity are missing.

A specified-stage alternative would require observed or defensibly modeled WSE.
An unsupported high stage could create excess backwater.
An unsuitable normal-depth slope could bias WSE in either direction.

A useful sensitivity comparison would vary plausible slopes and boundary locations while checking whether profile differences decay before the interpretation area.

### Tributary at a confluence

A specified or transferred stage is the best-supported instructional candidate because:

\[
104.60-103.10=1.50 \text{m}
\]

The supplied downstream stage is 1.50 m above the tributary normal-depth estimate.
That difference indicates material potential backwater under the stated subcritical interpretation.

A local normal-depth outflow is a plausible sensitivity alternative.
It would likely understate downstream depth if the supplied confluence stage and pairing are valid.

Operational use remains blocked by time pairing, transfer geometry, source identity, uncertainty, and observation gaps.
A paired normal-depth and transferred-stage comparison under identical upstream forcing would discriminate between the candidates.

### Lake outlet

A specified-stage range is the best-supported instructional candidate because the observed lake range is 3.00 to 3.40 m above the 103.00 m normal-depth estimate.
The lake range is:

\[
106.40-106.00=0.40 \text{m}
\]

A normal-depth alternative would likely understate stage if the lake is connected as described.
A closed edge could trap inflow unless storage and outlet behavior were represented elsewhere.

A useful comparison would apply the observed stage range and plausible normal-depth alternatives while checking lake volume and outlet flow.
Operations, wind setup, bathymetry, timing, and connection geometry still block operational use.

### Coastal reach

A time-varying specified stage is the best-supported physical candidate because the packet supplies a datum-compatible tide-and-surge series.
The range is:

\[
104.00-102.20=1.80 \text{m}
\]

The possible 4 h offset means that pairing only the river and coastal peaks can create a combination that did not occur.
A fixed-stage ensemble or normal-depth outlet can support sensitivity work but cannot reproduce the supplied timing.
The likely error direction from either substitute is indeterminate from the packet.
Depending on the actual joint timing and river response, a substitute could overstate or understate backwater.

A useful comparison would shift the coastal series across the stated timing uncertainty and compare WSE, depth, flow reversal, and extent.
Wave scope, density effects, implementation evidence, and event validation remain missing.

### Structure-controlled reach

No preferred generic boundary is adequately supported.
The missing gate state, crest geometry, submergence, operating rule, and stage-discharge evidence define the control.

A specified-stage or rating relationship could become suitable after the structure behavior is established.
A normal-depth condition could bypass the control, while a closed edge could overstore water.

The discriminating comparison must first use a defensible structure representation and then vary gate state and downstream submergence.

## 6. Evidence labels

| Statement | Label | Reason |
| --- | --- | --- |
| A downstream control can influence an upstream subcritical profile. | **Scientific foundation** | The concept is supported by [SCI-022](../../reference/bibliography.md#sci-022-hec-ras-flow-regime-boundary-guidance) and [SCI-024](../../reference/bibliography.md#sci-024-hec-ras-downstream-boundary-conditions). |
| The specified-stage profile is 0.866 m above the normal-depth profile at the boundary. | **Applied example** | The difference follows from the supplied synthetic values. |
| Boundary records should preserve quantity, geometry, value, units, datum, and time support. | **Design principle** | Those fields prevent a boundary label from hiding incompatible behavior. |
| Both profiles terminated numerically but lack observation comparison. | **Evidence note** | The packet supports completion and states the missing validation evidence. |
| The correct boundary for the structure-controlled reach is unresolved. | **Open question** | The controlling structure evidence is absent. |

## 7. Readiness

| Context | Instructional status | Main evidence needed before operational use |
| --- | --- | --- |
| Ordinary river | Normal depth is conditionally supportable for comparison. | Energy slope, geometry, roughness, placement sensitivity, and observed WSE. |
| Confluence | Specified or transferred stage is conditionally supportable for comparison. | Time pairing, transfer mapping, compatible identity and datum, and observed confluence profile. |
| Lake outlet | Stage range is conditionally supportable for comparison. | Lake operations, bathymetry, timing, connection geometry, and validation. |
| Coastal reach | Time-varying stage is conditionally supportable physically. | Joint timing, supported time-series boundary, wave and density scope, and event validation. |
| Structure-controlled reach | Preferred boundary is blocked. | Structure geometry, operation, stage-discharge behavior, numerical representation, and validation. |

**NOT READY for operational boundary selection.**
The profiles support a clear teaching comparison, but every context lacks material physical, sensitivity, implementation, or validation evidence.
