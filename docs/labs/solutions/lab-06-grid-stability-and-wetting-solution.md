# Lab 6 Solution: Grid Stability and Wetting

This solution gives one reproducible answer to the packets in [Lab 6](../lab-06-grid-stability-and-wetting.md).
Use it only after attempting the prompt.

## 1. CFL estimate

The gravity-wave speed is:

\[
c=\sqrt{gh}
=\sqrt{(9.81 \text{m/s2})(1.00 \text{m})}
=3.132 \text{m/s}
\]

The directional signal-speed estimates are:

\[
|u|+c=1.50+3.132=4.632 \text{m/s}
\]

\[
|v|+c=0.50+3.132=3.632 \text{m/s}
\]

The x-direction teaching estimate is:

\[
\Delta t_x
=\frac{(0.80)(20.0 \text{m})}{4.632 \text{m/s}}
=3.454 \text{s}
\]

The y-direction teaching estimate is:

\[
\Delta t_y
=\frac{(0.80)(20.0 \text{m})}{3.632 \text{m/s}}
=4.405 \text{s}
\]

The controlling estimate is:

\[
\Delta t_{est}=\min(3.454,4.405)=3.454 \text{s}
\]

For \(\Delta t=3.0 \text{s}\):

\[
C_x=\frac{(4.632)(3.0)}{20.0}=0.695
\]

\[
C_y=\frac{(3.632)(3.0)}{20.0}=0.545
\]

Both directional values are below 0.80.

For \(\Delta t=4.0 \text{s}\):

\[
C_x=\frac{(4.632)(4.0)}{20.0}=0.926
\]

\[
C_y=\frac{(3.632)(4.0)}{20.0}=0.726
\]

The 4.0 s candidate exceeds the x-direction target.
The 3.0 s candidate is the only supplied value that satisfies the teaching target in both directions.

This result is not an exact solver rule.
An operational rule depends on the numerical formulation, variable placement, grid geometry, wetting treatment, source terms, and configured controls.

## 2. Wetting transition

The cell area is:

\[
A_i=(20.0 \text{m})(20.0 \text{m})
=400 \text{m2}
\]

The supplied inflow volume is:

\[
\Delta V=Q_{in}\Delta t
=(0.80 \text{m3/s})(5.0 \text{s})
=4.0 \text{m3}
\]

The constant-area depth is:

\[
h=\frac{\Delta V}{A_i}
=\frac{4.0 \text{m3}}{400 \text{m2}}
=0.010 \text{m}
\]

The resulting WSE is:

\[
WSE=z_b+h
=100.25+0.010
=100.260 \text{m VD-2}
\]

The depth exceeds the illustrative \(0.005 \text{m}\) threshold.
The simplified comparison therefore classifies the target cell as wet.

The face flux is outward from the western cell and inward to the target cell.
Applying the same 4.0 m3 exchange with opposite signs transfers water without creating it across the pair.

The calculated depth does not exceed the illustrative \(0.020 \text{m}\) threshold.
The simplified comparison would classify the cell as dry under that threshold.
Whether the 4.0 m3 is retained as sub-threshold storage, limited at the face, represented as thin water, or handled another way is solver-specific.

The neighbour WSE exceeds the illustrative face elevation, so an opening is plausible in the teaching geometry.
The 0.80 m3/s flux remains a supplied value rather than a flux derived from that head difference.

## 3. Terrain pathway

The arithmetic mean is:

\[
z_{mean}
=\frac{100.60+100.05+100.60}{3}
=100.4167 \text{m VD-2}
\]

At \(WSE=100.30 \text{m VD-2}\), a mean face at 100.4167 m appears closed.
It blocks the 10 m notch below the trial WSE.

The minimum is:

\[
z_{\min}=100.05 \text{m VD-2}
\]

A one-value minimum face appears open.
It can incorrectly treat the full 30 m width as open at the notch elevation.

A supported subgrid relationship can represent 10 m below the trial WSE and 20 m above it.
That representation preserves the supplied width-elevation pattern better than one mean or minimum.
It still requires evidence for interpolation, roughness, terrain quality, face placement, and numerical treatment.

Shifting the coarse grid origin by 10 m changes which fine samples fall under a coarse face.
The notch can move to another face or be grouped with different ridge samples even when nominal resolution remains 30 m.

A reach centerline crossing a cell supplies network context.
It does not establish face opening, below-water terrain, active-mask state, roughness, structure treatment, or actual flux.

