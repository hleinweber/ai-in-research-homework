# Solvability review

**Historical review:** The verdict and curve-sensitivity experiments below describe the pre-exclusion grader. On 2026-11-16 the user requested excluding straight-line interiors, and this session implemented that change. See authoring/evidence/check_bridge_exclusions.py and bridge-exclusion-validation.json. The revised grader replaces excluded differences with endpoint-based interpolation before filtering, omits those interiors from RMS, retains endpoints, and skips empty windows. It detects 194 interiors, excludes 21721 record rows, retains 8332 scored rows in 559 windows, and skips 88 windows. Reference, curved interiors, and constant offsets pass; raw input, constant field, and blanket smoothing fail. This addresses interior-shape dependence but does not certify the numerical mask as a scientific catalogue or resolve other historical findings. This session has now helped build the task and cannot provide a new independent solvability verdict.

Reviewed: 2026-11-16, commit 45401be, by Codex using PowerShell and source inspection, in a session that did not help build the task.
**Verdict: not_solvable**

The scientist confirms in the review request that reasonable curved interpolation through unrecoverable interference can be as valid as the reference's straight-line bridges. This is scientific evidence against treating that particular reconstruction as uniquely correct. The task has a passing artifact; the verdict concerns whether scientific correctness is reliably graded.

| # | Check | Answer | Reason and evidence |
|---|---|---|---|
| 1 | Information | no | instruction.md fully states the numerical comparison, but does not supply evidence selecting the reference's interpolation in unrecoverable intervals. tests/test_outputs.py enforces that choice whenever its filtered difference exceeds 1 nT. A disclosed metric does not make an arbitrary scientific key identifiable. |
| 2 | Stated and hidden ranges | unclear | The instruction and verifier agree on units, cadence, gap handling, filter, and 1 nT per-window/component threshold. README.md reports 647 selected windows and 64 exclusions; the binary bundle and all data ranges were not independently numerically verified. |
| 3 | An honest reference | unclear | solution/solve.sh openly copies a shipped solution/data/solution.asc, a case permitted by the skill. authoring/provenance/README.md records manually tuned Python and public sharing permission, but the original code and detailed construction record are absent. Dependence only on available inputs remains unverified. Copying the key would not establish that provenance. |
| 4 | A right answer passes reliably | no | README.md explicitly says alternative expert solutions have not calibrated the criterion. The scientist now accepts multiple bridge shapes, yet every sample in every selected window is compared to one shape. No bound establishes that accepted alternatives stay within 1 nT after filtering. The reference passing against itself is not a reliability test. No alternative was numerically scored in this review. |
| 5 | A wrong answer fails reliably | unclear | Existing authoring/evidence/verifier-validation.json reports failures for raw input (100/106/175 windows by component), constant field (47/39/19), and blanket one-hour low-pass (39/37/14). These are recorded author runs, not rerun here. They are useful negative controls, but do not establish acceptance of valid alternatives or cover selective overcleaning. |
| 6 | Identifiable | no | The scientist describes these interference intervals as unrecoverable and accepts curved as well as linear interpolation. Input magnetic measurements alone therefore do not select a unique interior path. The grader asks for reference agreement in this undetermined quantity. This does not imply that interference detection or all surrounding signal is unidentifiable. |
| 7 | A defensible key | no | tests/test_outputs.py:window_errors compares every sample to one expert reconstruction; test_event_window_accuracy rejects any component/window above 1 nT. A second scientifically valid interpolation has no demonstrated acceptance guarantee. README.md also says the complete automated event selection still requires scientist approval. |
| 8 | Not misleading by construction | unclear | No explicit trap found in instruction.md or tests/test_outputs.py. Agreement with a visually smooth reconstruction alone cannot establish correctness in unrecoverable intervals; no independent physical discriminant was supplied. |
| 9 | One fact does not decide it | yes | instruction.md requires detection and correction throughout a multicomponent record, rather than recall of a single hidden formula. The disclosed filter is a scoring operation. |
| 10 | No shortcut, no leak | no | environment/Dockerfile copies only input data, but the public repository ships solution/data/solution.asc and tests/reference.npz. README.md acknowledges public sharing. A solver able to fetch the public key could bypass the science despite the instruction against task-specific online solutions. No network fetch was attempted here. |
| 11 | Limits | unclear | task.toml gives 18000 seconds, 1 CPU, 2048 MB RAM, 4096 MB storage, and public network. NumPy/SciPy are preinstalled in the agent image. The stated 200000-row filtering workload appears compatible, but no runtime was measured. The 160 expert-hour estimate has no explanation separating scientific judgment from repetitive editing. |

