# Ray Monthly Contribution Report — September 2026 / Ray 每月貢獻報告 — 2026 年 9 月

## English Version

- **Contributor:** [Jo-Leo-35](https://github.com/Jo-Leo-35)
- **Project:** [Ray](https://github.com/ray-project/ray)
- **Reporting period:** September 1–October 11, 2026
- **Fellowship start date:** September 28, 2026
- **Public activity checked as of:** October 10, 2026, 15:10 (Asia/Taipei, UTC+8)

### Summary

I progressed from onboarding to investigating macOS task memory metrics, independently validating a community PR, and submitting my first upstream Ray PR. [PR #66909](https://github.com/ray-project/ray/pull/66909) is a documentation-first response to the problem identified in issue #66729: users need accurate guidance about when Ray Data task-memory measurements are available and how to interpret missing samples. This September report includes early October work as required by the fellowship announcement; work after the snapshot time is not yet included.

### Contribution Status

| Contribution type | Count |
| --- | ---: |
| Pull requests authored and submitted upstream | 1 |
| Pull requests independently validated | 1 |
| Formal GitHub PR reviews submitted | 0 |
| Issues opened | 1 |

### Pull Requests

#### [#66909 — Clarify task USS metrics and platform limitations](https://github.com/ray-project/ray/pull/66909)

- **Role / status:** Author; opened October 10, 2026, at 11:30 (Asia/Taipei); open, ready for review, and not merged at the snapshot. The PR contains one signed-off commit and changes two documentation files with 12 additions and 2 deletions.
- **Problem I want to solve:** Ray Data's monitoring guide still names the obsolete `average_max_uss_per_task` metric, while the OOM guide recommends choosing a `memory` value from task measurements without stating that task USS samples are collected only on Linux. On macOS or Windows, users could mistake `max_uss_bytes.num_samples == 0` for measured zero memory usage or assume that no high-memory warning means their configuration is safe.
- **What the PR changes:** Documents `max_uss_bytes` as a distribution of estimated peak worker-process USS across tasks, identifies its exported mean and maximum gauges, explains the Linux-only estimate, and tells readers to confirm that samples exist before using the maximum for memory sizing or relying on periodic high-memory warnings.
- **Scope:** This is a documentation-only correction. It does not change runtime behavior or add a macOS sampler. The broader goal of macOS-compatible CPU task-memory sampling remains tracked in [issue #66729](https://github.com/ray-project/ray/issues/66729); MPS and CUDA/NVML accounting are separate scopes.
- **Validation:** `git diff --check`, four applicable pre-commit hooks, Vale, and an isolated Sphinx rendering check passed locally. The full Ray documentation build was not run. At the snapshot, DCO, Cursor Bugbot, Buildkite microcheck/release, and Read the Docs statuses were successful; these upstream checks do not replace human review, and no human approval had been submitted.
- **Branch / fork:** [`Jo-Leo-35:docs/task-uss-platform-limits`](https://github.com/Jo-Leo-35/ray/tree/docs/task-uss-platform-limits) in my [Ray fork](https://github.com/Jo-Leo-35/ray). I used a separate contribution branch and a signed-off commit, following the workflow principles in Chi-Sheng Liu's [Git workflow guide](https://chishengliu.com/zh-tw/posts/opensource-git-operations/).

### Reviewed PR / Independent Validation

#### [#66878 — Improve JSON read error context](https://github.com/ray-project/ray/pull/66878)

- **Author / status:** Yin-1022; open and not merged at the snapshot.
- **My contribution:** [Public validation comment](https://github.com/ray-project/ray/pull/66878#issuecomment-6076811929), posted October 9, 2026, 15:52 (Asia/Taipei).
- Using Ray 2.59.0 and pandas 2.3.3, I reproduced a parsing error that retained file context and the input path but lost parser details across the worker-to-driver boundary. Including the original exception text in the outer message made all three checks pass.
- This was an independent validation comment, not an authored PR or a formal approval. The comment does not identify the tested commit or contain full logs, so validation of the current PR revision remains pending.

### Other Contributions

#### [#66729 — Support task memory metrics on macOS](https://github.com/ray-project/ray/issues/66729)

- **Role / status:** Issue author; opened October 5, 2026; still open at the snapshot.
- Recorded CPU-only `map_batches` experiments on Apple M5, macOS 26.6, Python 3.12.14, and Ray 2.59.0. Both 64 MiB and 1 GiB allocations completed two tasks, but `max_uss_bytes.num_samples` remained zero.
- Traced the behavior to the Linux-only sampling capability check and explained why the high-memory detector skips operators without samples. Clarified that unavailable measurements do not mean zero memory usage.
- Added a separate CPU/MPS comparison to explain why CPU USS support and MPS accounting need separate scopes. The MPS experiment ran outside Ray and does not demonstrate Ray MPS support.
- Published reproduction details and an [evidence archive](https://github.com/user-attachments/files/33063395/ray-macos-evidence-redacted.zip). The original runs were captured on October 5, before the fork was created. Linux, master/nightly, and Dashboard/Prometheus were not tested in those runs; MPS observations were one run per device.
- Submitted the documentation-only [PR #66909](https://github.com/ray-project/ray/pull/66909) to correct the immediately actionable user guidance. Runtime support for macOS sampling is still pending and is not claimed as solved by that PR.

#### Learning and Preparation

- Learned Ray's contribution workflow and how to document reproducible issues with environment details and observed versus expected behavior.
- Studied Ray and Ray Data concepts and watched [Ray in 30 min](https://www.youtube.com/watch?v=4Rbd_1Kb2qU), as recorded in my earlier report.

### Pending Validation and Next Steps

- Address review feedback on #66909, obtain human review, and keep its open/merged status current in the report. The three terminology suggestions present at the snapshot came from an automated reviewer and are not an approval.
- Revalidate #66878 at a recorded commit, including parser details and JSON/JSONL invalid UTF-8 behavior. A [Pandas JSONL exception-subtype question](https://github.com/ray-project/ray/pull/66878#discussion_r4234649165) was raised by another reviewer; I have not independently verified it.
- Benchmark macOS CPU USS sampling accuracy and overhead, with repeated allocation measurements and a Linux control where feasible.
- If feasible, prepare a separate, small runtime change for CPU sampling with targeted tests. PR #66909 improves the current documentation but does not implement this sampler. MPS and CUDA/NVML remain separate follow-ups.
- Refresh dated evidence and contribution counts before the October 11 deadline. Pending work is not counted as completed work.

### References

- [Detailed validation evidence, current PR revision, and execution checklist](references/follow-up.md)
- [October report placeholder](https://github.com/Jo-Leo-35/opensource4you-review-september/blob/main/Ray/October/report.md); the October 9 validation is included here and should not be counted again next cycle.