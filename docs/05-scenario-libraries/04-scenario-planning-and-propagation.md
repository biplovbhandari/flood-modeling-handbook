# Scenario Planning and Propagation

Scenario planning determines which upstream discharge and downstream stage combinations should be simulated, which completed downstream scenario supplies each boundary, and which existing scenario supplies each hot start.
Network propagation orders that work from terminal or downstream reaches toward headwaters because upstream stage libraries depend on completed downstream hydraulic results.

## Why this topic matters

The KWSE execution job can run only the list it receives.
It cannot know whether the list spans selected scientific bounds, uses a valid downstream neighbor, respects lake or coast policy, or can be reproduced after a downstream library changes.

Planning is therefore a scientific and orchestration responsibility rather than a hidden solver detail.
The plan needs durable evidence because skipped stage targets, confluence assumptions, waterbody breaks, and changed downstream sources affect which maps can later be assembled.

## Prerequisites

Read [Watersheds, Reaches, and Hydrofabrics](../01-hydrology-for-fim/01-watersheds-reaches-and-hydrofabrics.md), [Flood Frequency, AEP, and Bounds](../01-hydrology-for-fim/04-flood-frequency-aep-and-bounds.md), [Network Preparation](../04-model-development/01-network-preparation.md), [Adaptive Discharge Selection](02-adaptive-discharge-selection.md), and [KWSE and Stage Transfer](03-kwse-and-stage-transfer.md).

## Learning objectives

After this chapter, the reader should be able to:

- explain why hydraulic dependencies propagate downstream to upstream while water flows upstream to downstream;
- separate discharge bounds, an adaptive ND `q_set`, downstream-stage bounds, stage-grid targets, and bound source runs;
- describe DR-032 and DR-033 without presenting unresolved planner extensions as registered methodology;
- explain topological ordering at terminal reaches and confluences;
- state why lake and coastal policies interrupt ordinary reach-to-reach propagation;
- distinguish current modeling-job execution from checked-in reconciler prototype behavior and target design; and
- identify the evidence needed to prove a planned library is materialized.

## Authority boundary

**Selected methodology:** DR-001 ALT-A selects KWSE scenarios for all reaches.
DR-032 ALT-D selects a lower bound from the downstream reach's minimum STL WSEL at the nearest downstream discharge below the current reach's discharge and an upper bound from the downstream reach's maximum WSEL.
DR-033 ALT-B selects a per-reach zero-anchored stage grid with increments from `{0.25, 0.5, 1, 2, 5}` m and binds each target to a downstream run within half an increment.
These records have Alternate Selected status.

**Selected waterbody methodology:** DR-005 ALT-C selects both KWSE and reach-slope ND treatment for lake and coastal reaches.
DR-006 ALT-E selects steep-slope handling on downstream-waterbody-informed edge cells.
DR-007 ALT-A selects trimming and tagging against a dead-pool polygon.
DR-008 ALT-B selects intersection of the model domain and waterbody polygon boundary as lake or coastal STL geometry.
DR-034 and DR-035 are Needs-Review, DR-036 is Draft with no alternatives, DR-037 ALT-B treats lakes as Flows2FIM breakpoints, and DR-038 ALT-A selects a coastal trimming and downstream-cascade approach.

**Open question:** The reviewed Decision Register and the standalone DR-006 marker identify ALT-E, while the latest standalone decision-history line says the method switched to ALT-D.
The registered ALT-E selection remains the methodology statement within its recorded status and scope, but the history conflict requires authorized governance cleanup.

**Current modeling jobs:** `build_model`, `run_nd_scenarios`, and `run_kwse_scenarios` consume prepared inputs and execute their bounded job contracts.
They do not perform network-wide scenario planning or propagation.

**Checked-in reconciler prototype:** The deployment checkout contains current local code for gap calculation, planning, payload construction, submission grouping, manifest observation, and materialized rows.
That code is outside `twod-fim-jobs`, is unpinned in the reviewed handbook evidence, cites decision numbers absent from the reviewed Decision Register, and does not prove deployed operation.

**Target design:** The system-design guide and propagation document assign desired-state comparison, content-addressed lookup, job scheduling, storage verification, and upstream cascade to a reconciliation loop.
Target design is not verified deployment evidence.

## Five different sets and bounds

Planning becomes easier to reason about when five quantities remain separate.

### 1. Authored discharge bounds

The selected DR-029 rule defines lower and upper discharge bounds before the ND job runs.
The current ND job receives those bounds as whole-m3/s inputs and does not derive their hydrologic provenance.

