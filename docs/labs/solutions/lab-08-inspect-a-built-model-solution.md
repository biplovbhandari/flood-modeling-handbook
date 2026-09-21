# Lab 8 Solution: Inspect a Synthetic Model Record

This solution uses only the synthetic packet in [Lab 8](../lab-08-inspect-a-built-model.md) and the linked handbook references.
It does not inspect a binary artifact or establish that another model is adequate.

## 1. Record categories

The requested inputs state the recipe before realization.
The realized settings state the exact sources, references, grid, domain, lookup, structure treatment, and artifact roles that the operation used.
The canonical identity object states which output-affecting values are intended to distinguish the model.
The artifact inventory records expected roles, sizes, and integrity values before promotion.
The warning record reports one implemented check.
The publication record reports staging and promotion events.
The independent observation reports what was present after promotion.

These categories answer different questions.
A valid request does not prove the realized sources, a complete record does not prove artifact presence, publication does not prove materialization, and materialization does not prove scientific adequacy.
That separation follows [MX-005](../../reference/decision-code-artifact-crosswalk.md#mx-005-model-development) and [MX-011](../../reference/decision-code-artifact-crosswalk.md#mx-011-materialization).

## 2. Spatial-compatibility calculation

The domain width is:

\[
22640-21020=1620\ \text{m}
\]

At 10 m per column:

\[
N_x=\frac{1620\ \text{m}}{10\ \text{m}}=162
\]

The domain height is:

\[
49620-48100=1520\ \text{m}
\]

At 10 m per row:

\[
N_y=\frac{1520\ \text{m}}{10\ \text{m}}=152
\]

The calculated dimensions match the model record and both raster tables.
The transform starts at the upper-left corner \((21020,49620)\) m, advances 10 m east by column, and advances 10 m south by row.
With 162 columns and 152 rows, it reaches the supplied bounds exactly.

Both rasters have the same dimensions, transform, bounds, nodata value, and horizontal reference.
They are horizontally aligned with each other and with the recorded domain.

The terrain table identifies elevation in metres on VD-1.
The roughness table identifies a dimensionless Manning coefficient, so a vertical datum does not apply to that quantity.
The metadata supports grid and reference compatibility for the two supplied tables.
It does not prove that the terrain source is accurate, bilinear resampling is adequate for the intended use, the categorical conversion is scientifically suitable, RL-3 is calibrated, structures are represented, or the domain and boundaries are hydraulically adequate.

## 3. Provenance and identity assessment

The readable labels Synthetic terrain collection and Synthetic land-cover collection are not immutable provenance by themselves.
The resolved content identities and full SHA-256 values are content-level evidence in the record.
The packet does not independently reproduce those source checksums from source bytes, so it supports inspection of the recorded provenance rather than independent source verification.

The canonical identity covers reach lineage, resolved terrain and roughness content, references, grid, mask, domain, boundary geometry, structure treatment, method, and producer.
It omits the realized roughness lookup RL-3.
That omission is material because a different lookup can produce a different roughness raster from the same land-cover content.

Two model requests can therefore differ in an output-affecting setting while retaining the same stated canonical identity.
The supplied digest cannot repair the omission because a digest distinguishes only the fields included in its canonical input.
Under [MX-008](../../reference/decision-code-artifact-crosswalk.md#mx-008-identity), the model identity is incomplete.

## 4. Warning assessment

The warning establishes that one implemented comparison found only 30 m between a connected terrain corridor and the east edge, below a 50 m review threshold.
It triggers a scenario-level edge and connectivity review.
It does not prove that water reaches the edge, that clipping occurs, or that a larger domain changes the result.

The absence of other warnings establishes only that no other implemented reporting rule emitted an entry into this record.
It does not establish that every relevant topology, datum, roughness, structure, domain, boundary, or integrity check ran and passed.

The smallest useful disposition packet would include the planned scenario range, connected wet components, edge roles, depth and WSE gradients near the east edge, local flow directions, alternate pathways, and a controlled expansion comparison where the original result is affected.

## 5. Publication and materialization assessment

The staged verification states that the pre-promotion copies matched the recorded inventory.
The promotion event states that generation G-02 became the intended visible generation.
The later observer checked final storage and found five matching roles plus one checksum mismatch.

The outflow bytes do not match the outflow integrity value in the promoted model record.
Generation G-02 therefore fails the complete materialization contract even though the role exists and its size matches.
The packet cannot show whether the mismatch came from replacement, corruption, or a record error.

If every checksum matched, the observation would support artifact integrity and one-generation consistency.
It still would not establish correct topology, terrain quality, roughness adequacy, boundary placement, domain containment, numerical behavior, validation, or acceptance.

## 6. Direct evidence verdict

**MODEL RECORD INSPECTED; SCENARIO READINESS NOT ESTABLISHED.**

The immediate blockers are the omitted roughness lookup in identity, the outflow checksum mismatch, and the unresolved east-side review warning.
The complete record also contains no scenario-range domain evidence, hydraulic sensitivity, numerical verification, validation, or acceptance decision.

The smallest correction and evidence set that could support another review is:

1. Add RL-3 to the canonical identity and publish a distinct protected generation if the identity changes.
2. Determine why the outflow integrity value differs, restore a complete internally consistent generation, and repeat independent observation.
3. Supply the scenario-level edge and controlled-expansion evidence needed to disposition the warning.
4. Confirm topology, terrain, roughness, structures, references, and boundary geometry for the intended use.
5. Supply numerical, hydraulic, sensitivity, validation, and acceptance evidence appropriate to the planned scenarios.

Those additions would support another review rather than guarantee a ready verdict.
