# Lab 10 Solution: Follow Adaptive ND Selection

This solution applies the reviewed current algorithm to the synthetic packet.
It does not validate a real reach, endorse the synthetic bands, or resolve project authority gaps.

## Use conditions

The prerequisites, goal, inputs, ordered steps, expected deliverable, execution boundary, and readiness stopping condition are defined in [Lab 10](../lab-10-follow-adaptive-nd-selection.md).
Use this solution only after attempting that prompt.
The reasoning uses only the prompt and cited local sources, requires no command or production access, and keeps synthetic metrics outside project authority.
Stop when the answer satisfies the prompt's competency criteria or retains `NOT READY` with the smallest evidence set that could change the verdict.

## Assumptions

All metric values, discharges, and event order come from the synthetic prompt.
Every area-percentage calculation uses the current reference area as denominator.
Ceiling exceedance has priority over floor attainment.
The q-grid is anchored to zero at 10 m3/s intervals.

## 1. First verdict calculations

### Baseline at 100 m3/s

The baseline has no reference.
Current code records zero changes and `accept`.
It begins as both reference and position and is published under the supplied assumptions.

### Trial at 150 m3/s against 100 m3/s

The maximum-depth change is:

\[
\Delta h_{max}=3.36-2.00=1.36\ \text{m}
\]

The median-depth change is:

\[
\Delta h_{med}=0.80-0.50=0.30\ \text{m}
\]

The flooded-area percentage change is:

\[
\Delta A_f
=100\frac{1.140-1.000}{1.000}
=14.0\%
\]

Median depth and flooded area lie inside their current default bands.
Maximum depth exceeds its 1.25 m ceiling.
Ceiling priority makes the result `reject_high`.

The reference remains 100 m3/s.
The position remains 100 m3/s because an ordinary high result does not advance it.
The 150 m3/s point remains available and published as search evidence.

### Trial at 140 m3/s against 100 m3/s

The changes are:

\[
\Delta h_{max}=3.00-2.00=1.00\ \text{m}
\]

\[
\Delta h_{med}=0.78-0.50=0.28\ \text{m}
\]

\[
\Delta A_f
=100\frac{1.120-1.000}{1.000}
=12.0\%
\]

No criterion exceeds its ceiling.
All three criteria reach their floors, so the result is `accept`.

The reference and position both advance to 140 m3/s.

## 2. Re-judgment and state update

The finished 150 m3/s scenario is now compared with the 140 m3/s reference.

\[
\Delta h_{max}=3.36-3.00=0.36\ \text{m}
\]

\[
\Delta h_{med}=0.80-0.78=0.02\ \text{m}
\]

\[
\Delta A_f
=100\frac{1.140-1.120}{1.120}
=100\frac{0.020}{1.120}
\approx1.79\%
\]

All three changes are below their floors.
The new verdict is `reject_low`.

The scenario changes category because the reference changed, not because the simulated hydraulic state changed.
No rerun is required because the three final metrics already exist in the manifest.

The 140 m3/s scenario remains the reference.
The 150 m3/s scenario becomes the position but is not an ordinary selected member.
Its final depth asset is the next hot-start source.
Under the current monotone-in-discharge control flow, later proposals and accepted references move above this rejected-low position, so the 150 m3/s point remains a published nonmember.
Later free-pass acceptance applies instead to higher finished points that were skipped as `reject_high` against an earlier reference.

## 3. Acceptance-window and next-trial arithmetic

The current reference is 140 m3/s with maximum depth 3.00 m, median depth 0.78 m, and flooded area 1.120 km2.
The final measured segment runs from 140 through 150 m3/s.

### Maximum-depth crossings

The absolute response floor and ceiling are:

\[
h_{max,floor}=3.00+0.75=3.75\ \text{m}
\]

\[
h_{max,ceiling}=3.00+1.25=4.25\ \text{m}
\]

The last-segment slope is:

\[
m_{max}=\frac{3.36-3.00}{150-140}
=\frac{0.36\ \text{m}}{10\ \text{m3/s}}
=0.036\ \frac{\text{m}}{\text{m3/s}}
\]

The floor crossing is:

\[
Q_{max,floor}
=150+\frac{3.75-3.36}{0.036}
\approx160.83\ \text{m3/s}
\]

The ceiling crossing is:

\[
Q_{max,ceiling}
=150+\frac{4.25-3.36}{0.036}
\approx174.72\ \text{m3/s}
\]

### Median-depth crossings

The response floor and ceiling are 1.03 m and 1.28 m.
The last-segment slope is:

\[
m_{med}=\frac{0.80-0.78}{10}=0.002\ \frac{\text{m}}{\text{m3/s}}
\]

The crossings are:

\[
Q_{med,floor}=150+\frac{1.03-0.80}{0.002}=265\ \text{m3/s}
\]

\[
Q_{med,ceiling}=150+\frac{1.28-0.80}{0.002}=390\ \text{m3/s}
\]

### Flooded-area crossings

The area targets are:

\[
A_{f,floor}=1.120(1+0.10)=1.232\ \text{km2}
\]

\[
A_{f,ceiling}=1.120(1+0.15)=1.288\ \text{km2}
\]

The last-segment slope is:

\[
m_A=\frac{1.140-1.120}{10}=0.002\ \frac{\text{km2}}{\text{m3/s}}
\]

The crossings are:

