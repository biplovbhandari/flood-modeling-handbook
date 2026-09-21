# Conflicts and Open Questions

This register preserves generic scientific, hydraulic, and software-contract conflicts that require evidence or an authorized decision.
The records do not describe a named implementation, and the `CQ-*` identifiers support handbook navigation only.
Do not resolve a conflict by choosing the most convenient interpretation or by treating an undocumented default as authority.

## CQ-001: Terminology-behavior mismatch

**Conflict:**
A boundary, convergence state, completion state, or acceptance label can imply behavior that differs from the mathematical rule, solver configuration, or observation actually used.

**Why it matters:**
Reviewers can infer free overfall from a slope-based normal-depth outflow, scientific acceptance from process success, or full mass balance from small storage change.
Each substitution changes the claim and can hide a material failure.

**Evidence needed:**
Provide the governing definition, equations or version-specific semantics, configured values, realized geometry, observed behavior, and every consumer-facing label.
Show that documentation, input validation, records, diagnostics, and review language use the same meaning.

**Review question:**
Does each term name the behavior that is actually configured and observed, or must the behavior, interface, or label change?

## CQ-002: Incomplete identity

**Conflict:**
An identity omits one or more output-affecting values such as source content, topology, boundary geometry, datum, method version, numerical settings, initial state, or producer build.

**Why it matters:**
Scientifically unequal models or scenarios can share one identifier, address, cache entry, or selected-library membership.
The collision can make provenance ambiguous and can mix generations during publication or reuse.

**Evidence needed:**
Inventory every output-affecting field and classify it as identity, realization, provenance, acceptance, or operational state.
Use controlled changes to show that every identity field changes the canonical object and that equivalent requests reproduce it deterministically.

**Review question:**
Can any scientifically meaningful change leave the identity unchanged or direct unequal requests to the same unprotected address?

## CQ-003: Incompatible datums

**Conflict:**
Terrain, stage, water-surface elevation, reference observations, or transferred boundary values use missing, ambiguous, or incompatible vertical datums, units, epochs, or transformations.

**Why it matters:**
Depth plus terrain, cross-reach transfer, model-reference residuals, and composite water surfaces can be numerically plausible while physically misregistered.
The resulting error can be systematic and spatially variable.

**Evidence needed:**
Provide source and target vertical references, units, epochs where relevant, transformation method and applicability, transformation grid or parameters, uncertainty, spatial coverage, and checks at known points.
Preserve horizontal registration evidence separately.

**Review question:**
Are all combined elevations and comparisons demonstrably on one compatible vertical basis with transformation uncertainty appropriate to the intended use?

## CQ-004: Domain clipping

**Conflict:**
A model domain contains the reach centerline and requested boundaries but may exclude connected floodplain storage, backwater influence, alternate pathways, transfer support, or an intended outflow region.

**Why it matters:**
Water can accumulate against an artificial edge, disappear through an unintended boundary, or produce biased depths while the grid and process remain numerically stable.

**Evidence needed:**
Provide the domain-construction record, full planned scenario range, connected wet extents, edge contact by component, depth and WSE gradients near edges, flow directions, alternate-path review, and sensitivity to controlled expansion.

**Review question:**
Does the domain contain all hydraulically relevant pathways and storage for the intended scenario range, or must it expand or reject affected scenarios?

## CQ-005: Insufficient convergence evidence

**Conflict:**
A final storage-change ratio, stable time step, solver exit, or visually smooth map is presented as proof that the hydraulic result converged and satisfies mass balance.

**Why it matters:**
Domain-total storage can change slowly while local water levels, wetting fronts, fluxes, or edges remain transient.
Storage change can also be small when inflow and outflow errors compensate or when required flux terms are missing.

**Evidence needed:**
Provide complete histories for the declared convergence quantities, consecutive-interval rules, local hydraulic signals, inflow and outflow volumes, sources and sinks, signed and absolute balance residuals, edge results, time-step behavior, process exit, and artifact completion.

**Review question:**
Which precise quasi-steady, conservation, numerical, hydraulic, and acceptance claims are supported, and which remain unproven?

## CQ-006: Unsafe reuse

**Conflict:**
Reuse is allowed because an address, readable label, schema-valid record, or partial input comparison matches, while complete identity, artifacts, integrity, compatibility, membership, or acceptance has not been observed.

**Why it matters:**
The reused object can be stale, partial, superseded, scientifically incompatible, or created under a different method while appearing operationally successful.

**Evidence needed:**
Provide the exact requested identity, observed generation, canonical stored identity, required artifact inventory, integrity results, compatibility checks, selected-membership state, acceptance state, and current-intent comparison.

**Review question:**
Would the reuse decision fail closed for a deleted artifact, changed source, superseded membership, incompatible generation, or same-address collision?

## CQ-007: Partial publication

**Conflict:**
Artifacts become visible individually at a live address before one complete generation has been verified and promoted.

**Why it matters:**
A consumer can observe new depth with an old record, a new record with missing companion artifacts, or another mixed set that never represented one scientific generation.
Retry can overwrite the last complete result without restoring consistency.

**Evidence needed:**
Provide the required artifact set, isolated staging generation, integrity checks, promotion protocol, visibility guarantees, failure-injection results, retry behavior, retained prior generation, and rollback evidence.

**Review question:**
Can any failure or concurrent read expose a partial or mixed generation to an ordinary consumer?

## CQ-008: Missing provenance

**Conflict:**
An artifact or result lacks immutable source identity, transformation history, method and producer version, creation time, parent objects, integrity metadata, or realized settings needed to interpret it.

**Why it matters:**
A file can exist and open successfully while its quantity, generation, datum, scenario, processing method, or relationship to current intent remains unknown.
The gap prevents reproducible validation, reuse, migration, and diagnosis.

**Evidence needed:**
Provide a record that links the artifact to canonical identities, source content, transformations, parameters, references, units, masks, parent generations, producer build, time, role, address, and integrity value.

**Review question:**
Can an independent reader reconstruct what the artifact represents, how it was made, and which scientific claims it can support without relying on a mutable path or memory?

## CQ-009: Validation gaps

**Conflict:**
The available comparisons omit one or more elements required for the intended-use claim, such as independent referents, representative conditions, comparability, uncertainty, numerical verification, predeclared metrics, or applicability limits.

**Why it matters:**
One favorable benchmark, plausible map, or low residual can be generalized beyond its sample or can conceal calibration dependence, data mismatch, and untested failure classes.

**Evidence needed:**
Provide the intended use, domain of validity, independent reference provenance, sampling design, comparable quantities and support, calibration separation, numerical-verification evidence, raw comparisons, uncertainty, criteria, failures, exclusions, and transfer limits.

**Review question:**
What exact claim does the supplied evidence validate, and what additional evidence is required before any broader use is accepted?

## CQ-010: Acceptance authority

**Conflict:**
Acceptance criteria or a readiness verdict exist, but no accountable person or group has documented decision rights for the method, validation, operation, rollback, or intended-use acceptance.

**Why it matters:**
A technically favorable packet can enter use without an authorized risk decision, while unresolved conditions, prohibited uses, stop triggers, and rollback obligations remain unenforced.

**Evidence needed:**
Provide the governing decision process, named accountable functions, evidence of their authority, predeclared criteria, permitted and prohibited uses, unresolved-risk dispositions, conditions, enforcement, review date, reassessment triggers, and signed verdict.

**Review question:**
Who is authorized and accountable to accept the named use, halt it, require evidence, enforce conditions, and order rollback when a criterion fails?
