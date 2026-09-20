# Case, Issue, and Experiment Catalog

This catalog indexes every checked-in Case, Experiment, and Issue record under `twod-fim-knowledge-base/system-decision-record/` at the exact reviewed `twod-fim-knowledge-base` revision `6ca2d8471676eb4d6b4a86ef3c4683e029ef017e`.
It records what each source actually contains rather than inferring evidence from a title or planned method.

## Why this matters

Project evidence is distributed across case narratives, experiment method records, issue signatures, decision histories, figures, and tables.
A title can describe a scientific question without recording a completed comparison.
An issue can record a symptom without proving its cause.
This catalog preserves those distinctions so later reviews can select evidence without overstating it.

## Prerequisites

Read [Source Authority](../reference/source-authority.md), [Validation Framework](01-validation-framework.md), [Diagnostic Workflow](02-diagnostic-workflow.md), and [Sensitivity and Uncertainty](03-sensitivity-and-uncertainty.md).
Use the [Decision-Code-Artifact Crosswalk](../reference/decision-code-artifact-crosswalk.md) for current implementation and artifact details.

## Learning objectives

After this chapter, the reader should be able to:

- distinguish a method record from a result record;
- identify which case produced a completed scoped observation;
- treat an issue as a symptom until discriminatory evidence establishes a cause;
- connect a record to relevant decisions and current implementation questions; and
- state the remaining uncertainty before using a record in a review.

## 1. Status vocabulary and authority

The catalog uses five conclusion statuses.

| Status | Meaning |
| --- | --- |
| Plan-only | The record identifies a candidate case, question, data source, or future use but contains neither a sufficiently specified completed method nor a result. |
| Method-only | The record describes a method or procedure but contains no completed observation in that record. |
| Partial-result | The record contains an observation or artifact, but its comparison, provenance, analysis, or conclusion remains incomplete. |
| Completed scoped result | The record reports a result and bounded conclusion for its stated configuration, without implying universal validation. |
| Unresolved | The record describes a symptom or uncertainty without establishing root cause or resolution. |

All entries retain the evidence label **Evidence or experiment**.
Decision records control selected-methodology status within their registered scope.
Current code controls checked-out implementation behavior.
Neither source is replaced by this catalog.

The three source roots are:

- `twod-fim-knowledge-base/system-decision-record/01_Cases/`;
- `twod-fim-knowledge-base/system-decision-record/03_Experiments/`; and
- `twod-fim-knowledge-base/system-decision-record/04_Issues/`.

Case source files follow `Case-NNN/Case-NNN - <title>.md` within the case root.
Experiment and issue source files begin with their unique `EXP-NNN` or `ISU-NNN` identifier within their respective roots.

## 2. Coverage summary

| Record type | Plan-only | Method-only | Partial-result | Completed scoped result | Unresolved | Total |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| Case | 3 | 0 | 5 | 11 | 0 | 19 |
| Experiment | 0 | 15 | 0 | 0 | 0 | 15 |
| Issue | 0 | 0 | 0 | 0 | 12 | 12 |
| **Total** | **3** | **15** | **5** | **11** | **12** | **46** |

Completed evidence described under EXP-013 and EXP-014 belongs to Case-018 because Case-018 contains the configurations, tables, figures, and conclusions.
The standalone EXP-013 and EXP-014 files remain method-only records.

## 3. Case catalog

