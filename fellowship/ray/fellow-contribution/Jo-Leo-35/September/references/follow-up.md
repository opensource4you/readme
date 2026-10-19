# Ray Verification and Handoff Checklist / Ray 驗證與交接清單

## English Version

This working checklist accompanies the [September report](../report.md). It separates historical evidence from work that still needs execution.

- **Public-record snapshot:** Checked on October 10, 2026; the latest evidenced GitHub update at audit was 15:10 Asia/Taipei (07:10 UTC).
- **Report repository:** [Jo-Leo-35/opensource4you-review-september](https://github.com/Jo-Leo-35/opensource4you-review-september).
- **Starting report commit:** `896a1d7`.
- **Current host:** Linux aarch64; Ray is not installed. This session checked public GitHub metadata, comments, and the PR diff. It did not run Ray, reproduce the reported behavior, or benchmark macOS.

### 1. Inventory and Attribution

| Item | Owner / my role | Status at audit | What is still pending |
| --- | --- | --- | --- |
| [PR #66909](https://github.com/ray-project/ray/pull/66909) | Jo-Leo-35 authored a documentation-only change linked to #66729 | Open, not merged; no human approval | Address or respond to terminology feedback and monitor human review |
| [PR #66878](https://github.com/ray-project/ray/pull/66878) | Yin-1022 authored it; Jo-Leo-35 posted an independent validation comment | Open, not merged | Revalidate the current revision and encoding cases |
| [Issue #66729](https://github.com/ray-project/ray/issues/66729) | Jo-Leo-35 authored the issue and recorded the experiments | Open; docs-only PR #66909 filed; runtime implementation checklist unchecked | Sampling benchmark, runtime implementation, and tests |
| [PR #66340](https://github.com/ray-project/ray/pull/66340) | xuwenyihust; related CUDA/NVML background | Open, not merged | No independent validation by Jo-Leo-35 established; not a contribution count |
| [Issue #66335](https://github.com/ray-project/ray/issues/66335) | xuwenyihust; related DGX Spark background | Open | Reference only; does not establish macOS or MPS behavior |

Public GitHub searches returned 1 authored PR, 1 commented PR, 0 formally reviewed PRs, and 1 authored issue for the queries below. Searches are a snapshot, may lag indexing, and cannot establish private or unposted work. Any newly discovered activity must be checked against its actual timestamps before inclusion.

```text
repo:ray-project/ray is:pr author:Jo-Leo-35 created:2026-09-01..2026-10-11
repo:ray-project/ray is:pr commenter:Jo-Leo-35 updated:>=2026-09-01
repo:ray-project/ray is:pr reviewed-by:Jo-Leo-35 updated:>=2026-09-01
repo:ray-project/ray is:issue author:Jo-Leo-35 created:2026-09-01..2026-10-11
```

For the commented and reviewed queries, check the dates of the contributor's comments/reviews, not only the PR creation date. No formal review by Jo-Leo-35 appeared in #66878's review list.

### 2. Before the October 11 Submission

- [x] Consolidate Ray's early October work into `Ray/September/report.md`.
- [x] Use the required September 1–October 11 period and separately record the September 28 start date.
- [x] Correct the public PR validation date to October 9.
- [x] Distinguish an authored PR, independent validation comment, and formal GitHub review.
- [x] Record authored docs-only PR #66909 and refresh the current public snapshot through its latest evidenced update on October 10 at 15:10 Asia/Taipei.
- [ ] Refresh the public activity and upstream status through the submission cutoff. Add only completed, evidenced work and update the snapshot timestamp.
- [ ] Confirm any additional October 9–11 work with dated links or execution logs; do not assume there was no activity merely because it is absent from the old report.
- [x] Consolidate KubeRay's early October learning entry into its September report and update the README. Public activity was rechecked on October 10; learning progress remains based on the existing reports.
- [x] Publish the initial consolidated Ray report: commit `75992d5` was pushed to `main`. Refresh and publish later report changes separately.

The engineering tasks below are follow-ups, not prerequisites for honestly reporting the contributions already completed.

### 3. PR #66878: Current Revision and Known Evidence

| Field | Value at audit |
| --- | --- |
| Head SHA | `3f4b50164d014e0082d936246b4fae8d1811c6e1` |
| Base SHA reported by GitHub | `13097981b2537776062251793f8c5e7c9d930661` |
| Branch | `Yin-1022:fix-json-read-error-context` |
| My comment | [2026-10-09 07:52:19 UTC](https://github.com/ray-project/ray/pull/66878#issuecomment-6076811929) |
| Last review comment at audit | [2026-10-09 21:21:30 UTC / October 10, 05:21 Taipei](https://github.com/ray-project/ray/pull/66878#discussion_r4234649165) |

My comment records Ray 2.59.0 and pandas 2.3.3 and the following narrow result:

| Check | Before including original error text | After including original error text |
| --- | --- | --- |
| File context present | True | True |
| Input path present | True | True |
| Parser detail present | False | True |

The tested commit, full environment, script, and raw output are not recorded in that comment or this report repository. Do not infer its tested commit from the comment time. Later PR commits changed the implementation and tests.

#### Questions requiring fresh execution

1. **Pandas JSONL invalid UTF-8:** [hikaru-212's comment](https://github.com/ray-project/ray/pull/66878#discussion_r4234649165) asks whether converting `UnicodeDecodeError` to plain `ValueError` is intentional. At the audited head, the [Pandas handler](https://github.com/ray-project/ray/blob/3f4b50164d014e0082d936246b4fae8d1811c6e1/python/ray/data/_internal/datasource/json_datasource.py#L230) catches `ValueError`, which includes `UnicodeDecodeError`. This is a static observation and another reviewer's reproduction, not a new runtime result from this session.
2. **Arrow fallback invalid UTF-8:** A bot [raised a worker/driver exception-type concern](https://github.com/ray-project/ray/pull/66878#discussion_r4229421649). The author [reported successful driver-side checks](https://github.com/ray-project/ray/pull/66878#discussion_r4229548791) and added assertions. Independently test the target environment before treating either claim as universal. This is distinct from the Pandas JSONL handler question.
3. **Parser diagnostics at the current head:** Repeat the original checks across an actual worker-to-driver boundary and cover both chunk-size estimation and subsequent reading.

#### Suggested execution order

- [ ] Fetch the latest PR metadata, comments, and head SHA. If the head has changed, inspect the delta before testing.
- [ ] Prepare a separate Ray checkout and follow its contributor/environment instructions. Record the checked-out SHA and the imported Ray path/version/commit so tests do not silently use an unrelated installed wheel.
- [ ] Identify and record the comparison revision (prefer the PR merge base for an isolated before/after comparison). Keep dependencies and inputs identical.
- [ ] Run the current regression tests listed below on the PR checkout, then compare relevant behavior with the baseline.
- [ ] Add temporary reproduction cases for invalid UTF-8 in the first JSONL record (estimation) and after a valid record (reading). Record which code path was actually exercised; do not assume file layout guarantees a specific path.
- [ ] Inspect driver-visible exception type, underlying cause, input path, parser/encoding details, and traceback. Preserve raw output.
- [ ] Summarize what is reproducible at the recorded SHA and what remains environment-dependent. Prepare any proposed public reply for review; this handoff does not request posting it.

Existing tests at the audited head (run from a prepared Ray checkout):

```bash
python -m pytest -q python/ray/data/tests/datasource/test_json.py -k 'test_json_read_invalid_format_includes_path or test_json_read_invalid_utf8_includes_path or test_jsonl_read_invalid_format_includes_path'
```

Source: [test_json.py at the audited revision](https://github.com/ray-project/ray/blob/3f4b50164d014e0082d936246b4fae8d1811c6e1/python/ray/data/tests/datasource/test_json.py#L133). This is a proposed command, not an executed result. The JSONL regression covers malformed syntax; it does not by itself close the JSONL invalid-UTF-8 question.

#### Upstream checks observed, not locally executed

- GitHub check runs for the audited SHA: DCO, disable-automerge, and Cursor Bugbot reported success.
- [Buildkite microcheck #55932](https://buildkite.com/ray-project/microcheck/builds/55932) reported success.
- [Buildkite release #110276](https://buildkite.com/ray-project/release/builds/110276) had a success status with the description “passed and blocked”; the docs status indicated a skipped build.
- These statuses do not establish independent local validation or complete platform coverage. The PR author's Windows baseline comparison is the author's result, not Jo-Leo-35's.

### 4. PR #66909: Authored Documentation Change

| Field | Value at audit |
| --- | --- |
| Status | Open, not merged; no human approval at the public-record snapshot |
| Opened | [2026-10-10 03:30:37 UTC / October 10, 11:30 Taipei](https://github.com/ray-project/ray/pull/66909) |
| Last evidenced public update | 2026-10-10 07:10 UTC / October 10, 15:10 Taipei |
| Head SHA | `85eea7c399052b672d84649da2b27ad11cc45ea2` |
| Branch | `Jo-Leo-35:docs/task-uss-platform-limits` |
| Diff scope | Two documentation files, 12 additions and 2 deletions |

#### Scope and problem addressed

- Replaces the obsolete `average_max_uss_per_task` documentation entry with `max_uss_bytes` and identifies the exported mean and maximum gauges.
- Explains that Ray Data estimates task USS only on Linux and that `max_uss_bytes.num_samples == 0` means measurements are unavailable, not that memory usage was measured as zero.
- Clarifies that log-based `memory` sizing requires USS samples and that the absence of a periodic high-memory warning does not prove the configured memory is sufficient.
- The change is documentation-only. It does not alter runtime behavior or add a macOS sampler, so the broader implementation request in #66729 remains open.

#### Validation and review status

- The PR author reports that `git diff --check`, four applicable pre-commit hooks, and Vale passed on macOS arm64. Vale retained five pre-existing suggestions without adding errors, warnings, or suggestions.
- The author also reports a successful isolated Sphinx smoke build of exact extracts from the two edited sections. The checkout was sparse, and the temporary smoke-build inputs are not part of the PR. This was not a full Ray documentation build and did not validate site-wide cross-references, autodoc, or the official theme.
- Upstream statuses observed for the PR head, rather than locally executed in this audit, include successful DCO and Cursor Bugbot checks, [Buildkite microcheck #56008](https://buildkite.com/ray-project/microcheck/builds/56008), [Buildkite release #110389](https://buildkite.com/ray-project/release/builds/110389) with the description “passed and blocked,” and the [Read the Docs PR build](https://anyscale-ray--66909.com.readthedocs.build/en/66909/).
- [Gemini Code Assist](https://github.com/ray-project/ray/pull/66909#pullrequestreview-5477416510) left three terminology-capitalization suggestions. This automated feedback is not human approval. Upstream CI success also does not establish independent local validation or acceptance of the change.

### 5. Issue #66729: macOS CPU USS Sampling

#### Historical evidence already public

- [Issue body](https://github.com/ray-project/ray/issues/66729): macOS runs captured on October 5 using Ray 2.59.0, commit `dd83c3a703a5422d0b47f142cb380c6330817608`.
- CPU allocations of 64 MiB and 1 GiB completed, with zero task USS samples and a false platform-capability check.
- [Attached evidence archive](https://github.com/user-attachments/files/33063395/ray-macos-evidence-redacted.zip) is linked publicly. This organizing session did not download or execute it.
- Linux, master/nightly, Dashboard/Prometheus behavior, and a sampling-overhead benchmark were not covered by the original issue validation.

#### Next steps and evidence required

- [ ] Read the issue's latest implementation checklist and relevant upstream code before designing a fix.
- [ ] Inspect the attached scripts/logs and recover the exact original environment and commands.
- [ ] On a macOS host, repeat the CPU-only baseline with the small allocation first; record sample count, public stats, versions, and allocation lifetime. Repeat on current master/nightly in a separate environment if supported.
- [ ] Use a comparable Linux run as a control and retain its exact revision and dependency versions. Linux results alone cannot validate macOS behavior.
- [ ] Evaluate a candidate macOS USS sampler with repeated baseline/allocation/release measurements at multiple allocation sizes. Record sampling latency, variability, and workload runtime with and without profiling. Treat USS accuracy as a measurement question, not a requirement to exactly match payload bytes.
- [ ] Define acceptable overhead and measurement semantics before adding support. Exercise unavailable/denied samples and process exit; preserve the distinction between unavailable and zero.
- [ ] If feasible, implement CPU-only sampling in a separate runtime PR with targeted unit tests, a macOS allocation test, and platform/measurement documentation as needed. Do not treat docs-only PR #66909 as runtime implementation evidence; record the implementation PR link only if such a PR is opened.
- [ ] Test high-memory detection separately with sufficient runtime, valid samples, and its actual threshold/interval. Absence of a warning in a short run is not evidence that detection works or fails.

Keep **Apple MPS accounting**, **CUDA/NVML accounting**, and the separate **unavailable-versus-zero runtime presentation change** out of the first CPU sampling implementation. Do not relabel `phys_footprint` as USS or combine unrelated memory counters.

The current Linux host can support code inspection and a Linux control after setup. macOS sampling and MPS conclusions require appropriate Apple hardware; leave those tasks pending when that environment is unavailable.

### 6. What to Save After Each Validation

For each completed run, record the date/time, OS/architecture, Python and dependency versions, Ray SHA and import path, exact command, input fixture, expected and observed behavior, exit code, and raw-log location. Distinguish baseline results from PR results and local findings from upstream CI or other reviewers' reports.

Only update the submission report's completed-work claims after that evidence exists. Rechecking the same PR expands its description; it does not increase the count of distinct PRs validated.

---

## 中文版本

這份工作清單是 [September 報告](../report.md)的補充，將既有證據與尚待實際執行的工作分開記錄。

- **公開紀錄快照：** 於 2026 年 10 月 10 日查核；查核時可確認的最近一次 GitHub 更新為 Asia/Taipei 15:10（UTC 07:10）。
- **報告 repository：** [Jo-Leo-35/opensource4you-review-september](https://github.com/Jo-Leo-35/opensource4you-review-september)。
- **整理前的報告 commit：** `896a1d7`。
- **目前主機：** Linux aarch64，尚未安裝 Ray。本次查核了 GitHub 公開中繼資料、留言與 PR 差異，未執行 Ray、重現回報的行為，或進行 macOS 效能量測。

### 1. 項目清單與貢獻歸屬

| 項目 | 作者／我的角色 | 查核時的狀態 | 尚待處理事項 |
| --- | --- | --- | --- |
| [PR #66909](https://github.com/ray-project/ray/pull/66909) | Jo-Leo-35 撰寫與 #66729 相關的純文件修改 | Open，尚未合併；尚無人工 approval | 處理或回覆術語意見，並追蹤人工 review |
| [PR #66878](https://github.com/ray-project/ray/pull/66878) | Yin-1022 撰寫；Jo-Leo-35 發表獨立驗證留言 | Open，尚未合併 | 重新驗證目前版本與編碼相關案例 |
| [Issue #66729](https://github.com/ray-project/ray/issues/66729) | Jo-Leo-35 提出 issue 並記錄實驗 | Open；已提出純文件 PR #66909；執行期實作清單尚未勾選 | 取樣基準量測、執行期實作與測試 |
| [PR #66340](https://github.com/ray-project/ray/pull/66340) | xuwenyihust；CUDA／NVML 相關背景資料 | Open，尚未合併 | 尚無 Jo-Leo-35 獨立驗證的證據，不計入貢獻數量 |
| [Issue #66335](https://github.com/ray-project/ray/issues/66335) | xuwenyihust；DGX Spark 相關背景資料 | Open | 僅供參考，不能據此判定 macOS 或 MPS 行為 |

以下 GitHub 公開搜尋結果為：自行提交的 PR 1 個、曾留言的 PR 1 個、正式 review 的 PR 0 個，以及自行提出的 issue 1 個。搜尋僅反映查核當時的結果，索引可能延遲，也無法證明私人或尚未公開的工作。若發現新增活動，需確認其實際時間後再納入報告。

```text
repo:ray-project/ray is:pr author:Jo-Leo-35 created:2026-09-01..2026-10-11
repo:ray-project/ray is:pr commenter:Jo-Leo-35 updated:>=2026-09-01
repo:ray-project/ray is:pr reviewed-by:Jo-Leo-35 updated:>=2026-09-01
repo:ray-project/ray is:issue author:Jo-Leo-35 created:2026-09-01..2026-10-11
```

對於留言與 review 的搜尋，應確認貢獻者實際發表留言／review 的日期，而非只看 PR 建立日期。#66878 的 review 清單中沒有 Jo-Leo-35 提交的正式 review。

### 2. 10 月 11 日繳交前的事項

- [x] 將 Ray 在 10 月初的工作合併到 `Ray/September/report.md`。
- [x] 使用規定的 9 月 1 日至 10 月 11 日報告範圍，另行記錄 9 月 28 日加入日期。
- [x] 將公開 PR 驗證日期修正為 10 月 9 日。
- [x] 區分自行提交的 PR、獨立驗證留言，以及 GitHub 正式 review。
- [x] 記錄自行提交的純文件 PR #66909，並將目前的公開紀錄快照更新至該 PR 在 10 月 10 日 Asia/Taipei 15:10 的最近一次可確認更新。
- [ ] 更新截至繳交期限的公開活動與上游狀態。只加入已完成且有證據的工作，並更新查核時間。
- [ ] 以附日期的連結或執行日誌確認 10 月 9–11 日是否有新增工作；不要因舊報告沒有記錄，就認定沒有活動。
- [x] 將 KubeRay 在 10 月初的學習紀錄合併到 September 報告，並更新 README。公開活動已於 10 月 10 日重新查核；學習進度仍依既有報告整理。
- [x] 發布首次合併的 Ray 報告：commit `75992d5` 已推送到 `main`。後續報告修改需另行更新與發布。

以下工程工作屬於後續待辦，不是如實回報已完成貢獻的先決條件。

### 3. PR #66878：目前版本與既有證據

| 欄位 | 查核時的內容 |
| --- | --- |
| Head SHA | `3f4b50164d014e0082d936246b4fae8d1811c6e1` |
| GitHub 回傳的 Base SHA | `13097981b2537776062251793f8c5e7c9d930661` |
| 分支 | `Yin-1022:fix-json-read-error-context` |
| 我的留言 | [2026-10-09 07:52:19 UTC](https://github.com/ray-project/ray/pull/66878#issuecomment-6076811929) |
| 查核時最新的 review 留言 | [2026-10-09 21:21:30 UTC／台北時間 10 月 10 日 05:21](https://github.com/ray-project/ray/pull/66878#discussion_r4234649165) |

我的留言記錄了 Ray 2.59.0、pandas 2.3.3，以及以下特定範圍的驗證結果：

| 檢查項目 | 加入原始錯誤文字前 | 加入原始錯誤文字後 |
| --- | --- | --- |
| 包含檔案上下文 | True | True |
| 包含輸入路徑 | True | True |
| 包含 parser 詳細訊息 | False | True |

該留言與這份報告 repository 沒有記錄受測 commit、完整環境、腳本與原始輸出。不要根據留言時間推測受測 commit；後續 PR commit 已修改實作與測試。

#### 需要重新執行驗證的問題

1. **Pandas JSONL 的無效 UTF-8：** [hikaru-212 的留言](https://github.com/ray-project/ray/pull/66878#discussion_r4234649165)詢問：將 `UnicodeDecodeError` 轉為一般 `ValueError` 是否為預期行為。查核版本的 [Pandas 例外處理程式](https://github.com/ray-project/ray/blob/3f4b50164d014e0082d936246b4fae8d1811c6e1/python/ray/data/_internal/datasource/json_datasource.py#L230)會捕捉 `ValueError`，也包含其子型別 `UnicodeDecodeError`。這是靜態程式碼觀察與另一位 reviewer 的重現結果，不是本次新執行的驗證結果。
2. **Arrow fallback 的無效 UTF-8：** 機器人曾[提出 worker／driver 之間例外型別的疑慮](https://github.com/ray-project/ray/pull/66878#discussion_r4229421649)。作者則[回報 driver 端檢查成功](https://github.com/ray-project/ray/pull/66878#discussion_r4229548791)，並加入斷言。應先在目標環境獨立測試，再判斷任一說法是否能普遍成立。此問題與 Pandas JSONL 例外處理的問題不同。
3. **目前 head 的 parser 診斷資訊：** 在實際的 worker-to-driver 傳遞過程中重跑原本的檢查，並涵蓋 chunk size 估算與後續讀取兩個階段。

#### 建議執行順序

- [ ] 取得最新 PR 中繼資料、留言與 head SHA。若 head 已變更，先檢查差異再測試。
- [ ] 準備獨立的 Ray checkout，依專案 contributor／環境說明設定。記錄 checkout 的 SHA，以及實際匯入的 Ray 路徑、版本與 commit，避免測試不小心使用無關的已安裝 wheel。
- [ ] 確認並記錄比較用版本；若要隔離 PR 變更做前後比較，優先使用 PR 的 merge base。保持相依套件與輸入一致。
- [ ] 在 PR checkout 上執行下列現有回歸測試，再將相關行為與基準版本比較。
- [ ] 增加暫時性的重現案例：JSONL 第一筆資料含無效 UTF-8（估算階段），以及有效資料之後含無效 UTF-8（讀取階段）。記錄實際執行到的程式路徑，不要假設檔案排列一定會觸發特定路徑。
- [ ] 檢查 driver 端可見的例外型別、底層原因、輸入路徑、parser／編碼細節與 traceback，並保留原始輸出。
- [ ] 整理在指定 SHA 上可重現的結果，以及仍依環境而異的部分。若準備公開回覆，先整理成可審閱的草稿；這份交接清單沒有要求直接發布留言。

查核版本已有的測試如下，需在準備完成的 Ray checkout 中執行：

```bash
python -m pytest -q python/ray/data/tests/datasource/test_json.py -k 'test_json_read_invalid_format_includes_path or test_json_read_invalid_utf8_includes_path or test_jsonl_read_invalid_format_includes_path'
```

來源：[查核版本的 test_json.py](https://github.com/ray-project/ray/blob/3f4b50164d014e0082d936246b4fae8d1811c6e1/python/ray/data/tests/datasource/test_json.py#L133)。這是建議執行的指令，不是已執行的結果。JSONL 回歸測試涵蓋語法錯誤，單靠它仍無法確認 JSONL 無效 UTF-8 問題已解決。

#### 已觀察的上游檢查，並非本機執行結果

- 查核 SHA 的 GitHub check runs 中，DCO、disable-automerge 與 Cursor Bugbot 均回報成功。
- [Buildkite microcheck #55932](https://buildkite.com/ray-project/microcheck/builds/55932)回報成功。
- [Buildkite release #110276](https://buildkite.com/ray-project/release/builds/110276)狀態為成功，但描述為「passed and blocked」；文件建置狀態則顯示已跳過。
- 這些狀態不能證明已完成獨立本機驗證，也不代表完整的平台測試覆蓋。PR 作者提供的 Windows 基準比較是作者的結果，不是 Jo-Leo-35 的驗證成果。

### 4. PR #66909：自行提交的文件修改

| 欄位 | 查核時的內容 |
| --- | --- |
| 狀態 | Open，尚未合併；公開紀錄快照時尚無人工 approval |
| 提出時間 | [2026-10-10 03:30:37 UTC／台北時間 10 月 10 日 11:30](https://github.com/ray-project/ray/pull/66909) |
| 最近一次可確認的公開更新 | 2026-10-10 07:10 UTC／台北時間 10 月 10 日 15:10 |
| Head SHA | `85eea7c399052b672d84649da2b27ad11cc45ea2` |
| 分支 | `Jo-Leo-35:docs/task-uss-platform-limits` |
| 差異範圍 | 兩份文件，新增 12 行、刪除 2 行 |

#### 修改範圍與要解決的問題

- 將文件中已過時的 `average_max_uss_per_task` 改為 `max_uss_bytes`，並標示匯出的平均值與最大值 gauge。
- 說明 Ray Data 只在 Linux 估算 task USS；`max_uss_bytes.num_samples == 0` 代表量測不可用，而不是記憶體用量實測為零。
- 釐清依日誌設定 `memory` 需要有效的 USS 樣本，而且沒有出現週期性 high-memory 警告，不代表目前設定的記憶體一定足夠。
- 此變更僅修改文件，不會改變執行期行為，也沒有加入 macOS sampler，因此 #66729 的較廣泛實作需求仍為 open。

#### 驗證與 review 狀態

- PR 作者回報在 macOS arm64 上通過 `git diff --check`、四個適用的 pre-commit hook 與 Vale。Vale 保留五項既有 suggestion，沒有新增 error、warning 或 suggestion。
- 作者也回報使用兩處修改段落的精確摘錄完成獨立 Sphinx smoke build。該 checkout 為 sparse，暫時的 smoke-build 輸入並未包含在 PR 中。這不是完整的 Ray 文件建置，也未驗證全站 cross-reference、autodoc 或官方 theme。
- 此次查核僅觀察到、並未在本機執行的上游狀態包括：DCO 與 Cursor Bugbot 成功、[Buildkite microcheck #56008](https://buildkite.com/ray-project/microcheck/builds/56008) 成功、[Buildkite release #110389](https://buildkite.com/ray-project/release/builds/110389) 顯示成功但描述為「passed and blocked」，以及 [Read the Docs PR build](https://anyscale-ray--66909.com.readthedocs.build/en/66909/) 成功。
- [Gemini Code Assist](https://github.com/ray-project/ray/pull/66909#pullrequestreview-5477416510)留下三項術語大小寫建議。這類自動化意見不等於人工 approval；上游 CI 成功也不能證明已完成獨立本機驗證或變更已被接受。

### 5. Issue #66729：macOS CPU USS 取樣

#### 已公開的歷史證據

- [Issue 內容](https://github.com/ray-project/ray/issues/66729)：macOS 執行紀錄擷取於 10 月 5 日，使用 Ray 2.59.0，commit 為 `dd83c3a703a5422d0b47f142cb380c6330817608`。
- 64 MiB 與 1 GiB 的 CPU 配置實驗均完成，但 task USS 樣本數為零，平台能力檢查回傳 false。
- 已公開[附加證據壓縮檔](https://github.com/user-attachments/files/33063395/ray-macos-evidence-redacted.zip)連結；本次整理未下載或執行其中內容。
- 原始 issue 驗證未涵蓋 Linux、master／nightly、Dashboard／Prometheus 行為，或取樣額外負擔的基準量測。

#### 下一步與所需證據

- [ ] 設計修正前，先閱讀 issue 最新實作清單與相關上游程式碼。
- [ ] 檢查附件中的腳本與日誌，還原原始環境及確切指令。
- [ ] 在 macOS 主機上，先以小型配置重跑純 CPU 基準，記錄樣本數、公開統計、版本與配置保留時間。若支援，再於獨立環境測試目前的 master／nightly。
- [ ] 執行條件可比較的 Linux 對照，保留確切版本與相依套件資訊。僅憑 Linux 結果無法驗證 macOS 行為。
- [ ] 以多種配置大小，重複量測基準、配置與釋放階段，評估候選 macOS USS 取樣方式。記錄取樣延遲、變異程度，以及啟用與未啟用 profiling 時的 workload 執行時間。USS 準確度需透過量測評估，不應要求數值必須精確等於 payload 的位元組數。
- [ ] 加入支援前，先定義可接受的額外負擔與量測意義。測試樣本不可用、存取被拒與程序結束等情況，保留「不可用」與「零值」的區別。
- [ ] 若可行，以另一個執行期 PR 實作純 CPU 取樣，加入針對性的單元測試、macOS 配置測試，以及必要的平台／量測說明文件。不要將純文件 PR #66909 當成執行期實作證據；只有實際提出實作 PR 後才記錄其連結。
- [ ] 另行測試 high-memory 偵測，確保執行時間足夠、樣本有效，並使用實際門檻與檢查間隔。短時間執行沒有警告，不能作為偵測正常或失效的證據。

第一個 CPU 取樣實作不包含 **Apple MPS 記憶體統計**、**CUDA／NVML 記憶體統計**，以及另行處理的**不可用與零值執行期呈現調整**。不要將 `phys_footprint` 改稱 USS，也不要合併意義不同的記憶體計數器。

目前的 Linux 主機可進行程式碼檢查，並在完成設定後執行 Linux 對照。macOS 取樣與 MPS 結論需要合適的 Apple 硬體；若缺少該環境，應保留為待辦。

### 6. 每次驗證後應保存的資料

每次完成執行後，記錄日期與時間、作業系統／架構、Python 與相依套件版本、Ray SHA 與匯入路徑、確切指令、測試輸入、預期與實際行為、結束碼，以及原始日誌位置。分開記錄基準與 PR 的結果，也要區分本機發現、上游 CI，以及其他 reviewer 的回報。

取得上述證據後，才能更新繳交報告中的已完成工作。重新檢查同一個 PR 可以補充成果描述，但不會增加已驗證的不同 PR 數量。
