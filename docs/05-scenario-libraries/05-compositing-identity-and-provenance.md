# Compositing, Identity, and Provenance

A reach-scenario result is one hydraulic realization.
A composite flood inundation map is a later network-scale product that selects compatible reach scenarios, aligns their spatial quantities, and resolves overlap under an explicit rule.

Identity and provenance connect both levels.
They explain which scientific recipe, model realization, forcing, downstream source, initial state, execution settings, and artifacts produced a value, while observed storage establishes whether required outputs are actually materialized.

## Why this topic matters

A depth raster can look plausible while being associated with the wrong model domain, stage source, datum, library membership, or scenario plan.
A manifest path can be returned while one referenced asset is missing.
A composite maximum can be deterministic while mixing incompatible quantities.

The scientific product is trustworthy only when selection, compatibility, identity, provenance, publication, materialization, and validation remain separate and inspectable.

## Prerequisites

Read [The Build-Model Job](../04-model-development/05-build-model-job.md), [Normal-Depth Libraries](01-normal-depth-libraries.md), [KWSE and Stage Transfer](03-kwse-and-stage-transfer.md), and [Scenario Planning and Propagation](04-scenario-planning-and-propagation.md).

## Learning objectives

After this chapter, the reader should be able to:

- distinguish a reach-scenario result, selected reach library, and composite FIM;
- explain selected pixelwise-maximum compositing without claiming current job support;
- distinguish model identity, model realization, run identity, and scenario realization;
- reconstruct current model and run addresses and identify omitted identity inputs;
- explain what current manifests and asset records preserve;
- distinguish exact reuse, publication, storage observation, materialization, and scientific acceptance; and
- identify the minimum provenance required for a reproducible composite.

## Authority boundary

**Selected methodology:** DR-004 ALT-D selects pixelwise maximum for overlap in composite maps and has Alternate Selected status.
The decision selects a pixel rule, not a complete compatible-grid, source-selection, uncertainty, or production contract.

**Current modeling jobs:** The current `twod-fim-jobs` package builds models and runs ND and KWSE scenarios.
No current modeling-job entry point creates a cross-reach composite FIM or a Flows2FIM product.

**Checked-in local compositing path:** `twod-fim-deployment/orchestrator/scripts/f2f.py` contains an unpinned current local export and Flows2FIM invocation path outside `twod-fim-jobs`.
It exports materialized scenarios and a library, invokes Flows2FIM for controls and VRT FIM output, and post-processes every VRT raster band to use `PixelFunctionType` value `max`.
The script is implementation evidence for a checked-in local path, not proof of deployed operation, complete product provenance, scenario compatibility, grid or datum compatibility, validation, or scientific acceptance.

**Target design:** The system-design guide separates identity from realization, places outputs at addressed paths, makes manifests self-describing, and expects a reconciliation loop to observe storage before recording materialization.
The guide's immutability intent is limited by its own statement that rerunning the same inputs can overwrite old content and by current non-transactional publication behavior.

**Checked-in reconciler prototype:** Current local deployment code predicts addresses, verifies selected manifest fields, and writes `materialized_*` rows after storage observation.
It is useful evidence of the intended seam but is not proof of verified deployment or scientific adequacy.

## Three product levels

### Reach-scenario output

One scenario combines one model realization, one discharge, one downstream boundary condition, one optional hot-start depth, one run configuration, and one bounded execution identity.
Its current public-job spatial outputs are final depth, inundation polygon, and STL.
Both public scenario inputs accept `save_zarr` but fail to forward it into `RunConfig`, and the generic true branch cannot complete manifest construction because it applies file-only hashing to a directory-backed Zarr store.

### Selected reach library

A library is the selected subset of scenarios intended to represent one reach over discharge and downstream-stage dimensions.
Current ND storage can contain published search trials that are not selected members.
Current KWSE execution runs every supplied scenario, but a separate plan and materialization record are still needed to show that the intended list is complete.

### Composite FIM

A composite FIM combines compatible reach-level scenario results for a network state or other defined use.
It needs a selection rule for which scenario represents each reach before it needs an overlap rule.