| ID | Type | Scientific question or symptom | Configuration or method | Evidence actually produced | Conclusion status | Related decision | Current implementation relevance | Remaining uncertainty |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Case-001 | Case | Does downstream KWSE matter at a low-gradient confluence, and may normal depth on every edge leak water? | Reach 3463057 ND versus KWSE, plus a combined three-reach model with all edges at normal depth. | Depth-profile difference and mapped unintended edge outflow. | Completed scoped result. | DR-001 and DR-003. | Relevant to current KWSE stage transfer and slope-based edge spans in XW-001 and XW-009. | Benchmark identity, quantitative uncertainty, other flows, and transfer across reach classes are not established. |
| Case-002 | Case | How do lake-terminal downstream conditions affect depth and edge pooling? | Reach 30683 comparisons among stage transfer, reach-slope normal depth, combined treatment, and a very flat edge slope. | Maps and qualitative comparison showing similar upstream depth for two treatments and excessive downstream pooling under the flat-slope treatment. | Completed scoped result. | DR-005 and DR-006. | Relevant to current waterbody flags, edge treatment, and unresolved lake planning outside current jobs. | Lake-stage observations, flow range, quantitative metrics, and coast transfer are not established. |
| Case-003 | Case | Do unrepresented culverts block or divert flow? | Reach 30704 run under an identified Decision Register revision with downstream KWSE. | FEMA comparison, DEM inspection, and maps of impoundment and prevented conveyance. | Completed scoped result. | DR-010. | Relevant because current build inputs have no explicit culvert or bridge hydraulic treatment. | Culvert geometry, capacity, losses, benchmark comparability, and validated correction are absent. |
| Case-004 | Case | Can a reach-divide-derived domain clip a floodplain backwater area? | Reach 30728 with reach divide buffered 100 m and compared with FEMA extent. | Domain and benchmark figures showing floodplain cutoff at the domain edge. | Completed scoped result. | DR-011 and DR-012. | Relevant because current computed domains do not implement scenario-driven expansion. | Alternative extent, event comparability, and an authorized expansion rule remain open. |
| Case-005 | Case | Can pooling near a common outlet trigger unsuitable domain expansion? | Reach 60868 under the EXP-005 reach-divide-domain concept. | A case location and one model figure are present without analysis or a recorded verdict. | Partial-result. | DR-011 and DR-012. | Relevant to domain-expansion ownership and confluence edge evidence. | The realized configuration, measured response, comparison, and conclusion are not recorded. |
| Case-006 | Case | Which inflow geometry avoids WSE artifacts on a mid-sized river? | Reach 3463421 with point and 100 m line alternatives at the reach start or 25 percent up the upstream mainstem. | WSE contours and depth maps showing strong point-source and milder line or reach-start artifacts. | Completed scoped result. | DR-004, DR-013, DR-015, and DR-016. | Relevant to current single-line inflow construction and pixelwise-maximum composite risk. | No acceptance threshold, active-cell audit, broader reach sample, or independent hydraulic validation is recorded. |
| Case-007 | Case | Which edge-pooling criterion yields a defensible domain at a complex low-gradient confluence? | Reach 3250307 at a 500-year flow with elevation, WSE, smoothed-WSE, and hand-drawn-STL criteria plus manual expansion. | Maps, approximate expansion distances, rejections of ALT-B and ALT-C, and promising but incomplete ALT-D and ALT-E observations. | Partial-result. | DR-012 and DR-025. | Relevant to the absent current domain-expansion loop and current smoothed STL production. | Automated STL performance, selected expansion ownership, representative cases, and quantitative acceptance remain unresolved. |
| Case-008 | Case | Can fixed or bankfull-scaled domain limits cover a very wide floodplain? | NFHL width of 12 to 22 km compared with a 4 km expansion limit and a 50-bankfull-width calculation. | A bounded geometry comparison, tentative rejection of ALT-F, and recommendation of ALT-G for this site. | Completed scoped result. | DR-012. | Relevant to selected ALT-G expansion, which current jobs do not implement. | No hydraulic run, network ownership test, cost analysis, or population-level evidence is recorded. |
| Case-009 | Case | Does preliminary reach-scale methodology behave plausibly in rural unconfined farmland and small confluences? | Ten reaches modeled under EXP-009 using gage and StreamStats forcing. | Composite figures and qualitative observations, including possible incomplete lateral STL coverage. | Partial-result. | DR-012, DR-025, and DR-026. | Relevant to current reach-scale execution and run-specific STL geometry. | No controlled benchmark, quantitative metric, provenance packet, or agricultural-drainage stress case is recorded. |
| Case-010 | Case | Does preliminary methodology show obvious failure in steep confined terrain? | Thirteen reaches at 500-year discharges under EXP-009 with gage and StreamStats forcing. | Model figures and a qualitative statement that no steepness-specific issue was observed. | Completed scoped result. | DR-012, DR-025, and DR-026. | Relevant to domain, inflow warm-up, and STL coverage in steep settings. | Absence of an observed issue is not quantified validation, and grid, stability, and transfer sensitivity remain untested. |
| Case-011 | Case | How materially do missing culvert pathways affect an urban large-river result? | Seven reaches at 100-year discharges, with original and manually corrected DEMs. | Maps showing reduced upstream backwater after estimated culvert geometry was added. | Completed scoped result. | DR-010. | Relevant because current terrain creation lacks an explicit structure contract. | Estimated geometry, structure hydraulics, independent observations, other flows, and transferable treatment remain uncertain. |
| Case-012 | Case | Can reach-based domains represent divergent desert-wash pathways and a road crossing? | Eight reaches at 100-year discharges under EXP-009, using gage and regional-regression forcing. | Composite and reach maps showing cross-reach spill plus an unburned highway crossing and upstream backup. | Partial-result. | DR-010 and DR-012. | Relevant to prepared-network topology, domain overlap, compositing, and structure representation. | Flow allocation, cross-domain exchange, correction experiment, and comparison with observations are incomplete. |
| Case-013 | Case | Can fixed-stage bathtub mapping support large inland waterbodies? | NHDPlus waterbody selection, downstream-reach identification, DEM outlet lower bound, and levee-crest upper bound. | Upper and lower categorical inundation extents. | Completed scoped result. | DR-034, DR-035, DR-036, and DR-037. | Relevant to lake planning and product generation outside current modeling jobs. | Dataset status, stage-bound authority, connectivity, levee provenance, depth accuracy, and implementation ownership remain open. |
| Case-014 | Case | What methodology should generate coastal inundation? | Coastal site identified at Plum Island, Massachusetts. | A location figure and case description only. | Plan-only. | DR-005 through DR-009 and DR-038. | Relevant to coastal network omission, terminal scheduling, and boundary planning outside current jobs. | No method, forcing, stage source, experiment, output, or conclusion is recorded. |
| Case-015 | Case | Which domain and below-water terrain choices work for a large river? | Three merged groups compared under reach-divide, bankfull-buffer, and coarse-model domains, plus with-bathymetry and without-bathymetry rating comparisons. | Domain maps, rejected ALT-A and ALT-B outcomes, promising coarse domains, and a rating plot closer to surveyed stage with bathymetry. | Completed scoped result. | DR-011, DR-012, and DR-021. | Relevant to current rectangular domain creation and the absence of a topobathymetry merge input. | Bathymetry provenance and merge details, roughness calibration separation, independent validation, and transfer to other rivers remain limited. |
| Case-016 | Case | Could a river-island site test mosaicking against 1D RAS and topobathymetry? | Wheeling Island is identified with candidate comparison data. | A location figure and statement of intended use. | Plan-only. | DR-002, DR-004, and DR-021. | Relevant to composite behavior, divergent flow, and topobathymetry. | No experiment configuration, source versions, outputs, comparison, or conclusion is recorded. |
| Case-017 | Case | Could the April 2024 Ohio River flood support event validation? | Candidate topobathymetry, 1D RAS, flow, stage, drone imagery, and USGS gages are listed. | Location and event figures plus links to two USGS monitoring pages. | Plan-only. | DR-002 and DR-021. | Relevant to intended-use validation across observed forcing, stage, terrain, and composite products. | Data retrieval, datum and timing alignment, model configuration, observation uncertainty, metrics, and results are absent. |
| Case-018 | Case | Do pixelwise-maximum reach models resemble one larger model, and which quasi-steady metric resembles modeler judgment? | Five Winooski reaches and one combined model at listed discharges, followed by extended runs with interval depth grids and candidate convergence metrics. | Case-018 states that more than 90 percent of inundated cells disagreed by less than 0.05 m in its recorded comparison, and it includes residual maps, DEM and WSE anomalies, metric time series, tabulated values, and a scoped `1e-3` volume-convergence sensitivity result. | Completed scoped result. | DR-002, DR-004, DR-022, and DR-028. | Relevant to composite validation, DEM reproducibility, inflow anomalies, and current storage-change termination. | Manual domain and outlet changes, one setting, metric-definition limits, and no complete mass balance constrain transfer; the record does not define a universal threshold, denominator, mask, aggregation rule, or acceptance conclusion. |
| Case-019 | Case | How does a normal-depth slope floor affect KWSE bounds on a large low-gradient river? | Ripple1D example at 290,000 cfs comparing a downstream-reach upstream WSE of 632 ft with an upstream-reach normal-depth floor of 639.7 ft. | Figures and a specific incompatibility observation tied to excluded lower-stage scenarios. | Partial-result. | DR-032. | Relevant to external scenario planning and KWSE bounds rather than current job execution alone. | Source revisions, exact model settings, comparison metric, correction, current applicability, and broader frequency are not recorded. |

