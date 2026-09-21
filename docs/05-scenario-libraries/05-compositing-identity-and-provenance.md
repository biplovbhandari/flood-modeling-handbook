# Compositing, Identity, and Provenance

Compositing combines compatible reach-scenario outputs into a larger inundation product.
The numerical overlap rule is only one part of the method because source selection, compatibility, identity, publication, observation, and provenance determine what the product means.

## Why this topic matters

A deterministic raster maximum can still combine incompatible scenarios, datums, quantities, or generations.
Storage can also contain search trials, obsolete results, and partial writes beside selected members.
A defensible composite therefore begins with explicit membership and ends with its own evidence record.

## Prerequisites

Read [The Model-Building Operation](../04-model-development/05-build-model-job.md), [Discharge-Only Scenario Libraries](01-normal-depth-libraries.md), [Downstream-Stage-Aware Libraries and Stage Transfer](03-downstream-stage-and-transfer.md), and [Scenario Planning and Propagation](04-scenario-planning-and-propagation.md).

## Learning objectives

After this chapter, the reader should be able to:

- distinguish reach-scenario output, selected reach library, and composite product;
- apply pixelwise maximum only to compatible quantities;
- distinguish scientific identity from storage realization;
- identify omissions that make reuse unsafe;
- explain generation-based publication and storage observation;
- specify a composite provenance record; and
- separate materialization from scientific acceptance.

## Three product levels

### Reach-scenario output

One reach-scenario output contains final depth or another stated quantity plus its complete model, forcing, boundary, run, and diagnostic record.

### Selected reach library

A selected library is an explicit subset of complete reach scenarios chosen by a documented method.
It excludes search overhead, superseded identities, and incomplete generations.

### Composite product

A composite combines selected, compatible reach scenarios for a stated network and hydrologic condition.
It needs its own identity, artifact set, provenance, diagnostics, and acceptance decision.

## Pixelwise maximum

For compatible depth rasters aligned to one output grid:

\[
h_{comp}(x,y)=\max_{r\in R(x,y)}h_r(x,y)
\]

$h_{comp}(x,y)$ is composite depth at output location $(x,y)$, $h_r(x,y)$ is the compatible depth from reach scenario $r$, and $R(x,y)$ is the set of valid contributors.

The maximum is deterministic and independent of draw order.
It preserves the deeper value in overlap under the stated rule.

The rule does not prove that the deepest contributor is physically correct.
Terrain error, an unsuitable boundary, clipping, nonconvergence, or incompatible network conditions can all make one source too deep.

Depth maximum and water-surface maximum are different operations when source terrains differ.
The composite record must name the exact quantity and units being combined.

## Compatibility checks before compositing

Verify at least:

- the same physical quantity and units;
- compatible horizontal reference and exact output transform;
- stated resampling and nodata rules;
- compatible terrain and vertical reference when water surface or derived depth is compared;
- compatible network and hydrologic conditions across reaches;
- selected membership rather than storage presence;
- observed complete source artifacts;
- adequate edge, convergence, mass-balance, and domain evidence; and
- explicit confluence, lake, coast, gap, and missing-source treatment.

Where source grids differ, resampling changes values and spatial support.
Preserve original source artifacts and transformation provenance.

## Applied composite

**Applied example:** The synthetic packet contains one grid-and-datum-compatible candidate from each of `R-100`, `R-300`, and `R-200`.
All three candidates use 10 m cells, metre depth units, the same horizontal reference, and vertical datum `VD-1`.
Those agreements are necessary but do not make the network condition compatible.

The selected network condition is:

| Reach | Discharge | Downstream-stage condition | Source role |
| --- | ---: | --- | --- |
| `R-100` | 100 m3/s | 102.5 m field interpolated from `R-200` 175 and 200 m3/s sources | Upstream mainstem candidate |
| `R-300` | 60 m3/s | 102.5 m field interpolated from `R-200` 175 and 200 m3/s sources | Tributary candidate |
| `R-200` | 150 m3/s | 0.0015 m/m terminal slope and reported confluence stage 102.2 m | Legacy downstream candidate |

The two tributary candidates sum to 160 m3/s, which exceeds the 150 m3/s `R-200` candidate before any local incremental contribution is added.
Under the 84:51:7 forcing contract, a 150 m3/s `R-200` scenario would instead allocate 89 m3/s to the `R-100` boundary, 54 m3/s to the `R-300` boundary, and 7 m3/s to local incremental inflow.
The packet therefore has a 160-versus-150 flow mismatch and does not satisfy the current forcing contract.

The upstream candidates use a 102.5 m field interpolated from the 175 and 200 m3/s `R-200` sources.
The included downstream candidate is a different 150 m3/s realization with a reported 102.2 m confluence stage.
The packet therefore also has a stage-source mismatch.

**Open question:** Which hydrologic joint-condition method should bind the tributary and downstream discharges for this composite?
This compatibility audit concludes that the packet is not ready for compositing.
The candidates must be replaced by a jointly consistent flow allocation and matching downstream stage-source realization before pixelwise combination.

## Identity and realization

Identity describes the scientific recipe.
Realization describes where and when that recipe was produced.

A model identity should cover network lineage, source content, grid, references, terrain, roughness, domain, boundary construction, structures, and method version.
A scenario identity should add total forcing, each named boundary allocation, local incremental forcing and geometry, downstream condition, source transfer field, run settings, initial state, and producer version.
A composite identity should add the selected source scenario identities, output grid, transformation rules, overlap method, gap treatment, and network-condition rule.

An address can include a short readable label, but the record should retain the canonical identity object and full digest.

## Identity omissions make reuse unsafe