### 2. Emergent ND discharge set

The adaptive ND algorithm simulates and re-judges candidate discharges.
The target design treats its selected `q_set` as emergent and expects a controller to observe whether it spans authored bounds with acceptable density.

Published ND trials are a broader set than selected members.
A planner must consume an explicit selected set rather than infer membership from every scenario directory in storage.

### 3. Downstream-stage bounds for each upstream discharge

DR-032 ALT-D makes the lower bound depend on the downstream reach's stage behavior at a nearby discharge.
It deliberately does not floor the lower bound with the upstream reach's own ND stage because an approximate slope can create an artificial water-surface bump.

The registered alternative describes the upper bound as the downstream reach's maximum WSEL.
The checked-in prototype applies additional discharge-conditioned ceiling logic that cites DR-043 through DR-045, which are absent from the reviewed Decision Register.
That prototype logic must be described as prototype behavior rather than selected methodology.

### 4. Stage-grid targets

For stage increment \(\Delta z\), DR-033 uses a grid anchored to zero rather than anchored to a reach-specific lower value.
A target can be represented as:

\[
z_k=k\Delta z, \qquad k\in\mathbb{Z}
\]

The selected menu is \(\Delta z\in\{0.25,0.5,1,2,5\}\) m.
The record rounds lower and upper bounds to the nearest grid value and steps between them.

For example, a range from 224.2 through 227.1 m with \(\Delta z=1\) m produces targets 224, 225, 226, and 227 m.
The grid target is a planning coordinate, not necessarily a spatially uniform boundary imposed on every cell.

### 5. Bound downstream source runs

DR-031 requires cell-specific WSE from an actual downstream simulation.
DR-033 binds a target stage to the downstream simulation whose nominal stage is nearest, provided the distance is no more than \(\Delta z/2\).

A target with no qualifying run is skipped as a gap in downstream sampling.
The skip should be preserved as plan evidence because silent removal would make the intended grid and realized plan indistinguishable.

The source run carries at least two different stage values.
Its achieved upstream-end nominal WSE is the value used to compare with a target, while its imposed downstream boundary value is the value used in its storage folder.
Neither value can be substituted for the other without changing the plan or source address.

## Dependency direction

River water and discharge normally move from upstream toward downstream.
The hydraulic information needed for KWSE libraries moves in the opposite dependency direction.

```text
physical flow:          headwaters  ->  outlet
library dependency:    headwaters  <-  outlet
```

The downstream model must finish first because an upstream run needs a real downstream depth raster, terrain raster, inundation polygon, STL, grid, domain, run identity, and scenario manifest.
This is dependency propagation, not hydrologic routing of a time-varying hydrograph.

## Topological ordering

A directed acyclic reach network can be processed in reverse topological order for hydraulic library dependency.
Terminal reaches establish the starting frontier because they have no ordinary modeled downstream reach.

After a terminal or downstream reach's required outputs are observed, each immediate upstream reach can be reconsidered.
The wave continues toward headwaters only after every required downstream dependency for each reach is satisfied.

A correct ordering algorithm needs authoritative direction and adjacency from the prepared reach network.
Line orientation on a map and the current model manifest's `downstream_reach_id` field are insufficient because current build code populates that field incorrectly from the modeled reach ID.

## Confluences

A confluence has multiple upstream reaches feeding one downstream reach.
Each upstream reach uses the common downstream reach as its hydraulic boundary source under ordinary reach-to-reach propagation.

This does not mean every tributary stage scenario is statistically compatible with every mainstem condition.
DR-033 records joint-frequency analysis as a possible alternative but keeps the zero-anchored stage-grid method selected.

The planner must keep these questions separate:

- Which downstream reach is topologically adjacent?
- Which discharge-stage combinations are within the selected library envelope?
- Which actual downstream run lies close enough to a grid target?
- Which combinations are hydrologically plausible for later forecast use?

The selected stage-grid method addresses the second and third questions under its assumptions.
It does not by itself solve joint-frequency plausibility at a confluence.

The checked-in reconciler prototype estimates how much additional discharge other drainage area can contribute to a downstream reach.
It uses drainage-area ratios and a fixed exponent of 0.7 to condition a ceiling.
The reviewed Decision Register contains no DR-043, DR-044, or DR-045 rows, so this logic has current prototype-code authority only and remains an open governance and validation question.

## Terminal reaches

A terminal reach has no ordinary downstream reach from which to obtain a stage library.
It needs an explicit terminal-boundary policy rather than a fabricated adjacency.

