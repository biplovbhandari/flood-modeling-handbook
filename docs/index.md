# Flood Modeling Handbook

This handbook introduces hydrology, open-channel flow, two-dimensional hydraulics, model development, scenario libraries, validation, and scientific software practice.
It connects physical reasoning, numerical methods, evidence, and software contracts so readers can understand how flood-inundation models are developed and evaluated.

## What this handbook covers

The handbook begins with flood-inundation concepts, hydrologic inputs, quantities, units, coordinate reference systems, and datums.
It then develops open-channel and two-dimensional hydraulic foundations before applying them to model construction, scenario libraries, validation, quality control, and scientific software practice.

## Prerequisites

Readers should be comfortable with algebra, unit conversion, maps, raster and vector data, coordinate reference systems, and basic probability.
Basic familiarity with Python and structured data is useful for following software examples but is not required for the scientific foundations.

## Suggested learning path

- **Orientation** introduces flood-inundation mapping, the end-to-end modeling process, and the quantities and reference systems used throughout the handbook.
- **Hydrology for Flood Modeling** connects watersheds, runoff, hydrographs, routing, flood frequency, and forcing uncertainty.
- **Open-Channel Flow** develops conservation, energy, momentum, flow regimes, Manning flow, normal depth, and boundary control.
- **Two-Dimensional Hydraulics** explains shallow-water models, grids, wetting and drying, time stepping, stability, convergence, mass balance, and solver choices.
- **Model Development** covers networks, terrain, structures, roughness, domains, boundaries, and model-building workflows.
- **Scenario Libraries** covers boundary scenarios, adaptive selection, stage transfer, planning, compositing, identity, and provenance.
- **Validation and Quality Control** develops validation, diagnosis, sensitivity, uncertainty, and evidence review.
- **Scientific Software Practice** connects scientific meaning to software contracts, evidence boundaries, and [Scientific Methodology Review](07-system-design/03-scientific-methodology-review.md).

## Labs and solutions

All exercises use self-contained packets or repository-shipped material.
Read each solution only after attempting the corresponding prompt.
See [Lab Conventions](labs/README.md) for the exercise sequence and solution links.

## Reference material

- [Source Authority](reference/source-authority.md)
- [Glossary](reference/glossary.md)
- [Equations and Units](reference/equations-and-units.md)
- [Method-Evidence-Artifact Crosswalk](reference/decision-code-artifact-crosswalk.md)
- [Conflicts and Open Questions](reference/conflicts-and-open-questions.md)
- [Bibliography](reference/bibliography.md)
- [Lab Conventions](labs/README.md)
- [Visual Source Register](assets/source-register.md)
