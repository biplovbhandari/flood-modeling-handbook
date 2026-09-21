# Lab 11 Solution: Trace a Two-Reach Stage Transfer

This solution derives the transfer only from [Lab 11](../lab-11-trace-stage-transfer.md) and the linked neutral handbook records.
The supplied evidence is synthetic.

## 1. Source binding and weights

The prepared network states that R-100 is immediately upstream of R-200.
River flow is downstream, but boundary information moves from completed R-200 source scenarios to the R-100 downstream boundary.

The upper-source weight is:

\[
w=\frac{102.5-102.4}{102.7-102.4}
=\frac{0.1}{0.3}
=\frac{1}{3}
\]

The lower-source weight is:

\[
1-w=\frac{2}{3}
\]

The nominal 102.5 m value is the interpolation coordinate in VD-1.
It is not substituted at every interface point.
Each realized point depends on two spatial source WSE values.

## 2. Mask and coverage result

P1 through P9 have valid terrain, valid depth, and strictly positive depth in both sources.
They belong to common wet support.
P10 is wet in Source L but dry in Source U because its Source U depth is zero.
P10 is therefore outside common wet support.

Coverage is:

\[
C=100\frac{9}{10}=90\%
\]

The method requires at least 90 percent, so the transfer passes the supplied coverage threshold exactly.
The denominator remains ten because all ten points define the intended interface.

Filling P10 from Source L alone would violate the common-support policy and change the method.
It would also hide the fact that the upper source represents that point as dry.
The record must omit P10 and retain that reason.

## 3. Point calculation table

At every common-wet point:

\[
\eta_L=z+h_L
\]

\[
\eta_U=z+h_U
\]

\[
\eta^*=\frac{2}{3}\eta_L+\frac{1}{3}\eta_U
\]

The resulting values are:

| Point | Source L WSE | Source U WSE | Interpolated target WSE |
| --- | ---: | ---: | ---: |
| P1 | 102.300 m | 102.600 m | 102.400 m |
| P2 | 102.320 m | 102.650 m | 102.430 m |
| P3 | 102.370 m | 102.700 m | 102.480 m |
| P4 | 102.420 m | 102.740 m | 102.527 m |
| P5 | 102.470 m | 102.800 m | 102.580 m |
| P6 | 102.520 m | 102.850 m | 102.630 m |
| P7 | 102.570 m | 102.900 m | 102.680 m |
| P8 | 102.610 m | 102.950 m | 102.723 m |
| P9 | 102.650 m | 103.000 m | 102.767 m |

For example, P4 gives:

\[
\eta^*_{P4}
=\frac{2}{3}(102.420)+\frac{1}{3}(102.740)
=102.526\overline{6}\ \text{m}
\]

The calculated minimum is 102.400 m and the calculated maximum is 102.767 m at the displayed precision.
Both match the scenario-record summary.
The range confirms that the realized boundary is spatial rather than a constant 102.5 m field.

## 4. Identity audit

The identity covers these contract groups.

| Group | Included evidence | Risk reduced |
| --- | --- | --- |
| Topology | Network N-1 and the R-100 to R-200 relationship | Transferring from the wrong reach or network version |
| Source provenance | Both source generations and their record, depth, and terrain integrity values | Using stale, changed, or ambiguous source scenarios |
| Spatial and datum compatibility | Transforms, bounds, SP-1, VD-1, depth definition, units, and nodata | Adding or interpolating misregistered or incompatible quantities |
| Mask and coverage policy | Source masks, common-wet rule, denominator, threshold, and omission reason | Treating dry or missing support as a valid boundary |
| Upstream forcing | R-100 model, 100 m3/s discharge, and inflow geometry | Reusing the boundary for a different upstream scenario |
| Initial and numerical state | INIT-DRY-1 and NS-4 | Hiding output-affecting state or numerical changes |
| Method and producer | ST-2 and PB-9 | Mixing results made under different transfer or producer behavior |

This inventory is complete relative to the supplied contract.
It cannot prove that the contract has identified every output-affecting variable that could exist in another implementation.
That limit is an evidence boundary rather than permission to infer an omitted field.

## 5. Publication and materialization assessment

Staged verification establishes that the five required U7 roles existed and matched their expected integrity values before promotion.
Atomic pointer promotion establishes which complete generation was intended to become visible.
The independent observation establishes that final storage contained one U7 generation, all required roles, matching integrity values, the requested identity, and the nine-point boundary summary.

Under the supplied observation contract, U7 is materialized.
The source generations were also observed with their required record, depth, terrain, references, units, and acceptance links before the transfer was constructed.

Materialization does not reveal the contents of the diagnostics artifact.
It does not establish convergence, conservation, local stability, edge adequacy, warm-start independence, numerical refinement, validation, or acceptance for the R-100 use.
[MX-010](../../reference/decision-code-artifact-crosswalk.md#mx-010-publication) and [MX-011](../../reference/decision-code-artifact-crosswalk.md#mx-011-materialization) require those distinctions.

## 6. Direct bounded verdict

**TRANSFER RECONSTRUCTED AND MATERIALIZED; HYDRAULIC ACCEPTANCE NOT ESTABLISHED.**

The packet supports the topology, source binding, interpolation weights, point calculations, mask policy, exact 90 percent coverage, identity trace, publication sequence, and complete materialization observation.
It does not support unrestricted scenario use.

The smallest packet for a new hydraulic acceptance review includes:

1. The complete diagnostics content, including convergence histories, full balance, local WSE histories, edge results, and process state.
2. Grid and time-step evidence for the quantities used downstream.
3. Sensitivity to initial state, the one omitted interface point, source-stage choice, and the 90 percent coverage boundary.
4. Evidence that the R-100 domain and transferred boundary remain adequate across the intended discharge and stage range.
5. Independent validation or a justified benchmark with compatible datum, spatial support, timing, uncertainty, and predeclared criteria.
6. An authorized acceptance decision with permitted and prohibited uses.

The favorable reconstruction and materialization results remain bounded evidence rather than hydraulic acceptance.
