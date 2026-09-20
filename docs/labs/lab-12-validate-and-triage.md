# Lab 12: Validate and Triage

This lab uses a bounded synthetic evidence packet to practice hypothesis-driven diagnosis.
Synthetic values are teaching inputs only and have no project authority.

## Prerequisites

Complete [Validation Framework](../06-validation-and-qc/01-validation-framework.md) and [Diagnostic Workflow](../06-validation-and-qc/02-diagnostic-workflow.md).
Review [KWSE and Stage Transfer](../05-scenario-libraries/03-kwse-and-stage-transfer.md), [Compositing, Identity, and Provenance](../05-scenario-libraries/05-compositing-identity-and-provenance.md), and [Lab 11](lab-11-trace-kwse-stage-transfer.md).

## Execution label

**No execution required.**
Use only the supplied packet and handbook references.
Do not run a solver, retrieve external data, or assume an unlisted artifact exists.

## Scenario

A reviewer is asked whether a synthetic upstream KWSE scenario is ready to supply a reach-library composite.
The final upstream WSE is about 0.35 m higher than a synthetic benchmark over a 160 m band near the transfer boundary.
The map looks smooth, the job returned a manifest path, and the manifest records `volume_convergence`.

The immediate decision is whether the scenario can be accepted for hydraulic interpretation and downstream-to-upstream continuity review.
The packet does not establish any project validation threshold.

## Bounded evidence packet

### A. Intended use and identities

| Item | Supplied synthetic observation |
| --- | --- |
| Intended use | Evaluate upstream reach `U-42` at discharge 420 m3/s with a downstream KWSE condition supplied by `D-42`, then include the accepted scenario in a diagnostic composite. |
| Prepared network | One versioned row states that `U-42` drains to `D-42`, but source-to-prepared lineage is not supplied. |
| Upstream model | Full model ID `u42hash_N20S20E18W18`, projected horizontal CRS EPSG:26918, 10 m grid. |
| Downstream model | Full model ID `d42hash_N24S16E22W22`, projected horizontal CRS EPSG:26918, 10 m grid. |
| Run identity | Both scenario manifests contain the same eight-character run identity. |
| Methodology revision | The manifests contain the jobs image's baked revision, but the packet does not compare it with the review's authorized revision. |

### B. Forcing and boundary evidence

| Item | Supplied synthetic observation |
| --- | --- |
| Upstream discharge | 420 m3/s in the request and upstream scenario manifest. |
| Nominal downstream stage | 102.8 m in the request and scenario address. |
| Transfer source | The upstream manifest names the `D-42` scenario with discharge 420 m3/s. |
| Boundary construction | The packet says current cell-specific transfer uses source `depth + terrain` at cells selected through the upstream-grid STL mapping. |
| Boundary sample record | Only three final `HFIX` WSE values are supplied: 102.72, 102.85, and 102.91 m. |
| Boundary geometry record | The STL href is named in both manifests, but no cell index, coordinate, source cell, mask, or coverage table is supplied. |

### C. Raster and datum evidence

| Item | Supplied synthetic observation |
| --- | --- |
| Downstream depth raster | Shape 300 by 240, EPSG:26918, 10 m nominal resolution, and no vertical datum field. |
| Downstream terrain raster | Shape 300 by 240, EPSG:26918, 10 m nominal resolution, and NAVD88 metres stated in separate metadata. |
| Affine transforms | The packet does not provide either transform, bounds, cell registration, or nodata mask. |
| Upstream terrain | EPSG:26918 and metres are stated, but the vertical datum is absent. |
| Transfer implementation fact | Current code can add raw equal-shaped arrays and does not explicitly verify transform, bounds, datum, nodata masks, or cell registration first. |

### D. Numerical evidence

| Item | Supplied synthetic observation |
| --- | --- |
| Termination | `volume_convergence` with final ratio 0.0008. |
| Local transfer-band WSE | Mean WSE rose by 0.09 m and then 0.06 m over the final two saved-output intervals. |
| Domain storage | Total positive-depth storage changed by less than the selected proxy threshold over the final interval. |
| Flux and balance | Inflow is known, but outflow, boundary fluxes, source and sink terms, and closure residual are not supplied. |
| Edge evidence | No `edge_error` is recorded, and boundary-check activation and wet-perimeter cells are not supplied. |
| Initial state | A depth-only hot start was used from another finished scenario, but no cold-start or alternative-hot-start comparison is supplied. |

### E. Benchmark and plausibility evidence

| Item | Supplied synthetic observation |
| --- | --- |
| Synthetic benchmark | A larger-domain model using nominally the same forcing and solver has WSE 0.35 m lower over the 160 m transfer band. |
| Comparability | Exact DEM bytes, terrain transformation, roughness, boundary locations, initial state, grid realization, and saved time are not supplied for the benchmark. |
| Visual review | Both maps look smooth, and neither shows an obvious disconnected pool at the display scale. |
| Observation data | No independent measured stage, WSE, depth, extent, or discharge is supplied. |
| Acceptance policy | No authorized tolerance, uncertainty allowance, or permitted-use criterion is supplied. |