A composite is not merely all depth rasters stacked together.
It must define source reach set, source scenario set, quantity, grid, CRS, vertical datum where relevant, resampling, nodata, overlap, clipping, waterbody handling, creation time, and lineage.

## Selected pixelwise maximum

For compatible depth rasters resampled or aligned to one output grid, the selected DR-004 overlap rule is:

\[
h_{comp}(x,y)=\max_{r\in R(x,y)} h_r(x,y)
\]

- \(h_{comp}(x,y)\) is composite depth at output location \((x,y)\), in m.
- \(h_r(x,y)\) is compatible depth contributed by reach scenario \(r\), in m.
- \(R(x,y)\) is the set of valid contributing reach scenarios at that location.

The maximum is deterministic and independent of raster draw order.
It also preserves the deeper value in transition-zone overlap under the selected rule.

The rule does not prove that the deepest value is physically correct.
One contributor can be too deep because of terrain error, a wrong boundary, clipping, nonconvergence, datum mismatch, or an incompatible network scenario.

The maximum also does not define how to combine WSE.
Depth maximum and WSE maximum can come from different source rasters when terrain differs.
The composite contract must name the exact quantity being maximized.

## Checked-in local Flows2FIM path

The unpinned deployment checkout includes `orchestrator/scripts/f2f.py`.
This script is outside the three modeling jobs and exposes three ordered commands: `scenarios`, `library`, and `aep`.

The `scenarios` step reads materialized ND and KWSE rows from the database through a read-only connection.
It uses `materialized_nd_runs.q_set` rather than every stored ND folder, combines ND and KWSE rows in `scenarios.db`, records exact depth-grid source addresses, writes network links and export provenance, creates start reaches, and exports model domain, inflow, and centerline layers when present.

The `library` step copies or downloads each selected depth grid into the directory layout Flows2FIM expects.
When a source depth grid is missing, it sets that scenario's `map_exists` value to zero so Flows2FIM controls do not select a missing map.

For each AEP flow column, the `aep` step writes forecast flows, invokes the configured Flows2FIM container's `controls` command, removes controls for reaches without forecast flow, and invokes its `fim` command with `-fmt VRT`.
The checked-in default image string is `ghcr.io/ngwpc/flows2fim:0.5.0`, but this file observation does not prove which image, digest, configuration, credentials, or data a deployed process uses.

After Flows2FIM writes `depth.vrt`, `post_process_vrt` rewrites local source paths as relative paths when needed.
For every top-level `VRTRasterBand`, it sets `subClass="VRTDerivedRasterBand"`, creates `PixelFunctionType` when absent, and sets its text to `max`.
The result is a checked-in implementation of pixelwise-maximum VRT overlap for this export path.

The script also publishes `flows.csv`, `controls.csv`, and `depth.vrt` for each AEP column.
Its `scenarios.db` preserves export time, AOI description, source database label, reach-number mapping, network links, scenario values, and source depth addresses.
Those fields are useful bounded provenance, but they do not establish a complete immutable composite manifest or scientific acceptance record.

No task check executed this script, inspected a produced VRT, or verified Flows2FIM or GDAL runtime behavior.
The checkout revision is unpinned under the workspace constraints.

## Compatibility checks before compositing

Pixelwise comparison requires more than equal array dimensions.
At minimum, verify:

- the same physical quantity and units;
- compatible horizontal CRS and exact output transform;
- a stated resampling method and nodata rule;
- compatible terrain and vertical datum when WSE or derived depth is compared;
- intended scenario compatibility across reaches;
- selected library membership rather than storage presence alone;
- completed materialization of every source manifest and asset;
- hydraulic acceptance for the intended product use; and
- explicit lake, coast, confluence, and breakpoint handling.

Where source grids differ, resampling changes values and spatial support.
The output needs to preserve both the original source artifact and the transformation provenance.

## Model identity and realization

The target design distinguishes identity, which describes a scientific recipe, from realization, which describes where that recipe was realized.
Current model code implements a bounded form of that distinction.

The current model identity hash includes:

- baked `SDR_COMMIT`;
- target reach-geometry WKT hash;
- grid resolution;
- EPSG code;
- DEM source-string hash;
- LULC source-string hash; and
- resolved LULC-to-Manning lookup hash.

