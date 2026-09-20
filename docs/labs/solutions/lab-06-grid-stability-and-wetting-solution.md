# Lab 6 Solution: Grid Stability and Wetting

This solution demonstrates one evidence-disciplined answer to the synthetic exercise.
It does not select a project time step, threshold, grid, terrain pathway, solver configuration, or readiness decision.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, and stopping criteria are defined in [Lab 6](../lab-06-grid-stability-and-wetting.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and keeps illustrative thresholds outside project authority.
Stop when the answer satisfies the prompt's competency criteria or records the exact evidence gap that blocks production use.

## 1. CFL estimate and candidate comparison

The shallow-water gravity-wave speed is

\[
c=\sqrt{gh}
=\sqrt{(9.81\ \text{m/s2})(1.00\ \text{m})}
=3.132\ \text{m/s}
\]

The directional signal-speed estimates are

\[
|u|+c=1.50+3.132=4.632\ \text{m/s}
\]

\[
|v|+c=0.50+3.132=3.632\ \text{m/s}
\]

The x-direction teaching estimate is

\[
\Delta t_x
=\frac{(0.80)(20.0\ \text{m})}{4.632\ \text{m/s}}
=3.454\ \text{s}
\]

The y-direction teaching estimate is

\[
\Delta t_y
=\frac{(0.80)(20.0\ \text{m})}{3.632\ \text{m/s}}
=4.405\ \text{s}
\]

The controlling value is

\[
\Delta t_{est}=\min(3.454,4.405)=3.454\ \text{s}
\]

For \(\Delta t=3.0\ \text{s}\),

\[
C_x=\frac{(4.632)(3.0)}{20.0}=0.695
\]

\[
C_y=\frac{(3.632)(3.0)}{20.0}=0.545
\]

Both directional values are below the stated target of 0.80.

For \(\Delta t=4.0\ \text{s}\),

\[
C_x=\frac{(4.632)(4.0)}{20.0}=0.926
\]

\[
C_y=\frac{(3.632)(4.0)}{20.0}=0.726
\]

The 4.0 s candidate exceeds the stated x-direction target even though it remains below the y-direction target.
Therefore, 3.0 s is the only supplied candidate that satisfies this lab's target in both directions.

**Scientific foundation:** The calculation applies a shallow-water wave-speed and directional CFL teaching estimate.
It is not an exact LISFLOOD-FP or SFINCS rule because actual stability and adaptive stepping depend on each solver's grid geometry, variable placement, discretization, source treatment, wetting logic, and configured controls.

## 2. Wetting-transition volume account

The cell area is

\[
A_i=(20.0\ \text{m})(20.0\ \text{m})=400\ \text{m2}
\]

The supplied inflow volume is

\[
\Delta V=Q_{in}\Delta t
=(0.80\ \text{m3/s})(5.0\ \text{s})
=4.0\ \text{m3}
\]

With zero outflow and no source or sink, the constant-area teaching depth is

\[
h=\frac{\Delta V}{A_i}
=\frac{4.0\ \text{m3}}{400\ \text{m2}}
=0.010\ \text{m}
\]

The resulting WSE is

\[
\eta=z_b+h
=100.25+0.010
=100.260\ \text{m SYN-2}
\]

The depth exceeds the illustrative \(h_{on}=0.005\ \text{m}\), so the lab classifies the target cell as wet after the interval.
The same face flux is positive outward from the western cell and positive inward to the target cell.
When one shared exchange is applied with equal magnitude and opposite signs, it transfers 4.0 m3 between the pair without creating water.

If \(h_{on}=0.020\ \text{m}\), the calculated 0.010 m depth does not exceed the illustrative threshold.
The threshold comparison would still classify the cell as dry under the simplified rule.
Whether a solver retains the 4.0 m3 as sub-threshold storage, limits the flux, applies a thin layer, or uses another state transition is implementation-specific and cannot be inferred from this calculation.

The neighbor WSE of 100.30 m exceeds the illustrative face elevation of 100.20 m, so a wet face opening is plausible in the teaching geometry.
The supplied \(0.80\ \text{m3/s}\) flux is nevertheless a given and was not derived or validated from that head difference.

## 3. Resolution-pathway reasoning

The arithmetic mean of the three face samples is

\[
z_{mean}
=\frac{100.60+100.05+100.60}{3}
=100.4167\ \text{m SYN-2}
\]

At WSE \(100.30\ \text{m SYN-2}\), a one-value mean face at 100.4167 m appears closed.
It blocks the 10 m notch that the finer samples place below WSE.

The minimum is

\[
z_{min}=100.05\ \text{m SYN-2}
\]

A one-value minimum face appears open at WSE 100.30 m.
It can incorrectly treat the full 30 m width as open at the notch elevation and therefore create excess cross-face area or conveyance.

A supported subgrid relationship could represent 10 m of the face below the trial WSE and 20 m above it.
That representation better preserves the supplied width-elevation pattern, but it is not automatically correct.
Its interpolation, roughness, terrain quality, face placement, and solver equations still require evidence.

Shifting the 30 m grid origin by 10 m changes the grouping of 10 m source samples under a coarse face.
The notch can move into a neighboring face or be combined with different ridge samples even though nominal resolution remains 30 m.
Resolution alone therefore does not establish the pathway.

A reach centerline crossing the cell supplies network or geometry context.
It does not establish the face opening, below-water terrain, active mask, roughness, structure treatment, or actual flux connection.

## 4. Evidence-label table

| Statement | Label | Reason |
| --- | --- | --- |
| \(c=\sqrt{gh}\) is a shallow-water gravity-wave relation. | **Scientific foundation** | It follows from the hydrostatic shallow-water model under the stated assumptions. |
| The 3.0 s candidate satisfies this lab's directional target. | **Synthetic instructional calculation** | It follows only from the supplied values and chosen target. |
| The reviewed current scenario path invokes LISFLOOD-FP. | **Current implementation** | [JOB-003](../../reference/bibliography.md#job-003-current-implementation-locations) maps the checked-out run path. |
| A SFINCS reference in an interface proves current SFINCS execution. | **Unsupported conclusion**, contradicted by **Current implementation** | [CONF-003](../../reference/conflicts-and-open-questions.md#conf-003-sfincs-documentation-and-current-support) records interface references alongside a current writer that raises `NotImplementedError` and no equivalent execution path. |
| Every project grid should use a 0.005 m wetting threshold. | **Unsupported conclusion** | The number is only a **Synthetic instructional calculation**, while wetting controls are solver, grid, and application specific. |

No synthetic calculation is Selected methodology, Current implementation, Target design, or project Evidence or experiment.

## 5. Missing evidence and readiness statement

| Missing item | Category | Why it matters |
| --- | --- | --- |
| Actual cell and face geometry, including local small faces | Missing input | The controlling travel distance can differ from nominal resolution. |
| Complete affine transform and horizontal CRS | Missing input | Resolution alone does not establish location, alignment, or horizontal units. |
| Terrain units, vertical datum, lineage, and error | Missing input | False elevation differences can create or remove pathways and depth. |
| Terrain sampling and face-elevation method | Implementation check | Mean, minimum, and subgrid representations produce different openings. |
| Roughness source, scale, resampling, and face treatment | Implementation check | Resistance changes speed, flux, and wetting behavior. |
| Active, inactive, and boundary mask semantics | Implementation check | Masks determine which connections exist and where external flux enters. |
| Solver executable, version, equation set, and numerical formulation | Implementation check | The teaching estimate cannot define the operational stability rule. |
| Actual internal adaptive or fixed-step logic and limits | Implementation check | Output spacing and nominal settings may not equal internal steps. |
| Wetting and drying thresholds, hysteresis, flux limiting, and thin-water treatment | Implementation check | Near-dry state changes affect connectivity, speed, and storage. |
| Forcing, boundary geometry, values, timing, and provenance | Missing input | The hydraulic state and wave speed depend on the imposed problem. |
| Cell, boundary, source, sink, and domain-wide volume accounting | Numerical-validation result | A stable-looking wetting front can still lose or create water. |
| Paired spatial and temporal sensitivity evidence | Numerical-validation result | One grid and time step do not establish numerical adequacy. |
| Observed WSE, depth, extent, timing, or velocity evidence | Physical-validation result | Numerical consistency does not establish physical adequacy. |
| Accepted project criteria for time-step, pathway, and wetting sensitivity | Unresolved project decision | Readiness needs stated tolerances and decision scope. |

**Readiness:** The arithmetic is complete as a synthetic instructional calculation.
It is not ready to define a production time step, wetting threshold, terrain aggregation, domain pathway, or solver acceptance decision.
Production readiness remains blocked by the missing input, implementation, numerical-validation, physical-validation, and decision evidence listed above.
