# Lab 5 Solution: Backwater and Boundaries

This solution demonstrates one evidence-disciplined answer to the synthetic exercise.
It does not select a project boundary, validate an implementation, or establish a universal rule for any reach class.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, and stopping criteria are defined in [Lab 5](../lab-05-backwater-and-boundaries.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and preserves boundary-condition conflicts rather than resolving them without authority.
Stop when the answer satisfies the prompt's competency criteria or records the exact evidence gap that blocks production use.

## 1. Reference-stage and Froude calculations

The normal-depth WSE is

\[
WSE_n=z_b+y_n=100.00\ \text{m SYN-1}+2.934\ \text{m}
=102.934\ \text{m SYN-1}
\]

The candidate known stage is higher by

\[
\Delta WSE=103.80-102.934=0.866\ \text{m}
\]

At normal depth, the rectangular area is

\[
A=by=(12.0\ \text{m})(2.934\ \text{m})=35.208\ \text{m2}
\]

Mean velocity is

\[
\bar{V}=\frac{Q}{A}=\frac{50.0\ \text{m3/s}}{35.208\ \text{m2}}
=1.420\ \text{m/s}
\]

For the rectangular section,

\[
D_h=\frac{A}{T_w}=\frac{35.208\ \text{m2}}{12.0\ \text{m}}
=2.934\ \text{m}
\]

The Froude number is

\[
Fr=\frac{1.420\ \text{m/s}}{\sqrt{(9.81\ \text{m/s2})(2.934\ \text{m})}}
\approx0.265
\]

The section-scale approximation is subcritical because \(Fr<1\).
If the datum-compatible 103.80 m WSE is imposed at the same downstream location, it is 0.866 m above the normal-depth reference.
The downstream depth should increase, and the raised profile can influence WSE upstream under the stated steady, subcritical, gradually varied assumptions.
The calculation does not establish the distance or magnitude of influence in a real two-dimensional domain.

## 2. Boundary-concept comparison

| Concept | Prescribed quantity or relationship | Continues throughout simulation? | Can directly act as downstream control? | Failure if misapplied |
| --- | --- | --- | --- | --- |
| Imposed discharge | A flux \(Q\), steady or time-varying. | Yes, for the configured boundary duration. | It can be an outflow specification, but it does not independently prescribe stage. | Wrong magnitude, sign, timing, or distribution breaks the water budget or forces an incompatible response. |
| Imposed WSE or known stage | WSE relative to a stated datum. | Yes. | Yes. | A datum, timing, location, or physical-control mismatch can impose a precise but false backwater condition. |
| Slope-based or normal-depth outflow | A friction slope used with geometry and roughness in a uniform-flow relation. | Yes. | Yes. | An unsuitable slope or placement can make the boundary too deep or too shallow and propagate error upstream. |
| Closed edge | Zero normal flux. | Yes. | Yes, as a no-flow constraint. | A real outlet can pool or reflect water if it is closed. |
| Transfer boundary | WSE information derived from another scenario and mapped to boundary geometry. | Yes. | Yes. | Incompatible scenario, terrain, datum, grid, or geometry can transfer the wrong stage or put it in the wrong place. |
| Initial condition or hot start | Water state at simulation start. | No, unless separately configured as a boundary. | No. | It can be mistaken for continuing control, and poor initialization can lengthen adjustment or create transient artifacts. |

Free outflow is incomplete because it does not specify whether the mathematics uses critical depth, a rating relation, zero-gradient extrapolation, normal depth, a steep slope, free overfall physics, or another solver-specific treatment.
An initial condition sets the starting state, while a downstream boundary continues to constrain or relate flux and state as the simulation advances.

## 3. Five context decisions and sensitivities

### Ordinary river reach

The slope-based normal-depth condition is the best-supported instructional candidate because no downstream stage evidence is supplied and the edge can be placed well below the interpretation area.
This remains conditional because only the final 500 m is described as prismatic, while the assumed adjustment distance is much longer.

A known-stage alternative would need observed or defensibly modeled WSE.
An unsupported high known stage could create excess backwater, while an unsuitable normal-depth slope could bias the boundary stage high or low.
The discriminating comparison is to vary plausible friction slopes and move the boundary farther downstream, then test whether WSE differences decay before the interpretation area.

### Tributary at a confluence

A known-stage or transfer condition from the compatible downstream scenario is the best-supported instructional candidate because the supplied mainstem WSE is

\[
104.60-103.10=1.50\ \text{m}
\]

above the tributary's local normal-depth estimate.
That difference indicates material possible backwater under subcritical conditions.

A local slope-based condition is an alternative for sensitivity, but it would likely understate downstream depth if the supplied confluence stage is valid.
The decision remains blocked for production because scenario pairing, timing, transfer geometry, asset identity, and observations are missing.
The discriminating comparison is a paired normal-depth and transferred-stage run with identical upstream forcing, evaluated along the tributary and against observed confluence WSE.

### Lake outlet

A stage condition spanning the observed lake level is the best-supported instructional candidate because the supplied lake range is 3.00 to 3.40 m above the local 103.00 m normal-depth estimate.
The range is

\[
106.40-106.00=0.40\ \text{m}
\]

and could require time-varying or multi-scenario treatment rather than one fixed value.

A slope-based alternative could be tested, but it would likely understate stage if the lake is hydraulically connected as described.
A closed boundary would likely trap river inflow unless the real outlet or storage process were represented elsewhere.
The discriminating comparison is a time-matched stage range versus plausible slope conditions, with lake-volume and outlet-flow checks.

### Coastal reach

A time-varying imposed stage is the best-supported physical candidate because a datum-compatible tide-and-surge series is supplied.
The stated range is

\[
104.00-102.20=1.80\ \text{m}
\]

and the possible 4-hour offset means that pairing only the two peaks can create a combination that never occurred.

A fixed-stage ensemble or slope-based outlet can support sensitivity comparisons but cannot reproduce the supplied timing by itself.
The discriminating comparison is to shift the coastal series within the supported timing uncertainty and compare maximum WSE, depth, flow reversal, and inundation in the river domain.
Production use remains blocked by the missing joint timing, wave and salinity scope, implementation support, and event validation.

### Structure-controlled reach

No generic boundary candidate is adequately supported because the structure's geometry, gate state, submergence, and operating rule are absent.
A known-stage or rating relation could be appropriate after the structure contract is established.
A normal-depth condition could bypass the physical control, and a closed edge could overstore water.
The discriminating comparison must first use a defensible structure representation or stage-discharge relation, then vary gate state and downstream submergence over the intended flow range.
Production use is blocked.

None of these conditional choices is a universal prescription for its context class.

## 4. Project terminology and behavior trace

**Selected methodology:** DR-003 is registered with Alternate Selected status and selects ALT-D for downstream-FIM-informed KWSE edge cells.
Its prose calls the condition freefall and explicitly contrasts it with normal-depth alternatives.

**Open question:** The standalone DR-039 file marks ALT-F `#current`, but the reviewed Decision Register contains no DR-039 row or registered status.
The file therefore cannot supply Selected methodology authority under the handbook rules.

**Current implementation:** `FreeBC` uses the token `FREE` and describes its numeric value as a normal-depth slope in m/m.
The token is not evidence that the solver applies a literal resistance-free fall.

**Current implementation:** The ND job estimates slope as the absolute terrain-elevation difference between centerline endpoint cells divided by reach length, then applies the configured minimum.

**Current implementation:** The KWSE job builds a `TRANSFER` condition from the downstream scenario's stage-transfer line, depth raster, terrain, grid properties, domain, and nominal boundary value.
The writer calculates WSE as downstream depth plus downstream terrain and writes an `HFIX` point only when the result is greater than zero.
It does not require positive depth, so a dry cell with positive terrain can produce a point, while zero or negative WSE is omitted.
The nominal `bc_value` participates in scenario labeling or identity but does not set the per-cell `HFIX` values.

**Current implementation:** The reviewed KWSE job also builds a `FREE` condition from the downstream scenario's inundation polygon with value \(0.5\ \text{m/m}\).
For each cardinal edge touched by the polygon, the writer emits one contiguous span between the overall bounds of all intersections on that edge.
That span includes dry gaps between disjoint intersections.

**Open question:** CONF-001 records that DR-003 freefall terminology and current slope-based `FREE` behavior do not have one consistent physical meaning.
A steep normal-depth slope can promote drainage at the edge, but the code token and value alone do not establish free-overfall physics.
The difference between the current contiguous span and DR-003's downstream-FIM-informed edge-cell wording remains open until the realized geometry and hydraulic response are validated.
Wet-cell filtering, zero or negative transferred WSE, source-target datum compatibility, and validation of the realized `HFIX` points also remain open.

## 5. Readiness statements

| Context | Instructional status | Physical evidence still needed | Implementation evidence still needed | Validation evidence still needed |
| --- | --- | --- | --- | --- |
| Ordinary river reach | Normal-depth candidate is conditionally supportable for comparison. | Energy slope, geometry continuity, roughness, and flow regime. | Exact edge geometry, submitted slope, roughness, and boundary placement. | Boundary-distance and slope sensitivity plus observed WSE comparison. |
| Confluence | Transfer or known-stage candidate is conditionally supportable for comparison. | Simultaneous tributary flow and mainstem WSE with confluence geometry. | Compatible scenario identity, transfer mapping, terrain, grid, and datum. | Paired-boundary sensitivity and observed confluence profile. |
| Lake outlet | Stage candidate is conditionally supportable for comparison. | Lake level timing, operations, bathymetry, wind, and outlet connection. | Supported stage representation and verified boundary geometry. | Lake-volume, outlet-flow, WSE, and sensitivity checks. |
| Coastal reach | Time-varying stage candidate is conditionally supportable physically but implementation support is unverified. | Joint river-coastal timing, datum, wave and salinity scope. | Verified time-series boundary contract in the selected current path. | Event hindcast and timing-sensitivity evidence. |
| Structure-controlled reach | Blocked even as a preferred boundary choice. | Structure geometry, operation, submergence, and stage-discharge behavior. | A supported structure or equivalent boundary contract. | Calibration and validation over the intended flow and operating range. |

**Overall readiness:** Every context is unready for production from the synthetic evidence alone.
The calculations establish useful directions and comparisons, not project authority or hydraulic validation.
