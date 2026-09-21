# Lab 1: Trace One Discharge Through the System

This lab traces one synthetic discharge from hydrologic evidence to a composite flood-depth product.
The goal is to identify each change in scientific meaning, representation, evidence, and responsibility.

## Prerequisites

Complete these chapters before starting:

- [What Is Flood Inundation Mapping?](../00-orientation/01-what-is-fim.md)
- [End-to-End Mental Model](../00-orientation/02-end-to-end-mental-model.md)
- [Quantities, Units, and Datums](../00-orientation/03-quantities-units-and-datums.md)

Read [Lab Conventions](README.md), [Source Authority](../reference/source-authority.md), [Glossary](../reference/glossary.md), and [Equations and Units](../reference/equations-and-units.md).

## Learning objectives

After completing this lab, the learner should be able to:

- preserve the meaning and units of discharge across hydrologic and hydraulic records;
- distinguish a model record, scenario definition, hydraulic response, scenario record, verified artifact set, library membership, and composite product;
- identify the evidence needed before moving from one claim to the next; and
- apply the five evidence labels without extending a synthetic packet beyond its stated scope.

## Synthetic network and forcing packet

Reaches `R-100` and `R-300` are immediate upstream neighbours of terminal reach `R-200`.
The forcing source is a synthetic event estimate for `R-200` at 09:00 on the event clock.

| Forcing item | Supplied value |
| --- | --- |
| Total discharge at the `R-200` model boundary | 250 m3/s |
| Contribution associated with the `R-100` inflow line | 148 m3/s |
| Contribution associated with the `R-300` inflow line | 90 m3/s |
| Named local contribution | 12 m3/s |
| Time support | One-hour average centred on 09:00 |
| Illustrative uncertainty interval for total discharge | 225 to 275 m3/s |
| Source limitation | No observation record, calibration record, or probability interpretation is supplied. |

The three contributions sum to the stated total.
The packet does not establish that the uncertainty interval is suitable for any external use.

## Synthetic model packet

The model record for `R-200` supplies:

- a prepared network in which `R-100` and `R-300` flow into `R-200`;
- terrain elevations in vertical datum `VD-1`;
- a roughness grid with a complete category lookup;
- 10 m square hydraulic cells in one projected horizontal reference;
- separate inflow lines for `R-100` and `R-300`;
- a named local-inflow geometry;
- a terminal downstream boundary region; and
- a model identity derived from the realized inputs and settings.

The model record contains no event discharge, downstream value, initial water state, or calculated flood response.

## Synthetic scenario and response packet

The scenario record supplies:

| Scenario item | Supplied value |
| --- | --- |
| Reach and model | `R-200` and the model described above |
| Scenario family | Discharge-only |
| Upstream forcing | 148, 90, and 12 m3/s through the three named input geometries |
| Downstream condition | Normal-depth outflow with slope 0.0015 m/m |
| Initial state | Dry active cells |
| Numerical setting | Saved output every 900 s |
| Requested outputs | Final depth, inundation geometry, diagnostic history, and scenario record |

The supplied hydraulic response contains:

| Response item | Supplied value |
| --- | --- |
| Final maximum depth | 3.20 m |
| Final flooded area | 1.84 km2 |
| Storage-change observation | The final saved interval satisfies the scenario's stated storage-change threshold. |
| Termination observation | The run controller requested a normal stop after the threshold was met. |
| Missing evidence | No complete inflow-outflow balance, grid sensitivity, boundary sensitivity, or observation comparison is supplied. |

The scenario record repeats the complete model identity, forcing allocation, downstream condition, initial state, numerical settings, output roles, diagnostic summary, and artifact integrity values.

## Synthetic materialization, library, and composite packet

A separate storage observation reports that the scenario record, final depth raster, inundation geometry, and diagnostic history are present at their recorded locations.
Each observed object has nonzero size and an integrity value matching the value in the scenario record.
This observation establishes presence and identity for the listed objects, but it does not establish hydraulic adequacy.

The response-library record lists selected `R-200` discharges of 100, 125, 175, 200, 225, and 250 m3/s.
It includes the 250 m3/s scenario because that value is the upper endpoint of the stated library range.
The record contains the complete selected set and an inclusion reason for every member.

