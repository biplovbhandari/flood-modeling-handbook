# Lab 1 Solution: Trace One Discharge Through the System

This solution shows one supported trace through the packet in [Lab 1](../lab-01-trace-the-system.md).
Use it only after attempting the prompt.

## 1. Forcing check

The allocation check is:

\[
148 \text{m3/s}+90 \text{m3/s}+12 \text{m3/s}
=250 \text{m3/s}
\]

The quantity is discharge for `R-200`, expressed as cubic metres per second.
Its time support is a one-hour average centred on 09:00.
The prompt supplies an illustrative total interval of 225 to 275 m3/s.

The value is not a volume because its unit contains inverse time.
It is not WSE or depth because those are lengths rather than flow rates.
It is not an instantaneous observation because the prompt defines one-hour support.
It is not a 1 percent AEP discharge because the prompt supplies no probability interpretation.

**Evidence note:** The arithmetic verifies internal allocation only.
It does not validate the source, the uncertainty interval, or suitability of 250 m3/s for an external use.

## 2. Model and scenario separation

The model record supplies terrain, roughness, hydraulic cells, two upstream inflow lines, local-inflow geometry, a downstream boundary region, network context, and model identity.
These items define the spatial realization in which a scenario can be calculated.

The model record does not supply the event discharge, allocation values, downstream slope, initial state, output interval, or requested outputs.
Those choices belong to the scenario definition.