## 4. Experiment catalog

Every standalone experiment entry below is method-only.
Completed observations must be cited to the case record that contains them.

| ID | Type | Scientific question or symptom | Configuration or method | Evidence actually produced | Conclusion status | Related decision | Current implementation relevance | Remaining uncertainty |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| EXP-001 | Experiment | How does KWSE enforcement compare with outlet normal depth and a benchmark? | Build ND and KWSE FIMs and compare both with a benchmark. | Description and three-step method only. | Method-only. | DR-001 and DR-005. | Relevant to current ND and KWSE scenario families. | The record contains no site, exact configuration, result, metric, or conclusion. |
| EXP-002 | Experiment | What happens when all edge cells use normal depth? | Use a correct domain, apply normal depth to all edges, and compare with a benchmark. | Description and method only. | Method-only. | DR-003. | Relevant to current slope-based edge handling. | Correct-domain criteria, slope, benchmark, metric, and result are absent. |
| EXP-003 | Experiment | Does a very flat normal-depth slope represent a level-pool receiving waterbody? | Change downstream-FIM-informed edge cells to a very flat slope and compare with a benchmark. | Method and one explanatory figure only. | Method-only. | DR-005 and DR-006. | Relevant to lake and coastal edge-policy questions. | Exact slope, site, observations, metrics, and result are absent from this record. |
| EXP-004 | Experiment | How does current Decision Register methodology compare with a benchmark? | Build according to the register and compare with a benchmark. | Generic method only. | Method-only. | Crosscutting Decision Register. | Relevant only when a precise Decision Register revision and realized inputs are supplied. | The method does not identify revision, case, benchmark, metric, or result. |
| EXP-005 | Experiment | Does a reach-divide-derived domain cover the needed area? | Create an inflow line, combine it with the reach divide, form and buffer a bounding box, then compare with benchmark FIM. | Detailed method only. | Method-only. | DR-011 and DR-012. | Relevant to current computed rectangular domains and absent expansion logic. | No result, flow, site, comparison rule, or acceptance criterion is recorded here. |
| EXP-006 | Experiment | Which inflow geometry minimizes WSE artifacts? | Compare a reach-start point, a 100 m line 25 percent up the upstream mainstem, and a perpendicular reach-start line. | Method only. | Method-only. | DR-013, DR-015, and DR-016. | Relevant to current inflow-line construction. | No result or artifact is recorded in this file, and active-cell distribution is unspecified. |
| EXP-007 | Experiment | Which edge-pooling criterion should trigger domain expansion? | Run a 500-year flow, test elevation, WSE, and STL-based edge criteria, expand triggered edges, and repeat. | Iterative method only. | Method-only. | DR-012 and DR-025. | Relevant to an expansion loop absent from current jobs. | No result, stop rule, maximum extent, ownership, or acceptance threshold is recorded here. |
| EXP-008 | Experiment | Is a generated domain large enough relative to NFHL? | Generate a domain, measure nearby NFHL 100-year width, and compare extents. | Method only. | Method-only. | DR-011 and DR-012. | Relevant to domain screening, not hydraulic validation. | NFHL comparability, event matching, metric, and result are absent. |
| EXP-009 | Experiment | How does preliminary pre-register methodology behave across cases? | Detailed 13-step reach preparation, forcing, 3DEP, NLCD, boundary, duration, diagnosis, rerun, and reference-comparison procedure. | Method only. | Method-only. | Historical precursor to many current decisions. | Relevant as historical evidence, not authority for selected or current behavior. | Source versions, case-specific execution, metrics, and results belong in case records. |
| EXP-010 | Experiment | Can fixed-stage bathtub analysis generate waterbody FIM? | Define an area and stage grid, acquire terrain, and map terrain below each WSE as categorical or depth output. | Method only. | Method-only. | DR-034, DR-035, DR-036, and DR-037. | Relevant to lake and coast product ownership outside current jobs. | Connectivity rule, stage authority, datum, implementation, and validation are incomplete. |
| EXP-011 | Experiment | Which automated domain approach is reasonable for a larger river? | Compare reach-divide buffer, bankfull-scaled centerline buffer, and coarse-model FIM extents. | Method and bankfull-width equation only. | Method-only. | DR-011 and DR-012. | Relevant to selected domain expansion and current rectangular construction. | No result, authorized multiplier, quantitative metric, or acceptance rule is recorded here. |
| EXP-012 | Experiment | How does below-LiDAR bathymetry affect FIM accuracy? | Calibrate roughness with a bathymetric terrain against a surveyed rating curve, apply the same roughness to a no-bathymetry terrain, and compare curves and FEMA if available. | Method only. | Method-only. | DR-021. | Relevant because current build inputs do not implement a topobathymetry merge. | This file contains no completed result, and calibration versus independent validation remains a key limit. |
| EXP-013 | Experiment | Do merged reach-scale models resemble one large model under ideal conditions? | Build reach models and one combined model, create a pixelwise-maximum depth mosaic, subtract depths, and summarize differences. | Method only in this standalone file. | Method-only. | DR-002 and DR-004. | Relevant to composite methodology and production ownership. | The completed Winooski evidence is in Case-018, not this record, and broader transfer remains untested. |
| EXP-014 | Experiment | Which automated convergence metric best resembles modeler-selected quasi-steady time? | Run longer than expected, review interval depth grids, select a subjective stable time, and compare candidate metric trigger times. | Method only in this standalone file. | Method-only. | DR-022 and DR-028. | Relevant to current storage-change termination. | The completed scoped analysis is in Case-018, not this record, and it does not close mass balance or define universal adequacy. |
| EXP-015 | Experiment | How should adjacent-reach joint flow frequency inform KWSE bounds and sampling? | Sample reach pairs by drainage-area ratio, use 40-year NWM retrospective flows, fit LP3 marginals and bivariate distributions, and derive conditional bounds. | Detailed proposed method and caveats only. | Method-only. | DR-032 and DR-033. | Relevant to external planning, not implemented current-job behavior or selected replacement methodology. | No dataset snapshot, executed sample, fitted model, validation, climate stratification result, or adopted rule is recorded. |

