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

---

## 中文版本

- **貢獻者：** [Jo-Leo-35](https://github.com/Jo-Leo-35)
- **專案：** [Ray](https://github.com/ray-project/ray)
- **報告範圍：** 2026 年 9 月 1 日至 10 月 11 日
- **加入 fellowship 日期：** 2026 年 9 月 28 日
- **公開活動查核截至：** 2026 年 10 月 10 日 15:10（Asia/Taipei，UTC+8）

### 摘要

我從入門學習進展到 macOS task 記憶體指標調查、社群 PR 的獨立驗證，以及提交第一個 Ray 上游 PR。[PR #66909](https://github.com/ray-project/ray/pull/66909) 是針對 issue #66729 所揭露問題的文件優先回應：使用者需要正確理解 Ray Data task 記憶體量測何時可用，以及沒有樣本時應如何判讀。依 fellowship 公告，這份 September 報告包含 10 月初的工作；查核時間之後的工作尚未納入。

### 貢獻狀態

| 貢獻類型 | 數量 |
| --- | ---: |
| 自行撰寫並向上游提交的 PR | 1 |
| 獨立驗證的 PR | 1 |
| 在 GitHub 提交的正式 PR review | 0 |
| 提出的 issue | 1 |

### Pull Requests

#### [#66909 — 釐清 task USS 指標與平台限制](https://github.com/ray-project/ray/pull/66909)

- **角色／狀態：** 作者；於 2026 年 10 月 10 日 11:30（Asia/Taipei）提出；查核時為 open、已進入可 review 狀態，尚未合併。此 PR 包含一個有 sign-off 的 commit，修改兩份文件，共新增 12 行、刪除 2 行。
- **我想解決的問題：** Ray Data 監控指南仍列出已過時的 `average_max_uss_per_task` 指標；OOM 指南則建議根據 task 量測設定 `memory`，卻沒有說明 task USS 樣本目前只在 Linux 上收集。在 macOS 或 Windows 上，使用者可能把 `max_uss_bytes.num_samples == 0` 誤解成記憶體用量實測為零，或因未出現高記憶體警告而誤以為設定安全。
- **PR 的修改內容：** 將 `max_uss_bytes` 說明為跨 task 的 worker process 峰值 USS 估計分布，列出匯出的平均值與最大值 gauges，說明估計僅適用於 Linux，並提醒讀者在使用最大值調整記憶體或依賴週期性高記憶體警告之前，先確認確實存在樣本。
- **範圍：** 這是一項純文件修正，不改變 runtime 行為，也沒有加入 macOS sampler。macOS 相容的 CPU task 記憶體取樣仍由 [issue #66729](https://github.com/ray-project/ray/issues/66729)追蹤；MPS 與 CUDA／NVML 統計屬於不同範圍。
- **驗證：** 本機已通過 `git diff --check`、四個適用的 pre-commit hooks、Vale，以及隔離環境中的 Sphinx render 檢查；未執行完整 Ray 文件建置。查核時 DCO、Cursor Bugbot、Buildkite microcheck／release 與 Read the Docs 狀態均為成功；這些上游檢查不能取代人工 review，當時也尚未有人類 reviewer 提交 approval。
- **分支／fork：** 我的 [Ray fork](https://github.com/Jo-Leo-35/ray) 中的 [`Jo-Leo-35:docs/task-uss-platform-limits`](https://github.com/Jo-Leo-35/ray/tree/docs/task-uss-platform-limits)。我依劉奇聖的[開發開源專案常用的 Git 操作指南](https://chishengliu.com/zh-tw/posts/opensource-git-operations/)所整理的原則，使用獨立貢獻分支與有 sign-off 的 commit。

### Reviewed PR／獨立驗證

#### [#66878 — 改善 JSON 讀取錯誤的上下文資訊](https://github.com/ray-project/ray/pull/66878)

- **作者／狀態：** Yin-1022；查核時仍為 open，尚未合併。
- **我的貢獻：** [公開驗證留言](https://github.com/ray-project/ray/pull/66878#issuecomment-6076811929)，發表於 2026 年 10 月 9 日 15:52（Asia/Taipei）。
- 使用 Ray 2.59.0 與 pandas 2.3.3，重現解析錯誤從 worker 傳到 driver 時，保留檔案上下文與輸入路徑，卻遺失 parser 詳細訊息的行為。在外層訊息加入原始例外文字後，三項檢查皆通過。
- 這是獨立驗證留言，不是自行提交的 PR，也不是正式 approval。留言未記錄受測 commit 或完整日誌，因此最新版 PR 仍待重新驗證。

### 其他貢獻

#### [#66729 — 在 macOS 上支援 task 記憶體指標](https://github.com/ray-project/ray/issues/66729)

- **角色／狀態：** Issue 作者；2026 年 10 月 5 日提出，查核時仍為 open。
- 記錄 Apple M5、macOS 26.6、Python 3.12.14、Ray 2.59.0 上的純 CPU `map_batches` 實驗。64 MiB 與 1 GiB 配置都完成兩個 task，但 `max_uss_bytes.num_samples` 仍為零。
- 追查到僅支援 Linux 的取樣能力檢查，並說明 high-memory detector 為何跳過沒有樣本的 operator。釐清量測不可用不代表實際記憶體用量為零。
- 補充獨立的 CPU／MPS 比較，說明 CPU USS 支援與 MPS 記憶體統計應分開處理。MPS 實驗在 Ray 之外執行，不能作為 Ray 支援 MPS 的證據。
- 公開重現步驟與[證據壓縮檔](https://github.com/user-attachments/files/33063395/ray-macos-evidence-redacted.zip)。原始實驗記錄於 10 月 5 日，早於 fork 建立日期；當時未測試 Linux、master／nightly 或 Dashboard／Prometheus，MPS 觀察則為每個裝置各執行一次。
- 提交純文件的 [PR #66909](https://github.com/ray-project/ray/pull/66909)，先修正目前可立即處理的使用者指引。macOS runtime 取樣支援仍待完成，不宣稱已由此 PR 解決。

#### 學習與準備

- 學習 Ray 貢獻流程，以及如何整理包含環境資訊、實際與預期行為的可重現 issue。
- 學習 Ray 與 Ray Data 概念，並觀看 [Ray in 30 min](https://www.youtube.com/watch?v=4Rbd_1Kb2qU)，此項已記錄在先前報告中。

### 待驗證項目與下一步

- 回應 #66909 的 review 意見、取得人工 review，並在報告中持續更新 open／merged 狀態。查核時的三項術語建議來自自動化 reviewer，不是 approval。
- 記錄受測 commit，重新驗證 #66878 的 parser 訊息與 JSON／JSONL 無效 UTF-8 行為。另一位 reviewer 提出了 [Pandas JSONL 例外子型別問題](https://github.com/ray-project/ray/pull/66878#discussion_r4234649165)，我尚未獨立驗證。
- 以重複配置實驗評估 macOS CPU USS 取樣準確度與額外負擔，並在可行情況下加入 Linux 對照。
- 若可行，另行準備小範圍的 CPU runtime 取樣變更與針對性測試。PR #66909 改善現有文件，但沒有實作 sampler；MPS 與 CUDA／NVML 仍分開處理。
- 在 10 月 11 日截止前更新具日期的證據與貢獻數量。待辦項目不計為已完成工作。

### 參考資料

- [詳細驗證證據、目前 PR 版本與執行清單](references/follow-up.md)
- [October 預留報告](https://github.com/Jo-Leo-35/opensource4you-review-september/blob/main/Ray/October/report.md)；10 月 9 日的驗證已納入本期，下期不重複計算。