If identity omits source bytes, topology, boundary geometry, domain, datum, or run settings, scientifically different results can share one address.
Comparing only a short folder label cannot detect the difference.

**Design principle:** Every output-affecting field belongs either in identity or in an explicitly justified realization dimension that prevents unsafe reuse.

## Address precision

An address function must map distinct identities to distinct locations or generations.
Lossy formatting can violate this rule.

**Applied example:** Stages 102.21 m and 102.24 m both round to `102.2` when an address keeps one decimal place.
Exact record comparison can reject reuse but cannot prevent a later unequal request from overwriting the same location.

Use validated naming grids, sufficient precision, full identity digests, or generation-specific addresses.

## Manifest provenance

A scenario or composite record should include:

- type, schema version, producer version, and creation time;
- canonical identity object and full digest;
- complete input and realized-settings records;
- exact source record addresses and integrity values;
- output artifact roles, addresses, sizes, and integrity values;
- horizontal and vertical references, units, transforms, extent, and nodata;
- transformation, resampling, and overlap methods;
- diagnostics, warnings, exclusions, and quality metrics; and
- materialization and scientific-acceptance states as separate fields.

The record should preserve enough information to reproduce the source selection and explain every pixel's eligible contributors.

## Exact reuse

Exact reuse requires more than a matching record.
The consumer should observe every required source and output artifact and verify identity, generation, role, integrity metadata, and compatibility.

Exact input equality does not prove that:

- artifacts still exist;
- bytes match their recorded integrity values;
- publication completed as one generation;
- library membership remains current;
- the source is still intended by the current plan; or
- the result is hydraulically adequate.

## Publication

Sequentially copying artifacts into a live address can expose partial or mixed generations.
Writing the record last makes many incomplete attempts detectable but does not prevent earlier assets from replacing part of an existing generation.

**Design principle:** Stage a complete generation, verify it, and promote one immutable generation reference atomically when supported.
Retain prior generations or another recoverable record so failed promotion does not destroy the last complete product.

## Storage observation and materialization

Publication is a producer action.
Materialization is an observer conclusion that the specific generation required by current intent exists and satisfies the observation contract.

A materialization check should:

1. Derive the complete intended identity.
2. Read the record from final storage.
3. Verify identity, generation, source membership, and method version.
4. Verify every required artifact and integrity value.
5. Verify quantity, units, references, transform, extent, and nodata.
6. Verify that boundary values map unambiguously to identity and address.
7. Verify plan completeness and selected membership.
8. Record observed evidence separately from execution status.

Materialization does not prove scientific acceptance.
It proves that the intended artifact set is present and internally satisfies the observer's checks.

## Reconciliation

Reconciliation repeatedly compares desired state with observed materialized state and acts on the difference.
It can recover from crashes, lost execution status, partial progress, and downstream changes without treating one process response as the system of record.

Storage inventory is not desired state.
It can contain obsolete models, rejected search trials, superseded identities, partial writes, and complete artifacts that current intent does not request.

## Composite provenance record

The composite record should state:

- immutable composite identity or generation;
- prepared-network version and lineage;
- target network and joint hydrologic condition;
- exact source scenario identities and observed artifacts;
- source library membership evidence;
- source and output grids, references, units, datums, extent, and nodata;
- alignment and resampling methods;
- the pixelwise-maximum rule and quantity;
- confluence, lake, coast, gap, and missing-source handling;
- output assets and integrity values;
- warnings and exclusions; and
- scientific acceptance separate from production completion.

Without this record, a later consumer cannot distinguish a reproducible product from an arbitrary stack of available rasters.

## Synthetic failure examples

### Maximum of incompatible quantities

One source contains depth and another contains water-surface elevation.
The numeric maximum has no consistent physical meaning.

### Datum mismatch hidden by depth

Depth rasters appear comparable, but their terrain and stage sources use different vertical references.
The overlap can look smooth while network water surfaces are inconsistent.

### Storage presence treated as membership

An obsolete 150 m3/s `R-200` trial is present and is included even though the current library index excludes it.

### Incompatible confluence conditions

The selected tributary discharges do not match the downstream total under any stated routing or timing rule.
Every individual raster can be valid while the joint composite is not.

### Identity collision by omission

Changed topology or boundary geometry shares an address because identity covers only reach, grid, and source names.

### Partial same-address overwrite

An unequal scenario collides after stage rounding and overwrites depth before publication fails.
The old record now points to new depth and old companion artifacts.

### Materialization mistaken for validation

All expected assets are present, but the scenario has unacceptable mass balance or domain clipping.
Observation and scientific acceptance must remain separate gates.

## Competency check

1. What must be selected before pixelwise maximum can be applied?
2. Why is maximum depth not necessarily maximum water-surface elevation?
3. Which flow and stage-source mismatches make the applied packet not ready for compositing?
4. Which output-affecting values belong in composite identity?
5. Why does exact record equality not prove current materialization?
6. How can lossy stage formatting cause a mixed generation?
7. What does materialization prove, and what does it not prove?
8. Which fields belong in a composite provenance record?

## Further reading and source notes

- **Scientific foundation:** Model credibility and validation boundaries are supported by [SCI-043](../reference/bibliography.md#sci-043-nasa-standard-for-models-and-simulations), [SCI-044](../reference/bibliography.md#sci-044-nist-assessment-of-accuracy-and-reliability), and [SCI-045](../reference/bibliography.md#sci-045-epa-environmental-model-guidance).
- **Scientific foundation:** Raster grid and transform concepts are supported by [SCI-030](../reference/bibliography.md#sci-030-gdal-geotransform) and [SCI-042](../reference/bibliography.md#sci-042-rasterio-reprojection-and-resampling).
