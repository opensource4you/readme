# Apache Airflow Contribution Report by josix (2026-07 ~ 2026-10/20)

- GitHub：[@josix](https://github.com/josix)
- 專案：[apache/airflow](https://github.com/apache/airflow)
- Slack：https://opensource4you.slack.com/team/U06T6TP3VT5
- 日期：2026 年 7 月 ~ 2026 年 10 月 20 日

## 總覽

- PR: 2 Merged（[清單](https://github.com/apache/airflow/pulls?q=is%3Apr+author%3Ajosix+is%3Amerged+created%3A%3E%3D2026-01-01)）
- Code Review: 2 Approved（[清單](https://github.com/apache/airflow/pulls?q=is%3Apr+reviewed-by%3Ajosix+-author%3Ajosix+created%3A%3E%3D2026-01-01)）

| PR 分類 | 數量 |
| --- | ---: |
| UI（Trigger Dag form、i18n） | 1 |
| TS SDK（Task State Store、e2e、文件） | 1 |

| 類型 | 數量 |
| --- | ---: |
| 新功能 | 2 |

### 貢獻主軸

UI 使用體驗：在 Trigger Dag 表單加回 Airflow 2 的「最近用過的設定」下拉選單。多語言 SDK：讓 TypeScript SDK 也能用 Task State Store，補上和 Python、Java、Go SDK 之間的功能落差。另外審查了兩個台灣華語（zh-TW）翻譯的 PR。

## Month: October 2026（截止到 10/20）

Summary:
- PR: 2 Merged（7 月、9 月開的 PR，在 10 月合併）

### PR

- [TS SDK: add the task state store to the task client - #73618](https://github.com/apache/airflow/pull/73618)：TS SDK｜`新功能` `文件` `e2e 測試`（9/23 開，10/9 合併，+759 / −56，18 個檔案）<br>
  - 問題／影響：Python task 可以透過 `context["task_state_store"]` 讀寫每個 task instance 的狀態，TypeScript task 卻不行。所以 TypeScript task 重試時，沒辦法接回上一次嘗試已經送出的外部 job（例如 Spark job）。
  - 解法：Supervisor 和自動產生的 wire types 本來就有 state store 的四種訊息，這個 PR 把它們接到 TypeScript client 上：`getClient().taskStateStore` 提供 `get` / `set`（可設 `retentionMs` 或 `NEVER_EXPIRE`）/ `delete` / `clear`，用法和 Python、Java（#73464）、Go（#73420）的 store 一致。
  - 驗證：補上 example bundle 和 `ts_sdk` e2e 模式的測試，並在 SDK 相容性表格中把 `task-state-store` 標為已支援。

- [UI: Add Select Recent Configurations dropdown to Trigger Dag form - #70413](https://github.com/apache/airflow/pull/70413)：UI（Trigger Dag form、i18n）｜`新功能`（7/24 開，10/13 合併，+423 / −25，關閉 [#69820](https://github.com/apache/airflow/issues/69820)）<br>
  - 問題／影響：在 Airflow 3 想重用過去某次 run 的 conf，得先離開 Trigger 流程，去 run 列表找到那一次 run，再按「Trigger again with this config」。
  - 解法：在 Trigger Dag 表單加回 Airflow 2 的下拉選單，列出最近幾次 run 用過、不重複的 conf，選了就直接帶入。資料來自既有的 dagRuns list endpoint，帶入方式和選單項目相同，沒有新增任何 API。
  - Review：經過 bbovenzi、pierrejeambrun 審查，由 bbovenzi 合併。

---

## Month: August 2026

Summary:
- Code Review: 1

### Code Review

- [Unify how timetable is translated in Taiwanese Mandarin - #71168](https://github.com/apache/airflow/pull/71168)：<br>
  - 審查台灣華語翻譯中 timetable 用詞的統一，並 Approve。

---

## Month: July 2026

Summary:
- PR: 1 Open（#70413，10 月合併）
- Code Review: 1

### Code Review

- [Fill Taiwanese Mandarin translation gap - #70379](https://github.com/apache/airflow/pull/70379)：<br>
  - 審查 Airflow UI 台灣華語翻譯缺漏的補齊，並 Approve。

---

## 附錄：2024 ~ 2025 的貢獻

這段時間共 29 個 PR，其中 22 個合併（[清單](https://github.com/apache/airflow/pulls?q=is%3Apr+author%3Ajosix+is%3Amerged+created%3A%3C2026-01-01)）。重點：

- Provider 遷移到新的目錄結構（2025-01）：搬了 Mongo、OpenAI、Common.io、FAB、Elasticsearch、Databricks、dbt Cloud、Slack、OpenSearch、GitHub、Salesforce 共 11 個 provider（例如 [#46207](https://github.com/apache/airflow/pull/46207)、[#46144](https://github.com/apache/airflow/pull/46144)）。
- SQLAlchemy 2.0 相容：把 FAB provider 的 model 改成 `mapped_column`（[#56827](https://github.com/apache/airflow/pull/56827)）。
- AIP-38 新版 UI：在 DAG 列表加上 tags 篩選（[#43303](https://github.com/apache/airflow/pull/43303)）、providers 表格停用排序（[#46785](https://github.com/apache/airflow/pull/46785)）、DAG response 改用 `logical_date`（[#46776](https://github.com/apache/airflow/pull/46776)）。
- OpenAI provider 支援 Batch API（hook / operator / trigger）（[#41554](https://github.com/apache/airflow/pull/41554)）。
- Bug 修正：Mongo provider（[#43024](https://github.com/apache/airflow/pull/43024)）、Airbyte hook（[#38860](https://github.com/apache/airflow/pull/38860)）、Graph view（[#41335](https://github.com/apache/airflow/pull/41335)）、Breeze shell params（[#42709](https://github.com/apache/airflow/pull/42709)），以及改用 libcst 重寫 remove task decorator（[#43383](https://github.com/apache/airflow/pull/43383)）。