The eight-character identity hash is joined with a domain code to form:

```text
<model_identity_hash>_N<north>S<south>E<east>W<west>
```

The domain code contains integer cell offsets from a grid-snapped anchor.
It is a human-readable realization code rather than a content hash or adequacy result.

### Current model-identity omissions

Current model identity does not include immutable DEM or LULC source content, prepared-network path or version, caller-supplied upstream topology, `ds_of_lake`, inflow placement settings, centerline-buffer multiplier, domain buffer, `other_geometries`, or authored domain.

Some omitted geometry changes alter the domain code.
That does not cover every omission because equal offsets do not prove equal geometry, source bytes, vertical datum, inflow placement, or topology.

Two scientifically different models can therefore share the same identity hash, and in some cases can target the same full model ID.
See [CONF-013](../reference/conflicts-and-open-questions.md#conf-013-model-identity-existing-model-and-warning-gaps).

## Run identity and scenario realization

The current run identity object contains only:

```json
{
  "sdr_commit_id": "<baked revision>",
  "solver": "<solver enum>"
}
```

Its hash is truncated to eight hexadecimal characters.
It does not include solver executable version or build, image digest, run configuration, hardware, boundary geometry, downstream source, or hot-start source.

Those omitted values appear partly in scenario inputs and provenance, but they do not move the run-identity directory.
The current jobs checkout also bakes a Decision Register revision different from the revision reviewed by this handbook.

The scenario realization is named by downstream boundary type and value plus discharge.
Current directories use `nd=<slope>/q=<Q>` or `kwse=<nominal_stage>/q=<Q>` under model identity and run identity.
ND slope names use one decimal place in scientific notation, and KWSE names use one decimal place.
These lossy formats allow distinct raw boundary values to map to the same directory and scenario-code text.

For KWSE, the nominal stage labels the scenario but the actual transfer uses cell-specific downstream `depth + terrain` values.
Changing the source field without changing the nominal label can change hydraulic forcing while preserving the same scenario folder unless another compared input prevents reuse.
Exact input equality prevents reuse when raw values differ, but it does not move a new run to another address or prevent sequential overwrite and mixed generations at the shared rounded path.
The contract must validate raw values onto the naming grid, increase address precision, or include a content-complete scenario identity that distinguishes the realizations.

## Current content-addressed paths

Model results currently use this hierarchy:

```text
<results_root>/
  reach=<reach_id>/
    <model_identity_hash>/
      <run_identity_hash>/
        <nd=slope|kwse=stage>/
          q=<discharge>/
            depth.tif
            inundated_area.geojson
            stl.geojson
            scenario_manifest.json
```

The path is content-addressed only to the degree that its hashes cover output-affecting content.
Current identity omissions and truncation mean it is not a complete cryptographic identity for the entire scientific realization.
The hierarchy also omits Zarr from current public-job artifacts because those constructors leave `RunConfig.save_zarr=false` regardless of the accepted public input.

The model directory instead uses the full `model_id`, including domain code.
Scenario paths intentionally use only the model identity portion, while the full model ID is recorded inside each scenario manifest.

This target-oriented choice aims to avoid stranding all reach runs when the domain realization changes.
Current reconciler verification still compares the full model ID in a scenario manifest, so reuse across domain changes is not guaranteed by the folder design alone.

## Manifest provenance

A current scenario manifest records:

- `type`, `hash_algo`, producer version, and creation time;
- reach ID, run identity hash, run identity object, scenario code, and full model ID;
- `self_href`;
- full `RunScenarioInputs`, including domain, grid, terrain, roughness, boundaries, hot-start asset, run config, centerline, base output, and identities;
- final termination and response properties; and
- output `Asset` records with href, checksum prefix, source metadata where applicable, retrieval time where applicable, and derived flag.

This is strong bounded provenance because the downstream transfer assets and hot-start depth are embedded in the target scenario inputs.
It allows a reviewer to trace exact source addresses and their recorded checksums.

The manifest still has limits.
It does not persist the complete solver log, raw return code, full convergence history, complete mass balance, detailed edge-check history, exact executable build, complete STL smoothing identity, library membership, scenario-plan rationale, storage-observation result, or scientific-acceptance decision.

## Exact reuse

`run_scenario` predicts one manifest address and checks whether a manifest already exists.
It reuses the scenario only when the stored manifest validates and its complete stored `RunScenarioInputs` equal the requested inputs.

Exact input equality is stronger than the looser `existing_scenarios` adoption predicate used by the ND adaptive job.
It prevents many accidental cross-config reuses.

Exact reuse still does not prove:

- that every referenced asset exists now;
- that every current asset matches its recorded checksum;
- that publication completed atomically;
- that selected membership remains current;
- that the source scenario is still topologically or scientifically intended; or
- that the result is hydraulically adequate.

It also does not prevent same-address collision when distinct raw ND slope or KWSE boundary values round to the same directory name.

An exactly reused result therefore needs observation and acceptance appropriate to its consumer.

## Publication

For a newly simulated public ND or KWSE scenario, current code copies depth, inundation polygon, STL, and finally the manifest.
It does not stage a complete generation and atomically promote the directory.

The generic `RunConfig.save_zarr=true` path creates a directory-backed store and then attempts to hash it with a helper that opens one file, so it fails before a manifest can advertise and publish that store.

Writing the manifest last helps distinguish many incomplete writes from completed sequences.
It does not prevent a failure from leaving earlier assets, replacing part of an existing generation, or pairing an older manifest with newly overwritten assets at the same address.
Rounded boundary-value collisions provide one current path by which unequal requests can target that same address.

The target design describes outputs as immutable but also says a rerun with the same inputs overwrites old content.
True immutable-version intent would require a generation-specific or content-complete address that never changes, or an atomic promotion protocol with retained prior generations.
Current code does not supply that complete guarantee.

## Storage observation and materialized state

Publication is a producer action.
Materialization is an observer conclusion that the specific object required by current intent exists and satisfies the observer's contract.

A robust materialization check should:

1. Compute or discover the address implied by current intent.
2. Read the manifest from storage rather than trust a returned path.
3. Verify reach, identity, realization, model, scenario folder, and requested revision.
4. Verify that raw boundary values map injectively to the address or satisfy an authorized naming grid.
5. Verify every required asset exists and matches integrity metadata.
6. Verify the scenario depth CRS against the realized model CRS rather than accepting the EPSG:5070 missing-CRS fallback without comparison.
7. Verify projected metre-based horizontal units or documented conversions for geometry, slope, discharge density, cell area, storage, flooded area, transfer, and compositing.
8. Check authored bounds, selected membership, and plan completeness where those are part of intent.
9. Record the observed proof separately from job status.

The checked-in reconciler prototype follows part of this model by writing `materialized_models`, `materialized_nd_runs`, and `materialized_kwse_runs` only after storage inspection.
Its KWSE observer requires every recomputed planned scenario to exist and pass manifest verification before writing one materialized row.

That row is target-oriented proof within the prototype's implemented checks.
It is not proof of deployed execution, full asset checksum verification, or scientific adequacy.

## Reconciliation

Reconciliation repeatedly compares desired state with observed materialized state and acts on the difference.
It is designed to recover from crashes, lost job status, partial progress, and downstream changes without treating a one-time response as the system of record.

A job can report success and still fail observation because the manifest is absent, refused, incomplete, or at a different address.
A job can be lost by the execution service and still be adopted later if complete storage evidence appears.

Reconciliation also separates intent from inventory.
Storage can contain obsolete domains, rejected ND search trials, superseded run identities, partial writes, and valid artifacts not requested by current intent.
Only an explicit materialization proof identifies which subset currently satisfies desired state.

## Composite provenance record

A composite product should have its own manifest rather than inherit trust from source filenames.
At minimum it should record:

- composite product identity and immutable version or generation;
- creation time and producer version;
- target network, hydrologic state, or scenario-selection request;
- prepared-network revision and source-to-prepared lineage;
- exact source reach-scenario manifest hrefs and checksums;
- source library-membership and materialization proofs;
- source model and run identities;
- source and output CRS, transform, resolution, extent, units, vertical datum, and nodata;
- resampling and alignment methods;
- DR-004 pixelwise-maximum rule and exact quantity to which it was applied;
- lake, coast, confluence, gap, and missing-source handling;
- output asset hrefs and checksums;
- warnings, exclusions, and quality metrics; and
- scientific acceptance status separate from production completion.

Without this record, a later consumer cannot distinguish one reproducible composite from an arbitrary stack of available rasters.

## Flows2FIM evidence boundary

The Decision Register uses Flows2FIM language for network map assembly and lake breakpoints.
The current jobs checkout has no cross-reach Flows2FIM entry point.
The deployment checkout has the unpinned local `scripts/f2f.py` export and invocation path described above.

The system-design guide discusses downstream-to-upstream libraries and later assembly as target system work.
The script proves that a local code path requests a Flows2FIM VRT and rewrites its bands to `max`.
It does not prove deployed operation, successful execution, source completeness, scenario compatibility, grid or datum compatibility, output validity, or scientific acceptance.

Any current product claim needs direct evidence from its producing repository, deployed configuration, source set, manifest, and output validation.

## Failure modes and diagnostic signals

### Maximum of incompatible quantities

Combining depth with WSE or mixing metres and feet can produce a numerical maximum with no scientific meaning.
Verify quantity and units before alignment.

### Datum mismatch hidden by depth

Depth rasters can appear directly comparable while their terrain and stage sources use incompatible datums or terrain realizations.
Trace the source terrain and vertical reference whenever network continuity or WSE matters.

### Storage presence treated as membership

Published ND search trials can remain beside selected members.
Require a durable library index or materialization record.

### Scenario incompatibility at a confluence

Individually valid reach scenarios can represent incompatible network conditions.
Preserve the scenario-selection rule and joint-condition limitations in the composite manifest.

### Identity collision by omission

Changed source bytes, topology, boundary geometry, or settings can share a current identity address.
Compare full manifest inputs and immutable source identities, not just the folder hash.

### Partial same-address overwrite

Sequential copy can expose mixed generations when publication fails.
Lossy one-decimal boundary naming can direct distinct raw scenarios to the same address before that copy begins.
Use generation-specific staging, atomic promotion where supported, complete observation, or another authorized recovery protocol.

### Scenario CRS mislabeled by fallback

When a solver `.wd` profile omits CRS, current conversion assigns EPSG:5070 instead of recovering the realized model CRS.
For a non-5070 model this can mislabel depth, inundation, STL relationships, later transfer, and compositing even when numeric arrays appear plausible.

### Materialization mistaken for validation

A materialized object can satisfy storage and intent checks while remaining hydraulically inadequate.
Keep validation and scientific acceptance as later explicit gates.

## Competency check

1. What must be selected before pixelwise maximum can be applied?
2. Why is a maximum-depth composite not necessarily a maximum-WSE composite?
3. Which values form current model and run identities?
4. Which output-affecting inputs are omitted from current identity?
5. Why does exact input reuse not prove materialization?
6. How can lossy boundary formatting cause same-address overwrite despite exact input comparison?
7. What does a materialized row prove, and what does it not prove?
8. What does `scripts/f2f.py` prove, and which deployment, compatibility, provenance, and validation claims remain unverified?

## Further reading and source notes

- [The Build-Model Job](../04-model-development/05-build-model-job.md) explains current model identity and publication.
- [KWSE and Stage Transfer](03-kwse-and-stage-transfer.md) explains source-manifest and asset provenance inside a scenario.
- [Scenario Planning and Propagation](04-scenario-planning-and-propagation.md) explains plan completeness and dependency provenance.
- [Decision-Code-Artifact Crosswalk XW-008](../reference/decision-code-artifact-crosswalk.md#xw-008-composite-fim-pixel-calculation) maps selected compositing to current modeling-job absence and the checked-in local Flows2FIM path.
- [Decision-Code-Artifact Crosswalk XW-015](../reference/decision-code-artifact-crosswalk.md#xw-015-build-model-lifecycle-identity-and-materialization-evidence) maps current build identity and materialization gaps.
- Bibliography entries SDR-001, SDR-002, SDR-011, JOB-003, JOB-006, JOB-007, JOB-009, JOB-010, SYS-001, SYS-002, SYS-003, SYS-005, SYS-006, and SYS-007 identify the reviewed local sources.

No external source was required for this chapter.
