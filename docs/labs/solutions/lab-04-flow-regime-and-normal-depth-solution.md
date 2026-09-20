# Lab 4 Solution: Flow Regime and Normal Depth

This solution demonstrates one reproducible answer to the synthetic exercise.
It does not validate a project reach, cross-section, roughness value, slope, boundary condition, or scenario.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, and stopping criteria are defined in [Lab 4](../lab-04-flow-regime-and-normal-depth.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and keeps synthetic calculations outside project authority.
Stop when the answer satisfies the prompt's competency criteria or records the exact evidence gap that blocks a production hydraulic decision.

## 1. Section geometry and flow regime

The rectangular hydraulic area is

\[
A=by=(10.0\ \text{m})(2.00\ \text{m})=20.0\ \text{m2}
\]

The water-surface top width is

\[
T_w=b=10.0\ \text{m}
\]

The wetted perimeter includes the bottom and both wet sides:

\[
P=b+2y=10.0+2(2.00)=14.0\ \text{m}
\]

The hydraulic radius is

\[
R_h=\frac{A}{P}=\frac{20.0\ \text{m2}}{14.0\ \text{m}}=1.4286\ \text{m}
\]

The mean velocity is

\[
\bar{V}=\frac{Q}{A}=\frac{40.0\ \text{m3/s}}{20.0\ \text{m2}}=2.00\ \text{m/s}
\]

The hydraulic depth is

\[
D_h=\frac{A}{T_w}=\frac{20.0\ \text{m2}}{10.0\ \text{m}}=2.00\ \text{m}
\]

The Froude number is

\[
Fr=\frac{2.00\ \text{m/s}}{\sqrt{(9.81\ \text{m/s2})(2.00\ \text{m})}}
=\frac{2.00}{4.429}
\approx0.452
\]

The section-scale result is subcritical because \(Fr<1\).
A real compound section can have main-channel and floodplain subsections with different depths and velocities, while a two-dimensional domain can have different local regimes and directions.
The single value does not classify every location.

## 2. Manning capacity and normal-depth solution

At the 2.00 m test depth, conveyance is

\[
K=\frac{1}{0.035\ \text{s/m}^{1/3}}
(20.0\ \text{m2})(1.4286\ \text{m})^{2/3}
\approx724.82\ \text{m3/s}
\]

The calculated Manning discharge is

\[
Q_{calc}=K S_f^{1/2}
=(724.82\ \text{m3/s})(0.0010)^{1/2}
\approx22.92\ \text{m3/s}
\]

The target 40.0 m3/s exceeds the capacity at 2.00 m.
The normal depth must be greater than 2.00 m under the fixed rectangular geometry, roughness, and slope.

The trial calculations are:

| Depth | Area | Wetted perimeter | Hydraulic radius | Calculated discharge |
| ---: | ---: | ---: | ---: | ---: |
| 2.90 m | 29.00 m2 | 15.80 m | 1.8354 m | 39.28 m3/s |
| 3.00 m | 30.00 m2 | 16.00 m | 1.8750 m | 41.21 m3/s |

Because \(39.28<40.0<41.21\ \text{m3/s}\), the root lies between 2.90 and 3.00 m.

The refined trials are:

| Depth | Calculated discharge |
| ---: | ---: |
| 2.93 m | 39.86 m3/s |
| 2.94 m | 40.05 m3/s |

Therefore,

\[
2.93<y_n<2.94\ \text{m}
\]

The more precise numerical root is about 2.937 m, so the requested result is

\[
y_n\approx2.94\ \text{m}
\]

Reporting 2.94 m does not imply centimetre-scale knowledge of the synthetic assumptions or a natural channel.
It is only rounded arithmetic for the stated inputs.

At \(y_n=2.937\ \text{m}\),

\[
A=(10.0)(2.937)=29.37\ \text{m2}
\]

\[
\bar{V}=\frac{40.0}{29.37}=1.362\ \text{m/s}
\]

For the rectangle, \(D_h=y_n=2.937\ \text{m}\), so

\[
Fr=\frac{1.362}{\sqrt{(9.81)(2.937)}}\approx0.254
\]

The normal-depth section remains subcritical in this section-scale approximation.

## 3. Sensitivity table and interpretation

The same root calculation gives:

| Case | Trial root | Area | Wetted perimeter | Hydraulic radius | Conveyance | Discharge check |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| Baseline | 2.937 m | 29.37 m2 | 15.87 m | 1.850 m | 1264.91 m3/s | 40.00 m3/s |
| \(Q=60.0\ \text{m3/s}\) | 3.927 m | 39.27 m2 | 17.85 m | 2.199 m | 1897.37 m3/s | 60.00 m3/s |
| \(n=0.045\ \text{s/m}^{1/3}\) | 3.512 m | 35.12 m2 | 17.02 m | 2.063 m | 1264.91 m3/s | 40.00 m3/s |
| \(S_f=0.0005\ \text{m/m}\) | 3.762 m | 37.62 m2 | 17.52 m | 2.147 m | 1788.85 m3/s | 40.00 m3/s |
| \(b=15.0\ \text{m}\) | 2.114 m | 31.71 m2 | 19.23 m | 1.649 m | 1264.91 m3/s | 40.00 m3/s |

For example, the greater-discharge case checks as

\[
A=(10.0\ \text{m})(3.927\ \text{m})=39.27\ \text{m2}
\]

\[
P=10.0+2(3.927)=17.85\ \text{m}
\]

\[
R_h=\frac{39.27}{17.85}=2.199\ \text{m}
\]

\[
K=\frac{1}{0.035}(39.27)(2.199)^{2/3}
\approx1897.37\ \text{m3/s}
\]

\[
Q_{calc}=(1897.37\ \text{m3/s})(0.0010)^{1/2}
\approx60.00\ \text{m3/s}
\]

The reported normal depths and directions are:

| Case | Normal depth | Change from baseline | Explanation |
| --- | ---: | ---: | --- |
| Baseline | 2.94 m | 0.00 m | Reference. |
| \(Q=60.0\ \text{m3/s}\) | 3.93 m | +0.99 m | Greater discharge requires greater area and hydraulic radius. |
| \(n=0.045\ \text{s/m}^{1/3}\) | 3.51 m | +0.57 m | Greater resistance reduces conveyance at a fixed depth. |
| \(S_f=0.0005\ \text{m/m}\) | 3.76 m | +0.82 m | The smaller square-root slope factor requires greater conveyance. |
| \(b=15.0\ \text{m}\) | 2.11 m | -0.83 m | Greater width supplies more area and changes wetted perimeter so the target conveyance occurs at a shallower depth. |

Each result changes only one input while holding all others fixed.
A natural reach can have correlated geometry, roughness, slope, floodplain activation, and discharge uncertainty.
This table shows local model sensitivity directions, not a probability distribution or validated uncertainty bound.

## 4. Project source-boundary trace

**Current implementation:** `get_normal_depth_boundary_condition` returns a `FreeBC` with the boundary token `FREE`.

**Current implementation:** `FreeBC.value` is described as a normal-depth slope in m/m.

**Current implementation:** `get_normal_depth_slope` takes the absolute difference between terrain values at centerline endpoint cells, divides by model reach length, and returns the larger of that ratio and the configured minimum.

**Current implementation:** The reviewed `MINIMUM_REACH_SLOPE` default is `1e-4`, which is dimensionless m/m in the boundary context.

**Open question:** CONF-001 records that DR-003 distinguishes freefall from normal depth while current code uses the `FREE` token for a slope-based normal-depth behavior and uses 0.5 m/m for KWSE edge handling.
The token, slope value, and selected-methodology term must not be treated as synonyms.

**Open question:** CONF-008 records that the standalone DR-039 file has a file-local `#current` marker but no row or registered status in the reviewed Decision Register.
It cannot be presented as selected methodology under the handbook authority model.

The synthetic calculation does not test the current code, reproduce its geometry, or validate its boundary condition.

## 5. Missing evidence and readiness statement

| Missing item | Category | Why it matters |
| --- | --- | --- |
| Surveyed or otherwise validated full cross-section geometry | Missing input | Area and wetted perimeter control conveyance. |
| Defensible below-water terrain or bathymetry | Missing input | Exposed terrain can omit main-channel conveyance. |
| Spatially and hydraulically appropriate roughness field | Assumption | One section-wide value can hide channel and floodplain differences. |
| Observed WSE or high-water evidence for roughness calibration | Calibration observation | A tabulated n value alone does not demonstrate effective resistance. |
| Friction-slope or energy-slope evidence near the boundary | Missing input | Bed slope can differ from friction slope outside uniform flow. |
| Discharge magnitude, timing, uncertainty, and provenance | Missing input | The target flow controls the solved depth. |
| Compatible horizontal location, CRS, vertical datum, and terrain reference | Assumption | Misalignment can turn elevations into false depths or slopes. |
| Flow-regime assessment across the boundary and interpreted area | Validation result | Downstream influence and boundary needs depend on regime. |
| Static check of the actual boundary geometry, slope, roughness, and token | Implementation check | The submitted model may differ from the intended calculation. |
| Boundary-location and plausible-value sensitivity results | Validation result | Results may remain contaminated by the boundary in the area of interest. |
| Comparison with observed or higher-fidelity hydraulic behavior | Validation result | Algebraic consistency does not prove physical adequacy. |

**Readiness:** The calculation is complete as a synthetic learning exercise.
It is not ready to support a production reach or boundary decision without the missing input, calibration, implementation, and validation evidence.