### F. Publication and materialization evidence

| Item | Supplied synthetic observation |
| --- | --- |
| Job result | The job returned the target scenario-manifest path. |
| Observed storage | The target manifest and depth object are present. |
| Missing object | The target STL named by the manifest is absent. |
| Other assets | Inundation presence and checksums are not independently observed. |
| Membership | No durable selected-library member list includes this scenario. |
| Composite | A draft composite references the scenario depth, but no complete product manifest or source compatibility record is supplied. |

## Part A: Frame the decision

State the intended decision, the quantity being judged, and the minimum evidence categories needed before acceptance.
Separate hydraulic readiness from storage materialization and selected-library membership.

## Part B: Define competing hypotheses

Write at least three competing hypotheses for the 0.35 m high-WSE band.
Your set must include:

1. A transfer-raster registration, datum, mask, or indexing hypothesis.
2. A residual transient or incomplete-balance hypothesis.
3. A physically or numerically real downstream-control hypothesis under correctly mapped inputs.

You may add forcing, terrain, roughness, benchmark-comparability, or initial-condition hypotheses.
Treat missing materialization evidence as a separate readiness failure unless you explain a mechanism by which it created the WSE values.

For each hypothesis, state one expected observation if it is true and one expected observation if it is false.

## Part C: Select one immediate next check

Choose exactly one immediate next check.
Defend it by explaining how its possible outcomes distinguish the leading hypotheses.

- **A.** Increase upstream roughness until the map agrees with the benchmark.
- **B.** Obtain the exact downstream source depth, terrain, STL, and final three saved grids; verify shape, transforms, bounds, CRS, horizontal units, vertical datum, masks, registration, STL coverage, and bounded source indices; then reconstruct every transferred `HFIX` value and its recent time history.
- **C.** Accept the scenario because the manifest inputs match and the final volume-convergence ratio is below 0.001.
- **D.** Expand every upstream domain edge by the largest allowed distance and rerun before inspecting the transfer.

The answer must name the expected discriminatory result under each required hypothesis.

## Part D: Apply the evidence hierarchy

Classify each project claim with one of the six handbook evidence labels.
For the invented benchmark and storage observations, use `Synthetic exercise observation` or `Outside the project evidence taxonomy` rather than granting project evidence authority.
Also state which approved label an analogous observation would receive if it came from a real, traceable project record.
Explain what the statement can and cannot establish.

1. Current code can add equal-shaped depth and terrain arrays without proving cell registration.
2. DR-031 selects cell-specific stage transfer.
3. The synthetic benchmark WSE is 0.35 m lower in the transfer band.
4. The target STL object is absent from observed storage.
5. No authorized validation tolerance is supplied.

## Part E: State stopping and escalation conditions

State:

- the observation that would support a bounded transfer-misregistration diagnosis;
- the observation that would move residual transient behavior ahead of misregistration;
- the observation that would justify investigating real downstream control after mapping and transient checks;
- the materialization condition required before the scenario can serve as an upstream transfer source;
- the policy condition that still requires escalation even if the technical anomaly is explained; and
- the condition under which the review should stop rather than tune another parameter.

## Part F: Issue a direct readiness verdict

Choose one verdict from [Validation Framework](../06-validation-and-qc/01-validation-framework.md):

- `READY FOR THE STATED USE`;
- `READY WITH RESTRICTIONS`;
- `NOT READY`; or
- `INSUFFICIENT EVIDENCE TO ASSESS`.

State the verdict first.
Then give the smallest evidence set that could change it.
Do not invent a pass threshold for the 0.35 m difference.

## Deliverable

Submit a short review with these sections:

1. Decision and evidence categories.
2. Hypothesis table with predictions.
3. One selected next check and expected discriminatory observations.
4. Evidence labels and limits.
5. Stopping and escalation conditions.
6. Direct readiness verdict.

## Competency criteria

The lab is complete when the answer:

- separates diagnosis, materialization, library membership, and hydraulic acceptance;
- states at least three competing hypotheses with both confirming and challenging observations;
- selects check B and explains how its outcomes discriminate rather than merely gather more data;
- does not treat `volume_convergence`, manifest equality, smooth maps, or one benchmark as adequacy proof;
- identifies the missing STL as a direct materialization failure;
- preserves the missing authorized tolerance as an Open question;
- states a stopping or escalation condition; and
- issues a direct readiness verdict without turning synthetic values into project authority.

After completing the lab, compare the reasoning with [Lab 12 Solution](solutions/lab-12-validate-and-triage-solution.md).
