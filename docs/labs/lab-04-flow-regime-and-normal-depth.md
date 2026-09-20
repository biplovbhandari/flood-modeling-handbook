# Lab 4: Flow Regime and Normal Depth

This lab combines section geometry, discharge, Froude number, Manning conveyance, normal depth, and sensitivity direction.
All numerical inputs are synthetic instructional givens outside the project authority hierarchy.
They are not project observations, selected methodology, current implementation, target design, or validation evidence.

## Prerequisites

Complete these chapters before starting:

- [Energy, Momentum, and Flow Regimes](../02-open-channel-flow/02-energy-momentum-and-flow-regimes.md)
- [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md)

Read [Source Authority](../reference/source-authority.md) and follow the evidence-label rules in [Lab Conventions](README.md).
Use [Equations and Units](../reference/equations-and-units.md) for the required equations.

## Execution boundary

All required steps are **Core inspection**.
No command or production access is required, and any independent tooling remains optional and user-run without changing the evidence boundary.

## Learning objectives

After completing this lab, the learner should be able to:

- calculate area, top width, wetted perimeter, hydraulic radius, velocity, hydraulic depth, and Froude number;
- calculate Manning conveyance and discharge at a trial depth;
- solve normal depth by a reproducible bracket-and-refine method;
- explain the sensitivity direction for discharge, roughness, slope, and width; and
- separate synthetic calculation results from project-specific source claims.

## Synthetic instructional givens

Use a rectangular channel with:

| Quantity | Symbol | Value |
| --- | --- | ---: |
| Bottom width | \(b\) | 10.0 m |
| Test depth | \(y\) | 2.00 m |
| Discharge | \(Q\) | 40.0 m3/s |
| Manning roughness | \(n\) | 0.035 s/m^(1/3) |
| Friction slope | \(S_f\) | 0.0010 m/m |
| Gravitational acceleration | \(g\) | 9.81 m/s2 |

Assume steady, one-dimensional, hydrostatic flow for the section calculations.
Assume a prismatic rectangular channel and use the same \(n\) across the section.
These assumptions support the exercise but do not validate them for a natural reach.

## Part A: Section geometry and flow regime

**Core inspection:** At the 2.00 m test depth, calculate:

1. Hydraulic area \(A=by\).
2. Water-surface top width \(T_w=b\).
3. Wetted perimeter \(P=b+2y\).
4. Hydraulic radius \(R_h=A/P\).
5. Mean velocity \(\bar{V}=Q/A\).
6. Hydraulic depth \(D_h=A/T_w\).
7. Froude number \(Fr=|\bar{V}|/\sqrt{gD_h}\).

Show every substitution and unit.
Classify the section-scale result as subcritical, critical, or supercritical.
State one limitation of applying that single classification to a real compound or two-dimensional flow field.

## Part B: Manning capacity and normal depth

**Core inspection:** Calculate conveyance and Manning discharge at the 2.00 m test depth.

\[
K=\frac{1}{n}AR_h^{2/3}, \qquad Q_{calc}=KS_f^{1/2}
\]

Compare \(Q_{calc}\) with the target 40.0 m3/s and predict whether normal depth is above or below 2.00 m.

Then solve for normal depth with this reproducible procedure:

1. Calculate \(Q_{calc}\) at 2.90 m and 3.00 m.
2. Confirm that those values bracket 40.0 m3/s.
3. Refine the bracket using 2.93 m and 2.94 m.
4. Report normal depth to the nearest 0.01 m and state the remaining rounding limitation.

At the calculated normal depth, recompute mean velocity and Froude number.
Do not reuse the 2.00 m area or velocity.

## Part C: One-at-a-time sensitivity

**Core inspection:** Solve normal depth for each case while keeping every unlisted input at its baseline value.

| Case | Changed input |
| --- | --- |
| 1 | \(Q=60.0\ \text{m3/s}\) |
| 2 | \(n=0.045\ \text{s/m}^{1/3}\) |
| 3 | \(S_f=0.0005\ \text{m/m}\) |
| 4 | \(b=15.0\ \text{m}\) |

For each case, report normal depth to the nearest 0.01 m and explain the direction of change through area, hydraulic radius, conveyance, or slope.
State why these one-at-a-time results do not quantify uncertainty for a real reach.

## Part D: Project source-boundary trace

**Core inspection:** Read the following current-code locations without running a project command:

- [`get_normal_depth_boundary_condition`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/run_nd_scenarios.py)
- [`get_normal_depth_slope`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/jobs/run_nd_scenarios.py)
- [`FreeBC`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/models/solvers.py)
- [`MINIMUM_REACH_SLOPE`](https://github.com/NGWPC/twod-fim-jobs/blob/40192ef7cbb92e6847e6c4ecdc8ebf90b07b9c5e/twod_fim_jobs/consts.py)

Record four **Current implementation** observations:

1. The boundary token.
2. The meaning and unit of its numeric value.
3. The slope-estimation arithmetic.
4. The configured default minimum slope.

Then record one **Open question** from [CONF-001](../reference/conflicts-and-open-questions.md#conf-001-boundary-condition-terminology-and-behavior) and one from [CONF-008](../reference/conflicts-and-open-questions.md#conf-008-unregistered-dr-039-selection).
Do not use the synthetic normal depth as evidence that the project boundary is correct.

## Part E: Missing evidence and uncertainty

**Core inspection:** List at least eight items needed before the synthetic calculation pattern could support a production reach decision.
Include cross-section geometry, below-water terrain, roughness calibration, friction-slope evidence, discharge provenance, datum and location compatibility, flow-regime variation, and boundary-sensitivity evidence.

Classify each item as a missing input, assumption, calibration observation, implementation check, or validation result.

## Deliverable

Submit a short answer with these sections:

1. Section geometry and flow regime.
2. Manning capacity and normal-depth solution.
3. Sensitivity table and interpretation.
4. Project source-boundary trace.
5. Missing evidence and readiness statement.

## Competency criteria

The lab is complete when the answer:

- calculates \(A=20.0\ \text{m2}\), \(P=14.0\ \text{m}\), \(R_h\approx1.429\ \text{m}\), and \(Fr\approx0.45\) at the test depth;
- calculates \(Q_{calc}\approx22.92\ \text{m3/s}\) at the test depth;
- brackets and reports \(y_n\approx2.94\ \text{m}\) for the baseline;
- reports the correct direction for all four sensitivity cases;
- preserves `FREE`, freefall, normal-depth, and DR-039 authority distinctions; and
- states that the synthetic results are not production-ready evidence.

After completing the lab, compare the reasoning with [Lab 4 Solution](solutions/lab-04-flow-regime-and-normal-depth-solution.md).
