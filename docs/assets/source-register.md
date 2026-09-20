# Visual Source Register

This register records provenance for every file-based original, project-derived, and external visual used by the handbook.
A file-based visual must be registered before a chapter relies on it.
Inline text schematics are governed as handbook prose rather than as reusable figure files.
They must be plainly identified as synthetic instructional constructions, preserve any source boundary stated in the surrounding text, and must not be presented as copied, measured, or project-derived geometry.

## First-edition visual review status

All nine file-based figures are registered below and contain an SVG `title`, an SVG `desc`, a teaching-purpose description, and nearby chapter guidance about what to notice.
The integration review inspected SVG source and Markdown references only.
No renderer was run, so normal-zoom legibility and rendered layout remain user-validation-pending rather than passed.

## Required fields

### Identifier

Assign a stable identifier that remains unchanged if the file moves.

### File

Record the handbook-relative path of the visual file.

### Description

State what the visual shows and what the learner should notice.

### Creator

Name the person, organization, project team, or original handbook source that created the visual.

### Source URL or local path

Record the direct source URL or the exact local project path from which the visual was derived.
Use `Original handbook asset` when the visual was created from scratch for the handbook.

### License or reuse status

Record the known license, public-domain status, project-owned status, permission, or unresolved reuse status.
Do not treat public availability as permission to reproduce a visual.

### Access date

Record the date on which an external or project source was accessed.
Use the creation date for an original handbook visual.

### Modification notes

Describe cropping, redrawing, annotation, color changes, simplification, or other transformation.
Use `None` only when the file is used unchanged.

## Record template

### VIS-identifier: Short title

- **File:** `assets/example.svg`
- **Description:** A concise statement of the mechanism, relationship, or diagnostic judgment shown.
- **Creator:** Creator or organization.
- **Source URL or local path:** Direct URL, exact local path, or `Original handbook asset`.
- **License or reuse status:** Confirmed status or `Reuse status unresolved; do not publish`.
- **Access date:** YYYY-MM-DD.
- **Modification notes:** Exact changes or `None`.

## Registration policy

Prefer original diagrams, project-owned artifacts, public-domain sources, and clearly licensed sources.
Do not reproduce figures from the supporting hard-copy textbooks.
For a project-derived screenshot or rendering, register both the source artifact and the transformation.
For an external visual with unresolved reuse status, keep it out of distributable outputs until permission or a compatible license is confirmed.
Every instructional visual also needs a caption or nearby explanation of what to notice and why it matters.
Inline text schematics do not receive a VIS identifier unless they are promoted into a standalone file-based figure.

## Registered visuals

### VIS-001: Stage, depth, and datum

- **File:** `assets/stage-depth-datum.svg`
- **Description:** An original schematic cross-section showing terrain elevation, WSE, local depth, a stage reference, vertical datum, discharge, and spatially varying velocity.
  The learner should notice that WSE and terrain are elevations from a datum, depth is their compatible local difference, and stage uses its own stated reference.
- **Creator:** Original handbook source.
- **Source URL or local path:** `Original handbook asset`.
- **License or reuse status:** Project-owned original learning material.
- **Access date:** 2026-09-17.
- **Modification notes:** Created as a code-native SVG for the handbook.
  The geometry is schematic, not derived from a measured cross-section, external figure, or textbook.
  Revised so the stage arrow terminates on the represented water surface and the relation `WSE = z_r + s` is visible.

### VIS-002: Hydrology to hydraulics

- **File:** `assets/hydrology-to-hydraulics.svg`
- **Description:** An original process diagram showing precipitation partitioning and network routing producing a discharge hydrograph, selection of one discharge with its context, and application of that discharge at a reach-scale hydraulic-model inflow.
  The learner should notice the prepared-forcing boundary and that one steady discharge preserves only a sample of the complete event.
- **Creator:** Original handbook source.
- **Source URL or local path:** `Original handbook asset`.
- **License or reuse status:** Project-owned original learning material.
- **Access date:** 2026-09-17.
- **Modification notes:** Created as a code-native SVG for the handbook.
  The catchment, network, hydrograph, grid, channel, and inflow geometry are schematic and were not copied, traced, downloaded, or derived from an external figure or textbook.

### VIS-003: Open-channel downstream controls

- **File:** `assets/open-channel-controls.svg`
- **Description:** An original longitudinal schematic comparing the same falling channel under a slope-based downstream condition and a raised known downstream WSE.
  The learner should notice the approximately uniform normal-depth reference in the first panel, the deeper downstream profile in the second panel, and the arrow showing possible upstream backwater influence under subcritical conditions.