\[
Q_{A,floor}=150+\frac{1.232-1.140}{0.002}=196\ \text{m3/s}
\]

\[
Q_{A,ceiling}=150+\frac{1.288-1.140}{0.002}=224\ \text{m3/s}
\]

### Combined window and proposal

The earliest floor and ceiling are both set by maximum depth.
The window is approximately 160.83 through 174.72 m3/s.
The only 10 m3/s grid value inside is 170 m3/s, so the next proposal is 170 m3/s.

## 4. Next-trial and maximum behavior

### Trial at 170 m3/s against 140 m3/s

\[
\Delta h_{max}=3.85-3.00=0.85\ \text{m}
\]

\[
\Delta h_{med}=0.84-0.78=0.06\ \text{m}
\]

\[
\Delta A_f
=100\frac{1.170-1.120}{1.120}
\approx4.46\%
\]

Maximum depth reaches its 0.75 m floor and does not exceed its 1.25 m ceiling.
The other two criteria remain below their floors, and no ceiling is exceeded.
The result is `accept`.

Reference and position both advance to 170 m3/s.

### Maximum at 220 m3/s against 170 m3/s

\[
\Delta h_{max}=5.30-3.85=1.45\ \text{m}
\]

\[
\Delta h_{med}=1.10-0.84=0.26\ \text{m}
\]

\[
\Delta A_f
=100\frac{1.320-1.170}{1.170}
\approx12.82\%
\]

Maximum depth exceeds its ceiling, so ceiling priority makes the measured verdict `reject_high`.
Median depth and flooded area being inside their bands cannot override that result.

The maximum remains a published forced endpoint under the supplied non-edge-error assumption.
Because its measured result is high, the loop continues to fill the gap below it instead of stopping immediately.

## 5. Publication and membership classification

| Discharge | Published | Selection state | Position state |
| ---: | --- | --- | --- |
| 100 m3/s | Yes | Baseline selected member. | Initial position until 140 is accepted. |
| 140 m3/s | Yes | Ordinary accepted selected member. | Position and reference until re-judgment moves position to 150. |
| 150 m3/s | Yes | Published search point, not currently selected. | Position after it is re-judged low against 140. |
| 170 m3/s | Yes | Ordinary accepted selected member. | New position and reference. |
| 220 m3/s | Yes | Forced endpoint member despite measured high. | Not advanced through the ordinary accept branch at this point. |

Storage presence cannot reconstruct this table because current code publishes ordinary non-edge-error trials before verdict.
The result also omits free-pass comparisons and lacks an explicit membership field or list.

## 6. Limitations and evidence labels

### Limitations

1. Straight-line interpolation cannot reveal an unsampled floodplain threshold.
A trial beyond the threshold can correct the curve with a measured high result, but the first overshoot has already occurred and complete transition resolution is not guaranteed.
2. Sequential depth-only hot starts prevent ordinary parallel execution and can create path dependence when omitted dynamic state or insufficient duration affects the target result.
3. Flooded-area percentage uses the reference area as denominator.
The same absolute area increase appears large when the reference extent is small and small when the reference extent is broad.
4. Current outputs do not preserve an explicit selected set, free-pass re-judgments, finest-step membership overrides, or a dedicated maximum-member marker.
Published storage therefore contains more than selected membership.
5. Current q-grid logic does not validate adopted points, endpoints, or the opening authored step.
6. The current depth and area metrics and their defaults do not establish alignment with DR-030's monitor-point stage quantities or its differing median and extent bands.
7. The public ND input accepts `save_zarr` but the job does not forward it into `RunConfig`, so no Zarr artifact is produced.
If a generic caller sets `RunConfig.save_zarr=true`, post-processing creates a directory-backed store and manifest construction fails when the file-only hash helper opens that directory as a file.

### Evidence labels

| Claim type | Evidence label | Reason |
| --- | --- | --- |
| Metric values in this lab | Synthetic exercise assumption, outside the project evidence taxonomy | The values are invented for instruction and cannot support a project claim. |
| DR-030 ALT-C selection | Selected methodology | The Decision Register supplies the selected alternative and status within its scope. |
| Current verdict and publication branches | Current implementation | The reviewed jobs code supplies the behavior. |
| Target observation and emergent `q_set` | Target design | The system-design guide states intended ownership and materialization behavior. |
| Absent DR-041 row and grid authority | Open question | Current code cites a record that is absent from the reviewed register. |

## 7. Readiness verdict

**NOT READY.**

The packet is synthetic and contains no authoritative bound derivation, authorized resolution of the DR-030 metric and default mismatch, durable selected-membership record, authorized q-grid contract, storage observation, numerical history, edge and domain evidence, hot-start sensitivity, or hydraulic validation.

The smallest evidence set that could change the verdict includes:

1. Traceable \(Q_{HFT}\), \(Q_{100}\), source version, period, fit, uncertainty, and rounding for real reach bounds.
2. Authorized metric definitions and bands aligned across DR-030 and current code.
3. A durable member index separate from all published trial manifests.
4. An authorized q-grid rule enforced or explicitly excepted at adoption, endpoints, and bootstrap.
5. Storage observation of every required selected artifact.
6. Convergence history, complete mass-balance evidence, edge and domain diagnostics, process provenance, and failure evidence.
7. Cold-start and alternative-hot-start sensitivity plus hydraulic validation for the intended result quantities.