## What I ran

- Read the skill, instruction.md, all test source and container definitions, task.toml, solution/solve.sh, README.md, provenance, authoring/attempts.md, event-summary.json, revise_events.py, build_verifier.py, and verifier-validation.json. Inspected the input/reference ASCII headers and initial rows. Did not execute authoring scripts, since they rewrite task artifacts.
- git rev-parse --short HEAD returned 45401be. git status --short showed a pre-existing modified authoring/attempts.md and an untracked nested repository. Neither was changed.
- Checked applicable ancestor AGENTS.md locations; none were present at those paths.
- python was unavailable through the Windows command alias. uv python find --offline located C:/Users/hleinweber/AppData/Roaming/uv/python/cpython-3.14-windows-x86_64-none/python.exe. Importing numpy/scipy/pytest with that interpreter stopped with ModuleNotFoundError: numpy. Docker was not found by Get-Command.
- Therefore did not run pytest, an isolated oracle, fresh numeric baselines, or a curved-interpolation sensitivity experiment. Reported numeric baseline results are explicitly from the existing validation JSON. No scratch task was created or left behind. No external factual sources or network lookups were needed: the scientific premise is the user's scientist confirmation.

## Problems

1. **Reference agreement conflates cleaning with an arbitrary reconstruction choice.** Let H be the disclosed segment-wise high-pass operator. If two accepted reconstructions differ by d inside an unrecoverable interval, their graded difference is H(d). Endpoint agreement and smoothness do not force RMS(H(d)) <= 1 nT. A one-hour high-pass filter retains shorter-timescale curvature; it does not erase all reasonable bridge differences. Conversely, some curves may fit within tolerance. This review does not claim that every curve fails or provide an experimentally measured false-rejection rate.
2. **All-or-nothing grading magnifies this uncertainty.** There are 647 windows and 1941 component/window constraints. One discrepancy fails the reward. authoring/attempts.md reports an earlier agent passed 629/647 windows with 24/1941 component failures; this is a report, not an independently audited trajectory. It is not evidence that those failures were scientific errors, nor proof they all came from interpolation.
3. **Filtering couples uncertain and apparently reliable samples.** window_errors filters complete continuous segments before window selection. A bridge-only difference produces differences outside the bridge through the forward/backward filter. Merely masking the interior after filtering does not remove its grading influence. One-hour exclusions at acquisition-gap boundaries do not address internal interpolation boundaries.
4. **The key and catalogue need scientific validation.** Correction-derived candidate boundaries reproduce where this reference was edited, not independently established interference truth. README.md acknowledges that the complete selection lacks scientist approval. The original cleaning code is absent, so input-only reachability is incompletely documented.
5. **The public answer is a direct shortcut.** This must be considered when interpreting benchmark runs even though instructions prohibit its use.

## Smallest fix

Keep the data, output format, and interference-cleaning objective. Have the scientist annotate which intervals contain genuinely unrecoverable signal, approve the scored event catalogue, and distinguish those intervals from events with recoverable variations. State that physically reasonable interpolation is accepted and define its constraints (for example, boundary agreement, plausible slopes/curvature, and absence of residual spikes), without demanding unknown interior truth.

Remove dependence on the arbitrary interior bridge from the reference-agreement score. For that score only, replace candidate and reference interiors with the same canonical bridge before applying the existing full-segment filter; exclude the uncertain interiors from reference RMS. This makes outside scores independent of the submitted interior shape. Add separate method-independent checks on the submitted interiors and boundaries, calibrated by the scientist, so that leaving raw interference, a discontinuity, or pathological oscillations cannot pass via canonicalization. Do not modify recoverable events this way. Another option is to score reliable regions independently with justified padding and transient exclusions; an uncalibrated fixed guard interval is not enough.

Calibrate the revised grader using independently produced, scientist-approved curved alternatives at representative amplitudes and durations. Change only unrecoverable intervals to isolate interpolation sensitivity, then test a complete second expert cleaning. Valid alternatives should pass; raw input, selective overcleaning, constants, and blanket smoothing should fail. Keep the 1 nT threshold on supported quantities unless calibration justifies a change. A global tolerance increase or allowing a fraction of failed windows is not a targeted remedy.

Explicitly prescribing straight-line bridges would make interpolation a stated output convention, but would change what this benchmark measures and would not validate rejection of curved scientific answers under the present objective. Do not interpret the earlier reward-zero run as established scientific failure until its failed windows are reviewed under the corrected criterion.

