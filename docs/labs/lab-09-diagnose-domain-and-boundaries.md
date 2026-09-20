# Lab 9: Diagnose Domain and Boundaries

This lab uses a synthetic schematic and evidence packet to diagnose clipping, edge-water evidence, inflow placement, slope, STL overlap, datum, and identity risks.
All coordinates, elevations, and observations are instructional givens.
They are not project measurements, model results, acceptance thresholds, or proof of current production behavior.

## Prerequisites

Complete [Domain and Boundary Geometry](../04-model-development/04-domain-and-boundary-geometry.md), [The Build-Model Job](../04-model-development/05-build-model-job.md), and [Lab 8](lab-08-inspect-a-built-model.md).
Review [Backwater and Boundary Control](../02-open-channel-flow/04-backwater-and-boundary-control.md) and [Convergence, Mass Balance, and Hot Starts](../03-2d-hydraulics/04-convergence-mass-balance-and-hot-starts.md).

## Execution boundary

All required steps are **Core inspection**.
No command or production access is required, and any independent tooling remains optional and user-run without changing the evidence boundary.

## Learning objectives

After completing this lab, the learner should be able to:

- distinguish an intended outlet from unexpected wet perimeter cells;
- explain why absence of `edge_error` cannot prove acceptable boundaries;
- test whether an inflow line has enough evidence of connected conveyance;
- assess the sign and support of a slope estimate;
- assess STL containment, source-raster overlap, vertical datum, and scenario identity;
- identify mutable source and incomplete model-identity risks; and
- choose the next check that best discriminates among competing diagnoses.

## Synthetic schematic

The current model uses a 20 m square grid with bbox `[0, 0, 1000, 800]` m in a projected horizontal CRS.
The following diagram is not to scale.

```text
                         north y=800
       +--------------------------------------------------+
       | I                                                |
 west  | I   target reach start                           | east
 x=0   | I       o                                        | x=1000
       |          \                                       |
 wet   |           \                                      |
 edge  |            \                                     |
 cells |             \                                    |
       |              \                                   |
       |               \                                  |
       |                \                 source raster   |
       |                 \                +-----------+   |
       |                  \               |           |   |
       |                   o outlet        +-----------+   |
       |                                ========= STL      |
       +--------------------------------OOOOOOOOOOOOOOOOOO+
                         south y=0

       I = claimed inflow line near the reach start.
       O = intended south-edge outflow cells.
       Wet edge cells on the west side are not assigned an outlet role.
```

## Synthetic model and geometry packet

| Item | Supplied observation |
| --- | --- |
| Domain | Bbox `[0, 0, 1000, 800]` m, 20 m resolution, 50 columns, and 40 rows. |
| Target reach | Directed first to last coordinate from `(100, 700)` to `(900, 100)`, with recorded length 1,000 m. |
| Endpoint terrain | Terrain at the first coordinate is 100.0 m, and terrain at the last coordinate is 100.8 m. |
| Inflow line | Line from `(80, 660)` to `(80, 760)`. |
| Upstream mainstem | Caller supplies ID `U-17`, but its geometry and prepared-network row are not in the packet. |
| Inflow warning | The build manifest has no `centerline_inflow_multi_intersection` warning. |
| Largest scenario extent | One connected wet region touches the west edge from y=300 m through y=500 m and also reaches the intended south-edge outflow span. |
| Wet west-edge WSE | Values range from 103.1 m through 103.4 m. |
| Target endpoint WSE | WSE is 103.8 m at the first reach endpoint and 102.7 m at the last endpoint. |
| Scenario termination | `volume_convergence`; no `edge_error` is recorded. |
| Edge settings | `allow_water_on_edges` and persisted boundary-check activation details are not supplied. |
| Outflow area | The intended south-edge outflow span is x=760 m through x=1000 m. |
| STL geometry | A downstream-derived line runs along y=40 m from x=760 m through x=1000 m. |
| Downstream transfer raster | Valid source cells cover x=820 m through x=980 m and y=0 m through y=200 m. |
| STL source values | WSE values are labeled as meters in NAVD88. |
| Upstream terrain datum | The model manifest has no vertical-datum field, and no separate terrain metadata is supplied. |
| Downstream scenario identity | Its manifest names downstream reach `D-09`, but the packet has no prepared-network proof that `D-09` is downstream of the target. |
| Model source identity | DEM and LULC identity fields hash connection strings, and immutable source versions or source-byte checksums are not supplied. |
| Warnings | The model warnings list is empty. |

## Current-code facts supplied for the exercise

Use these current-code facts without running a job.

1. `get_normal_depth_slope` calculates the absolute endpoint terrain difference divided by recorded reach length and then applies a minimum of \(10^{-4}\).
2. The build warning checks only whether the inflow line intersects the target reach centerline more than once.
3. A missing `edge_error` does not show whether simultaneous convergence suppressed an edge reason, the first-grid sentinel returned, a dry downstream endpoint withheld the check, edge water was allowed, wet cells fell outside the implemented WSE classification, or a completed check found no flagged cells.
4. A transfer line must intersect valid downstream source cells before those source WSE values can support a cell-specific transfer.
5. A horizontal CRS match does not establish vertical-datum compatibility.
6. The model identity hashes source strings rather than source bytes.

