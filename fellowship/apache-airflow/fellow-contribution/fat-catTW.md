# Apache Airflow Contribution Report by fat-catTW(VincentHsiao) (2026-03 ~ 2026-10/14)

- GitHub：[@fat-catTW](https://github.com/fat-catTW)
- 專案：[apache/airflow](https://github.com/apache/airflow)
- Slack：https://opensource4you.slack.com/team/U08T4PK37EC
- 日期：2026 年 3 月 ~ 2026 年 10 月 14日


## 總覽
- PR: 24 Merged([清單](https://github.com/apache/airflow/pulls?q=is%3Apr+author%3Afat-catTW+is%3Amerged)) + 5 Open
  - 其中 15 個 PR 曾被加上回補目標標籤（`v3-3-test` 12、`v3-2-test` 2、`v3-1-test` 2、`airflow-ctl/v0-1-test` 1）
- Code Review([清單](https://github.com/apache/airflow/pulls?q=is%3Apr+involves%3Afat-catTW+-author%3Afat-catTW)): 53
- Dev discussion: 2

| PR 分類 | 數量 |
| --- | ---: |
| Providers（AWS、Google、Microsoft、Neo4j、Teradata、Yandex、Qdrant） | 14 |
| Airflow Core（DAG Bundle、Operators、Executor、Params、Deadline、權限） | 6 |
| API server / Execution API | 5 |
| UI（DAG Runs、Task Group、i18n） | 4 |

### 貢獻主軸

Provider Template Field／Jinja Rendering、Execution／API 的資料與權限安全，以及 UI 可用性；並完成 53 次 Code Review，協助強化 API、Scheduler 與 Provider 功能的正確性與測試。



## Month: October 2026 (截止到 10/14)

Summary: 
- PR: 0 
- Code Review: 27 
- Dev List: 2


### Code Review
- [Fix 400 and 500s on POST /connections/defaults in multi-team mode- #74615](https://github.com/apache/airflow/pull/74615):<br>
  - 審查 Multi-Team 模式下 Connection API 的錯誤處理與權限驗證修正，並提交 Approved Review。


- [Enforce the Dag import timeout from the task supervisor side- #74571](https://github.com/apache/airflow/pull/74571):<br>
  - 審查 Task Supervisor 的 DAG Import Timeout 強制執行機制。


- [Add missing Taiwanese Mandarin translations- #74545](https://github.com/apache/airflow/pull/74545):<br>
  - 審查 Airflow UI 的繁體中文翻譯補全與修正。


- [Fix KubernetesJobOperator RBAC and Kueue preemption issues- #74497](https://github.com/apache/airflow/pull/74497):<br>
  - 審查 KubernetesJobOperator 的 RBAC 與 Kueue Preemption 修正，指出 Job 識別資訊及 XCom 處理流程的潛在問題，並提出修改建議。


- [Add missing Taiwanese Mandarin translations for task states and Dag list settings- #74486](https://github.com/apache/airflow/pull/74486):<br>
  - 審查 Airflow UI 的繁體中文翻譯補全與修正。


- [Allow slashed connection IDs in GET and DELETE connection routes- #74475](https://github.com/apache/airflow/pull/74475):<br>
  - 審查 Connection API 支援含斜線 ID 的修正，確認 GET、DELETE 路由與測試


- [Fix dag.test() failing for task ids with non-ASCII characters- #74472](https://github.com/apache/airflow/pull/74472):<br>
  - 審查 dag.test() 處理非 ASCII Task ID 的修正，建議補充測試，並指出測試程式的 Ruff F821 錯誤。


- [Validate connection ports in Task SDK- #74463](https://github.com/apache/airflow/pull/74463):<br>
  - 審查 Task SDK 的 Connection Port 驗證功能與邊界測試

- [Validate connection ports in API schema- #74462](https://github.com/apache/airflow/pull/74462):<br>
  - 審查 Connection Port 的 API 驗證功能，指出 OpenAPI Schema 未同步更新的問題，並提出修正建議。

- [Validate connection ports in core model- #74461](https://github.com/apache/airflow/pull/74461):<br>
  - 審查 Connection Port 驗證邏輯，發現 CLI 相容性問題及 port=0 的邊界情況，並提出修正建議。

- [Stop one failed executor callback from crashing the scheduler- #74446](https://github.com/apache/airflow/pull/74446):<br>
  - 審查 Scheduler 的 Callback 錯誤處理，指出資料庫與 Executor 記憶體狀態不一致的問題，並建議補充測試。

- [Add an e2e test for the Java SDK task state store- #74403](https://github.com/apache/airflow/pull/74403):<br>
  - 審查 Java SDK Task State Store 的 E2E 測試

- [Fail Dag runs stranded by a deleted task instance- #74213](https://github.com/apache/airflow/pull/74213):<br>
  - 審查 DAG Run Deadlock Detection 修正，指出 Task Concurrency 測試覆蓋不足的問題。

- [API: Translate domain ValueError to HTTPException in Asset State Store routes- #74179](https://github.com/apache/airflow/pull/74179):<br>
  - 審查 Asset State Store API 的錯誤處理修正，確認 HTTP 500 轉換為 HTTP 400 的處理方式。

- [Honor the polling interval for deferred Kubernetes jobs- #74143](https://github.com/apache/airflow/pull/74143):<br>
  - 審查 KubernetesJobOperator 在 Deferred 模式下的 Polling Interval 修正與測試。

- [Improve LocalExecutor bookkeeping: correctly add tasks to the running list- #74142](https://github.com/apache/airflow/pull/74142):<br>
  - 審查 LocalExecutor 的 Task 狀態追蹤與 Worker 管理修正。

- [Allow ECS tasks to continue when an Airflow task is killed- #74129](https://github.com/apache/airflow/pull/74129):<br>
  - 審查 ECS Task 終止行為，指出 Log Fetcher 清理流程的邊界問題並提出修正建議。

- [Add name filtering to the Deadline Alerts API- #74127](https://github.com/apache/airflow/pull/74127):<br>
  - 審查 Deadline Alerts API 的名稱篩選功能，指出測試依賴不穩定排序的問題並提出修正建議。

- [Make the in-process supervisor safe to call from several threads- #74074](https://github.com/apache/airflow/pull/74074):<br>
  - 審查 In-Process Supervisor 的多執行緒安全性修正，指出文件描述不一致的問題並提出建議。

- [Fix breeze down ignoring the --cleanup-build-cache flag- #73964](https://github.com/apache/airflow/pull/73964)<br>
  - 審查 Breeze Build Cache 清理機制，指出 CLI 說明與實際行為不一致的問題。

- [Fix Postgres data volume path in Breeze- #73862](https://github.com/apache/airflow/pull/73862):<br>
  - 審查 Breeze 的 PostgreSQL Volume 路徑修正，確認資料持久化機制。

- [Mask provider-declared sensitive connection extras by connection type- #73794](https://github.com/apache/airflow/pull/73794):<br>
  - 審查 Connection API 依 Provider 設定遮蔽敏感資訊的機制。

- [Mask XCom values in the audit log- #73791](https://github.com/apache/airflow/pull/73791):<br>
  -審查 XCom Audit Log 的敏感資訊遮蔽機制，指出審計紀錄遺失 XCom 識別資訊的問題並提出修正建議。

- [Stop SQLAlchemy subquery warning when loading the latest serialized Dag- #73461](https://github.com/apache/airflow/pull/73461):<br>
  - 審查 SQLAlchemy 查詢最新 Serialized DAG 時的警告修正。

- [Return structured XCom not-found errors- #73410](https://github.com/apache/airflow/pull/73410):<br>
  - 審查 SQLAlchemy 查詢最新 Serialized DAG 時的警告修正。

- [Migrate GET /execution/variables/keys to an async DB session- #73407](https://github.com/apache/airflow/pull/73407):<br>
  - 審查 Execution API 的非同步資料庫 Session 遷移。

- [Avoid a full asset table scan per request in simple auth manager- #72934](https://github.com/apache/airflow/pull/72934):<br>
  - 審查 Simple Auth Manager 的 Asset 權限驗證效能優化，避免不必要的資料表掃描。


### Dev List

- [[DISCUSS] Next step after the PR limit: "your PR, your CI" - running CI for external contributors' PRs in their own forks](https://lists.apache.org/thread/ky392mrrcmj312y1r276r30p584n804c):<br>
  - 參與 Airflow CI 改革討論，認同 Andrew 的觀點，建議在 PR 合併前保留 ASF CI 驗證，以兼顧 CI 資源效率與程式碼可靠性。
- [[DISCUSS] Merging "almost" ready PRs with maintainer-fixups from contributors](https://lists.apache.org/thread.html/zpv8f6gsokomrw2f516cxhwkvqkx1sxh):<br>
  - 參與 Airflow PR Review 流程改善討論，支持 Maintainer 直接修正 Minor Issues，並指出此方式能減少 Review 往返、提升效率，避免 PR 因 Contributor 未及時回應而延遲合併。
---


## Month: September 2026

Summary: 
- PR: 3 Merged + 2 Open 
- Code Review: 20 

### PR
- [Add missing Amazon provider unit tests- #73169](https://github.com/apache/airflow/pull/73169): Providers（AWS）｜`測試` (OPEN)<br>
  - AWS Executor：驗證 BaseConfigKeys 的迭代行為。
  - AWS Batch Executor：驗證不支援的 submit_job_kwargs 參數是否正確觸發錯誤。

- [Allow API clients to refresh Dag bundles- #72715](https://github.com/apache/airflow/pull/72715): API server / Execution API｜`新功能` `backport: airflow-ctl/v0-1-test` `backport: v3-3-test` (OPEN)<br>
  - 問題／影響：使用者原本只能等待排程解析，無法在部署後主動加速 DAG 更新。
  - 解法：新增 REST API 觸發 DAG Bundle 刷新，透過既有的 Priority Parsing 機制支援多個 DAG Processors 的最終一致性。

- [Add missing Amazon utility tests- #72985](https://github.com/apache/airflow/pull/72985): Providers（AWS）｜`測試`<br>
  - 補上 Amazon provider 裡 RDS / SageMaker utility enum 的測試，並把已覆蓋的檔案從 OVERLOOKED_TESTS 清單移除。

- [Improve deadline diagnostics for null DagRun fields- #71767](https://github.com/apache/airflow/pull/71767): Airflow Core（Deadline）｜`Bug 修正` `backport: v3-3-test`<br>
改善 Airflow deadline alert 在處理 DagRun-based reference 時的診斷訊息。
  - 當 deadline reference 需要讀取 `logical_date` 或 `queued_at`，但對應的 DagRun 欄位是 `None` 時，系統會跳過建立 deadline alert。這個 PR 讓 warning log 明確指出 dag_id、run_id、deadline_alert_id、reference type，以及是哪個 DagRun 欄位缺值，方便後續除錯。


- [Fix XCom slice endpoint for crossed bounds- #72584](https://github.com/apache/airflow/pull/72584): API server / Execution API｜`Bug 修正`<br>
  - 問題／影響：mapped XCom slice 的起訖邊界交錯時，可能產生負數 SQL LIMIT，造成不正確的資料庫查詢。
  - 解法：此情境改為回傳空結果，使 API 行為符合 Python slicing。



### Code Review

- [Catch format-string arguments passed to exception constructors- #73827](https://github.com/apache/airflow/pull/73827):<br>
  - 審查 Exception 訊息格式修正與 Pre-commit 檢查機制。

- [Go SDK: stop log attributes from displacing standard log fields- #73820](https://github.com/apache/airflow/pull/73820):<br>
  - 審查 Go SDK 的日誌欄位覆蓋問題修正，確認標準欄位的保護機制與測試。

- [Fix dateless dag.test and tasks test destroying unrelated Dag runs- #73813](https://github.com/apache/airflow/pull/73813):<br>
  - 審查 dag.test() 與 tasks test 誤刪其他 DAG Runs 的修正與回歸測試。

- [Fail the local yml mounts prek hook when synchronization fails- #73785](https://github.com/apache/airflow/pull/73785):<br>
  - 審查 Pre-commit Hook 同步失敗時的錯誤處理與測試。

- [Add Cloud SQL Auth Proxy v2 support to CloudSqlProxyRunner- #73646](https://github.com/apache/airflow/pull/73646):<br>
  - 審查 Cloud SQL Auth Proxy v2 的支援功能，包含版本相容性、連線設定與測試。

- [Add task state store API to the Java SDK- #73464](https://github.com/apache/airflow/pull/73464):<br>
  - 審查 Java SDK Task State Store 功能，指出文件中資料持久化範圍的描述不準確，提出修正建議。

- [Hide assets the caller may not read from the UI dependencies graph- #73008](https://github.com/apache/airflow/pull/73008):<br>
  - 審查 Dependencies Graph 的 Asset 權限過濾機制，指出無效 Asset ID 可能導致 HTTP 500 的問題。

- [Scope upstream asset events of a Dag run to readable assets and Dags- #72924](https://github.com/apache/airflow/pull/72924):<br>
  - 審查 DAG Run 上游 Asset Events 的權限過濾機制，確認資料存取控制修正。

- [Avoid refreshing the Dag processor latest-log symlink on every parse- #72869](https://github.com/apache/airflow/pull/72869):<br>
  - 審查 DAG Processor 日誌 Symlink 的效能修正，建議避免重複取得日期。

- [Fix airflow db check reporting one fewer retry than it will make- #72708](https://github.com/apache/airflow/pull/72708):<br>
  - 審查 airflow db check 的重試次數顯示修正與回歸測試。

- [Fix the translation Gap for Taiwanese Mandarin- #72615](https://github.com/apache/airflow/pull/72615):<br>
  - 審查 Airflow UI 的繁體中文翻譯補全與修正。

- [Mask password-format Param values in conf, logs, and rendered templates- #72499](https://github.com/apache/airflow/pull/72499):<br>
  - 審查敏感參數遮蔽機制，發現日誌與 API 的密碼洩漏風險、DAG 版本快取問題，並提出多項修正與測試建議。

- [Add tests for common compat standard utilities- #72487](https://github.com/apache/airflow/pull/72487):<br>
  - 審查 Common Compat 的測試覆蓋率，指出測試無法偵測 API 匯出遺漏的問題，並提出改善建議。

- [Stop airflow standalone leaking components when one fails to start- #72464](https://github.com/apache/airflow/pull/72464):<br>
  - 審查 Airflow Standalone 啟動失敗時的元件清理機制與回歸測試，提出小幅改善。

- [Add Has Events filter to Assets search- #72460](https://github.com/apache/airflow/pull/72460):<br>
  - 審查 Assets 的 Has Events 篩選功能，建議補充 Newsfragment 以記錄新功能。

- [Fix timedelta serde to accept int payloads- #72326](https://github.com/apache/airflow/pull/72326):<br>
  - 審查 Timedelta 反序列化導致 Scheduler 崩潰的修正，並建議補充 Bugfix Newsfragment。

- [Fix test_get_task_states_with_task_group_id_and_task_id to actually cover its name- #72294](https://github.com/apache/airflow/pull/72294):<br>
  - 審查 Task States API 的測試修正，確認 Task Group 與 Task ID 聯合篩選的測試覆蓋。

- [Fix asset dependency graph dropping outlets for tasks that both consume and produce- #72272](https://github.com/apache/airflow/pull/72272):<br>
  - 審查 Asset Dependency Graph 遺漏下游 Assets 的修正與回歸測試。

- [Pass the Dag bundle's team to secrets backends during Dag parsing- #72139](https://github.com/apache/airflow/pull/72139):<br>
  - 審查 DAG Parsing 的 Team 權限傳遞機制，指出安全性與 Variable API 測試覆蓋不足的問題。

- [contain gemini batch-job results file within results_folder- #71832](https://github.com/apache/airflow/pull/71832):<br>
  - 審查 Gemini Batch Job 的路徑安全性修正，指出 Symlink 可能造成路徑穿越的漏洞，建議加強驗證與測試。
---



## Month: August 2026

Summary: 
- PR: 5 Merged + 3 Open
- Code Review: 6

### PR
- [Fix Dag run filtering for null start dates- #71654](https://github.com/apache/airflow/pull/71654): API server / Execution API｜`Bug 修正` (OPEN)<br>
  - 修正 Airflow DAG Run API 的日期篩選邏輯，避免已結束但 start_date=NULL 的 DAG Runs 被錯誤包含在查詢結果中，提升 API 篩選的正確性。

- [Limit Glue retry task UUID scan- #71546](https://github.com/apache/airflow/pull/71546): Providers（AWS）｜`效能` (OPEN)<br>
  - 優化 AWS GlueJobOperator 的重試機制，將歷史 Job Runs 搜尋限制在最多 500 筆，避免無限制查詢造成效能問題。同時改善 AWS 錯誤處理與日誌紀錄，提升系統可靠性。

- [Store executor callback DagRun references in the database- #71214](https://github.com/apache/airflow/pull/71214): Airflow Core（Executor）｜`重構` (OPEN)<br>
  - 問題／影響：Callback 與 DagRun 的關聯原本依賴 Payload，資料關聯較脆弱且不易維護。
  - 解法：新增 `dagrun_id` 外鍵，將關聯直接儲存在資料庫，提升資料一致性與可維護性。

- [Document config-backed enum Dag Params- #71180](https://github.com/apache/airflow/pull/71180): Airflow Core（Params）｜`文件`<br>
  - 補充 Airflow Params 文件與範例，說明 DAG author 可以在 DAG parse time 從外部 INI config 讀取選項，並把結果放進 Param 的 JSON Schema `enum`。
  - 這讓 Trigger DAG form 可以直接使用 serialized DAG Params 顯示 dropdown，不需要在開啟表單時呼叫 dynamic choice provider。

- [Fix Keycloak multi-team permission repair- #71614](https://github.com/apache/airflow/pull/71614): Airflow Core（權限）｜`安全性`<br>
  - 問題／影響：舊版 global permissions 過寬，一般 team 使用者可能透過 REST API 讀取其他 team 的 DAGs、Variables 或 Connections。
  - 解法：修正 Keycloak multi-team 權限，並讓 `create-team` 可修復既有安裝的 permissions。

- [Support certificate auth for Microsoft Graph filesystem- #71362](https://github.com/apache/airflow/pull/71362): Providers（Microsoft）｜`新功能`<br>
  - 為 Microsoft Graph Filesystem 新增憑證認證（Certificate-based Authentication），解決原本僅支援 Client Secret 的限制。
  - 支援透過 certificate_path 或 certificate_data 取得 Access Token，讓無法使用 Client Secret 的環境也能透過 ObjectStoragePath 存取 Microsoft Graph。
  - 保留既有認證方式，並補充單元測試與文件。

- [Register Stackdriver remote task logging handler- #70549](https://github.com/apache/airflow/pull/70549): Providers（Google）｜`新功能`<br>
  - 將 Google Cloud Stackdriver Remote Logging 整合至 Airflow 新的 Provider-based Remote Logging 機制，減少對舊有 Logging Configuration 的依賴。
  - 新增 StackdriverRemoteLogIO.from_config()，支援從 Airflow 設定自動建立 Logging Handler，並透過 stackdriver:// scheme 進行註冊與查找。
  - 補充單元測試，確保設定解析、Handler 註冊與錯誤處理正常運作。

- [Delay BigQueryToMsSqlOperator source table parsing- #70493](https://github.com/apache/airflow/pull/70493): Providers（Google）｜`Bug 修正` `backport: v3-3-test`<br>
  - 修正 BigQueryToMsSqlOperator 在初始化階段過早解析來源資料表名稱，導致 Jinja Template 尚未渲染就觸發解析錯誤的問題。
  - 將解析邏輯延後至 execute()，確保使用者能透過 Jinja Template 動態指定 BigQuery 來源資料表。
  - 移除 validate_operators_init 例外設定，並新增 Regression Tests 驗證修正。
### Code Review

- [Reject non-string prompts in LLMFileAnalysisOperator before reading files- #71734](https://github.com/apache/airflow/pull/71734):<br>
  - 審查 LLMFileAnalysisOperator 的 Prompt 型別驗證修正，確認錯誤處理與檔案讀取流程。

- [CLI: Add --limit flag to airflow dags list-runs- #71729](https://github.com/apache/airflow/pull/71729):<br>
  - 審查 DAG Runs CLI 的 --limit 功能，建議補充 Newsfragment 並修正文件用詞。

- [Fix InfluxDB hook methods failing when called before get_conn- #71705](https://github.com/apache/airflow/pull/71705):<br>
  - 審查 InfluxDB Hook 的連線初始化與錯誤修正。

- [Keep MSGraph request configuration across paginated pages- #71649](https://github.com/apache/airflow/pull/71649):<br>
  - 審查 MSGraph API 分頁請求的設定傳遞修正，建議補充端到端回歸測試。

- [Add SSL cipher list option to the api server- #71645](https://github.com/apache/airflow/pull/71645):<br>
  - 審查 API Server 的 SSL Cipher 設定功能，包含 TLS 加密套件驗證與伺服器相容性。

- [Add airflowctl tasks states-for-dag-run command (#69366)- #70276](https://github.com/apache/airflow/pull/70276):<br>
  - 審查 airflowctl 新增的 tasks states-for-dag-run 指令。
---



## Month: July 2026

Summary: 
- PR: 10 Merged

### PR:
- [Fix BashOperator script detection after templating- #70369](https://github.com/apache/airflow/pull/70369): Airflow Core（Operators）｜`Bug 修正` `backport: v3-3-test`<br>
  -  修正 BashOperator 在 __init__() 階段過早判斷 Template Field 的問題，避免 Jinja Template 尚未渲染就選擇錯誤的指令執行方式。
  - 將判斷邏輯移至 execute()，確保正確識別 Inline Command 與 Shell Script，並移除 CI 檢查的例外項目、補充 Regression Test。


- [Drop redundant BigQueryInsertJobOperator job ID snapshot in __init__- #70494](https://github.com/apache/airflow/pull/70494): Providers（Google）｜`重構` `backport: v3-3-test`<br>
  -  移除 BigQueryInsertJobOperator 在 __init__() 階段對 job_id 進行的冗餘儲存，避免不必要地讀取尚未渲染的 Template Field。
  -  保留 execute() 階段原有的 Job ID 處理邏輯，確保既有行為不變，並移除 CI 檢查例外項目、補充單元測試。


- [Move AwsToAwsBaseOperator fallback out of init- #70445](https://github.com/apache/airflow/pull/70445): Providers（AWS）｜`重構` `backport: v3-3-test`<br>
  - 重構 AwsToAwsBaseOperator 的 Destination Connection Fallback Logic，避免在 __init__() 階段對 Template Field 進行額外處理。
  - 改用 @property 動態處理預設連線，保留未指定目的地連線時使用來源連線的行為，並正確支援明確傳入 None。
  - 移除 validate_operators_init 例外項目，並新增單元測試驗證連線設定行為。


- [Allow templated fields in AppFlow operators- #70440](https://github.com/apache/airflow/pull/70440): Providers（AWS）｜`Bug 修正` `backport: v3-3-test`<br>
  -  修正 AWS AppFlow Operators 在 __init__() 階段過早驗證 source、filter_date 等 Template Fields，導致 Jinja Template 無法正常使用的問題。
  -  將參數驗證邏輯移至 execute()，確保在 Template Rendering 完成後才進行驗證，並統一多個 AppFlow Operators 的驗證方式。
  -  移除 validate_operators_init 例外項目，新增測試驗證動態參數能正常執行。


- [Fix EcsRunTaskOperator deferred-logs region and template field init- #70464](https://github.com/apache/airflow/pull/70464): Providers（AWS）｜`Bug 修正` `backport: v3-3-test`<br>
  -  將 EcsRunTaskOperator 的 Template Field 處理邏輯移出 __init__()，避免在 Jinja Template 渲染前進行參數判斷。
  -  修正 Deferrable Mode 下，Task 執行完成後使用錯誤 AWS Region 查詢 CloudWatch Logs 的問題，新增 resolve_awslogs_region() 統一處理 Log Region 的選擇。
  -  移除 validate_operators_init 例外項目，並補充單元測試驗證日誌讀取與參數處理。

- [Fix Teradata transfer template field initialization- #70392](https://github.com/apache/airflow/pull/70392): Providers（Teradata）｜`Bug 修正` `backport: v3-3-test`<br>
  -  將 TeradataToTeradataOperator 的 sql_params 預設值處理延後至 execute()，避免在 Jinja Template 渲染前修改參數。
  -  移除 validate_operators_init 例外項目，並新增單元測試驗證預設參數行為。


- [Read fresh Dag bundle state during refresh- #69830](https://github.com/apache/airflow/pull/69830): Airflow Core（DAG Bundle）｜`Bug 修正` `backport: v3-3-test`<br>
  - 問題／影響：多個 DAG Processors 運作時，SQLAlchemy Identity Map 可能讀到過期 DAG Bundle 狀態，延後刷新結果的可見性。
  - 解法：直接查詢 `last_refreshed` 與 `version` 欄位，避開 ORM 快取，確保 Processor 取得最新資料庫狀態。
  - 驗證：新增 Regression Test，覆蓋跨 Session 更新。


- [Fix Neo4jOperator templated query validation- #70348](https://github.com/apache/airflow/pull/70348): Providers（Neo4j）｜`Bug 修正` `backport: v3-3-test`<br>
  - 將 Neo4jOperator 的 Cypher Query 驗證邏輯移至 execute()，確保 Jinja Template 渲染後才進行驗證。
  - 調整已棄用的 sql 參數處理方式，保留 Template Rendering 支援，並移除 validate_operators_init 例外項目、補充單元測試。


- [Route SQLAlchemyError through global exception handler- #69267](https://github.com/apache/airflow/pull/69267): API server / Execution API｜`重構` `backport: v3-3-test`<br>
  - 將 Execution API 的 SQLAlchemyError 處理統一交由 FastAPI 全域 Exception Handler，減少重複程式碼並確保錯誤回應一致。
  - 保留 DataError 回傳 HTTP 422、一般資料庫錯誤回傳 HTTP 500 的行為，並新增 Regression Tests 驗證錯誤處理流程。
  - 印象最深的 PR，因為一度上七月好棒棒PR，但後面 maintainer 發現 Script 有誤重跑後就沒上好棒棒PR了。 Sad。

- [Remove stale TODO comments for XCom map_index handling- #69369](https://github.com/apache/airflow/pull/69369): API server / Execution API｜`重構`<br>
  -  確認 XCom 的 map_index 傳遞方式符合 Execution API 與 OpenAPI 規範，移除已不適用的 TODO 註解，改善程式碼可維護性。
---

## Month: April 2026

Summary: 
- PR: 2 Merged

### PR
- [Add dag runs filters (Consuming Asset) - #63624](https://github.com/apache/airflow/pull/63624): UI（DAG Runs）｜`新功能` `backport: v3-1-test` `backport: v3-2-test`<br>
  - 為 DAG Runs 新增 Consuming Asset 篩選功能，支援透過 Asset 名稱或 URI 進行模糊搜尋，方便使用者查找消費特定 Asset 的 DAG Runs。
  - 整合 FastAPI Backend、OpenAPI 與前端篩選介面，並新增單元測試驗證搜尋功能。


- [open task group when navigating via group name; preserve arrow toggl…- #64284](https://github.com/apache/airflow/pull/64284): UI（Task Group）｜`新功能` `backport: v3-2-test`<br>
  - 改善 Task Group 的互動方式，讓使用者能透過點擊 Group 名稱進行導航與群組切換，減少對小型展開箭頭的依賴，提升操作便利性。
---

## Month: March 2026

Summary: 
- PR: 4 Merged

### PR:
- [Add dag runs filters (bundleVersion)- #62810](https://github.com/apache/airflow/pull/62810): UI（DAG Runs）｜`新功能` `backport: v3-1-test`<br>
  - 為 DAG Runs 新增 bundle_version 篩選功能，讓使用者能依據 DAG Bundle 版本查詢執行紀錄，提升版本追蹤與除錯便利性。
  - 整合 DAG Processor 的版本資料儲存、FastAPI、OpenAPI 與前端篩選介面，並新增 API 測試。


- [Migrate yandex connection UI metadata to YAML- #63506](https://github.com/apache/airflow/pull/63506): Providers（Yandex）｜`重構`<br>
  - 將 Yandex Cloud Connection 的 UI Metadata 整合至 provider.yaml，定義專屬認證及連線欄位，並隱藏不適用的預設欄位。
  - 同步更新 Provider Info，統一設定管理方式並改善 Connection 表單的使用體驗。


- [Migrate Qdrant connection UI metadata to YAML- #62644](https://github.com/apache/airflow/pull/62644): Providers（Qdrant）｜`重構`<br>
  - 將 Qdrant Connection 的 UI Metadata 遷移至 provider.yaml，統一定義 URL、gRPC、HTTPS 等連線欄位。
  - 調整 Connection 表單顯示方式，隱藏不適用欄位並將 Password 標示為 API Key，改善設定管理與使用體驗。

- [feat(i18n): Complete zh-TW translations - Add 10 missing keys- #62652](https://github.com/apache/airflow/pull/62652): UI（i18n）｜`翻譯`<br>
  - 補齊 Airflow UI 中 DAG Runs、Asset Events 與 Version Indicator 等功能缺少的繁體中文翻譯，使當時的 zh-TW 翻譯覆蓋率達到 100%（786/786），並通過翻譯完整性檢查