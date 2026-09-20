# Lab 12 Solution: Validate and Triage

This solution demonstrates evidence-disciplined reasoning for the bounded synthetic packet.
It does not validate a project model, establish a 0.35 m tolerance, or authorize a transfer, materialization, membership, or acceptance policy.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, execution boundary, and stopping or escalation conditions are defined in [Lab 12](../lab-12-validate-and-triage.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and keeps diagnosis, materialization, membership, and acceptance distinct.
Stop when the answer satisfies the prompt's competency criteria or states the exact escalation and evidence needed to change the verdict.

## 1. Decision and evidence categories

The immediate decision is whether the `U-42` scenario can support hydraulic interpretation, reach-continuity review, and inclusion in a diagnostic composite for the stated 420 m3/s scenario.
The decision-relevant symptom is the approximately 0.35 m high-WSE band over 160 m near the transfer boundary.

Acceptance requires more than one comparison.
The minimum categories are:

1. Verified forcing, prepared-network relationship, source provenance, methodology revision, and scenario identity.
2. Congruent terrain, depth, STL, CRS, horizontal units, vertical datum, masks, and cell registration.
3. Realized boundary cells and values with bounded source indices.
4. Solver-state, local convergence, full-balance, edge, and initial-condition evidence.
5. Comparable benchmark or independent observational evidence with uncertainty.
6. Complete scenario and composite materialization with selected-library membership recorded separately.
7. Authorized, predeclared acceptance criteria for the intended use.

Hydraulic readiness, materialization, and membership are separate decisions.
The missing target STL directly prevents complete materialization under the stated manifest.
The absent selected-member list means the published depth does not establish library membership.
Neither fact by itself explains the computed 0.35 m difference, but each blocks the requested use.

## 2. Hypothesis table

| Hypothesis | Expected observation if true | Expected observation if false |
| --- | --- | --- |
| H1: Transfer values were sampled from misregistered, datum-incompatible, invalid, or wrongly indexed downstream cells. | Depth and terrain transforms, bounds, datum, masks, or cell centers disagree; one or more STL queries lack valid coverage or use a wrong source index; reconstructed valid-cell WSE differs from the stored `HFIX` values or changes their spatial pattern. | Source rasters and manifest agree in shape, transform, bounds, CRS, units, datum, masks, and registration; every source index is bounded and wet under the authorized rule; reconstructed WSE matches every `HFIX` value. |
| H2: The source or target retained a residual transient despite the domain-total storage-change trigger. | Transfer-band WSE continues changing materially over the final grids, inflow and outflow do not close, local flux or WSE remains directional, or cold and alternative hot starts approach different final states. | Local WSE and flux stabilize over the review interval, complete balance closes within authorized criteria, and compatible initial states converge to materially equivalent results. |
| H3: A correctly mapped downstream condition creates a real modeled backwater response that differs from the benchmark configuration. | Transfer mapping is congruent and stable, source WSE is reproducible, and the upstream longitudinal profile responds coherently to the same downstream control; the difference aligns with a documented boundary, terrain, roughness, or domain distinction from the benchmark. | The difference disappears after correcting mapping or transient state, lacks a coherent backwater profile, or depends on incomparable benchmark inputs rather than the realized downstream control. |
| H4: The benchmark comparison is not like-for-like. | Exact source bytes, transformations, roughness, boundaries, grid, initial state, or comparison time differ, and the 0.35 m band follows one of those differences. | The benchmark and reach scenario use demonstrably comparable inputs, settings, support, and time, leaving another hypothesis to explain the residual. |
| H5: The depth-only hot start influenced the reported final state. | A cold start or a different compatible seed produces a materially different transfer-band WSE or convergence path under the same target forcing and boundaries. | Independent compatible initial states approach equivalent local state, balance, and WSE under the authorized comparison criteria. |

The missing STL is not the leading mechanism for the already produced WSE values because the packet says a boundary was realized and supplies three `HFIX` values.
It is instead direct evidence that the stored scenario is incomplete and cannot be used as the next upstream transfer source.

## 3. Selected next check and expected discriminatory observations

**B. Obtain the exact downstream source depth, terrain, STL, and final three saved grids; verify shape, transforms, bounds, CRS, horizontal units, vertical datum, masks, registration, STL coverage, and bounded source indices; then reconstruct every transferred `HFIX` value and its recent time history.**

This is the best first check because it directly observes the mechanism shared by the leading hypotheses without changing terrain, roughness, boundaries, or domain.
It combines one bounded transfer audit with the minimum local time history needed to distinguish wrong mapping from a changing source state.

The discriminatory outcomes are:

- **H1 favored:** A transform, datum, mask, coverage, or index mismatch appears, or correctly reconstructed source WSE fails to match the realized `HFIX` values.
- **H1 challenged and H2 favored:** Registration and reconstruction agree, but source or transferred WSE continues to rise or fall across the final saved grids.
- **H1 and H2 challenged, H3 becomes actionable:** Registration is exact, reconstructed values match, local state is stable, and the WSE profile responds coherently to the realized downstream control.
- **H4 remains material:** Mapping and time-history evidence are internally sound, but exact benchmark inputs and support remain incomparable.
- **H5 remains material:** The audit can show which source state was used, but it cannot establish independence from the depth-only hot start without a separate initial-condition comparison.

Choice A is calibration by appearance before geometry, datum, and transient causes are resolved.
It can create a compensating roughness error.
Choice C substitutes manifest equality and a storage-change proxy for mapping, balance, materialization, validation, and acceptance.
Choice D changes a broad geometry dimension without evidence that an edge or clipped pathway causes the transfer-band difference.

## 4. Evidence labels and limits

### 4.1 Raw equal-shape array addition

**Current implementation:** The reviewed code behavior can add equal-shaped depth and terrain arrays without first verifying their transforms, bounds, datum, masks, or cell registration.
This establishes a current failure opportunity and defines a required diagnostic check.
It does not prove that misregistration occurred in this synthetic scenario until the exact source rasters and mapping are inspected.

### 4.2 DR-031 cell-specific transfer

**Selected methodology:** DR-031 ALT-B selects cell-by-cell stage transfer within its recorded status and scope.
This establishes intended methodology.
It does not prove that current code realized the intended cells correctly or that the resulting boundary was valid.

### 4.3 Synthetic benchmark difference

**Outside the project evidence taxonomy, Synthetic exercise observation:** The teaching packet reports a synthetic benchmark WSE 0.35 m lower over the transfer band.
This establishes one bounded difference in the teaching packet.
It does not establish the cause, a project tolerance, field accuracy, or comparable model inputs.
An analogous observation from a real, traceable project benchmark would receive the top-level label **Evidence or experiment** with its benchmark or case provenance and limits.

### 4.4 Missing target STL

**Outside the project evidence taxonomy, Synthetic exercise observation:** The teaching packet states that the target STL named by the manifest is absent from observed storage.
Within the packet, this directly establishes incomplete materialization under the stated artifact contract.
It does not establish why publication was incomplete or independently diagnose the WSE anomaly.
An analogous observation from a real, traceable storage inspection would receive the top-level label **Evidence or experiment**, qualified as operational evidence and bounded to the inspected generation and time.

### 4.5 Missing validation tolerance

**Open question:** The packet supplies no authorized criterion for interpreting the 0.35 m difference with uncertainty.
This establishes a policy gap for the requested acceptance decision.
It does not authorize the reviewer to invent a threshold or substitute visual plausibility.

## 5. Stopping and escalation conditions

### Bounded transfer-misregistration diagnosis

Stop with transfer misregistration as the bounded leading diagnosis if the exact audit finds an incompatible transform, datum, mask, coverage, or source index and the correctly reconstructed values remove or materially alter the high-WSE band.
Record the affected cells, quantities, manifests, source generations, and why the observation discriminates H1 from H2 and H3.

### Residual transient ahead of misregistration

Move H2 ahead of H1 if the rasters and mapping are fully congruent and reproduce every `HFIX` value, but local WSE or flux continues changing across the final grids or the complete balance remains materially open under an authorized criterion.
The next controlled check would then extend or otherwise assess the run under the same inputs and compare local state, balance, and compatible initial conditions.

### Real downstream control

Investigate H3 after mapping is congruent, source values reproduce, local state is stable, complete balance is acceptable, and initial-condition sensitivity is bounded.
Then compare longitudinal WSE, terrain, roughness, boundary geometry, and source conditions against an exactly comparable benchmark or independent observation.

### Materialization condition

Before the scenario can serve as a transfer source, independent observation must find a readable target manifest and every required depth, inundation, and STL asset at the expected address.
Their identities, checksums, schema, CRS, datum contracts, and one-generation consistency must satisfy the applicable materialization contract.
A returned path and present manifest are insufficient.

### Policy escalation

Escalate to the authorized methodology and acceptance owner even if the anomaly is technically explained because the packet has no decision-specific validation tolerance, uncertainty allowance, permissible-use rule, or evidence that the manifest's baked methodology revision is the authorized revision.
The handbook cannot select these rules.

### Stop rather than tune

Stop parameter tuning when an unresolved geometry, datum, mapping, provenance, materialization, or authority gap can explain the symptom.
Changing roughness at that point would not distinguish the cause and could create a compensating error.

## 6. Direct readiness verdict

**NOT READY.**

The packet contains direct materialization failure and several independent evidence gaps.
The target STL is absent, selected membership is unproved, transfer cells cannot be reconstructed, raster registration and vertical-datum compatibility are unknown, local WSE is still changing, full balance and edge evidence are absent, hot-start independence is untested, benchmark comparability and observations are incomplete, and no authorized acceptance policy is supplied.

The smallest evidence set that could change the verdict includes:

1. The selected transfer audit with complete cell mapping and recent local time history.
2. Full inflow, outflow, storage, source, sink, local convergence, and edge evidence under authorized criteria.
3. A cold-start or compatible alternative-hot-start comparison if initial-state sensitivity remains material.
4. Independent observation of every required target artifact and checksum, including the missing STL.
5. A durable selected-library membership record and a complete composite source manifest.
6. An exactly comparable benchmark or independent hydraulic observations with datum, support, timing, and uncertainty.
7. An authorized intended-use acceptance policy and methodology revision match.

The 0.35 m synthetic difference must remain an unresolved comparison result until those conditions define its meaning.