## Part A: Diagnose domain clipping

**Core inspection:** State whether the west-edge observation is sufficient to accept or reject the domain.
Separate these claims.

1. State what is directly observed about wet-edge connectivity and boundary role.
2. State the leading clipping hypothesis.
3. State at least two competing explanations that the packet has not eliminated.
4. Explain how a closed west edge and an incorrectly opened west edge could bias the result differently.
5. State why `volume_convergence` does not resolve the clipping question.

Do not prescribe an expansion distance until evidence identifies the connected hydraulic pathway and intended boundary role.

## Part B: Interpret the edge evidence

**Core inspection:** Evaluate the statement, "No `edge_error` means the west edge is acceptable."

Use the supplied WSE ranges to determine whether west-edge WSE lies inside the inclusive range between the two target endpoint WSE values.
Then explain why that arithmetic still does not prove that the current check activated, that edge water was disallowed, or that the resulting classification was persisted.
Also explain why persisted `volume_convergence` does not exclude a simultaneous implemented edge violation on the same saved grid.

List the minimum boundary-check fields that should be persisted for a later acceptance review.

## Part C: Diagnose inflow geometry

**Core inspection:** Determine whether the supplied inflow line intersects the target reach segment.
Then answer these questions.

1. Is a zero target-reach intersection necessarily wrong for an inflow intended for upstream mainstem `U-17`?
2. What does the empty build warning list establish about the line?
3. Which missing geometry and topology evidence is required before accepting the inflow?
4. Which rasterized evidence is required before accepting discharge distribution across grid cells?

## Part D: Diagnose slope

**Core inspection:** Calculate the slope value that current code would use.
Carry units through the calculation.

Then interpret the sign information that the absolute difference removes.
State why a positive scalar can still be suspicious when the directed reach's last endpoint is higher than its first endpoint.
State why the m/m interpretation depends on the packet's metre-based projected horizontal CRS and why current code's positive-EPSG check would not prove that condition for a real model.
List the next evidence needed to decide whether the issue is reach direction, terrain sampling, a flat or adverse bed, a structure, or another local condition.

## Part E: Diagnose STL overlap, datum, and identity

**Core inspection:** Evaluate the STL in three separate dimensions.

1. Calculate how much of the 240 m STL length lies within the supplied downstream raster x coverage.
2. State whether geometric overlap alone makes the line usable.
3. Identify the vertical-datum evidence required before adding downstream depth and elevation or applying transferred WSE.
4. Identify the topology and scenario-provenance evidence required before accepting downstream reach `D-09` as the source.
5. Explain why source-string identity does not rule out changed DEM or LULC content.

## Part F: Choose the next discriminating check

**Core inspection:** Choose exactly one immediate next check and defend the choice.

- **A.** Expand every domain edge by the full DR-012 maximum before inspecting connectivity.
- **B.** Map the connected wet component, cell-by-cell boundary roles, terrain, WSE, and flow direction along the west edge and trace that component back to the reach and intended outlet.
- **C.** Accept the domain because the scenario terminated on volume convergence.
- **D.** Increase roughness everywhere and rerun without resolving geometry, datum, or boundary evidence.

The best choice should distinguish clipping from isolated or intentionally bounded edge water before committing to an expansion or parameter change.

## Part G: Issue a readiness verdict

**Core inspection:** Choose exactly one verdict.

- `READY FOR HYDRAULIC INTERPRETATION` means the packet establishes domain, inflow, outflow, slope, STL, datum, identity, and edge adequacy.
- `NOT READY` means one or more of those categories remain unsupported or contradictory.

State the verdict first.
Then list the smallest follow-up evidence set that could change it.

## Deliverable

Submit a short answer with these sections:

1. Domain-clipping diagnosis.
2. Edge-evidence assessment.
3. Inflow and slope calculations.
4. STL, datum, and identity assessment.
5. Selected next discriminating check.
6. Direct readiness verdict.

## Competency criteria

The lab is complete when the answer:

- treats connected water on the unintended west edge as unresolved clipping evidence rather than automatic acceptance or automatic expansion;
- does not infer a completed clean edge check from the missing `edge_error` or persisted `volume_convergence`;
- identifies that the west-edge WSE values lie within the supplied endpoint-WSE range but preserves the missing activation and configuration evidence;
- identifies zero target-reach intersection and requests the exact `U-17` geometry and topology before judging the upstream-mainstem inflow;
- calculates a current code slope of 0.0008 and explains that `abs` removes the adverse sign;
- distinguishes the synthetic metre-based CRS assumption from current positive-EPSG validation;
- calculates 160 m of valid x overlap out of a 240 m STL, leaving 80 m outside supplied source coverage;
- preserves the unknown upstream vertical datum and mutable source-content identity risks;
- selects check B as the next discriminating check; and
- issues `NOT READY`.

After completing the lab, compare the reasoning with [Lab 9 Solution](solutions/lab-09-diagnose-domain-and-boundaries-solution.md).