The checked-in reconciler prototype currently gives terminal reaches an ND step but no KWSE step.
Its gap logic treats a terminal reach's absent KWSE library as satisfied so the next reach upstream does not wait forever.

That is a coherent prototype scheduling rule, but it does not implement the full selected lake and coastal stage methodology.
The code comments identify unresolved lake and coastal stages as the reason for the stop.

An ordinary plain outlet, a lake inlet, a lake outlet, and a coastal handoff are scientifically different terminal contexts.
They need distinct source data, stage bounds, geometries, and acceptance evidence even if one scheduler flag currently groups them as terminal.

## Lakes

Selected DR-007 trims or removes reaches against a dead-pool polygon and tags lake inlets and outlets.
DR-034's source dataset and shrink criteria remain Needs-Review.
DR-035's lower-stage choice remains Needs-Review, and DR-036 has no documented upper-bound alternatives.

DR-037 ALT-B treats lakes as Flows2FIM breakpoints and places lake FIM lookup outside Flows2FIM.
That selection means ordinary reach-to-reach propagation cannot simply cross a removed lake reach without a separate lake-stage source and downstream-to-upstream handoff contract.

The current modeling jobs have a `ds_of_lake` build input and can consume supplied geometry, but they do not create lake stage libraries, choose dead-pool elevations, or close the missing upper-bound policy.
The checked-in reconciler can publish a waterbody polygon address for terminal ND boundary geometry, but its own comments state that selected steep-slope terminal behavior is not realized by that payload path.

## Coasts

DR-038 ALT-A selects dropping or trimming reaches that overlap coastal coverage and dropping connected reaches farther downstream.
The record itself preserves data and handoff caveats.

Coastal stage can be influenced by tides, surge, waves, and coastal-model coupling that are outside the first-edition inland hydraulic job contract.
A coastal breakpoint therefore needs an explicit boundary-source identity, vertical datum, time or scenario semantics, and responsibility for coupling inland discharge with coastal stage.

Current `twod-fim-jobs` does not construct coastal stage scenarios or implement a coastal model.
Target or prototype scheduling logic must not be presented as evidence that this handoff is scientifically or operationally complete.

## Checked-in reconciler prototype trace

The following trace is a current local-code observation outside the modeling jobs.
It explains the prototype seam without claiming verified deployment.

### 1. Calculate the next missing rung

`recon/gap.py` models three ordered rungs: `build_model`, `run_nd_scenarios`, and `run_kwse_scenarios`.
A non-terminal model waits for the downstream model and ND library because the upstream computed domain includes a downstream maximum-ND-discharge STL.

A non-terminal ND library waits for the downstream ND library because its boundary polygon comes from the downstream maximum-ND-discharge inundation polygon.
A non-terminal KWSE library waits for its own model and ND library and for the downstream model, ND library, and KWSE library, except that a terminal downstream neighbor needs no KWSE proof.

### 2. Observe prerequisites rather than trust job status

The prototype records materialized model, ND, and KWSE rows only after reading storage manifests and checking address and identity fields.
Its in-flight poller explicitly treats a job success report as insufficient and requests another storage observation.

This is the correct evidence boundary for target materialization.
The presence of prototype code does not prove that a deployed controller is currently performing the checks.

### 3. Assemble downstream candidates

`recon/scenarios.py` reads the downstream materialized ND curve and, when present, downstream KWSE scenario index.
It represents each source run with discharge, achieved upstream-end WSE, source boundary type, and imposed source boundary value.

The prototype includes both ND and KWSE source runs in the binding pool.
This supports low target stages that can bind to downstream ND and higher targets that can bind to downstream KWSE.

### 4. Calculate one prototype plan

`recon/plan.py` takes the upstream selected `q_set`, stage increment, downstream runs, upstream ND slope, optional authored KWSE upper cap, a prototype estimate of other-basin flow, and downstream selected `q_set`.

The prototype first calculates the discharge attributed to all drainage area other than the current upstream reach:

\[
Q_{other}
=
\left(1-\frac{A_{up}}{A_{ds}}\right)^{0.7}Q_{ds,upper}
\]

- \(A_{up}\) is the current reach drainage area in km2.
- \(A_{ds}\) is the downstream reach drainage area in km2.
- \(Q_{ds,upper}\) is the downstream reach's authored upper discharge bound in m3/s.
- The fixed exponent 0.7 is current unpinned prototype code, not registered methodology in the handbook-reviewed Decision Register.

