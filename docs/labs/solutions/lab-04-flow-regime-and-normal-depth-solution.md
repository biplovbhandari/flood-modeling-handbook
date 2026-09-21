# Lab 4 Solution: Flow Regime and Normal Depth

This solution gives one reproducible answer to the synthetic section in [Lab 4](../lab-04-flow-regime-and-normal-depth.md).
Use it only after attempting the prompt.

## 1. Section geometry and flow regime

The hydraulic area is:

\[
A=by=(10.0 \text{m})(2.00 \text{m})=20.0 \text{m2}
\]

The water-surface top width is:

\[
T_w=b=10.0 \text{m}
\]

The wetted perimeter is:

\[
P=b+2y=10.0+2(2.00)=14.0 \text{m}
\]

The hydraulic radius is:

\[
R_h=\frac{A}{P}
=\frac{20.0 \text{m2}}{14.0 \text{m}}
=1.4286 \text{m}
\]

The mean velocity is:

\[
\bar{V}=\frac{Q}{A}
=\frac{40.0 \text{m3/s}}{20.0 \text{m2}}
=2.00 \text{m/s}
\]

The hydraulic depth is:

\[
D_h=\frac{A}{T_w}
=\frac{20.0 \text{m2}}{10.0 \text{m}}
=2.00 \text{m}
\]

The Froude number is:

\[
Fr=\frac{2.00 \text{m/s}}{\sqrt{(9.81 \text{m/s2})(2.00 \text{m})}}
\approx0.452
\]

The section-scale result is subcritical because \(Fr<1\).
A compound section can contain channel and floodplain subsections with different depths and velocities.
A two-dimensional domain can also contain different local flow regimes and directions.

## 2. Manning capacity and normal depth

At 2.00 m depth:

\[
K=\frac{1}{0.035 \text{s/m}^{1/3}}
(20.0 \text{m2})(1.4286 \text{m})^{2/3}
\approx724.82 \text{m3/s}
\]

\[
Q_{calc}=KS_f^{1/2}
=(724.82 \text{m3/s})(0.0010)^{1/2}
\approx22.92 \text{m3/s}
\]

The target discharge exceeds the capacity at 2.00 m.
Normal depth must therefore be greater than 2.00 m under the fixed assumptions.

The first bracket is:

| Depth | Area | Wetted perimeter | Hydraulic radius | Calculated discharge |
| ---: | ---: | ---: | ---: | ---: |
| 2.90 m | 29.00 m2 | 15.80 m | 1.8354 m | 39.28 m3/s |
| 3.00 m | 30.00 m2 | 16.00 m | 1.8750 m | 41.21 m3/s |

Because \(39.28<40.0<41.21 \text{m3/s}\), the root lies between 2.90 and 3.00 m.

The refined trials are:

| Depth | Calculated discharge |
| ---: | ---: |
| 2.93 m | 39.86 m3/s |
| 2.94 m | 40.05 m3/s |

The more precise numerical root is about 2.937 m.
The requested rounded result is:

\[
y_n\approx2.94 \text{m}
\]

The centimetre display precision does not imply centimetre-scale knowledge of a real channel or its parameters.

At \(y_n=2.937 \text{m}\):

\[
A=(10.0)(2.937)=29.37 \text{m2}
\]

\[
\bar{V}=\frac{40.0}{29.37}=1.362 \text{m/s}
\]

\[
D_h=2.937 \text{m}
\]

\[
Fr=\frac{1.362}{\sqrt{(9.81)(2.937)}}\approx0.254
\]

The normal-depth section remains subcritical in this section-average calculation.

## 3. Sensitivity

The same root calculation gives:

| Case | Calculated root | Rounded normal depth | Change from baseline | Directional explanation |
| --- | ---: | ---: | ---: | --- |
| Baseline | 2.937 m | 2.94 m | 0.00 m | Reference. |
| \(Q=60.0 \text{m3/s}\) | 3.927 m | 3.93 m | +0.99 m | Greater discharge requires greater area and hydraulic radius. |
| \(n=0.045 \text{s/m}^{1/3}\) | 3.512 m | 3.51 m | +0.57 m | Greater resistance reduces conveyance at a fixed depth. |
| \(S_f=0.0005 \text{m/m}\) | 3.762 m | 3.76 m | +0.82 m | A smaller square-root slope factor requires greater conveyance. |
| \(b=15.0 \text{m}\) | 2.114 m | 2.11 m | -0.83 m | Greater width supplies more area at a shallower depth. |