- **Creator:** Original handbook source.
- **Source URL or local path:** `Original handbook asset`.
- **License or reuse status:** Project-owned original learning material.
- **Access date:** 2026-09-17.
- **Modification notes:** Created as a code-native SVG for the handbook.
  The bed, water profiles, boundary lines, and influence arrow are qualitative and were not copied, traced, downloaded, or derived from an external figure, measured reach, or textbook.
  The file includes an accessible title and description.

### VIS-004: Grid flux and wetting

- **File:** `assets/grid-flux-wetting.svg`
- **Description:** An original two-panel structured-grid schematic showing an eastward flux across a shared face into a dry active cell and the same cell wet at the next time level.
  The learner should notice cell centers, faces, active and inactive states, equal and opposite shared-face accounting, the wetting transition, and the explicitly illustrative threshold.
- **Creator:** Original handbook source.
- **Source URL or local path:** `Original handbook asset`.
- **License or reuse status:** Project-owned original learning material.
- **Access date:** 2026-09-17.
- **Modification notes:** Created as a code-native SVG for the handbook.
  The grid, flux, threshold, cell states, and coordinates are schematic and were not copied, traced, downloaded, or derived from an external figure, measured grid, project artifact, solver output, or textbook.
  The file includes an accessible title and description, and color is paired with labels, hatching, and line style.

### VIS-005: Convergence evidence lanes

- **File:** `assets/convergence-evidence.svg`
- **Description:** An original evidence diagram separating numerical stability, process or exit status, storage convergence, true mass-balance evidence, hydraulic adequacy, and the final acceptance gate.
  The learner should notice that every lane asks a different question and that no passing lane substitutes for another.
- **Creator:** Original handbook source.
- **Source URL or local path:** `Original handbook asset`.
- **License or reuse status:** Project-owned original learning material.
- **Access date:** 2026-09-17.
- **Modification notes:** Created as a code-native SVG for the handbook.
  The evidence cards, arrows, labels, and acceptance gate are conceptual and were not copied, traced, downloaded, or derived from an external figure, project artifact, solver output, or textbook.
  The file includes an accessible title and description, and color is paired with numbered labels and text.
  Five straight routes run vertically from card centers at x coordinates 130, 360, 590, 820, and 1050 from y=470 to distinct gate ports at y=655.
  The routes do not cross, merge, enter another evidence card, or share an arrow segment.

### VIS-006: Model-development chain

- **File:** `assets/model-development-chain.svg`
- **Description:** An original process diagram showing a prepared reach network, realized DEM source, and realized LULC source and lookup flowing through geometry, terrain, and roughness transformations into six model assets, a model manifest, and a directory addressed by identity hash and domain code.
  The learner should notice that the DEM and roughness rasters share an exact transform, dimensions, CRS, and extent, while vectors require compatible horizontal coordinates plus explicit intersection, rasterization, or cell-mapping checks.
  The learner should also notice that all six assets and the manifest form one sequential publication set and that storage observation and scientific acceptance remain separate evidence gates.
- **Creator:** Original handbook source.
- **Source URL or local path:** `Original handbook asset`.
- **License or reuse status:** Project-owned original learning material.
- **Access date:** 2026-09-17.
- **Modification notes:** Created as a code-native SVG for the handbook.
  The process boxes, arrows, asset names, directory address, and review gate were drawn from the reviewed local current-code contracts but were not copied, traced, downloaded, or derived from an external figure, project screenshot, solver output, or textbook.
  The figure uses three source lanes at y coordinates 202, 377, and 552.
  Geometry reaches its asset box on y=202, terrain reaches the raster box on y=377, and roughness reaches the same raster box through x=575 and y=420.
  A dashed grouping rectangle spans x=590 through x=875 and y=120 through y=670 to enclose the six asset outputs and manifest as one publication set.
  The geometry asset reaches the manifest through x=865 and enters its right side at y=500, while the raster asset enters the manifest vertically at x=790.
  The publication-set route begins at `(875, 645)`, runs through x=890, and enters the model directory at `(905, 265)`.
  A separate dashed vertical route connects the directory to the evidence gate without crossing another box.
  The file includes an accessible title and description, and color is paired with text labels and layout.

### VIS-007: Adaptive ND proposal and measured verdict