## 5. Issue catalog

An issue record preserves a symptom.
It does not prove that a named decision, parameter, or implementation defect caused that symptom.

| ID | Type | Scientific question or symptom | Configuration or method | Evidence actually produced | Conclusion status | Related decision | Current implementation relevance | Remaining uncertainty |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| ISU-001 | Issue | WSE is lower than expected near the downstream end of a reach. | Symptom statement only. | One textual signature. | Unresolved. | DR-001, DR-005, DR-009, and DR-031. | Could involve boundary, transfer, forcing, terrain, roughness, duration, or datum behavior. | Expected referent, magnitude, scope, and root cause are absent. |
| ISU-002 | Issue | FIM extent is narrower than expected near a confluence. | Symptom statement only. | One textual signature. | Unresolved. | DR-011 through DR-016 and DR-023 through DR-026. | Could involve flow allocation, domain, STL coverage, network topology, inflow placement, or compositing. | No linked case, metric, configuration, or cause proof is recorded. |
| ISU-003 | Issue | Water leaves the domain away from intended outlets. | Symptom statement noting that outlet flow then differs from inflow. | Textual signature only. | Unresolved. | DR-003, DR-006, DR-012, and DR-039 authority question. | Relevant to current edge spans, boundary audit, and mass-balance evidence. | The record does not distinguish boundary configuration, domain clipping, connectivity, or numerical leakage. |
| ISU-004 | Issue | WSE is higher than expected near the downstream end of a reach. | Symptom statement only. | One textual signature. | Unresolved. | DR-005, DR-006, DR-009, and DR-031. | Could involve restrictive downstream condition, terrain, structure, roughness, forcing, or datum. | Expected referent, magnitude, configuration, and cause proof are absent. |
| ISU-005 | Issue | Flow follows a divergent path into another reach. | Case-003 is named and one figure is included. | Scoped visual symptom. | Unresolved. | DR-007, DR-010, DR-012, DR-023, and DR-024. | Relevant to terrain connectivity, structures, prepared network, domains, and composite overlap. | Whether the path is physical, terrain-induced, network-induced, or a domain artifact is not established. |
| ISU-006 | Issue | Inundation ends abruptly at a model edge. | Case-004 is named and one figure is included. | Scoped visual symptom. | Unresolved. | DR-011 and DR-012. | Relevant because current jobs have no scenario-driven expansion loop. | Boundary role, expected extent, flow, and exact cause are not independently established. |
| ISU-007 | Issue | A culvert location impounds flow. | Case-003 is named and two figures are included. | Scoped visual symptom. | Unresolved. | DR-010. | Relevant because current inputs lack an explicit culvert contract. | Culvert dimensions, capacity, blockage, terrain correction, observations, and root-cause proof are absent. |
| ISU-008 | Issue | WSE contains local anomalies. | Case-006 is named and a contour figure is included. | Scoped visual symptom. | Unresolved. | DR-004, DR-013, DR-015, and DR-016. | Relevant to current inflow geometry and maximum compositing. | The issue file does not quantify the anomaly or exclude terrain, grid, datum, and transient causes. |
| ISU-009 | Issue | A model domain is excessively large and overlaps areas better represented by other reaches. | Qualitative definition only. | Textual efficiency and ownership concern. | Unresolved. | DR-011, DR-012, DR-023, and DR-024. | Relevant to domain planning, network ownership, cost, and composite membership. | No authorized size, overlap, influence, or cost criterion is recorded. |
| ISU-010 | Issue | Modeled WSE is higher than benchmark FIM. | Generic comparison statement only. | Textual signature. | Unresolved. | DR-002, DR-010, DR-018 through DR-021. | Relevant to benchmark comparability, terrain, structures, roughness, forcing, boundary, and datum. | A higher WSE does not by itself identify inaccuracy or cause without comparable quantities and uncertainty. |
| ISU-011 | Issue | Successive 3DEP VRT requests yield different DEMs. | The source VRT is named, and query or transformation is proposed as a possible cause. | Reproducibility symptom and hypotheses. | Unresolved. | DR-018 and identity or reuse contracts. | Relevant to current source-string identity, mutable upstream source, hashing, and model reuse. | No retained pair, request trace, checksum analysis, or root-cause isolation is recorded. |
| ISU-012 | Issue | A result may not have reached quasi-steady state. | Symptom description covering continued filling, draining, or WSE equalization. | Textual diagnostic signature. | Unresolved. | DR-022 and DR-028. | Relevant to current duration, storage-change proxy, watcher termination, and hot starts. | No run, metric series, local response, mass balance, threshold authority, or root-cause proof is included. |

