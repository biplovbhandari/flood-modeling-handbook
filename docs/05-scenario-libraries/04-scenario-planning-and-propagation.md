# Scenario Planning and Propagation

Scenario planning converts authored bounds, emergent discharge selections, downstream-stage evidence, and network dependencies into a complete set of reach scenarios.
Propagation moves newly observed downstream information upstream through the directed network.

## Why this topic matters

Per-reach scenarios are not independent when upstream boundaries depend on downstream water surface.
A planner must distinguish intended bounds, selected members, stage targets, bound source runs, submitted work, and observed artifacts.

## Prerequisites

Read [Network Preparation](../04-model-development/01-network-preparation.md), [Discharge-Only Scenario Libraries](01-normal-depth-libraries.md), [Adaptive Discharge Selection](02-adaptive-discharge-selection.md), and [Downstream-Stage-Aware Libraries and Stage Transfer](03-downstream-stage-and-transfer.md).

## Learning objectives

After this chapter, the reader should be able to:

- distinguish authored bounds, emergent discharge sets, stage bounds, stage targets, and bound source runs;
- order work from downstream to upstream without reversing river direction;
- plan confluences without losing tributary identity;
- bind every stage target to exact downstream source evidence;
- distinguish submission state from observed completion;
- propagate changes through affected dependencies; and
- specify a reproducible planning artifact.

## Five different sets and bounds

### 1. Authored discharge bounds

Hydrologic analysis or another stated method defines lower and upper discharge bounds for each reach.
The planner preserves their provenance but does not treat every value inside the interval as a scenario.

### 2. Emergent discharge set

The adaptive process selects a finite discharge set from measured response.
For `R-200`, that set contains 100, 125, 175, 200, 225, and 250 cubic metres per second.

### 3. Downstream-stage bounds

For each upstream discharge, the planner needs a lower and upper downstream stage that cover intended downstream influence.
The bounds must use the same vertical datum as the upstream terrain.

### 4. Stage-grid targets

For lower bound $\eta_{min}$, upper bound $\eta_{max}$, and positive step $\Delta\eta$, one explicit grid rule is:

\[
G_\eta=\{\eta_{min}+k\Delta\eta\mid k=0,1,\ldots,K\}\cup\{\eta_{max}\}
\]

where $K$ is the greatest integer for which $\eta_{min}+K\Delta\eta\leq\eta_{max}$.
The union ensures that an off-grid upper bound is retained as an endpoint.

The applied stage grid contains exactly 102.0, 102.5, and 103.0 m in datum `VD-1`.

### 5. Bound downstream source runs

Each stage target must name an exact downstream source or bracketing pair, not only a scalar value.
The binding preserves source scenario identities, observed artifacts, interpolation method, transfer geometry, and diagnostic status.

## Dependency direction

River flow moves from `R-100` and `R-300` downstream into `R-200`.
Stage information moves from completed `R-200` scenarios upstream into `R-100` and `R-300`.

The planner therefore develops the terminal `R-200` discharge-only library first.
It then plans stage-aware families for both upstream reaches.

**Design principle:** Store river direction and stage-dependency direction as separate relationships.
Do not reverse the prepared network to express stage propagation.

## Topological ordering

For a directed acyclic reach graph, process terminal reaches before their immediate upstream dependencies.
Within one dependency layer, independent reaches can be planned in parallel if they do not share mutable planning state.

A cycle in the prepared graph is a blocking topology error unless the method explicitly represents a tidal, diversion, or looped system with another solver contract.

## Confluences

`R-100` and `R-300` remain separate upstream models even though both receive stage information from `R-200`.
Their discharge families and transfer geometry differ.

The planner must also state how joint tributary conditions relate to the total flow represented by each `R-200` source scenario.
Independent pairing of every `R-100` and `R-300` scenario can create combinations that violate the downstream total or hydrologic dependence.

**Open question:** Which joint-flow rule should connect tributary discharges to the `R-200` source family for a particular product?
Possible choices include event-coincident hydrographs, proportional allocation, conditional sampling, or another documented hydrologic model.

## Terminal, lake, and coastal reaches

A terminal river reach needs an external downstream condition before it can seed upstream propagation.
The applied `R-200` example uses a slope-based terminal condition for its discharge-only library.

Lake and coastal breakpoints need separate stage or circulation products and explicit handoff ownership.
The planner should not invent an ordinary reach dependency where the prepared network marks a breakpoint.

## Applied plan

The complete synthetic plan is:

| Reach | Discharge set | Downstream-stage targets | Dependency |
| --- | --- | --- | --- |
| `R-200` | 100, 125, 175, 200, 225, 250 m3/s | Not applicable | External slope boundary at 0.0015 m/m |
| `R-100` | 60, 100, 140 m3/s | 102.0, 102.5, 103.0 m | `R-200` source scenarios |
| `R-300` | 40, 60, 80 m3/s | 102.0, 102.5, 103.0 m | `R-200` source scenarios |

Each `R-200` discharge is a total flow allocated across the `R-100` boundary, the `R-300` boundary, and the local incremental area by the stated 84:51:7 rule.
The paired boundary and local values for the six totals are 59:36:5, 74:45:6, 104:63:8, 118:72:10, 133:81:11, and 148:90:12 m3/s respectively.