- **File:** `assets/nd-adaptive-selection.svg`
- **Description:** An original response-curve diagram showing a reference point, monotone measured and extrapolated curve, floor and ceiling crossings, shaded discharge acceptance window, proposed discharge, predicted response, and later measured `reject_high` verdict.
  The learner should notice that the curve places a proposal inside the predicted window, while the actual measurement at the same discharge can fall above the ceiling and control the verdict.
  The learner should also notice that the floor and ceiling define a discharge window only through a response model built from existing scenario points.
- **Creator:** Original handbook source.
- **Source URL or local path:** `Original handbook asset`.
- **License or reuse status:** Project-owned original learning material.
- **Access date:** 2026-09-17.
- **Modification notes:** Created as a code-native SVG for the handbook.
  The axes, metric values, response curve, crossings, window, prediction, and measured result are schematic and were not copied, traced, downloaded, or derived from an external figure, project artifact, solver output, Decision Register video, or textbook.
  The proposed response is an open purple diamond and the later measured response is a filled red point at the same discharge.
  The shaded and hatched window spans the earliest floor through earliest ceiling crossings.
  The file includes an accessible title and description, and color is paired with labels, point shapes, line style, and hatching.

### VIS-008: KWSE downstream-to-upstream stage transfer

- **File:** `assets/kwse-stage-transfer.svg`
- **Description:** An original two-domain schematic showing physical flow downstream, hydraulic scenario dependency upstream, overlapping upstream and downstream grids, a run-specific STL, one downstream depth-plus-terrain WSE sample, one upstream cell-specific transfer point, QFIX inflow, current FREE edge spans, nominal scenario labeling, and ownership boundaries.
  The grids have visibly different origins and resolutions.
  The learner should notice that one shared map query coordinate lies inside distinct upstream and downstream containing cells, that the downstream sampled cell center and value are separate from the upstream transfer cell, and that the nominal stage labels the scenario while downstream raster values set pointwise HFIX.
  The shared coordinate does not imply coincident centers, footprints, resolution, affine transform, or raster registration.
  Planning, execution, storage, and observation remain distinct responsibilities.
- **Creator:** Original handbook source.
- **Source URL or local path:** `Original handbook asset`.
- **License or reuse status:** Project-owned original learning material.
- **Access date:** 2026-09-17.
- **Modification notes:** Created as a code-native SVG for the handbook.
  The domains, river, STL, samples, transfer cells, edge spans, addresses, and ownership lanes are schematic and were not copied, traced, downloaded, or derived from an external figure, project screenshot, measured reach, solver output, or textbook.
  The rectangular domain overlap spans x=510 through x=690 and y=300 through y=575 in SVG coordinates.
  The upstream pattern uses 34-unit cells, while the downstream pattern uses 42-unit cells with an offset origin.
  The shared query coordinate is `(562,382)`, the highlighted upstream containing cell spans x=544 through x=578 and y=374 through y=408, and the highlighted downstream containing cell spans x=552 through x=594 and y=349 through y=391.
  Separate center markers and connector labels make the noncoincident grid objects explicit.
  The file includes an accessible title and description, and color is paired with labels, shapes, line style, and layout.

### VIS-009: Validation evidence chain

- **File:** `assets/validation-evidence-chain.svg`
- **Description:** An original evidence-chain diagram that separates question and intended use, input verification, numerical evidence, hydraulic or benchmark evidence, uncertainty characterization, an acceptance-criteria gate, operational monitoring, and hypothesis-driven feedback.
  The learner should notice that each stage answers a different question and states what it cannot establish independently.
  The learner should also notice that acceptance is an authorized decision gate rather than a model output, and that monitoring follows acceptance without replacing missing verification or validation.
- **Creator:** Original handbook source.
- **Source URL or local path:** `Original handbook asset`.
- **License or reuse status:** Project-owned original learning material.
- **Access date:** 2026-09-18.
- **Modification notes:** Created as a code-native SVG for the handbook.
  The stage geometry, feedback arrows, labels, and evidence statements were not copied, traced, downloaded, or adapted from an external figure, project artifact, solver output, or textbook.
  Four feedback paths start at the bottom boundaries of numerical evidence `(387,478)`, hydraulic evidence `(620,478)`, uncertainty `(855,478)`, and the acceptance gate `(1065,478)`.
  Their arrowheads terminate at `(387,548)`, `(620,548)`, `(855,548)`, and `(1065,548)` on the top boundary of the hypothesis and diagnostic question panel.
  The file includes an accessible title and description, and color is paired with labels, borders, stage numbers, arrow direction, and layout.