## 4. Evidence labels

| Statement | Label | Reason |
| --- | --- | --- |
| \(c=\sqrt{gh}\) is a shallow-water gravity-wave relation under the stated assumptions. | **Scientific foundation** | The relationship is part of the shallow-water context described by the cited hydraulic sources. |
| The 3.0 s candidate satisfies this lab's directional target. | **Applied example** | The conclusion follows from the supplied values and selected target. |
| Grid and time-step sensitivity should precede a resolution decision. | **Design principle** | [SCI-029](../../reference/bibliography.md#sci-029-hec-ras-grid-size-and-time-step-guidance) supports joint space-time review. |
| The prompt supplies no solver run or mass-balance evidence. | **Evidence note** | The solver-evidence boundary states the absence directly. |
| Every solver and grid should use a 0.005 m wetting threshold. | **Open question** rejecting the proposed conclusion | The packet provides no basis for a universal threshold. |

Resolving the final question would require solver-specific equations and controls, grid and terrain evidence, volume handling, sensitivity results, and intended-use acceptance criteria.

## 5. Public-source scope

| Source | Supported concept | Unsupported extension |
| --- | --- | --- |
| [SCI-031](../../reference/bibliography.md#sci-031-lisflood-fp-local-inertial-formulation) | A peer-reviewed local-inertial formulation, face flux, friction, wetting, time stepping, and numerical-efficiency context | Exact behavior of an unnamed executable, version, grid, or scenario |
| [SCI-032](../../reference/bibliography.md#sci-032-sfincs-user-manual) | Solver-specific grid, mask, elevation, roughness, subgrid, CFL, wet-state, and numerical-parameter documentation for the stated release | An exact rule for another solver or proof that the lab values are accepted settings |
| [SCI-033](../../reference/bibliography.md#sci-033-lisflood-fp-user-manual) | Documented input, output, boundary, and initial-depth semantics for the stated historical release | Exact behavior, build, or validation status of another executable |
| [SCI-034](../../reference/bibliography.md#sci-034-sfincs-forcing-documentation) | Water-level and discharge forcing concepts, units, and time references for the stated release | Solver equivalence or validation of this teaching packet |

Public capability documentation supports scientific and software concepts within its stated scope.
It does not supply run evidence for this lab.

## 6. Missing evidence and readiness

| Missing item | Category | Why it matters |
| --- | --- | --- |
| Actual cells and face geometry | Missing input | The controlling travel distance can differ from nominal resolution. |
| Complete affine transform and horizontal reference | Missing input | Resolution alone does not establish location or alignment. |
| Terrain units, vertical datum, lineage, and error | Missing input | Elevation error can create or remove pathways. |
| Terrain sampling and face-elevation method | Implementation record | Mean, minimum, and subgrid choices produce different openings. |
| Roughness source, scale, resampling, and face treatment | Implementation record | Resistance changes speed, flux, and wetting. |
| Active-mask and boundary semantics | Implementation record | Masks determine connections and external flux locations. |
| Numerical engine, version, equations, and formulation | Implementation record | The teaching estimate cannot define the operational rule. |
| Actual internal time-step logic and limits | Implementation record | Saved-output spacing does not identify internal steps. |
| Wetting thresholds, hysteresis, flux limiting, and thin-water treatment | Implementation record | Near-dry treatment affects connectivity and storage. |
| Forcing and boundary values, geometry, timing, and provenance | Missing input | The hydraulic state depends on the imposed problem. |
| Complete volume accounting | Numerical-verification result | A plausible front can still create or lose water. |
| Paired grid and time-step sensitivity | Numerical-verification result | One resolution and one step do not establish adequacy. |
| Observation or accepted-benchmark comparison | Physical-validation result | Numerical consistency does not establish physical adequacy. |
| Predeclared tolerances and intended use | Acceptance criterion | Readiness requires a stated decision basis. |

**NOT READY for an operational time step, wetting threshold, terrain pathway, or hydraulic acceptance decision.**
The arithmetic is reproducible, but the missing input, implementation, numerical-verification, physical-validation, and acceptance evidence is material.
That boundary is consistent with [MX-002](../../reference/decision-code-artifact-crosswalk.md#mx-002-convergence) and [CQ-005](../../reference/conflicts-and-open-questions.md#cq-005-insufficient-convergence-evidence).