**Design principle:** A reusable model realization is an input to a scenario rather than a flood response by itself.
The distinction is also stated in [MX-005](../../reference/decision-code-artifact-crosswalk.md#mx-005-model-development).

## 3. Boundary allocation

The spatial assignment is:

| Source contribution | Boundary geometry | Assigned discharge |
| --- | --- | ---: |
| `R-100` contribution | `R-100` inflow line | 148 m3/s |
| `R-300` contribution | `R-300` inflow line | 90 m3/s |
| Local contribution | Named local-inflow geometry | 12 m3/s |

A magnitude without geometry does not state where water enters the domain.
A geometry without magnitude does not state the imposed flow rate.
The sign convention is also necessary because the same numerical magnitude could mean inflow or outflow.

The allocation uses each supplied contribution once and sums to the source total.
It therefore passes the packet's internal water-input check.

## 4. Completed trace

| Step | Value or object | Scientific meaning | Representation or artifact | Evidence label | Required check before the next claim |
| --- | --- | --- | --- | --- | --- |
| 1. Discharge source | 250 m3/s for `R-200` at 09:00 | One-hour average flow rate with an illustrative interval of 225 to 275 m3/s | Synthetic forcing packet | **Applied example** and **Evidence note** | Confirm reach, unit, time support, source method, and uncertainty. |
| 2. Model record | Realized `R-200` terrain, roughness, grid, network, and boundary geometry | Spatial hydraulic problem without event forcing | Model record and listed model assets | **Applied example** | Confirm identities, references, geometry, and completeness. |
| 3. Boundary conditions | 148, 90, and 12 m3/s inflows plus 0.0015 m/m normal-depth outflow | Spatially located fluxes and a downstream relationship | Boundary portion of the scenario record | **Applied example** and **Design principle** | Confirm allocation, sign, geometry, units, slope meaning, and intended outlet. |
| 4. Scenario definition | Model plus forcing, downstream condition, dry start, output interval, and output requests | One complete calculation request | Scenario record before calculation | **Applied example** | Confirm that every output-affecting input is present in identity. |
| 5. Hydraulic response | 3.20 m maximum depth, 1.84 km2 flooded area, and a final storage-threshold observation | Calculated water state and diagnostics under the scenario | Depth raster, inundation geometry, and diagnostic history | **Evidence note** | Review numerical, balance, edge, sensitivity, and physical evidence. |
| 6. Scenario record | Inputs, identities, diagnostics, output roles, and integrity values | Provenance claim for one realized scenario | Published scenario record | **Evidence note** | Read the record and identify every required artifact without assuming presence. |
| 7. Verified artifact set | Four listed objects observed with nonzero size and matching integrity | Presence and identity of the expected generation | Storage observation plus scenario record | **Evidence note** | Separate materialization from hydraulic acceptance. |
| 8. Library membership | 250 m3/s included as the upper endpoint of the selected set | One accepted coordinate in a bounded response library | Library record with selected set and reasons | **Applied example** and **Evidence note** | Confirm bounds, selection rule, completeness, and intended use. |
| 9. Composite product | Compatible depths combined by pixelwise maximum | Product assembled from selected reach responses | Composite raster and source provenance | **Applied example** and **Design principle** | Confirm quantity, units, grid, context, identity, nodata, and overlap rule. |

Every transition changes at least one of scientific meaning, representation, responsibility, or evidence scope.
A valid claim at one row is not automatically sufficient for the next row.

## 5. Forcing, response, record, and materialization

The three discharges and downstream slope are imposed conditions.
The dry state is an imposed initial condition.
The 900 s saved-output interval is an imposed numerical and output setting.
The requested final depth, inundation geometry, diagnostic history, and scenario record are requested output roles in the scenario definition.
Those requested roles are not calculated responses or materialized artifacts until the calculation and storage observations supply that evidence.
Maximum depth and flooded area are calculated response summaries.
The storage-threshold observation and termination observation are diagnostics.
Model identity, input allocation, output roles, and integrity values are provenance.
The separate storage observation is materialization evidence.

The 3.20 m maximum depth is a length calculated from the water response.
It is not the imposed 250 m3/s discharge.

A scenario record states which artifacts should exist and records their integrity values.
The record alone cannot observe storage.
The separate storage observation establishes that the listed objects were present and matched their recorded integrity values.

Materialization still does not establish full mass balance, numerical verification, boundary sensitivity, observation agreement, or hydraulic adequacy.
That limit is explicit in [MX-010](../../reference/decision-code-artifact-crosswalk.md#mx-010-publication) and [MX-011](../../reference/decision-code-artifact-crosswalk.md#mx-011-materialization).

## 6. Library membership

The library record establishes that 250 m3/s is a selected member and the upper endpoint of the supplied `R-200` set.
It also establishes the complete selected discharge list stated in the prompt.

The record does not establish that 100 and 250 m3/s are scientifically correct hydrologic bounds.
It does not repair the missing mass balance, sensitivity, or observation evidence.
It does not establish suitability for another event or intended use.

Execution and membership are different because a completed scenario can remain outside the selected bounds, fail a quality criterion, or be redundant.
Membership needs a separate inclusion decision and complete library record.
The distinction is mapped in [MX-003](../../reference/decision-code-artifact-crosswalk.md#mx-003-discharge-selection).

## 7. Composite calculation

The supplied overlap values are 1.10 m, 0.90 m, and 1.40 m.

\[
h_{composite}=\max(1.10,0.90,1.40) \text{m}=1.40 \text{m}
\]

Before applying the rule across complete rasters, verify:

1. Every input is depth rather than WSE, velocity, arrival time, or a category.
2. Every depth uses metres.
3. Horizontal references match.
4. Grid resolution, alignment, transform, and bounds are compatible.
5. Wet, dry, mask, and nodata conventions are compatible.
6. Scenario meanings and library selections belong in the same product context.
7. Source identities and integrity values are preserved.
8. The pixelwise-maximum rule is stated for the intended product.

A maximum rule is quantity-specific.
Taking the maximum of WSE can require compatible elevation datums, velocity maxima can ignore direction, arrival-time maxima reverse the usual meaning of earliest arrival, and categorical hazard needs an ordered classification rule.

## 8. Evidence limits

**Applied example:** The forcing packet establishes the values and time support to use in this exercise.
It does not establish a real hydrologic source or probability model.

**Applied example:** The model and scenario packets establish a complete teaching configuration.
They do not establish external implementation behavior.

**Evidence note:** The response packet establishes the stated summary values and final storage-threshold observation.
It does not establish full numerical or hydraulic adequacy.

**Evidence note:** The storage observation establishes presence and matching integrity for the listed objects.
It does not establish scientific acceptance.

**Evidence note:** The library record establishes inclusion of 250 m3/s under the supplied selection record.
It does not validate the hydrologic bounds.

**Applied example:** The composite packet supports the 1.40 m cell calculation under the stated compatibility assumptions.
It does not prove that every cell or every contributing scenario is suitable.

**Open question:** Operational use remains unresolved until the forcing source, uncertainty, balance, sensitivity, boundary influence, validation evidence, intended-use criteria, and acceptance authority are supplied.
