## Apache Airflow 開源貢獻紀錄 by ColtenOuO（2026-04 ～ 2026-10-07）

- GitHub：[@ColtenOuO](https://github.com/ColtenOuO)
- Slack：https://opensource4you.slack.com/team/U0BN9TJSSTE
- 專案：[apache/airflow](https://github.com/apache/airflow)
- 日期：2026 年 4 月 ~ 2026 年 9 月
---

### 已合併的 PR 分類

| 分類 | 數量 |
| --- | --- |
| Core（airflow-core / task-sdk / shared） | 30 |
| Provider：common-ai | 12 |
| Provider：amazon | 2 |
| Provider：http / neo4j / cncf-kubernetes / apache-kafka / google | 各 1 |
| 跨多個 Provider | 1 |
| airflow-ctl | 1 |
| CI / 開發工具 | 2 |

| 類型 | 數量 |
| --- | --- |
| 效能優化 | 12 |
| Bug 修正 | 11 |
| OpenAPI 規格修正 | 5 |
| 新功能 | 4 |
| 測試 | 3 |
| 文件 | 8 |
| 程式品質 / Lint / CI | 10 |

### 前言 (含一些個人心得)

考量到可能會有人翻這裡的東西來參閱加上我自己算是今年 3 月第一次加入開源的人所以寫下自己的一些小心得希望有助於幫助到後面的人，雖然我自己不是強者，但相信剛加入的人一定也會遇到跟我類似的問題，希望能帶給這些人一些啟發。

<details>
<summary>個人心得 (文筆不好請見諒)</summary>

我以前是一個專注在競技程式的人，自己的 side-project 大概也就 2-3 個，對一些 framework 或技術可以說是知道一些但不能說精通。開源一直是我很想要踏入的圈子，原因其實很單純，~~因為可以跟別人說你現在使用的 xxx 專案有自己的程式碼感覺很帥~~。

大約在兩年前想加入開源這個圈子，一個算啟蒙的點應該是因為我的室友哲佑在大一的時候開始在 Airflow 上貢獻覺得很帥氣 (當然他本人也是很帥)，第二是以前當成大莊坤達老師的助教時有一次慶功宴剛好邀請嘉平老大回來對大家的專題進行評分，那也是我第一次聽到源來適你這個社群。大家在聚餐中講了好多好多技術，但我對那些東西其實懵懵懂懂的，大家常常掛在嘴上說的知名開源專案我可以說是完全不知道那些在幹嘛，更不用說提到的那些技術了。其實也是那時候更意識到自己在這個圈子是很渺小的，程式競賽帶給我的就是思考的樂趣以及演算法與資料結構的知識，在真正的實務上我其實根本什麼都不會。雖然帶著一點點的失落，但「誠實面對自己」是很重要的，所以我開始把踏入開源這個圈子放在心裡。我覺得我總有一天一定會開始嘗試離開自己的舒適圈的。

在 2025 年時自己偶爾就常常湧上一個想要正式跨入開源圈的第一步，但都節節敗退。後來我自己想了一下原因其實可以歸納出以下幾點 (我認為這也是第一次接觸開源的人一定會碰到的)

1. 知名專案通常都很大，不知道如何下手
2. 當你不知道如何下手後，這個東西就不會帶給你成就感，沒有成就感你就不會想要繼續

我必須老實承認我自己也是一個很需要成就感驅使我不斷做下去的人，在新手期沒有任何成就感真的太容易感到挫敗，覺得自己不適合踏入這樣的圈子。

從現在的我看來我覺得我很認同嘉平老大所說的「開源在前期是很容易沒有任何成果的，即使花費的時間已經一兩個月也很正常」

但今年 3 月的時候我自己翻程式碼找到第一個貢獻 Airflow 的 PR 後開啟了我大量的開源動力 (不知道大家還記不記得自己第一個 PR 被 merged 的那種熱血)。另外也因為時代的改變，現在有比較厲害的 AI 工具輔助，遇到不懂的東西往往可以很快找到答案，AI 甚至也可以幫忙找有哪些適合貢獻的地方。因此我開始藉由 AI 工具的輔助發了不少 PR，但在一個月後我開始思考一個很重要的問題。

**這些 PR 讓我學到了什麼？**

嗯，答案是，沒學到什麼。

那我不就只是一個刷 PR 數字的白痴嗎？想想自己為什麼會想要加入開源這個圈子，不就是為了想要在技術能力上有所提升嗎？那我幹嘛做一些沒有意義的 PR？

在思考到這一點後我開始轉變我「使用 AI 的方式」。對，我還是會用 AI 輔助我寫程式，但我的用法更專注在：

**找到更有意義的 PR (所以每一個問題我都會開始自己先看過然後評估，不是無腦做，做了之後也會每一行程式碼自己都非常了解，才讓 PR 發出去)**

那也是因為這個轉變我必須親自讓自己去下場讀懂每一個問題發生的原因以及實際造成的影響，我也感覺是從這個轉變開始我才「真正」慢慢的有開始了解 Airflow 的運作原理以及相關 model 彼此的依賴關係。

後來也因為做的 PR 影響面開始比較大，PR 開始常常有比較多回饋，從這些回饋中我真的學到很多以前自己寫專案學不到的東西。這些幫忙 review PR 的人對大型專案有一定的經驗，很多事情不是說想改就改或能動就好。因為一個小改動影響的是好幾萬的使用者，甚至是企業級的用戶。所以是需要非常小心的。每一個 review 的過程我都從中偷到了許多大型專案維護上的相關知識，自己真的從這些地方學到不少東西。

在最近幾個月我也開始意識到我幫別人 code review 所需要花費的時間完全遠大於自己做一個 PR。現在生 code 變得很廉價，code review 變得越來越重要。幫別人 code review 你就需要懂別人寫的每一行程式碼的用途是什麼。也是因為這樣我在 review 的過程中讓自己對專案有更多的理解，所以我也蠻推薦除了發 PR 之外可以多去 code review 的。

後來做一做哲佑就問我要不要加入源來適你，猶豫了一下後覺得自己做開源太無聊了跟大家一起討論才好玩所以就加入了 (一開始不加入其實是擔心自己太菜，在裡面發表一些很笨的發言感覺很抽象，但其實這個社群對新手非常友善及包容，甚至可以用「愛」來說)


講了很多流水帳的東西，但總結一下想要跟大家分享從我自己親身經歷得到的一些東西：

1. AI 在現在這個時代是很好用的工具，但你應該思考的是怎麼讓他發揮他應該有的價值。
2. 盲目的使用 AI 發一堆連自己都看不懂的 PR 只會顯得自己是一個很可憐還會造成別人困擾的人。
3. 做一個有意義的事 >>>>>>>>>> 做很多個沒什麼意義的事
4. 做開源一開始沒有成果是很正常的，但你只要跨過了這個難關，後面的世界是很美妙的。
5. 對一個專案有貢獻不是只有 PR，還有很多其他的事情也是對專案有貢獻的 (issue 討論、code review 等)

對了，我講這些都是真實的，你從最底下把我的 PR 往回看就可以發現我以前的 PR 的品質真的很差... 到後面才比較有點樣子，希望用這個真實的例子讓大家有所警惕 (希望有啦)。

</details>

### 預計接下來的大目標

- 在還沒有 PR 空間時盡量幫忙 code review 其他人的 PR (尤其是社群小夥伴的)。
- 想要在明年 SITCON 分享這個開源心路歷程的酸甜苦辣。
- PR 有空間時把某些重要但因為 limit 被關掉的 PR 重做一下。
- 追蹤一下 common-ai provider 的最新發展，我對這塊做了蠻多 PR 也蠻有興趣的。

### 重點成果

**效能**

- **API 端點 N+1 查詢系列**：清掉 bulk pool delete（[#66222](https://github.com/apache/airflow/pull/66222)）、bulk task instance delete（[#67304](https://github.com/apache/airflow/pull/67304)）、Variables 與 Pools 的 bulk update（[#71918](https://github.com/apache/airflow/pull/71918)）、asset 相依圖 API（[#71915](https://github.com/apache/airflow/pull/71915)）裡的 $N+1$ 查詢。每次請求的 DB round trip 從 $O(N)$ 降到 $O(1)$。
- **刪除大量資料時的記憶體**：刪除歷史龐大的 Dag 時，PostgreSQL 上快約 11 倍，Python heap 從 42 MiB 降到約 0（[#71185](https://github.com/apache/airflow/pull/71185)）。刪除 queued asset events 快約 16 倍（[#71917](https://github.com/apache/airflow/pull/71917)）。
- **common-ai DataFusion 查詢**：`SELECT *` 的 peak memory 從 1,121 MiB 降到 219 MiB，耗時從 4.20 秒降到 0.05 秒（[#73384](https://github.com/apache/airflow/pull/73384)）。
- **airflowctl 分頁列表**：處理 20,000 筆資料時，省下約 520 ms 和 94 MiB peak memory（[#71928](https://github.com/apache/airflow/pull/71928)）。

**正確性**

- `HttpOperator` 在 deferrable 模式下做分頁，只會回傳最後一頁，前面的頁面會直接被扔了（[#72388](https://github.com/apache/airflow/pull/72388)）。
    - 這個 bug 其實蠻大的，但不知道為什麼沒人發現，~~可能沒有使用者在用吧~~。
- ECS deferrable 任務到錯誤的 region 讀 log，整個 deferral 期間看不到任何 log（[#70474](https://github.com/apache/airflow/pull/70474)）。
- Task state store 的 DELETE 對不存在的 task instance 也回傳成功（[#70983](https://github.com/apache/airflow/pull/70983)）。
    - 嚴格上來說這算一開始的設計問題。
- `airflow connections test` 連線失敗時 exit code 仍是 0（[#72548](https://github.com/apache/airflow/pull/72548)）。
- common-ai `SQLToolset(allowed_tables=[])` 會讓 agent 看到所有資料表，這不是預期中的行為（[#73381](https://github.com/apache/airflow/pull/73381)）。

**API 規格**：用 5 個 PR 修正十多個端點在 OpenAPI spec 中與實際行為不一致的 HTTP 狀態碼。依 spec 自動產生的 SDK client 因此能正確處理這些回應。

**考古一些古老 issue，把它解了**：

- [#36842](https://github.com/apache/airflow/issues/36842)：資料庫 ERD 改成可以用快捷鍵搜尋的 Mermaid 圖。
    - 所以現在在 Airflow 官網上的圖已經是 Mermaid 圖了。
- [#44033](https://github.com/apache/airflow/issues/44033)：先前兩次嘗試都失敗。
    - 小 refactor。
- [#50992](https://github.com/apache/airflow/issues/50992)：AIP-86 deadline alerts 的測試覆蓋。
    - 之前有人因為沒 maintainer review 被 close，把它撿起來做。
- [#70465](https://github.com/apache/airflow/issues/70465)：ECS log 的 region 問題。
    - ~~我人工搶 issue 搶的比機器人還快~~，這個 bug 其實應該是面向企業級的用戶，一般使用者應該很難碰到。

---

## Month: October 2026（10/01 ～ 10/07）

Summary: PR: 1, Code Review: 0, Issue Triage: —, Dev List: —

### PR

- **[Add deadline alert coverage to Dag endpoint tests](https://github.com/apache/airflow/pull/72296)**（#72296）`core｜測試`
  - 改了什麼：為公開 Dag API 的 `GET /dags`、`GET /dags/{id}`、`GET /dags/{id}/details`、`PATCH /dags/{id}`、`PATCH /dags`、`DELETE /dags/{id}` 補上帶有 deadline alert 的 Dag 的測試，共 7 個測試方法。
  - 影響：關閉 AIP-86 上的 issue [#50992](https://github.com/apache/airflow/issues/50992)。先前的 PR #64815 已關閉，這次重新完成。補上 deadline alert 功能在公開 API 上缺少的回歸測試。

---

## Month: September 2026

Summary: PR: 14, Code Review: 21, Issue Triage: —, Dev List: —

### PR

#### Core

- **[Reduce per-task DB round trips in the asset dependency graph API](https://github.com/apache/airflow/pull/71915)**（#71915）`core｜效能`
  - 改了什麼：Assets 相依圖 API（`get_data_dependencies()`）原本在 BFS 迴圈中，對每個 producer / consumer task 各查一次 DB。改成每一輪 BFS 收集所有 task key，再用一次 `tuple_(dag_id, task_id).in_(...)` 批次查詢。
  - 影響：一個 asset 被大量 Dag / task 共用的情況在 multi-team 部署中很常見，打開它的相依圖原本要發出數百次循序 DB 查詢，現在每輪 BFS 最多 2 次。
  - 版本：**Airflow 3.4.0**

> [!TIP]
> 一直很想搜集到 3.4.0 的徽章，剛好這個 PR 不好 backport，所以就被扔到 3.4.0 了。

- **[Add SSL cipher list option to the api server](https://github.com/apache/airflow/pull/71645)**（#71645）`core｜新功能`
  - 改了什麼：新增 `[api] ssl_ciphers` 設定與 `airflow api-server --ssl-ciphers` 參數，uvicorn 和 gunicorn 兩種 server backend 都支援。OpenSSL 無法解析的 cipher list 會在啟動時直接報錯，不會等到 bind socket 時才丟出不太好理解的 `SSLError`。
  - 影響：資安政策要求指定 TLS cipher suite 的部署（金融、政府等合規環境）以前只能用 Python 預設值，現在可以自行設定。未設定時行為不變，向下相容。

> [!TIP]
> 做 PR 71645 的過程中，自己也有特別注意向下兼容的設計問題，很開心這個 PR 一次就被 approved : )

- **[Fix airflow connections test returning success exit code on failure](https://github.com/apache/airflow/pull/72548)**（#72548）`core｜Bug 修正`
  - 改了什麼：`airflow connections test` 連線失敗時只印出 "Connection failed!"，exit code 卻是 0。改成失敗時 `SystemExit(1)`，與同函式其他失敗路徑一致。
  - 影響：部署前的 pre-flight 檢查、健康檢查腳本靠 `$?` 判斷連線是否正常，原本會把失敗當成成功。
  - 版本：**Airflow 3.3.2**

- **[Fix airflow plugins and dags pause printing prose with --output json](https://github.com/apache/airflow/pull/73270)**（#73270）`core｜Bug 修正`
  - 改了什麼：`airflow plugins` 和 `airflow dags pause/unpause` 在查無結果時會先印一段固定文字，忽略 `--output json/yaml`。改成交給 `AirflowConsole.print_as` 輸出 `[]`。
  - 影響：把 CLI 輸出 pipe 給 `jq` 或 YAML parser 的自動化腳本在結果為空時會解析失敗。修正後與其他 list 類 CLI 指令及文件描述一致。

- **[Render the database ERD as a searchable Mermaid diagram instead of an image](https://github.com/apache/airflow/pull/72006)**（#72006）`core + provider-fab + provider-edge3｜文件`
  - 改了什麼：core、FAB、Edge3 三份資料庫 ERD 文件從 SVG 圖片改成 Mermaid `erDiagram`。
  - 影響：關閉 2024 年開的 issue [#36842](https://github.com/apache/airflow/issues/36842)。core 有 57 張資料表，原本的 SVG 很難閱讀，資料表和欄位名稱也無法搜尋。現在可以用瀏覽器搜尋 (Ctrl+F)。

- **[Fix flaky bundle version lock concurrency tests](https://github.com/apache/airflow/pull/72572)**（#72572）`core｜測試`
  - 改了什麼：兩個 `BundleVersionLock` 併發測試原本用固定的 `sleep(0.1)` ，改成確定性的同步機制，讓測試更穩定。
  - 影響：在 Breeze 中實測，高負載下每個 assertion 約有 0.5% 的機率失敗。修正後讓 CI 更穩定一點，在 GitHub action 的繁忙環境下失敗機率可能會更高。
  - 版本：**Airflow 3.3.2**

- **[Fix test_get_task_states_with_task_group_id_and_task_id to actually cover its name](https://github.com/apache/airflow/pull/72294)**（#72294）`core｜測試`
  - 改了什麼：這個測試的名稱說會同時帶 `task_group_id` 和 `task_ids`，實際卻只帶了前者，所以其實根本沒測試到。補上 `task_ids`。
  - 影響：Execution API `GET /task-instances/states` 的「task group ∪ task_ids」聯集分支原本沒有被任何測試覆蓋，現在有了。

#### airflow-ctl

- **[Drop the redundant re-validation round trip in airflowctl list pagination](https://github.com/apache/airflow/pull/71928)**（#71928）`airflow-ctl｜效能`
  - 改了什麼：`BaseOperations.execute_list` 合併所有分頁後，又對已驗證過的物件做了一次 `model_dump()` + `model_validate()`。移除這段多餘的往返。
  - 影響：實測 20,000 筆時省下約 520 ms 和 94 MiB peak memory，所有 `airflowctl ... list` 指令都可以被這個改進影響。

#### Provider：common-ai

- **[Stop DataFusionToolset from materializing full results for a capped query](https://github.com/apache/airflow/pull/73384)**（#73384）`provider-common-ai｜效能`
  - 改了什麼：`DataFusionToolset` 的 `query` 工具最多只回傳 `max_rows` 筆，卻先把整個查詢結果轉成 Python dict 再截斷。改成在 DataFusion 層加上 `LIMIT max_rows + 1`。
  - 影響：3M 筆、65 MiB 的 parquet 檔實測：

    | Query | Peak Memory | 耗時 |
    | --- | --- | --- |
    | `SELECT *` | 1,121 → 219 MiB | 4.20 → 0.05 秒 |
    | `ORDER BY` | 1,358 → 279 MiB | 3.30 → 0.30 秒 |

  - 版本：下一個 common-ai 版本。

- **[Reject empty allowed_tables in SQLToolset instead of allowing all tables](https://github.com/apache/airflow/pull/73381)**（#73381）`provider-common-ai｜Bug 修正（防護機制）`
  - 改了什麼：`SQLToolset(allowed_tables=[])` 原本被當成 `None`（不限制），結果 agent 可以看到並查詢所有資料表。改成空 list 直接丟出 `ValueError`，並在 changelog 註明行為變更。
  - 影響：allow-list 經常是動態產生的（例如來自 `Variable.get` 或設定檔），一旦結果是空的，原本會默默的關掉整個資料表白名單。修正後改成在 Dag import 時就失敗。
  - 版本：**apache-airflow-providers-common-ai 0.10.0**

- **[Reject non-string prompts in LLMFileAnalysisOperator before reading files](https://github.com/apache/airflow/pull/71734)**（#71734）`provider-common-ai｜Bug 修正`
  - 改了什麼：非字串的 prompt 原本要等所有檔案都從儲存體讀完，才丟出不太好懂的 `TypeError`。改成在 `execute()` 一開始就檢查，錯誤訊息會指出應改用 `multi_modal=True`。
  - 影響：省掉沒有意義的讀取，錯誤訊息也更清楚，與 decorator 的行為一致。
  - 版本：**apache-airflow-providers-common-ai 0.10.0**

- **[Add require_approval preflight check to @task.llm_schema_compare](https://github.com/apache/airflow/pull/71688)**（#71688）`provider-common-ai｜Bug 修正`
  - 改了什麼：`@task.llm_schema_compare` 補上其他三個 LLM decorator 都有的 preflight 檢查。
  - 影響：使用錯誤時，錯誤訊息會指出使用者實際寫的 decorator 名稱，不再顯示內部類別名稱。四個 decorator 的行為變得一致。
  - 版本：**apache-airflow-providers-common-ai 0.9.0**

> [!TIP]
> 其實應該不會有使用者這麼奇怪用圖片去做 schema compare，但我認為只要使用者可以在系統上做出這種事，那我們就應該要確保專案給出的錯誤訊息是正確的。

- **[Document usage_limits on all common.ai LLM operators](https://github.com/apache/airflow/pull/73283)**（#73283）`provider-common-ai｜文件`
  - 改了什麼：為 `LLMBranchOperator`、`LLMSQLQueryOperator`、`LLMSchemaCompareOperator`、`LLMFileAnalysisOperator` 的文件和 docstring 補上 `usage_limits`（token、請求數、工具呼叫次數、費用上限），並統一連到同一個說明段落。
  - 影響：使用者原本無法得知這些 operator 也能設定 LLM 成本上限。
  - 版本：**apache-airflow-providers-common-ai 0.10.0**

#### Provider：http

- **[Fix HttpOperator deferrable pagination returning only the last page instead of all page](https://github.com/apache/airflow/pull/72388)**（#72388）`provider-http｜Bug 修正`
  - 改了什麼：`execute_complete()` 丟掉了 `paginate_async()` 已彙整好的所有頁面結果，改用只含當前頁的結果覆蓋。另外修正原本的回歸測試：它的 assertion 被 `contextlib.suppress(TaskDeferred)` 跳過，所以永遠不會執行。
  - 影響：把有分頁的 `HttpOperator` 改成 `deferrable=True` 的 Dag，原本會遺失最後一頁以外的所有資料，也不會報錯。這是會直接造成資料錯誤的 bug。
  - 版本：**apache-airflow-providers-http 6.1.0**

### Code Review 重點

這個月做了 21 次 code review。

- [#64105](https://github.com/apache/airflow/pull/64105) Prevent cleartext credential storage in Git Dag bundles：指出 askpass helper 會回應任何帳密提示，包含跨來源的 git submodule，可能把父 repo 的憑證送到不相關的 host。建議先驗證目標 URL 或 host 再回傳憑證。
- [#72486](https://github.com/apache/airflow/pull/72486) Respect update_mask in bulk connection updates：指出 `update_mask` 由所有 entity 共用，某個 entity 沒提供 `extra` 時，會被 `set_extra(None)` 清掉既有資料。
- [#73748](https://github.com/apache/airflow/pull/73748) Fix naive datetime XCom corruption：指出這個改動改變了 naive datetime 的解讀方式，但 serde `__version__` 沒有升版。rolling upgrade 期間新舊 worker 會用不同方式解讀同一份資料，這也會影響 trigger kwargs。建議升到 v3 並依版本分流處理。
- [#72845](https://github.com/apache/airflow/pull/72845) Add DuckDB provider：指出明確指定了一個不存在的 connection id 時，會靜默退回 in-memory 資料庫，建議和「未指定 connection」的情況分開處理。
- [#72470](https://github.com/apache/airflow/pull/72470)：要求用 Airflow 目前指定的 OpenAPI Generator 7.22.0 驗證 Java client 的產生結果。
- [#72575](https://github.com/apache/airflow/pull/72575)：提醒貢獻者該 issue 已有人認領並開了 PR，引導他改去 review 既有的 PR，避免重工。
- 另外 Approve 了 zh-TW 翻譯、DocumentLoaderOperator JSON Lines 支援、REST API metrics、Assets 篩選等 PR。

---

## Month: August 2026

Summary: PR: 18, Code Review: 33, Issue Triage: —, Dev List: —

### PR

#### Core

- **[Reduce memory used when deleting a Dag with a large history](https://github.com/apache/airflow/pull/71185)**（#71185）`core｜效能`
  - 改了什麼：`delete_dag` 的每個 bulk delete 都強制使用 SQLAlchemy 的 `synchronize_session="fetch"`，會把每一筆被刪除列的主鍵讀回 Python，但 session 裡其實沒有物件需要同步。改用預設的 `"auto"` 策略。
  - 影響：200,000 筆、資料表形狀同 `task_instance` 的實測：

    | Backend | 耗時 | Peak heap |
    | --- | --- | --- |
    | PostgreSQL 16 | 0.625 → 0.055 秒（約 11 倍） | 42.1 MiB → 約 0 |
    | MySQL 8.4 | 耗時相近 | 43.6 MiB → 約 0，並少一次 `SELECT` |

    歷史越長的 Dag，刪除時 API server 的記憶體壓力越大，這個問題已經解決了。
  - 版本：**Airflow 3.3.2**

- **[Reduce memory used when deleting queued asset events](https://github.com/apache/airflow/pull/71917)**（#71917）`core｜效能`
  - 改了什麼：#71185 的後續。把同樣的 `"fetch"` 問題從兩個刪除 queued asset event 的端點移除。
  - 影響：50,000 筆實測，PostgreSQL 和 SQLite 都從約 0.75 秒降到約 0.045 秒（約 16 倍），peak heap 從 14.2 MiB 降到約 0。
  - 版本：**Airflow 3.3.2**

- **[Fix N+1 query in bulk update for Variables and Pools](https://github.com/apache/airflow/pull/71918)**（#71918）`core｜效能`
  - 改了什麼：`BulkVariableService` 和 `BulkPoolService` 的 bulk update 先批次查一次，卻把結果丟掉，再逐筆重新查詢。改成重用已查到的 ORM 物件，與 `connections.py` 的設計一致。
  - 影響：這是 N+1 系列（#66222、#67304）的最後一塊，處理 update 那一側。UI「匯入 variables」等 bulk 操作的 DB round trip 從 O(N) 降到 O(1)。
  - 版本：**Airflow 3.3.2**

- **[Return 404 from task state store endpoints for unknown task instances](https://github.com/apache/airflow/pull/70983)**（#70983）`core｜Bug 修正`
  - 改了什麼：task state store 是每個 task instance 的 key/value 儲存，用來存外部 job ID、checkpoint 等。它的兩個 DELETE 端點不會檢查 task instance 是否存在，對不存在的 TI 也回傳 `204`。改成回傳 `404`，並讓 6 個端點的 OpenAPI 宣告與實際行為一致。
  - 影響：client 打錯 dag_id / run_id / task_id 時原本收到「刪除成功」，實際上什麼都沒刪。
  - 版本：**Airflow 3.3.2**

- **[Document HTTP statuses that API routes raise but never declared](https://github.com/apache/airflow/pull/71011)**（#71011）`core｜OpenAPI 規格修正`
  - 改了什麼：一次補齊 8 個端點實際會回傳、但 OpenAPI spec 沒宣告的狀態碼，涵蓋 backfill dry run、刪除 Dag run 或 Dag 時的 409、HITL、clearTaskInstances、UI dependencies 等。
  - 影響：依 OpenAPI spec 自動產生的各語言 SDK client 現在有這些回應的模型，能正確處理。
  - 版本：**Airflow 3.3.2**

- **[Remove the unreachable 404 from the create Variable endpoint](https://github.com/apache/airflow/pull/71245)**（#71245）`core｜OpenAPI 規格修正`
  - 改了什麼：建立 Variable 的端點有一個永遠走不到的 `404` 分支，而且在 create 端點回 404 本身就不合理。移除它，同時保持 mypy 型別檢查通過。
  - 影響：spec 不再宣告這個端點不可能回傳的狀態碼。這是從 #71011 的 review 中拆出來的。
  - 版本：**Airflow 3.3.2**

- **[Document the 409 from XCom create and drop a duplicated condition](https://github.com/apache/airflow/pull/70992)**（#70992）`core｜OpenAPI 規格修正`
  - 改了什麼：在 OpenAPI 宣告建立 XCom 時 key 重複會回傳的 `409 Conflict`，並移除一段重複巢狀的 `if not dag_run`。
  - 影響：generated client 能處理 XCom key 衝突。
  - 版本：**Airflow 3.3.1**

- **[Complete the Traditional Chinese UI translations](https://github.com/apache/airflow/pull/71019)**（#71019）`core｜新功能（i18n）`
  - 改了什麼：補上 16 個缺漏的繁體中文翻譯 key，移除 6 個英文版已不存在的 key。
  - 影響：zh-TW UI 完整度： 1009/1009。

- **[Fix inverted docstring for config write hide_sensitive option](https://github.com/apache/airflow/pull/71623)**（#71623）`core (shared)｜文件`
  - 改了什麼：`AirflowConfigParser.write()` 的 docstring 寫錯了參數名稱，而且描述的行為和實作相反（說「包含」敏感值，實際是「隱藏」）。
  - 影響：避免開發者誤用這個參數，以為設了它會輸出敏感設定值。

- **[Fix stale TypeScript SDK docs on bundle metadata and packing setup](https://github.com/apache/airflow/pull/72115)**（#72115）`core｜文件`
  - 改了什麼：TypeScript SDK 文件仍描述已在 #70273 移除的 `airflow-metadata.yaml` sidecar，而且缺少安裝 `apache-airflow-ts-sdk` 和選用的 peer dependency `esbuild` 的步驟。
  - 影響：照著文件操作的使用者原本無法完成。

#### Provider：common-ai

- **[Add test_connection support to LangChainHook and LlamaIndexHook](https://github.com/apache/airflow/pull/71841)**（#71841）`provider-common-ai｜新功能`
  - 改了什麼：common-ai 的四種 connection type 中，有兩種沒有實作 `test_connection()`。為它們補上，做法與 `PydanticAIHook` 相同：只解析 model 設定，不發出真正的 API 呼叫。
  - 影響：原本在 UI 對 LangChain / LlamaIndex connection 按「Test」一律報錯，現在可以正常驗證設定。
  - 版本：**apache-airflow-providers-common-ai 0.9.0**

- **[Let the agent retry on a rejected DataFusion query instead of failing](https://github.com/apache/airflow/pull/71445)**（#71445）`provider-common-ai｜Bug 修正`
  - 改了什麼：`DataFusionToolset` 遇到 `SQLSafetyError`（例如 LLM 產生的 SQL 有語法錯誤）時直接讓 task 失敗。改成丟出 `ModelRetry`，讓 agent 自行修正。
  - 影響：與同一 method 的其他錯誤路徑及 `SQLToolset` 的行為一致。LLM 只是打錯字時，不會再讓整個 task 失敗。
  - 版本：**apache-airflow-providers-common-ai 0.8.0**

- **[Document LLMFileAnalysisOperator's inherited LLM and HITL parameters](https://github.com/apache/airflow/pull/71856)**（#71856）`provider-common-ai｜文件`
  - 改了什麼：補上 `model_id`、`system_prompt`、`agent_params` 和 Human-in-the-Loop 審核參數的文件，並新增一個 `require_approval` 範例 Dag。
  - 版本：**apache-airflow-providers-common-ai 0.9.0**

- **[Add LLMSchemaCompareOperator to the common-ai operator index table](https://github.com/apache/airflow/pull/71738)**（#71738）`provider-common-ai｜文件`
  - 改了什麼：在 operator 選擇對照表中補上缺漏的 `LLMSchemaCompareOperator`。
  - 版本：**apache-airflow-providers-common-ai 0.8.0**

- **[Add prek hook to catch operators missing from common-ai docs index](https://github.com/apache/airflow/pull/71783)**（#71783）`provider-common-ai｜CI`
  - 改了什麼：新增 `check-common-ai-operators-index` prek hook，自動檢查每個 operator 都有出現在文件索引表中。
  - 影響：從機制上防止 #71738 這類文件漏掉東西的狀況再次發生。

#### Provider：cncf-kubernetes

- **[Remove stale is_async docstring param from await_pod_start](https://github.com/apache/airflow/pull/72261)**（#72261）`provider-cncf-kubernetes｜文件`
  - 改了什麼：移除 docstring 中已不存在的 `is_async` 參數說明。
  - 版本：**apache-airflow-providers-cncf-kubernetes 10.22.0**

#### CI / 開發工具

- **[Fix misspelled variable in CI migration test DB manager export](https://github.com/apache/airflow/pull/71334)**（#71334）`CI｜程式品質`
  - 改了什麼：修正 CI migration test 中的 `DB_MANGERS` 拼字錯誤。這個錯誤讓 `EXTERNAL_DB_MANAGERS` 一直是空值，CI 卻都會通過。

- **[Fix stale flag name in Airflow translations agent skill](https://github.com/apache/airflow/pull/71332)**（#71332）`開發工具｜文件`
  - 改了什麼：把翻譯工具說明中已改名的 `--remove-extra` 更新為 `--remove-unused`。

### Code Review 重點

這個月在別人的 PR 上送出 33 次 review。

- [#71880](https://github.com/apache/airflow/pull/71880) db_cleanup：指出新增的 `skip_if_cascade_blocked` 參數與既有的 NOT EXISTS guard 在結構上重複，建議沿用既有機制，並指導 PR 標題的規範。（Request changes）
- [#71669](https://github.com/apache/airflow/pull/71669) Fix slow triggerer log draining：指出 `buffer[newline_pos + 1:]` 會建立新物件，原本的註解描述有誤，建議用 `memoryview` 減少一次中間複製。
- [#72032](https://github.com/apache/airflow/pull/72032)、[#72023](https://github.com/apache/airflow/pull/72023)、[#71927](https://github.com/apache/airflow/pull/71927)：引導新貢獻者遵守 PR 標題與描述規範，並提醒不要搶已有人認領的 issue。
- common-ai 相關：review 了 [#71403](https://github.com/apache/airflow/pull/71403)（per-run 成本上限），Approve 了 [#72012](https://github.com/apache/airflow/pull/72012)（Vertex AI hook 靜默丟棄 credentials）、[#72011](https://github.com/apache/airflow/pull/72011)、[#72013](https://github.com/apache/airflow/pull/72013)。
- 繁體中文翻譯：Approve 了 [#71168](https://github.com/apache/airflow/pull/71168)、[#71482](https://github.com/apache/airflow/pull/71482)、[#72056](https://github.com/apache/airflow/pull/72056)。
- 其他 Approve：[#69774](https://github.com/apache/airflow/pull/69774)（DBDagBag TTL cache）、[#72026](https://github.com/apache/airflow/pull/72026)（api-server access log 遺失）、[#72109](https://github.com/apache/airflow/pull/72109)（`DAG.cli()` crash）、[#71767](https://github.com/apache/airflow/pull/71767)（deadline 診斷）等。

---

## Month: July 2026

Summary: PR: 4, Code Review: 0, Issue Triage: —, Dev List: —

### PR

#### Provider：amazon

- **[Fix EcsRunTaskOperator deferred logs read from the wrong region](https://github.com/apache/airflow/pull/70474)**（#70474）`provider-amazon｜Bug 修正`
  - 改了什麼：`EcsRunTaskOperator` 在 `deferrable=True` 且 container log 送到另一個 region 時，trigger 會用 ECS 的 region 去讀 CloudWatch log。為 `TaskDoneTrigger` 新增 `log_region_name`，預設 fallback 到 `region_name`，所以已序列化的舊 trigger 不受影響。
  - 影響：關閉 [#70465](https://github.com/apache/airflow/issues/70465)。跨 region 存放 log 的使用者，原本在整個 deferral 期間完全看不到 task log。這是 log region 修正的最後一塊（另一半是 #70464）。
  - 版本：**apache-airflow-providers-amazon 9.34.0**

#### Provider：neo4j

- **[Fail Neo4jOperator argument mistakes at Dag parse time](https://github.com/apache/airflow/pull/70508)**（#70508）`provider-neo4j｜Bug 修正`
  - 改了什麼：把 `cypher` / `sql` 參數是否有提供的檢查移回 `__init__`，屬於 #70503 追蹤的一系列 revert。
  - 影響：開啟 `render_template_as_native_obj=True` 時，有提供的參數可能 render 成 `None` 而被誤判為缺少。修正後，寫錯參數會在 Dag import 階段就報錯，不必等每個 task instance 執行時才失敗。
  - 版本：**apache-airflow-providers-neo4j 3.12.1**

#### Provider：common-ai

- **[Fix LLMSQLQueryOperator not stripping single-line markdown code fences](https://github.com/apache/airflow/pull/70137)**（#70137）`provider-common-ai｜Bug 修正`
  - 改了什麼：LLM 回傳單行的 code fence（例如 ` ```SELECT 1``` `）時，反引號沒有被移除，會直接送進 SQL 驗證與執行。補上單行情況的處理。
  - 影響：LLM 常常不遵守「不要用 markdown」的指示，做一個小小的防護。
  - 版本：**apache-airflow-providers-common-ai 0.7.0**

- **[Support .txt files in LLMFileAnalysisOperator](https://github.com/apache/airflow/pull/70431)**（#70431）`provider-common-ai｜新功能`
  - 改了什麼：`LLMFileAnalysisOperator` 原本拒絕 `.txt` 檔，沒有副檔名的檔案反而可以用。把 `txt` 加入支援格式，包含 gzip。
  - 影響：與同一 provider 的 `DocumentLoaderOperator` 及官方範例 Dag 中使用的 `.txt` 輸入一致。
  - 版本：**apache-airflow-providers-common-ai 0.7.0**

---

## Month: June 2026

Summary: PR: 0, Code Review: 0, Issue Triage: —, Dev List: —

為了畢業讀期末考，抱歉了開源。

---

## Month: May 2026

Summary: PR: 14, Code Review: 0, Issue Triage: —, Dev List: —

### PR

#### Core

- **[Fix N+1 query pattern in bulk pool delete endpoint](https://github.com/apache/airflow/pull/66222)**（#66222）`core｜效能`
  - 改了什麼：bulk pool delete 先用 `categorize_pools()` 一次查出所有 pool，卻丟掉結果，在迴圈中逐一重查。改成重用已查到的物件。
  - 影響：刪除 N 個 pool 的 DB round trip 從 2N+1 降到 N+1，並加上 query count 回歸測試。這是 N+1 系列的第一個 PR，且被 backport 到 3.2 維護版。
  - 版本：**Airflow 3.2.2**

- **[Fix N+1 query in bulk task instance delete endpoint](https://github.com/apache/airflow/pull/67304)**（#67304）`core｜效能`
  - 改了什麼：bulk task instance delete 在「指定 `(task_id, map_index)`」的分支裡逐筆重新查詢。改成重用 `_categorize_task_instances` 已載入的對照表。
  - 影響：查詢數從 5+2N 降到 5+N。刪除 20 個 TI 時從 45 次降到 25 次。
  - 版本：**Airflow 3.3.0**

- **[Avoid rebuilding option filter sets per iteration in update_config CLI](https://github.com/apache/airflow/pull/66369)**（#66369）`core｜效能`
  - 改了什麼：`airflow config update` 在每次迴圈中重建 4 個小寫 list 再做 `in` 判斷。改成在迴圈外預先建好 set。
  - 影響：複雜度從 O(N·M) 降到 O(N+M)。
  - 版本：**Airflow 3.3.0**

- **[Avoid rebuilding callback field list on every serialization loop](https://github.com/apache/airflow/pull/66343)**（#66343）`core｜效能`
  - 改了什麼：Dag 序列化（`serialized_objects.py`）有 4 處重複的 callback 欄位 list comprehension，每次迴圈都重建。提升為模組層級的 frozenset 常數。
  - 影響：Dag 序列化是 Dag processor 的熱路徑，membership 檢查從 O(n) 變 O(1)，callback 類型名稱也只剩一處定義。
  - 版本：**Airflow 3.3.0**

- **[Convert RUNTIME_VARYING_CALLS to frozenset](https://github.com/apache/airflow/pull/66306)**（#66306）`core｜效能`
  - 改了什麼：Dag version inflation checker 的查找表從 list 改成 frozenset。
  - 版本：**Airflow 3.3.0**

- **[Convert STATES_SENT_DIRECTLY to frozenset](https://github.com/apache/airflow/pull/66317)**（#66317）`core (task-sdk)｜效能`
  - 改了什麼：task supervisor 的狀態查找表從 list 改成 frozenset。
  - 版本：**apache-airflow-task-sdk 1.3.0**（隨 Airflow 3.3.0）

- **[Fix GET /pools list endpoint incorrectly documenting 404 in OpenAPI spec](https://github.com/apache/airflow/pull/67570)**（#67570）`core｜OpenAPI 規格修正`
  - 改了什麼：`GET /pools` 一定回傳一個集合（可能為空），不可能回 404。從 spec 中移除錯誤的 404 宣告。
  - 影響：generated SDK 不會再為不可能出現的 404 產生處理分支。這是 OpenAPI 規格修正系列的第一個 PR。
  - 版本：**Airflow 3.3.0**

- **[Fix GET /auth/login missing 400 in OpenAPI spec](https://github.com/apache/airflow/pull/67571)**（#67571）`core｜OpenAPI 規格修正`
  - 改了什麼：`/auth/login` 在 `next` 重導 URL 不安全時會回 400，但 spec 沒有宣告。補上宣告，並改用 `status.HTTP_400_BAD_REQUEST` 常數。
  - 版本：**Airflow 3.3.0**

- **[Make Pool model session parameter keyword-only](https://github.com/apache/airflow/pull/66967)**（#66967）`core｜程式品質`
  - 改了什麼：`Pool.create_or_update_pool()` 和 `Pool.delete_pool()` 的 `session` 改成 keyword-only，並移除函式內的 `session.commit()`。
  - 版本：**Airflow 3.3.0**

- **[Use contextlib.suppress instead of try-except-pass and re-enable SIM105](https://github.com/apache/airflow/pull/66193)**（#66193）`core｜程式品質`
  - 改了什麼：與 #66178 一起完成 SIM105 清理（core、task-sdk、dev 共 8 個檔案），並把這條 ruff 規則重新設為全 repo 強制。
  - 影響：這條規則原本在 #53475 中莫名其妙被關掉，現在恢復強制檢查。
  - 版本：**Airflow 3.2.2**、apache-airflow-task-sdk 1.2.2、apache-airflow-providers-mongo 5.4.0

- **[Use shared Zulu datetime formatter in remaining FastAPI tests](https://github.com/apache/airflow/pull/66439)**（#66439）`core｜測試 / 程式品質`
  - 改了什麼：把最後 3 處手寫的 `isoformat().replace("+00:00", "Z")` 換成共用 helper。
  - 影響：關閉 AIP-84 清理 issue [#44033](https://github.com/apache/airflow/issues/44033)，這個 issue 從 2024 年開到現在，先前兩次 PR 嘗試（#44108、#52042）都沒有成功。
  - 版本：已收錄於 **Airflow 3.3.0** 分支（測試程式碼）

#### Provider

- **[Use contextlib.suppress instead of try-except-pass and re-enable SIM105](https://github.com/apache/airflow/pull/66178)**（#66178）`provider（跨 6 個）｜程式品質`
  - 改了什麼：把 17 個檔案中的 24 個 `try-except-pass` 改寫為 `contextlib.suppress`。
  - 影響：讓 SIM105 規則可以在全 repo 重新啟用。
  - 版本：providers-amazon 9.28.0、celery 3.20.0、cncf-kubernetes 10.17.0、common-ai 0.2.0、ssh 5.0.2、apache-spark 6.0.2

- **[Iterate file objects directly instead of calling readlines()](https://github.com/apache/airflow/pull/66291)**（#66291）`provider-google + dev｜程式品質`
  - 改了什麼：修正全 repo 剩下的 5 處 FURB129（`for line in f.readlines()` 改成 `for line in f`）。
  - 影響：其中兩處改為逐行讀取（lazy），不必把整個檔案載入記憶體。
  - 版本：**apache-airflow-providers-google 21.3.0**

- **[Enable PT007 rule to apache.kafka Provider test](https://github.com/apache/airflow/pull/66147)**（#66147）`provider-apache-kafka｜程式品質`
  - 改了什麼：逐步清理 PT007 的其中一步，把 parametrize 的值統一為 tuple。
  - 版本：apache-airflow-providers-apache-kafka 1.14.0（測試程式碼）

---

## Month: April 2026（04/07 ～ 04/30）

Summary: PR: 2, Code Review: 0, Issue Triage: —, Dev List: —

### PR

- **[Make error messages consistent in local API client create_pool](https://github.com/apache/airflow/pull/66039)**（#66039）`core｜程式品質`
  - 改了什麼：統一 `Client.create_pool` 的錯誤訊息用字，與 codebase 其他地方一致。
  - 版本：**Airflow 3.2.2**

> [!TIP]
> 這是我的第一個 Airflow PR ：）

- **[Fix ASYNC110 violation in RedshiftDataTrigger](https://github.com/apache/airflow/pull/66157)**（#66157）`provider-amazon｜程式品質`
  - 改了什麼：修正全 repo 唯一剩下的 ASYNC110 違規（async 程式中的 busy-wait 迴圈），並把這條規則從全域 ignore list 移除。
  - 影響：async trigger 中的 busy-wait 寫法從此會被 lint 擋下。
  - 版本：**apache-airflow-providers-amazon 9.28.0**

---

#### 備註

- 有些 PR 因為 5 個 PR 上限的問題被關掉這邊就先不列出來了，等之後重新 open 並重新處理後再記錄下來。