To address the remaining checks, document how the shipped reference was obtained solely from visible inputs, record actual compute requirements and the basis of the expert-time estimate, and protect held-out grading answers from public retrieval where possible. Until then, audit traces for key retrieval and report remaining uncertainty. No task, reference, or verifier changes were made in this review.

## What to check in a run's trace

- Downloads, git operations, or HTTP requests for this repository's solution/data/solution.asc, tests/reference.npz, authoring files, or mirrors, including the pre-existing nested checkout if accessible in a run.
- For each failed component/window, separate unrecoverable reconstruction differences, filter spillover, residual lander interference, and destruction of genuine variations. Do not label a smooth curve wrong solely because it differs from a straight line.
- Confirm whether the agent actually identified interference and preserved recoverable rapid variations; passing a reference score alone does not establish that.

## Follow-up numerical tests (2026-11-16)

The user supplied C:/Users/hleinweber/miniconda3/envs/py3p12/python.exe. NumPy 2.5.3 and SciPy 1.18.1 are available; pytest is absent. This section supersedes the earlier statement that numeric tests could not be run. The task containers pin NumPy 2.1.3 and SciPy 1.14.1, so these are local numerical results, not a pinned-container validation.

Executed the original tests/test_outputs.py window_errors and validate_structure functions unchanged. To import that module without pytest, inserted a temporary in-process pytest stub providing identity fixture/parametrize decorators; no pytest tests or fixtures were executed. All arrays and alternative outputs stayed in memory; no task, reference, or grader files were changed. The source was supplied through PowerShell's here-string to the specified Python executable on stdin. Python bytecode writing was disabled.

Verified the 200000-by-4 supplied solution is exactly equal to reference.npz's reference array and loaded all 647 grading windows. Reference self-comparison passed at zero error. Raw input failed 228 distinct windows and 381 component/window constraints; its maximum RMS values [4.490259476645342, 6.275305814834244, 7.532811592841084] nT reproduce the recorded baseline closely.

### How curves were constructed

Automatically identified 184 approximately straight runs overlapping scored windows. This is a numerical proxy for possible reference bridges, not a scientist-approved map of unrecoverable intervals. The criterion was max(abs(diff(reference[:,1:], n=2, axis=0)), axis=1) < 0.00201 nT, using runs of at least 12 second-difference entries, requiring raw/reference difference above 0.5 nT, available 12-sample context on both sides, and no timestamp step >= 7.5 seconds in that context. If a true run of second-difference entries was [a,e), used sample slice [a,b) with b=e+1; this conservative convention can omit the last straight sample. All sample indices below are zero-based, stop-exclusive. Existing reference values were retained outside those slices. Timestamps and array shape were unchanged.

For PCHIP, used scipy.interpolate.PchipInterpolator on four context anchors [a-12, a, b-1, b+11] with actual timestamps, independently for all three components. Replaced each slice with that curve, or a 50% blend of it and the reference. Endpoints agree with the reference. PCHIP is a context-based alternative, although this alone does not certify the surrounding reference or the physical validity of every inferred interval.

For controlled sensitivity, added A * 16*u^2*(1-u)^2 with u=(t-t[a])/(t[b-1]-t[a]) inside each slice. This bow has zero displacement and zero added slope at both endpoints, and peak amplitude A. Added the same positive bow to each component. These are diagnostic curves, not an independent scientific solution and not curves specifically approved by the scientist. The all-runs intervention may also affect numerically straight intervals that are not actually unrecoverable.

| Alternative | Maximum change from reference (nT) | Maximum window RMS across components (nT) | Failed windows | Failed component/windows | Result |
|---|---:|---:|---:|---:|---|
| Reference | 0 | 0 | 0 | 0 | pass |
| 50% context PCHIP | 0.834 | 0.274 | 0 | 0 | pass |
| Full context PCHIP | 1.667 | 0.548 | 0 | 0 | pass |
| 0.5 nT bow, all 184 slices | 0.5 | 0.394 | 0 | 0 | pass |
| 1 nT bow, all 184 slices | 1 | 0.788 | 0 | 0 | pass |
| 2 nT bow, all 184 slices | 2 | 1.575 | 67 | 201 | fail |
| 3 nT bow, all 184 slices | 3 | 2.363 | 161 | 483 | fail |
| Isolated 2 nT bow, B1 only, rows [62991,63312) | 2 | 0.756277 | 0 | 0 | pass |
| Isolated 3 nT bow, B1 only, rows [62991,63312) | 3 | 1.134415 | 1 | 1 | fail |