For the increased-discharge case:

\[
A=(10.0)(3.927)=39.27 \text{m2}
\]

\[
P=10.0+2(3.927)=17.85 \text{m}
\]

\[
R_h=\frac{39.27}{17.85}=2.199 \text{m}
\]

\[
K=\frac{1}{0.035}(39.27)(2.199)^{2/3}
\approx1897.37 \text{m3/s}
\]

\[
Q_{calc}=(1897.37)(0.0010)^{1/2}
\approx60.00 \text{m3/s}
\]

Each case changes one input while holding all others fixed.
A natural reach can have correlated uncertainty in geometry, roughness, slope, floodplain activation, and discharge.
The table therefore shows local sensitivity direction rather than a probability distribution.

## 4. Boundary-note review

A precise rewrite is:

> At the downstream edge, apply a slope-based normal-depth outflow using \(S_f=0.0010 \text{m/m}\), the stated geometry and roughness, SI units, and the steady uniform-flow assumption.

The rewrite states the prescribed relationship and its inputs.
It avoids claiming free-overfall physics.

A normal-depth outflow relates discharge, geometry, roughness, and friction slope under a uniform-flow approximation.
A free overfall is a physical control with rapidly varied or critical-flow behavior near an abrupt drop.
A specified-stage boundary directly imposes WSE in a stated datum.
The three concepts can produce different downstream depths and upstream influence.

**Open question:** The original phrase free outlet remains ambiguous until its intended mathematics, geometry, and realized behavior are stated.
That is the general terminology risk recorded in [CQ-001](../../reference/conflicts-and-open-questions.md#cq-001-terminology-behavior-mismatch).

## 5. Evidence labels

| Statement | Label | Reason |
| --- | --- | --- |
| The stated Froude relation classifies the supplied section average. | **Scientific foundation** | [SCI-021](../../reference/bibliography.md#sci-021-usace-subdivision-froude-number) supports the concept and its limits. |
| The baseline normal depth is about 2.94 m. | **Applied example** and **Evidence note** | The result follows from the supplied geometry and parameters only. |
| A boundary record should state quantity, relationship, geometry, units, and reference. | **Design principle** | Those fields are necessary to interpret the control. |
| The one-at-a-time table does not quantify joint uncertainty. | **Evidence note** | The calculation varies only one input at a time. |
| The phrase free outlet is unresolved without a mathematical definition. | **Open question** | The phrase alone does not identify the boundary behavior. |

## 6. Missing evidence and readiness

| Missing item | Category | Why it matters |
| --- | --- | --- |
| Full cross-section geometry | Missing input | Area and wetted perimeter control conveyance. |
| Below-water terrain or bathymetry | Missing input | Exposed terrain can omit main-channel conveyance. |
| Spatially suitable roughness | Assumption | One value can hide channel and floodplain differences. |
| WSE or high-water evidence for roughness calibration | Calibration observation | A tabulated value does not demonstrate effective resistance. |
| Friction-slope or energy-slope evidence | Missing input | Bed slope can differ from friction slope. |
| Discharge source, timing, and uncertainty | Missing input | The target flow controls the solved depth. |
| Horizontal location and vertical-reference compatibility | Assumption | Misalignment can create false elevations and slopes. |
| Flow-regime assessment over the boundary region | Physical-validation result | Boundary influence depends on regime and controls. |
| Realized boundary geometry and submitted values | Numerical-verification result | The calculation must match the actual numerical problem. |
| Boundary-location and boundary-value sensitivity | Numerical-verification result | Error can propagate into the interpretation area. |
| Observation or accepted-benchmark comparison | Physical-validation result | Algebraic consistency does not establish physical adequacy. |

**NOT READY for a real boundary decision.**
The arithmetic is reproducible for the supplied applied example, but the missing input, calibration, numerical-verification, and physical-validation evidence remains material.
