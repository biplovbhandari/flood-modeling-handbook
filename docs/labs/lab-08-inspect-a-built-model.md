# Lab 8: Inspect a Synthetic Model Record

This lab uses a complete synthetic model record and supplied metadata tables.
No binary raster or external artifact is required.

## Prerequisites

Complete these chapters before starting:

- [Network Preparation](../04-model-development/01-network-preparation.md)
- [Terrain, Topobathymetry, and Structures](../04-model-development/02-terrain-topobathymetry-and-structures.md)
- [Roughness and Land Cover](../04-model-development/03-roughness-and-land-cover.md)
- [Domain and Boundary Geometry](../04-model-development/04-domain-and-boundary-geometry.md)
- [The Model-Building Operation](../04-model-development/05-build-model-job.md)

Read [Source Authority](../reference/source-authority.md), [Lab Conventions](README.md), [MX-005](../reference/decision-code-artifact-crosswalk.md#mx-005-model-development), [MX-008](../reference/decision-code-artifact-crosswalk.md#mx-008-identity), and [MX-011](../reference/decision-code-artifact-crosswalk.md#mx-011-materialization).

## Execution boundary

All required evidence is embedded below.
Optional tools may be used only for generic arithmetic.
Do not infer information from a file, service, or model run that is not in this prompt.

## Learning objectives

After completing this lab, the learner should be able to:

- distinguish requested inputs, realized settings, identity, artifacts, publication, and storage observation;
- verify grid dimensions, transforms, bounds, references, and raster compatibility from metadata;
- distinguish source identity from a mutable source label;
- determine whether model identity covers output-affecting settings;
- interpret structured warnings within their implemented scope;
- distinguish matching integrity values from scientific adequacy; and
- issue a bounded evidence verdict.

## Complete synthetic model record

The record describes one constructed generation for reach R-200.
All names and values in the record are teaching data.

### Record and lineage

| Field | Recorded value |
| --- | --- |
| Record type | Model record |
| Record version | 2 |
| Model label | R-200-M2 |
| Generation | G-02 |
| Prepared network | N-1 |
| Target reach | R-200 |
| Immediate upstream reaches | R-100 and R-300 |
| Downstream relation | Terminal modeling reach |
| Creation time | 2030-04-12 15:00 UTC |
| Producer build | PB-7 |
| Model-building method | MB-2 |

### Requested inputs and realized settings

| Field | Requested value | Realized value |
| --- | --- | --- |
| Terrain source | Synthetic terrain collection | Content T-200-A |
| Roughness source | Synthetic land-cover collection | Content M-200-A |
| Roughness lookup | Lookup RL-3 | Lookup RL-3, with no unmapped classes |
| Grid resolution | 10 m | 10 m square cells |
| Horizontal reference | SP-1 | SP-1, projected metres |
| Vertical reference | VD-1 | VD-1, metres |
| Domain method | Computed and snapped outward | Bbox \([21020,48100,22640,49620]\) m |
| Structure treatment | No explicit adjustments | No explicit adjustments |
| Required artifact roles | Terrain, roughness, domain, inflows, outflow, and model record | Same roles |

The resolved terrain content checksum is 1d50d412ceafcf530804bdf4c39498d42690c19d78bd486ab85d96a90dbbf1d8.
The resolved roughness content checksum is 5b602f93fb5589ca7f356e26e8344f53d7dd7064cf9dabd944f53d13a871650f.

### Grid and domain

| Field | Recorded value |
| --- | --- |
| Grid origin | Upper-left corner at \((21020,49620)\) m |
| Affine transform | \((21020,10,0,49620,0,-10)\) |
| Columns | 162 |
| Rows | 152 |
| Active cells | 22,944 |
| Domain bbox | \([21020,48100,22640,49620]\) m |
| Domain geometry identity | DOMAIN-R200-G2 |
| Grid anchor | \((21020,49620)\) m |

### Boundary record

| Boundary role | Geometry identity | Realized description |
| --- | --- | --- |
| R-100 inflow | INFLOW-R100-G2 | Six selected north-edge faces, kept separate from the other inflow. |
| R-300 inflow | INFLOW-R300-G2 | Four selected west-edge faces, kept separate from the other inflow. |
| Local contribution | LOCAL-R200-G2 | One interior source region with its own allocation record. |
| Terminal outflow | OUTFLOW-R200-G2 | Twenty-six selected south-edge faces. |

### Canonical identity object

The canonical identity object includes:

- target reach R-200, prepared network N-1, and reach lineage;
- terrain content identity and checksum;
- roughness content identity and checksum;
- horizontal reference SP-1 and vertical reference VD-1;
- 10 m resolution, the exact transform, dimensions, active mask identity, and domain bbox;
- all four boundary geometry identities and their selected-cell identities;
- structure treatment;
- model-building method MB-2; and
- producer build PB-7.

The canonical identity object does not include roughness lookup RL-3.
Its recorded full SHA-256 digest is 543f1d5a28ea6a7e22a99b8d80a90b9c88829375b03f4f59d6d3d132b029c0eb.

### Structured warnings

| Warning | Severity | Observation | Review threshold | Recommended check |
| --- | --- | --- | --- | --- |
| East-side domain clearance | Review | The connected terrain corridor is 30 m from the east domain edge. | 50 m | Inspect connected wet components and edge gradients across the planned scenario range. |

The warning list contains no other entries.

### Artifact inventory recorded before promotion

| Role | Artifact identity | Size | Recorded SHA-256 |
| --- | --- | ---: | --- |
| Terrain | TERRAIN-R200-G2 | 98,496 bytes | 1d50d412ceafcf530804bdf4c39498d42690c19d78bd486ab85d96a90dbbf1d8 |
| Roughness | ROUGHNESS-R200-G2 | 98,496 bytes | 5b602f93fb5589ca7f356e26e8344f53d7dd7064cf9dabd944f53d13a871650f |
| Domain | DOMAIN-R200-G2 | 2,140 bytes | 9f47fe3004ee2b9a261442124aa4aff5f2dced178a0dcc0b515ff2bd464d51f0 |
| Inflows | INFLOWS-R200-G2 | 3,010 bytes | 0fd0ac78a1da7ad90d83eb15b145715cae765a211fc59a6fc6aada46f0615eb4 |
| Outflow | OUTFLOW-R200-G2 | 1,420 bytes | 1b294e3dd1d20aeca625f423c2a3b7930ad59f7c78c076ad152e1366cc0eb1d4 |
| Model record | MODEL-RECORD-R200-G2 | 8,220 bytes | 4d558a0265927e27d19cf8ebd71cbea3d4c89cf91eb9f91d74cc8f4eda722332 |

### Publication and observation

The publication record says that all six roles were staged, their staged checks passed, and generation G-02 was promoted at 15:02 UTC.
An independent observation at 15:05 UTC produced this table.

| Role | Observed generation | Observed size | Observed SHA-256 | Observation result |
| --- | --- | ---: | --- | --- |
| Terrain | G-02 | 98,496 bytes | 1d50d412ceafcf530804bdf4c39498d42690c19d78bd486ab85d96a90dbbf1d8 | Match |
| Roughness | G-02 | 98,496 bytes | 5b602f93fb5589ca7f356e26e8344f53d7dd7064cf9dabd944f53d13a871650f | Match |
| Domain | G-02 | 2,140 bytes | 9f47fe3004ee2b9a261442124aa4aff5f2dced178a0dcc0b515ff2bd464d51f0 | Match |
| Inflows | G-02 | 3,010 bytes | 0fd0ac78a1da7ad90d83eb15b145715cae765a211fc59a6fc6aada46f0615eb4 | Match |
| Outflow | G-02 | 1,420 bytes | f5a3c79ba365e87959f6339d14547b1c793316925695806d5ca3673d7c01e67d | Checksum mismatch |
| Model record | G-02 | 8,220 bytes | 4d558a0265927e27d19cf8ebd71cbea3d4c89cf91eb9f91d74cc8f4eda722332 | Match |

### Supplied raster metadata

| Property | Terrain raster | Roughness raster |
| --- | --- | --- |
| Artifact identity | TERRAIN-R200-G2 | ROUGHNESS-R200-G2 |
| Width | 162 columns | 162 columns |
| Height | 152 rows | 152 rows |
| Data type | 32-bit floating point | 32-bit floating point |
| Nodata | -9999 | -9999 |
| Affine transform | \((21020,10,0,49620,0,-10)\) | \((21020,10,0,49620,0,-10)\) |
| Bounds | \([21020,48100,22640,49620]\) m | \([21020,48100,22640,49620]\) m |
| Horizontal reference | SP-1, projected metres | SP-1, projected metres |
| Quantity and units | Terrain elevation in metres | Manning roughness, dimensionless |
| Vertical reference | VD-1 | Not applicable to roughness |
| Resampling record | Bilinear from the resolved terrain source | Nearest category before lookup RL-3 |
| Realized range | 99.40 to 106.20 m | 0.030 to 0.085 |

## Part A: Check the record structure

Separate requested inputs, realized settings, identity fields, artifacts, warning evidence, publication claims, and observation results.
State why those categories cannot be collapsed into one completion claim.

## Part B: Check spatial compatibility

Calculate the expected number of columns and rows from the bbox and 10 m resolution.
Confirm whether the recorded transform, dimensions, raster bounds, horizontal reference, and cell sizes agree.
State what this metadata can and cannot establish about terrain quality, roughness suitability, vertical compatibility, and hydraulic adequacy.

## Part C: Assess provenance and identity

Identify which source evidence is immutable and which values are only readable labels.
Determine whether the canonical identity covers every output-affecting realized setting in the record.
Explain the consequence of omitting RL-3 from identity.

## Part D: Interpret the warning

State exactly what the east-side warning establishes.
State what the absence of other warnings establishes.
List the smallest scenario evidence needed to disposition the warning.

## Part E: Assess publication and materialization

Classify the staged verification, promotion event, and independent observation as separate evidence.
Determine whether generation G-02 satisfies the complete materialization contract.
Explain why matching checksums would still not prove scientific adequacy.

## Part F: Issue an evidence verdict

Choose exactly one verdict.

- MODEL RECORD INSPECTED; SCENARIO READINESS NOT ESTABLISHED
- MODEL READY FOR SCENARIO USE

State the verdict first.
Then list the smallest evidence or correction set that could change it.

## Deliverable

Submit a short review with these sections:

1. Record categories.
2. Spatial-compatibility calculation.
3. Provenance and identity assessment.
4. Warning assessment.
5. Publication and materialization assessment.
6. Direct evidence verdict.

## Competency criteria

The lab is complete when the answer:

- derives 162 columns and 152 rows;
- finds the two raster grids horizontally aligned with the record and domain;
- distinguishes terrain elevation metadata from the dimensionless roughness quantity;
- identifies RL-3 as an output-affecting setting omitted from identity;
- treats the east-side warning as a review trigger rather than proof of clipping;
- identifies the outflow checksum mismatch as a materialization failure;
- keeps integrity, materialization, and scientific adequacy separate; and
- issues MODEL RECORD INSPECTED; SCENARIO READINESS NOT ESTABLISHED.

After completing the lab, compare the reasoning with [Lab 8 Solution](solutions/lab-08-inspect-a-built-model-solution.md).