The prototype refuses this calculation when either drainage area is nonpositive, when upstream drainage area exceeds downstream drainage area, or when the downstream upper discharge bound is nonpositive.
The impure caller also refuses planning when downstream effective intent, the downstream upper bound, or either drainage-area value is absent.

For each upstream discharge \(Q\), the prototype floor uses one downstream discharge.
It selects the largest downstream-run discharge at or below \(Q\).
If no downstream run is at or below \(Q\), it clamps to the smallest downstream-run discharge.
The floor is the minimum achieved upstream-end WSE among downstream runs at that selected discharge:

\[
Q_{floor,ds}
=
\begin{cases}
\max\{q_{ds}:q_{ds}\le Q\}, & \text{when that set is nonempty}\\
\min\{q_{ds}\}, & \text{otherwise}
\end{cases}
\]

\[
z_{floor}(Q)=\min\{WSE_r:q_r=Q_{floor,ds}\}
\]

The prototype then calculates a downstream discharge cap:

\[
Q_{cap}=Q+Q_{other}
\]

It reads the downstream selected ND `q_set`, not every published discharge, and chooses the first selected discharge at or above \(Q_{cap}\), allowing a small floating-point tolerance.
If the cap exceeds every selected downstream discharge, it clamps to the maximum selected discharge:

\[
Q_{read}
=
\begin{cases}
\min\{q\in q_{set,ds}:q\ge Q_{cap}-\epsilon\}, & \text{when that set is nonempty}\\
\max(q_{set,ds}), & \text{otherwise}
\end{cases}
\]

The candidate pool contains every downstream ND or KWSE run whose discharge is no greater than \(Q_{read}\), including published leftover runs below that limit.
The uncapped prototype ceiling is the maximum achieved upstream-end WSE in that pool:

\[
P(Q)=\{r:q_r\le Q_{read}\}
\]

\[
z_{ceiling,raw}(Q)=\max_{r\in P(Q)}WSE_r
\]

When `kwse_upper_bound` is authored, it can only lower that ceiling:

\[
z_{ceiling}(Q)=
\min\left(z_{ceiling,raw}(Q),z_{authored,max}\right)
\]

Without an authored cap, \(z_{ceiling}=z_{ceiling,raw}\).
The prototype records \(Q\), \(Q_{cap}\), \(Q_{read}\), and the final ceiling for every upstream discharge even when its stage envelope later closes.

It snaps the floor and ceiling to the nearest zero-anchored \(\Delta z\) values.
If the snapped floor exceeds the snapped ceiling, that discharge produces no scenarios and is not treated as a planning failure.

For every target in a nonempty ladder, the binding candidate pool is the same full \(P(Q)\), not only runs at the floor-setting discharge.
The nearest candidate minimizes absolute achieved-WSE distance, with lower downstream discharge breaking a tie.
A target farther than \(\Delta z/2\) from its nearest source is returned as `SkippedTarget` with target, nearest WSE, and distance.

The planner rejects a nonpositive stage increment, an empty downstream-run list, a negative or NaN `others` value, an empty downstream selected `q_set`, or any selected downstream discharge that has no corresponding downstream run.
The separate scenario-gathering caller also refuses a terminal reach, a missing stage increment, an absent own model or ND library, an absent downstream ND library, or a downstream ND folder that cannot be resolved uniquely.

For each upstream discharge, the resulting first retained target seeds from the current reach's ND scenario at the same discharge.
Each later retained target seeds from the prior retained KWSE target at that discharge.

The plan preserves its scenarios, skipped targets, and calculated ceilings.

The prototype rounds both envelope bounds to the nearest stage increment.
Because the binding tolerance is half an increment, the first or last target can lie up to half an increment outside the unsnapped envelope and still bind.

### 5. Preserve serial seed order within one discharge

For each upstream discharge, the first KWSE target seeds from that reach's ND scenario at the same discharge.
Each later stage seeds from the previous lower stage at the same discharge.

The prototype separates scenarios into one chain per discharge.
Chains are independent and can be submitted as separate jobs, while order inside one chain is load-bearing because the seed must already exist.

### 6. Submit only missing scenarios

The prototype looks up each planned manifest at the predicted address and validates it.
It removes already observed scenarios from submission and creates one `run_kwse_scenarios` payload per remaining discharge chain.

The payload preserves scenario order and supplies `upstream_discharge`, target `bc_value`, exact downstream manifest address, and explicit hot-start coordinates and run identity.
The current KWSE job then executes that supplied list without recalculating the plan.

### 7. Observe the complete plan