The `R-200` source-stage values are 101.8, 102.0, 102.4, 102.7, 102.9, and 103.2 m for discharges 100, 125, 175, 200, 225, and 250 m3/s respectively.

The bindings are:

| Target stage | Lower source | Upper source | Method |
| ---: | --- | --- | --- |
| 102.0 m | `R-200`, 125 m3/s, 102.0 m | Same source | Exact compatible transfer values |
| 102.5 m | `R-200`, 175 m3/s, 102.4 m | `R-200`, 200 m3/s, 102.7 m | Linear interpolation on common wet support |
| 103.0 m | `R-200`, 225 m3/s, 102.9 m | `R-200`, 250 m3/s, 103.2 m | Linear interpolation on common wet support |

For a target stage $\eta_t$ between source stages $\eta_0$ and $\eta_1$, the scalar interpolation weight is:

\[
w=\frac{\eta_t-\eta_0}{\eta_1-\eta_0}
\]

For a compatible pointwise source field $E$, the interpolated value is:

\[
E_t=(1-w)E_0+wE_1
\]

For an interpolated target, calculate values only where both brackets have valid terrain, valid depth, and strictly positive depth.
Emit no value where either bracket is dry or nodata.
Reject the planned scenario when common wet support covers less than 90 percent of the intended interface.
Include the wetness, nodata, common-support, and coverage rules in the transfer identity.

**Evidence note:** The tables define the synthetic plan and its scalar source binding.
They do not resolve the joint-flow relationship between `R-100` and `R-300` or prove pointwise interpolation is valid everywhere.

## Planning loop

### 1. Calculate the next missing dependency layer

Compare desired libraries with observed materialization records.
For the applied network, missing `R-200` members block both upstream families.

### 2. Observe prerequisites

Read source records and assets from storage.
Do not infer completion from a submitted job, successful process status, or returned address.

### 3. Assemble source candidates

Use only selected and scientifically accepted `R-200` members whose required artifacts have been observed.
Exclude search trials, partial generations, obsolete identities, and sources with incompatible datum or transfer support.

### 4. Calculate the plan

Generate every upstream discharge-stage pair from the fully stated discharge sets and stage grid.
Bind each pair to exact downstream source evidence.

For this example, each upstream reach has $3\times3=9$ planned scenarios.
Together, `R-100` and `R-300` require 18 stage-aware scenarios.

### 5. Preserve serial seed order where required

Within one upstream discharge, increasing stages may use a compatible prior stage as a warm start.
Record the seed chain explicitly and do not assume that alphabetical or address order is hydraulic order.

### 6. Submit only missing scenarios

Compute complete scenario identity and compare with observed complete generations.
Do not submit work merely because a scheduler record is absent.

### 7. Observe the complete plan

Materialize the family only when every planned scenario record and required artifact has been observed and verified.
Keep scientific acceptance separate from completeness.

## Propagation after change

A changed `R-200` model, discharge membership, boundary method, transfer geometry, or scenario artifact can invalidate upstream bindings.
The planner should identify affected source identities and recompute only dependent plans.

Deterministic identities make this change propagation auditable.
Mutable aliases and omitted inputs make it impossible to know which upstream results remain reusable.

## Planning artifact requirements

A planning record should include:

- prepared-network version and dependency direction;
- authored discharge bounds and selected discharge sets;
- stage bounds, step, endpoint rule, and final target grid;
- exact downstream source identities and observation results;
- interpolation, extrapolation, nodata, and wet-support rules;
- upstream model and scenario identities;
- warm-start order and compatibility checks;
- submitted, observed, excluded, and unresolved items; and
- the joint-flow assumptions used at confluences.

## Failure modes and diagnostic signals

### Wrong network direction

Planning upstream sources from an upstream reach reverses the intended dependency.
Check prepared adjacency and the separate stage-dependency relation.

### Planning from every published trial

Storage may include adaptive search overhead.
Require the selected library index.

### Silent grid gaps

An off-grid upper bound can be lost when integer stepping stops below it.
Preserve endpoints explicitly.

### Source-stage confusion

A nominal target can be mistaken for the realized source field.
Preserve source identities and interpolation evidence.

### Missing waterbody policy

Treating a lake or coastal breakpoint as an ordinary reach can create unsupported dependencies.

### Planner drift

Changing a stage step or source-binding rule without changing plan identity can cause unsafe reuse.

## Competency check

1. What distinguishes discharge bounds from the selected discharge set?
2. Why does stage information propagate opposite river flow?
3. How many stage-aware scenarios are planned for each upstream reach?
4. Which `R-200` sources bind the 102.5 m target?
5. Why must confluence planning state a joint-flow rule?
6. What must be observed before a family is materialized?
7. Which changes should invalidate upstream plans?

## Further reading and source notes

- **Scientific foundation:** Downstream boundary and backwater concepts are supported by [SCI-024](../reference/bibliography.md#sci-024-hec-ras-downstream-boundary-conditions) and [SCI-026](../reference/bibliography.md#sci-026-hec-ras-2d-external-boundary-conditions).
- **Scientific foundation:** Model sensitivity and evidence requirements are supported by [SCI-015](../reference/bibliography.md#sci-015-hydraulic-model-sensitivity) and [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations).
