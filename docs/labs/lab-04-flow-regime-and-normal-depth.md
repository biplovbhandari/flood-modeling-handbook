# Lab 4: Flow Regime and Normal Depth

This lab combines section geometry, discharge, Froude number, Manning conveyance, normal depth, and one-at-a-time sensitivity.
All numerical inputs are synthetic applied-example values.

## Prerequisites

Complete these chapters before starting:

- [Energy, Momentum, and Flow Regimes](../02-open-channel-flow/02-energy-momentum-and-flow-regimes.md)
- [Manning Flow and Normal Depth](../02-open-channel-flow/03-manning-flow-and-normal-depth.md)

Read [Lab Conventions](README.md), [Source Authority](../reference/source-authority.md), and [Equations and Units](../reference/equations-and-units.md).

## Learning objectives

After completing this lab, the learner should be able to:

- calculate area, top width, wetted perimeter, hydraulic radius, velocity, hydraulic depth, and Froude number;
- calculate Manning conveyance and discharge at a trial depth;
- solve normal depth by a reproducible bracket-and-refine method;
- explain sensitivity direction for discharge, roughness, slope, and width; and
- distinguish an equation result from evidence that a downstream boundary is suitable.

## Synthetic section packet

Use a rectangular channel with:

| Quantity | Symbol | Value |
| --- | --- | ---: |
| Bottom width | \(b\) | 10.0 m |
| Test depth | \(y\) | 2.00 m |
| Discharge | \(Q\) | 40.0 m3/s |
| Manning roughness | \(n\) | 0.035 s/m^(1/3) |
| Friction slope | \(S_f\) | 0.0010 m/m |
| Gravitational acceleration | \(g\) | 9.81 m/s2 |

Assume steady, one-dimensional, hydrostatic, uniform flow for the Manning calculation.
Assume a prismatic rectangular channel and one roughness value across the section.
These assumptions make the arithmetic reproducible but do not establish suitability for a natural reach.

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
State one limitation of applying one section-average classification to a compound channel or two-dimensional flow field.

## Part B: Manning capacity and normal depth

**Core inspection:** Calculate conveyance and Manning discharge at the 2.00 m test depth.

\[
K=\frac{1}{n}AR_h^{2/3}
\]

\[
Q_{calc}=KS_f^{1/2}
\]

Compare \(Q_{calc}\) with the target 40.0 m3/s.
Predict whether normal depth is above or below 2.00 m.

Use this reproducible bracket-and-refine procedure:

1. Calculate \(Q_{calc}\) at 2.90 m and 3.00 m.
2. Confirm whether those values bracket 40.0 m3/s.
3. Refine the bracket using 2.93 m and 2.94 m.
4. Report normal depth to the nearest 0.01 m.
5. State the rounding and model-form limitations.

At the calculated normal depth, recompute area, mean velocity, hydraulic depth, and Froude number.
Do not reuse the 2.00 m area or velocity.

## Part C: One-at-a-time sensitivity

**Core inspection:** Solve normal depth for each case while keeping every unlisted input at its baseline value.

| Case | Changed input |
| --- | --- |
| 1 | \(Q=60.0 \text{m3/s}\) |
| 2 | \(n=0.045 \text{s/m}^{1/3}\) |
| 3 | \(S_f=0.0005 \text{m/m}\) |
| 4 | \(b=15.0 \text{m}\) |

For each case, report normal depth to the nearest 0.01 m.
Explain the direction of change through area, hydraulic radius, conveyance, or slope.
State why one-at-a-time results do not quantify uncertainty for a natural reach.

## Part D: Interpret the boundary meaning

The synthetic boundary note says:

> Use the calculated normal depth at the downstream edge and call the edge a free outlet.

Review this note using [MX-001](../reference/decision-code-artifact-crosswalk.md#mx-001-boundary-conditions) and [CQ-001](../reference/conflicts-and-open-questions.md#cq-001-terminology-behavior-mismatch).

**Core inspection:** Rewrite the note so that it states the prescribed relationship, geometry, slope, roughness, units, and assumptions without using the ambiguous phrase free outlet.
Explain why a slope-based normal-depth outflow is not automatically a physical free overfall.
Explain how a specified-stage boundary differs from both concepts.
Classify the original terminology mismatch as an **Open question** until the intended mathematics and realized behavior are stated.

## Part E: Apply the five evidence labels

Classify each statement as **Scientific foundation**, **Applied example**, **Design principle**, **Evidence note**, or **Open question**.

1. \(Fr=|\bar{V}|/\sqrt{gD_h}\) is the stated section-scale Froude relation.
2. The baseline normal depth is about 2.94 m for the supplied geometry and parameters.
3. A boundary record should state its quantity, relationship, geometry, units, and reference.
4. The one-at-a-time table does not quantify the joint uncertainty of a natural channel.
5. The meaning of free outlet is unresolved until the boundary mathematics are defined.

## Part F: Missing evidence and readiness

List at least ten items needed before the calculation pattern could support a real downstream-boundary decision.
Include cross-section geometry, below-water terrain, roughness calibration, friction-slope evidence, discharge provenance, location and datum compatibility, flow-regime variation, boundary placement, boundary sensitivity, and observational validation.
Classify each item as a missing input, assumption, calibration observation, numerical-verification result, or physical-validation result.

## Deliverable

Submit a short answer with these sections:

1. Section geometry and flow regime.
2. Manning capacity and normal-depth solution.
3. Sensitivity table and interpretation.
4. Boundary-note rewrite and evidence labels.
5. Missing evidence and readiness statement.

## Competency criteria

The lab is complete when the answer:

- calculates \(A=20.0 \text{m2}\), \(P=14.0 \text{m}\), \(R_h\approx1.429 \text{m}\), and \(Fr\approx0.45\) at the test depth;
- calculates \(Q_{calc}\approx22.92 \text{m3/s}\) at the test depth;
- brackets and reports \(y_n\approx2.94 \text{m}\) for the baseline;
- reports the correct direction for all four sensitivity cases;
- distinguishes normal-depth outflow, free overfall, and specified stage; and
- states that the synthetic calculation alone cannot support a real boundary decision.

After completing the lab, compare the reasoning with [Lab 4 Solution](solutions/lab-04-flow-regime-and-normal-depth-solution.md).

## Source notes

- **Scientific foundation:** Froude-number interpretation is supported by [SCI-021](../reference/bibliography.md#sci-021-usace-subdivision-froude-number).
- **Scientific foundation:** Uniform-flow and normal-depth calculations are supported by [SCI-023](../reference/bibliography.md#sci-023-hec-ras-uniform-flow-computations).
- **Scientific foundation:** Roughness variability and calibration limits are supported by [SCI-040](../reference/bibliography.md#sci-040-usace-land-cover-and-mannings-n-guidance).
- **Evidence note:** The section, parameter values, and sensitivity cases are constructed teaching material.