`observe_kwse_runs` recomputes the same plan and requires every planned scenario manifest to exist and pass its verification before writing one `materialized_kwse_runs` row.
An empty plan is considered materialized, while any missing or refused planned scenario prevents the row.

The observed index groups runs by upstream discharge and records achieved upstream nominal WSE and imposed downstream boundary value.
Skipped grid targets are not required because the plan, rather than the complete ideal grid, defines what was requested.

## Propagation after change

Target propagation starts when a downstream identity, scenario set, stage response, STL, or relevant artifact changes.
A controller should bound candidates by walking immediate upstream topology and then use recorded boundary provenance to identify which upstream libraries actually reference the superseded source.

Topology alone can over-invalidate because an upstream plan may still reference an unchanged downstream run.
Provenance alone can miss candidates if records are absent, stale, or incomplete.
The target design therefore proposes both mechanisms.

Reprocessing continues recursively toward headwaters until each candidate either already references current downstream evidence or has been rebuilt, rerun, replanned, and observed.
This is a desired reconciliation property, not proof of present production operation.

## Planning artifact requirements

A durable plan should record at least:

- prepared-network revision and authoritative downstream reach ID;
- planner implementation and methodology revision;
- upstream model and run identities;
- upstream selected ND `q_set` and its membership evidence;
- downstream model and run identities;
- all downstream candidate run addresses and achieved versus imposed stage values;
- each discharge-specific lower and upper bound with source evidence;
- stage increment, zero anchor, rounding rule, and tolerance;
- every target, bound source run, and hot-start source;
- every skipped target with nearest WSE and distance;
- terminal, lake, coast, and confluence classifications;
- assumptions or prototype calculations not authorized by the Decision Register; and
- creation time, planner version, and content checksum.

The current KWSE job response does not supply this plan artifact.
Without it, a set of scenario manifests can show what ran but not fully reconstruct why those scenarios were selected or omitted.

## Failure modes and diagnostic signals

### Wrong network direction

An incorrect `reach_to_id` reverses or breaks the dependency chain and can bind an upstream reach to the wrong source.
Verify prepared-network direction, adjacency, lineage, and terminal classification before planning.

### Planning from every published ND trial

Current ND publication includes rejected search points.
Using every directory as `q_set` can create unsupported KWSE libraries and ceiling reads.
Require explicit selected membership.

### Silent grid gaps

A target without a downstream run inside \(\Delta z/2\) is omitted under DR-033.
Preserve the skipped target, nearest value, and distance so downstream sampling quality remains visible.

### Source-stage confusion

Matching a target against the imposed boundary value instead of achieved upstream WSE can select the wrong source run.
Addressing a source by achieved WSE instead of imposed value can point to a path that does not exist.

### Missing waterbody policy

Treating a lake or coast as an ordinary reach can invent nonexistent topology or apply inappropriate stage bounds.
Treating every terminal as complete can hide missing stage science.

### Planner drift

If submission and observation compute different plans, the controller can resubmit forever or accept an incomplete library.
Use one deterministic plan function and persist enough inputs to reproduce its answer.

### Prototype decision numbers mistaken for authority

The checked-in planner comments cite DR-042 through DR-045, which are absent from the reviewed register.
Treat those formulas as prototype code facts and open questions until registered authority and evidence are supplied.

## Competency check

1. Why does a KWSE dependency wave move upstream while physical flow moves downstream?
2. What is the difference between a published ND trial and selected `q_set` membership?
3. Which stage value is used for target matching, and which value addresses the source folder?
4. Why can a target be skipped under DR-033?
5. How should a terminal lake reach differ from an ordinary terminal outlet?
6. What evidence would prove a planned set is materialized?

## Further reading and source notes

- [KWSE and Stage Transfer](03-kwse-and-stage-transfer.md) describes current execution after the list is authored.
- [Compositing, Identity, and Provenance](05-compositing-identity-and-provenance.md) distinguishes reach-scenario artifacts from a network product.
- [Decision-Code-Artifact Crosswalk XW-006](../reference/decision-code-artifact-crosswalk.md#xw-006-scenario-library-bounds-and-sampling) maps bounds and sampling.
- [Decision-Code-Artifact Crosswalk XW-007](../reference/decision-code-artifact-crosswalk.md#xw-007-short-reach-lake-and-coast-network-modification) maps waterbody and network responsibilities.
- Bibliography entries SDR-001, SDR-006, SDR-007, SDR-008, SDR-011, JOB-005, JOB-010, SYS-001, SYS-002, SYS-003, and SYS-006 identify the reviewed local sources.

No external source was required for this chapter.