The composite packet supplies compatible final-depth rasters for `R-100`, `R-300`, and `R-200`.
All three rasters use metres of depth, the same 10 m grid, the same horizontal reference and alignment, the same wet and dry convention, and the same nodata convention.
The stated overlap rule is pixelwise maximum depth, as described in [MX-012](../reference/decision-code-artifact-crosswalk.md#mx-012-compositing).
At one overlapping cell, the three depths are 1.10 m, 0.90 m, and 1.40 m.

## Part A: Check the forcing

**Core inspection:** Verify that the three contributions sum to 250 m3/s.
State the quantity, unit, reach, time support, and supplied uncertainty limit.
Explain why the total cannot be interpreted as a volume, WSE, water depth, instantaneous observation, or 1 percent AEP discharge.

## Part B: Separate model and scenario

**Core inspection:** List at least four assets or settings supplied by the model record.
List at least four scenario choices that the model record does not supply.
Explain why a complete model record is necessary but insufficient to define a hydraulic response.

## Part C: Trace discharge into spatial boundaries

**Core inspection:** Map each discharge contribution to its named input geometry.
Explain why a discharge magnitude without location, sign convention, and geometry does not completely define hydraulic forcing.
Check that the allocated discharge equals the source total without double counting the local contribution.

## Part D: Separate forcing, response, record, and materialization

**Core inspection:** Classify every scenario and response item as an imposed forcing, imposed condition, calculated response, diagnostic, provenance, or storage observation.
Explain why the final maximum depth is not the imposed discharge.
Explain why a scenario record is not proof that each named artifact is present.
Explain why verified presence and matching integrity do not establish numerical or hydraulic adequacy.

Use [MX-005](../reference/decision-code-artifact-crosswalk.md#mx-005-model-development), [MX-010](../reference/decision-code-artifact-crosswalk.md#mx-010-publication), and [MX-011](../reference/decision-code-artifact-crosswalk.md#mx-011-materialization) to check the distinctions.

## Part E: Evaluate library membership

**Core inspection:** State what the response-library record establishes about the 250 m3/s scenario.
State what it does not establish about the hydrologic bounds, numerical adequacy, hydraulic validity, or suitability for another event.
Explain why an executed scenario and a selected library member are different claims.
Use [MX-003](../reference/decision-code-artifact-crosswalk.md#mx-003-discharge-selection) to check the separation between attempted scenarios and selected membership.

## Part F: Calculate one composite cell

**Core inspection:** Apply the stated pixelwise-maximum rule to the three supplied depth values.
Carry the depth unit through the calculation.
List at least six compatibility checks that must hold before the same rule is applied across complete rasters.
Explain why a maximum-depth rule cannot be transferred without review to WSE, velocity, arrival time, or categorical hazard.

## Part G: Complete the trace table

Create one table with these columns.

| Step | Value or object | Scientific meaning | Representation or artifact | Evidence label | Required check before the next claim |
| --- | --- | --- | --- | --- | --- |
| 1. Discharge source |  |  |  |  |  |
| 2. Model record |  |  |  |  |  |
| 3. Boundary conditions |  |  |  |  |  |
| 4. Scenario definition |  |  |  |  |  |
| 5. Hydraulic response |  |  |  |  |  |
| 6. Scenario record |  |  |  |  |  |
| 7. Verified artifact set |  |  |  |  |  |
| 8. Library membership |  |  |  |  |  |
| 9. Composite product |  |  |  |  |  |

Use only **Scientific foundation**, **Applied example**, **Design principle**, **Evidence note**, and **Open question** as evidence labels.
Mark every row where scientific meaning, representation, responsibility, or evidence scope changes.

## Part H: State the evidence limits

**Core inspection:** Write one direct sentence for each supplied packet stating what it establishes.
Write one direct sentence for each packet stating the most important claim that it does not establish.
Record at least one **Open question** that must be resolved before operational use.

## Deliverable

Submit the completed trace table, the allocation and composite calculations, and the evidence-limit statements.

## Competency criteria

The lab is complete when the answer:

- verifies \(148+90+12=250 \text{m3/s}\);
- preserves discharge meaning, units, reach, and time support;
- distinguishes the model record from the scenario definition;
- distinguishes the calculated response from the scenario record and verified artifact set;
- explains why execution does not automatically confer library membership;
- calculates a composite depth of 1.40 m at the supplied cell;
- uses only the five public evidence labels; and
- records at least one unresolved evidence requirement.

After completing the lab, compare the reasoning with [Lab 1 Solution](solutions/lab-01-trace-the-system-solution.md).

## Source notes

- **Scientific foundation:** Discharge, depth, WSE, and datum concepts are defined in [Glossary](../reference/glossary.md) and [Equations and Units](../reference/equations-and-units.md).
- **Design principle:** [MX-001](../reference/decision-code-artifact-crosswalk.md#mx-001-boundary-conditions) separates physical boundary meaning from labels and records.
- **Evidence note:** Every numerical value and record in this prompt is constructed teaching material.