## 6. How to use a catalog entry

Use an entry as a routing aid, then read the source record and every relevant decision, implementation, artifact, and conflict before drawing a conclusion.
A defensible evidence statement should name:

1. the exact record that contains the observation;
2. the realized configuration and source revisions;
3. the quantity, units, spatial and temporal support, metric, and referent;
4. the conclusion status from this catalog;
5. the decision question the evidence can inform;
6. the current implementation or target-design boundary; and
7. the remaining uncertainty and prohibited generalizations.

Case-018 can support the literal statement that more than 90 percent of inundated cells in its recorded Winooski comparison disagreed by less than 0.05 m under its stated configuration.
The record does not establish a universal threshold, denominator, mask, aggregation rule, or acceptance conclusion.
It cannot support the statement that EXP-013 universally validated reach-based compositing.
ISU-011 can support the statement that a DEM reproducibility symptom was recorded.
It cannot support the statement that the remote VRT service was proven to be the root cause.

## Common misconceptions

### "The experiment file contains the result"

Every standalone EXP record in the reviewed source contains a description and method, not a completed observation.
Case records contain the completed and partial observations cataloged here.

### "Rejected in one case means rejected universally"

A case conclusion is bounded by its recorded site, configuration, forcing, sources, and comparison.
Decision authority comes from the Decision Register, not from broadening a case conclusion.

### "An issue title identifies the cause"

An issue title identifies a symptom.
Use the hypothesis-driven process in [Diagnostic Workflow](02-diagnostic-workflow.md) to test causes.

## Competency check

1. Which record contains the completed reach-composite comparison, and why is EXP-013 not the result source?
2. Which records provide completed or partial evidence about domain size or clipping?
3. Explain why ISU-010 does not prove that one terrain or roughness choice is wrong.
4. Select one plan-only case and state the minimum evidence needed to move it to a completed scoped result.
5. Use one issue entry to write three competing hypotheses and one discriminating next check.

## Further reading and source notes

- [SDR-004](../reference/bibliography.md#sdr-004-cases-issues-and-experiments) identifies the local evidence roots and authority boundary.
- [SDR-001](../reference/bibliography.md#sdr-001-system-decision-register) controls selected-methodology status rather than this catalog.
- [XW-018](../reference/decision-code-artifact-crosswalk.md#xw-018-intended-use-validation-and-acceptance) maps intended-use acceptance evidence.
- [XW-019](../reference/decision-code-artifact-crosswalk.md#xw-019-diagnostic-signatures-and-discriminatory-evidence) maps the issue signatures to current evidence and competing causes.