The isolated interval lasts approximately 1599.98 seconds (26.7 minutes). For its 3 nT bow, the sole failing grading window was zero-based window 232, rows [63011,63206), with RMS [1.134415402491569,0,0] nT. This demonstrates rejection from one smooth interior difference without requiring accumulation across many events. It does not establish that this particular 3 nT curve is physically justified. For the 2 nT all-runs experiment the worst window was rows [164623,164653), with approximately 1.575267 nT RMS per component.

### Updated interpretation

The grader demonstrably accepts the tested context-based curved alternatives. Therefore, the scientist's statement that curves can be valid does not by itself demonstrate a false rejection: the acceptable class must include an actual curve that lies outside the numerical pass region. Curvature alone is not the issue; its amplitude, duration, and the scored window matter. The bow experiment establishes sensitivity and a concrete candidate for scientist review, not a verified false-negative rate.

Check 4's empirical question (whether scientist-approved alternatives reliably pass) remains unclear until these or independently generated curves are approved and scored. Check 7 still lacks a calibrated acceptance criterion for the full scientifically accepted class, and Check 6 still identifies that unrecoverable interiors are not uniquely recoverable; nonuniqueness would be harmless if the grader accepted all scientifically reasonable alternatives. Retain the not_solvable verdict as a grading-readiness judgment with this qualification, rather than claiming the experiment proved a scientifically correct output was rejected. The separately identified public-key shortcut also remains unchanged.

Before changing the grader, ask the scientist to assess the isolated 3 nT bow and realistic endpoint/context-based alternatives over the genuinely unrecoverable intervals. If all accepted alternatives lie within 1 nT filtered RMS, the existing tolerance may already accommodate interpolation ambiguity. If an approved curve fails, apply the targeted grading fix described above. Neither globally raising the threshold nor attributing the earlier agent's failures to curvature is supported by these measurements.


## Full-coverage implementation update

At the user's subsequent request, grading was expanded to all 178279 non-excluded samples, including quiet data and acquisition-segment edges. The 559 retained event windows are supplemented with uncovered runs split into blocks of at most 120 samples, yielding 2340 windows. The other 21721 samples remain excluded. Exhaustive local assertions establish exact coverage and no gap-crossing windows. Updated baseline results and implementation details are in README.md and authoring/evidence/bridge-exclusion-validation.json. Earlier counts in this historical review describe earlier grader versions. This session implemented the change; an independent re-review remains outstanding.


## Pinned pytest verification update

The actual verifier suite was run with Python 3.11 and all four packages pinned exactly as in tests/Dockerfile. Reference, curved excluded interiors, and constant offsets return pytest exit 0; missing output, raw input, and nonfinite excluded values return exit 1. The local artifact path alone is redirected, and no pytest stubs are used. Temporary artifacts and CTRF files are automatically cleaned. Reproducible command: `uv --system-certs run --no-project --python 3.11 --with pytest==8.4.1 --with pytest-json-ctrf==0.3.5 --with numpy==2.1.3 --with scipy==1.14.1 python -B week-1/submissions/hleinweber/insight-ifg-interference-cleaning/authoring/evidence/run_pinned_pytest.py`. The same environment reran check_bridge_exclusions.py with all assertions passing. Results are saved in pinned-pytest-validation.json and bridge-exclusion-validation.json. Docker is absent, so these are local pytest results, not Harbor/container rewards. Prior statements about unavailable local pinned testing are superseded; independent scientific review remains pending.


## Docker validation update

On 2026-11-16 local time (2026-11-17 UTC), Docker Desktop 4.99.0 with Linux engine 29.9.3 successfully built the unchanged task Dockerfiles. The real solution/solve.sh ran in the agent image; only its output artifact was copied into a separate network-disabled verifier image. tests/test.sh produced oracle reward 1 with six pytest checks passing. A fresh verifier with no artifact produced nop reward 0 and six expected missing-output fixture errors. Both containers used 1 CPU and 2048 MB RAM. Temporary containers and artifacts were cleaned; images remain as build cache. Results and logs are in authoring/evidence/container-validation.json and docker-*.log; run_container_validation.py reproduces the check. The CTRF plugin aggregates parameterizations into four summary entries; the pytest logs show all six collected checks. Static check_task.py passed with one task-size warning. This was direct Docker validation, not a Harbor-framework or CI run. Prior statements that Docker validation was unavailable are superseded.
