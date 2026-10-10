# Apache Airflow Contribution Report by Andrushika

## 總覽：2026 年 6 月 – 10 月（截至 10/07）

**Summary**

- PR: apache/airflow merged 53 個 + apache/airflow-site 2 個 + apache/infrastructure-asfyaml 1 個（open）
- Code Review: 36
- Issue Triage / RC Testing: 14
- Dev List: 3

### Merged PR 分類（依區域）

| 分類 | 數量 |
| --- | --- |
| Breeze / 本地開發工具 | 19 |
| UI（task log、Dags list、zh-TW 翻譯） | 12 |
| CI / static checks / selective checks | 8 |
| Language SDK（Java / Go / TS） | 5 |
| Providers（common.ai、google、microsoft.azure、keycloak） | 4 |
| Task SDK | 2 |
| 文件 | 2 |
| airflow-ctl | 1 |
| apache/airflow-site（文件版本選單、theme build） | 2 |
| apache/infrastructure-asfyaml | 1 |

---

## October 2026（截至 10/07）

**Summary**

- PR: 2 merged + 4 open
- Code Review: 3
- Issue: 1

### PR（merged）

#### Breeze / 本地開發工具

- [Add breeze verify to list local checks for changed files (#73241)](https://github.com/apache/airflow/pull/73241)：`新功能`
  - 新增一個 command `breeze verify`，基於 selective check，讓「要跑什麼 checks」清楚能被寫出來
  - **背景**
    1. 因為現在社群很依賴用 AI agent 來開發和 review，但因為這件事普及，很多開發者會不看開發文件就讓 agent 去發 PR。所以 Airflow 直接在 `SKILL.md` 裡面要求 agent，在 push 之前要自己去看專案的 CI 怎麼寫的，自行把相關 test 跑一遍。但「讓 agent 自己看 CI 設定、並自己決定要跑什麼 tests」這件事情本身花時間又燒 token；有些 test 需要在 container 裡面跑，還常常自己撞到環境問題
    2. 對於新貢獻者來說，因為 repo 太龐大，常常會不知道自己的修改的影響面到哪裡，也自然不知道要跑什麼 check
  - 發現這件事情之後想到，既然 airflow 本來就有 selective check，那依照 diff 自動選出應該跑的所有 checks 應該也能夠辦到
  - 還有另一個 open [PR](https://github.com/apache/airflow/pull/74229)，讓這個 command 除了「列出要跑的 checks list」之外，可以真正下去執行
  - 我私心最喜歡的 PR

#### Task SDK

- [Enforce execution_timeout from the task supervisor side (#73806)](https://github.com/apache/airflow/pull/73806)：`Bug 修正`
  - 接手長期無人處理的 [issue #53337](https://github.com/apache/airflow/issues/53337) task runner timeout 失效被忽略的問題，讓 timeout 從 supervisor 那一側來管控
  - 在自己的 PR 中，唯一一個 task-sdk 領域中的非 minor issue，應該是目前摸到最深的架構改進
  - 自己之前也開過一些 task-sdk 的 PR，但因為是影響較大的敏感區域，感覺 Ash 和 Amogh 大大看到不認識的 ID 都不會想點進來 review，最近好像有比較願意看我的東西的感覺...？
  - 一次關掉四個老 issue （太好啦）

#### Language SDK（Java / Go / TS）

- [Add task state store API to the Java SDK (#73464)](https://github.com/apache/airflow/pull/73464)：`新功能`
  - 讓 Java SDK 與 Python 的 task state store 對齊

### PR（open）

#### Breeze / 本地開發工具

- [Make breeze verify run the checks it lists instead of only listing them (#74229)](https://github.com/apache/airflow/pull/74229)：`新功能`
- [Fix breeze down ignoring the --cleanup-build-cache flag (#73964)](https://github.com/apache/airflow/pull/73964)：`Bug 修正`

#### Language SDK（Java / Go / TS）

- [Warn on suspicious Dag and task IDs at Java SDK build time (#69937)](https://github.com/apache/airflow/pull/69937)：`新功能`

### Code Review

- [Scope the mapped task group skip-decision query to its own Dag run (#74390)](https://github.com/apache/airflow/pull/74390)
  - 原本想要速修 CI failure，結果被其他人搶先一步
- [Clean up deleted worktrees' Breeze resources automatically (#74282)](https://github.com/apache/airflow/pull/74282)
  - 實際在本地驗證這個 PR 的清理效果
  - 指出在 MacOS 上執行時，container 若正在執行就無法被正常清理
- [Add missing Taiwanese Mandarin UI translations (#74223)](https://github.com/apache/airflow/pull/74223)

### Issue

- [Support [workers] state_store_backend in language SDK tasks (#74400)](https://github.com/apache/airflow/issues/74400)

---

## September 2026

**Summary**

- PR: 22 merged（另有 ASF Infra 1 個 open）
- Code Review: 23
- Issue Triage / RC Testing: 5
- Dev List: 3

### PR

#### Breeze / 本地開發工具

- [Fix breeze down crashing when a start-airflow container is running (#73955)](https://github.com/apache/airflow/pull/73955)：`Bug 修正`
- [Fix Postgres data volume path in Breeze (#73862)](https://github.com/apache/airflow/pull/73862)：`Bug 修正`
  - 意外發現 breeze 一直掛著的 docker volume 都命名錯誤，所以跑 `breeze down --preserve-volumes` 之後 volume 還是會不見，壞掉快一年（在此證明這個功能完全沒有人會用？）
  - Review Ash 修的 [#73836](https://github.com/apache/airflow/pull/73836) 時意外發現的 bug
- [Speed up breeze integration startup by shortening the healthcheck interval (#73320)](https://github.com/apache/airflow/pull/73320)：`效能`
  - 縮短 docker compose 啟動時的 healthy heartbeat 間隔時間
  - 一行修改，加速一點點（~3s）breeze command 的啟動時間
- [Speed up breeze start by caching Python bytecode in a docker volume (#72567)](https://github.com/apache/airflow/pull/72567)：`效能`
  - 把 Python bytecode cache 存進 docker volume，下次啟動不用再重新編譯
  - container 內 `start-airflow` 的啟動時間：16.8s → 8.8s
- [Speed up breeze database startup by shortening the healthcheck interval (#73167)](https://github.com/apache/airflow/pull/73167)：`效能`
  - 和 [#73320](https://github.com/apache/airflow/pull/73320) 同一招，先做在 postgres / mysql backend；[#73320](https://github.com/apache/airflow/pull/73320) 是 review 時被要求補上的後續
  - postgres 的啟動等待時間：7.75s → 3.4s
- [Speed up breeze start-airflow by skipping a redundant CLI call (#73174)](https://github.com/apache/airflow/pull/73174)：`效能`
  - `breeze start-airflow` 每次啟動都會多跑一次用不到的 `airflow config get-value`
  - 拿掉之後，每次啟動省 2.2–2.6s
- [Skip pnpm store when detecting UI asset changes (#72783)](https://github.com/apache/airflow/pull/72783)：`效能`
  - `breeze start-airflow` 每次啟動前會 hash UI 目錄，判斷要不要重新 build assets
  - 但它連第三方套件庫 `.pnpm-store` 也一起掃，所以就算什麼都沒改，每次都要多等 16–30 秒
  - 改成掃描時直接跳過 `node_modules` 和 `.pnpm-store`，檢查時間降到 1 秒內
  - 以 3 票之姿~~灌票~~當選 **2026 年 9 月 PR of the Month**
- [Speed up provider asset change detection by pruning dependency directories (#72784)](https://github.com/apache/airflow/pull/72784)：`效能`
  - 和 [#72783](https://github.com/apache/airflow/pull/72783) 同一個問題，發生在 provider 的 asset 檢查
- [Remove unused Breeze uv timeout and WSL helpers (#72704)](https://github.com/apache/airflow/pull/72704)：`重構`
- [Fix breeze --include-mypy-volume not mounting the mypy cache volume (#72569)](https://github.com/apache/airflow/pull/72569)：`Bug 修正`
- [Show which processes hold the Breeze UI dev ports when start-airflow fails (#72564)](https://github.com/apache/airflow/pull/72564)：`開發體驗`
  - 幫 UI port 衝突時加上提示，因為突然前端壞掉害我找半天
  - 在跑 `breeze start-airflow` 的時候前端有時候會打不開，後來發現是自己在多個 worktree 裡面重複啟動了 airflow，造成 UI port 被佔用
- [Stop Breeze from hanging on unresponsive Docker (#72439)](https://github.com/apache/airflow/pull/72439)：`Bug 修正`

#### UI

- [Sync the UI Dags list applied sort rule with URL query param (#73622)](https://github.com/apache/airflow/pull/73622)：`Bug 修正`
  - 在做 [#72558](https://github.com/apache/airflow/pull/72558) 的時候看到很不直覺的程式碼
  - 讓 UI 的 Dag list 頁面 sort 狀態和 query param 同步
- [Support multi-column sort in the Dags list table (#72558)](https://github.com/apache/airflow/pull/72558)：`新功能`
  - 讓 Dags list 可以同時依多個欄位排序
- [Add missing Traditional Chinese UI translations (#73180)](https://github.com/apache/airflow/pull/73180)：`翻譯`

#### CI / static checks

- [Bump flit_core in the provider pyproject template on CI upgrades (#73649)](https://github.com/apache/airflow/pull/73649)：`CI failure 修復`
- [Skip the CI disk cleanup when the runner already has room (#73635)](https://github.com/apache/airflow/pull/73635)：`效能`
  - 社群在抱怨 CI 需要花很多時間跑，想看看有沒有改進的空間
  - 自己做了 40 次 benchmark，發現每個 job 會花 2 分鐘（中位數）去做「清理 runner disk 空間」的工作
  - 但其實 runner 的記憶體空間相當充足，完全可以跳過這一步
  - full CI matrix 下（149 jobs）每次約可以省下 132 分鐘 runner 時間
  - 很印象深刻的一支 PR，因為花在 benchmark 的時間 >>> review AI 寫的 code 的時間
- [Stop UI and ts-sdk lint hooks from rewriting the global pnpm config (#73538)](https://github.com/apache/airflow/pull/73538)：`Bug 修正`
  - Airflow 之前在某些 prek hook 會誤寫全域的 `.pnpm-store` 儲存位置的 global env var，所以每開一個 worktree 都會重複下載第三方套件，影響範圍超出 Airflow 本身
  - 有在這個 PR 之前跑過 breeze 的開發者的 global env 都被污染了，唯一的解法是 user 自己把 env var 清乾淨
  - [被塑膠的 Slack 訊息...？](https://apache-airflow.slack.com/archives/C06K9Q5G2UA/p1790134257600189)

#### Language SDK（Java / Go / TS）

- [Improve Java SDK quick start setup (#72440)](https://github.com/apache/airflow/pull/72440)：`文件`
  - 讓文件寫得直覺一點、加上可以一步一步跟著做的 quick-start
  - 這麼好的新功能一定要大力推廣給使用者

#### Providers

- [Fix mypy error in common.ai decision helper with pydantic-ai 2.46 (#73652)](https://github.com/apache/airflow/pull/73652)：`CI failure 修復`
- [Move AzureFileShareToGCSOperator directory_name alias out of \_\_init\_\_ (#70740)](https://github.com/apache/airflow/pull/70740)：`重構`

#### airflow-ctl

- [Stop airflowctl integration tests timing out right after a Dag run starts (#72523)](https://github.com/apache/airflow/pull/72523)：`CI failure 修復`
  - flaky test，airflowctl 的 integration test 常常隨機 timeout
  - AI 追下去，發現是 Celery 預設平行起很多個 worker，在 4 vCPU 的 runner 上一次 fork 十幾個 task process 造成效能爆炸
  - api-server 因為效能爆炸有 20–35 秒搶不到 CPU，造成對應的 test 超時
  - 把測試環境的 worker concurrency 降到 2 之後就不再卡住
  - 最讓我感嘆「這個沒有 AI 我根本辦不到」的一支 PR

#### ASF Infra（asfyaml）

- [Add GitHub pull request creation cap bypass list support (#135)](https://github.com/apache/infrastructure-asfyaml/pull/135)：`新功能`
  - 讓 ASF 下面的專案都可以具備「PR number limit 白名單」的功能（Github 已經具備，但 ASF 尚未引入）
  - Airflow 開始限制非 committer 同時開的 PR 數量，新規則引起了[如火如荼的討論](https://lists.apache.org/thread.html/y0vzry5s55k2gy17rc93632zp0d5733o)
  - Henry 大大曾經提過加上白名單的提議，讓部分貢獻者可以不受到限制，但 Jarek 說 "I propose that anyone with another idea submits a few line PR implementing it, not discussing some "wild ideas"." 所以我就發了這個 PR
  - PR limit 引進之後其實有點心痛，因為自己想要盡量維持高 merge rate，結果引進後被一次 close 8 個 PR 還不能重開（雖然不知道有沒有人在看 merge rate）

### Code Review

- [Automatically isolate `breeze testing` for each git worktree (#73836)](https://github.com/apache/airflow/pull/73836)
  - 印象最深刻的 code review 所以獨立出來說
  - 留了兩個 inline comment，原本被 Ash 大大說 [I don't understand your comment](https://github.com/apache/airflow/pull/73836#discussion_r4125155158)
  - 因為算是莫名被罵（？）所以難過了一下，後來 Ash 也自己發現那真的是需要解的問題，我也開了 follow-up PR [#73862](https://github.com/apache/airflow/pull/73862) 修掉
  - 原本在 Airflow Slack 上面拜託 maintainers 幫忙看一下 PR 通常都會沒下文，從這次 review 之後 Ash 好像都會點進來幫我看兩眼
- Inline comment：[#73998](https://github.com/apache/airflow/pull/73998), [#73855](https://github.com/apache/airflow/pull/73855)
- 指出邏輯 / 行為上的問題：[#73527](https://github.com/apache/airflow/pull/73527), [#73316](https://github.com/apache/airflow/pull/73316), [#73420](https://github.com/apache/airflow/pull/73420), [#73200](https://github.com/apache/airflow/pull/73200)
- 建議補 / 刪除 / 簡化測試：[#72672](https://github.com/apache/airflow/pull/72672), [#73206](https://github.com/apache/airflow/pull/73206), [#73785](https://github.com/apache/airflow/pull/73785), [#72701](https://github.com/apache/airflow/pull/72701)
- 標題、描述、註解、docstring 的修改建議：[#73185](https://github.com/apache/airflow/pull/73185), [#73182](https://github.com/apache/airflow/pull/73182), [#73804](https://github.com/apache/airflow/pull/73804)
- 翻譯 review：[#73943](https://github.com/apache/airflow/pull/73943), [#72766](https://github.com/apache/airflow/pull/72766)
- Approve：[#72683](https://github.com/apache/airflow/pull/72683), [#73956](https://github.com/apache/airflow/pull/73956), [#73944](https://github.com/apache/airflow/pull/73944), [#73632](https://github.com/apache/airflow/pull/73632), [#73612](https://github.com/apache/airflow/pull/73612), [#73171](https://github.com/apache/airflow/pull/73171), [#72919](https://github.com/apache/airflow/pull/72919)

### Issue Triage / RC Testing

- [Status of testing of Apache Airflow 3.3.2rc1 (#73089)](https://github.com/apache/airflow/issues/73089)
  - 幫忙測試 3.3.2rc1，確認自己 7 個被 backport 的修正都正常
- [Handle task timeouts (execution_timeout) at supervisor (#53337)](https://github.com/apache/airflow/issues/53337)
  - 接手這個停了很久的 issue，對應的 PR 是 [#73806](https://github.com/apache/airflow/pull/73806)
  - 同一個問題也出現在 [#57174](https://github.com/apache/airflow/issues/57174)、[#57712](https://github.com/apache/airflow/issues/57712)
- [Language-SDK Dag and task ids are not validated the way Python's are (#73800)](https://github.com/apache/airflow/issues/73800)

### Dev List

- PR creation cap 討論串（9/21、9/22、9/26）
  - 社群在討論要限制非 committer 同時開的 PR 數量，我自己也在會被影響的名單內
  - 整理了每位貢獻者的 open PR 統計，提供給討論當參考
  - 後續回報 asfyaml [#135](https://github.com/apache/infrastructure-asfyaml/pull/135)（bypass list）的進度
  - 回報「PR 被自動關閉之後，作者沒辦法自己 reopen」的問題
  - 也提出 PR [#73747 ](https://github.com/apache/airflow/pull/73747)使作者有辦法透過留言方式 reopen...... 最後沒有下文就 close 了

---

## August 2026

**Summary**

- PR: 15 merged
- Code Review: 6
- Issue Triage / RC Testing: 2

### PR

#### Breeze / 本地開發工具

- [Clarify Breeze CI image build helper names (#71912)](https://github.com/apache/airflow/pull/71912)：`重構`
  - 自己在閱讀 breeze 的程式碼的時候被各種花式 function 命名刁難，順手把它修成比較容易理解的方式
- [Reuse CI image built from the same sources in another checkout (#71886)](https://github.com/apache/airflow/pull/71886)：`效能`
  - 開多個 git worktree 時，每個 worktree 都會各自重新 build 一次 CI image
  - 改成只要 source 相同，就直接重用其他 checkout 已經 build 好的 image
- [Fail loudly when a UI dev server dies in breeze dev mode (#71784)](https://github.com/apache/airflow/pull/71784)：`開發體驗`
- [Breeze: Fail when UI development ports are already in use (#71241)](https://github.com/apache/airflow/pull/71241)：`開發體驗`

#### UI

這邊做了一系列關於 task log 視窗的 UX 改進，起因是 lazy loading 的 log rows 在被滑鼠 select 的時候行為會怪怪的（無法被選中、反白的區塊跳來跳去、複製貼上功能不正常等等）

- [Make copied task log text match the on-screen format (#71270)](https://github.com/apache/airflow/pull/71270)：`Bug 修正`
- [Keep task log selection stable while dragging (#71155)](https://github.com/apache/airflow/pull/71155)：`Bug 修正`
- [Fix copying task logs dropping rows that scrolled out of view (#71156)](https://github.com/apache/airflow/pull/71156)：`Bug 修正`
- [Keep task log text selection alive while scrolling (#71148)](https://github.com/apache/airflow/pull/71148)：`Bug 修正`
- [Stop auto-scrolling task logs while the user is selecting text (#70594)](https://github.com/apache/airflow/pull/70594)：`Bug 修正`
- [UI: Refresh task details immediately when switching tasks (#70789)](https://github.com/apache/airflow/pull/70789)：`Bug 修正`

#### CI / static checks

- [Stop .gitignore from hiding the UI Logs directory on macOS (#71475)](https://github.com/apache/airflow/pull/71475)：`Bug 修正`
- [Prevent agent checks from starting local servers (#71239)](https://github.com/apache/airflow/pull/71239)：`文件`

#### Task SDK

- [Fix short-read handling in task-sdk IPC framing (#69253)](https://github.com/apache/airflow/pull/69253)：`Bug 修正`
  - supervisor 和 task process 之間的 IPC 沒有處理 socket 一次讀不滿（short read）的情況，frame 可能被讀壞，自此之後 supervisor 和 task process 就會斷聯

#### Language SDK（Java / Go / TS）

- [Warn on suspicious Dag and task IDs at TS SDK build time (#70993)](https://github.com/apache/airflow/pull/70993)：`新功能`
  - 把 Go, Java 也一起做了，Java 還沒合併

#### Providers

- [Add WasbRemoteLogIO.from_config and register wasb remote logging scheme (#70301)](https://github.com/apache/airflow/pull/70301)：`新功能`
  - remote logging 與 core 解耦（[#70265](https://github.com/apache/airflow/issues/70265)）的其中一個子任務，負責 Azure wasb 的部分

### Code Review

- [Derive a mixed-language marker for Dags with task.stub (#71213)](https://github.com/apache/airflow/pull/71213)
  - 指出 `is_mixed_language_dag` 這個欄位同時代表兩件事
  - 建議拆出另一個欄位 `definition_role`
- [Drop the stale RFC 9457 TODO on the task-instance-run endpoint (#71552)](https://github.com/apache/airflow/pull/71552)
  - approve；這個過時的 TODO 一直讓貢獻者誤以為還沒做，重複開了好幾個 PR
- [Reject unknown update_mask fields instead of silently ignoring them (#71003)](https://github.com/apache/airflow/pull/71003)
- [Release TI lock before asset listener callbacks (#70951)](https://github.com/apache/airflow/pull/70951)
- zh-TW 翻譯 review：[#71168](https://github.com/apache/airflow/pull/71168), [#71019](https://github.com/apache/airflow/pull/71019)

### Issue Triage / RC Testing

- [Status of testing of Apache Airflow 3.3.1rc2 (#71274)](https://github.com/apache/airflow/issues/71274)
- [Status of testing Providers that were prepared on August 01, 2026 (#70953)](https://github.com/apache/airflow/issues/70953)

---

## July 2026

**Summary**

- PR: 9 merged（另有 airflow-site 2 個、test-infra 1 個）
- Code Review: 4
- Issue Triage / RC Testing: 6

### PR

#### Breeze / 本地開發工具

- [Clarify how to stop Breeze environments (#70560)](https://github.com/apache/airflow/pull/70560)：`文件`

#### UI

- [Reset task try when switching Graph tasks (#70780)](https://github.com/apache/airflow/pull/70780)：`Bug 修正`
- [UI: Fix task states stuck stale when a run finishes quickly (#70319)](https://github.com/apache/airflow/pull/70319)：`Bug 修正`
- [Add missing zh-TW translations for team strings (#70321)](https://github.com/apache/airflow/pull/70321)：`翻譯`

#### CI / static checks

- [Skip ts-sdk supervisor schema check for doc-only changes (#70022)](https://github.com/apache/airflow/pull/70022)：`selective checks`
- [Skip Go SDK CI jobs for doc-only changes under go-sdk/ (#70021)](https://github.com/apache/airflow/pull/70021)：`selective checks`
- [Skip Java SDK jobs for doc-only changes (#69674)](https://github.com/apache/airflow/pull/69674)：`selective checks`

#### Language SDK（Java / Go / TS）

- [Warn on suspicious Dag and task IDs at Go SDK build time (#69965)](https://github.com/apache/airflow/pull/69965)：`新功能`
- [Add build toolchain reference to Java SDK README (#69670)](https://github.com/apache/airflow/pull/69670)：`文件`

#### apache/airflow-site

- [Add filter input and current-version highlight to docs version selector (#1580)](https://github.com/apache/airflow-site/pull/1580)：`新功能`
  - Airflow 文件網站的版本選單很長，很難找到想要的版本
  - 加上搜尋過濾，並標出目前所在的版本（解決被放了一年的 [#51455](https://github.com/apache/airflow/issues/51455)）
- [Exclude gitignored files from sphinx theme hash computation (#1582)](https://github.com/apache/airflow-site/pull/1582)：`Bug 修正`

### Code Review

- [Reduce task success asset registration lock contention (#66854)](https://github.com/apache/airflow/pull/66854)
  - 實際跑 benchmark 找出真正慢的地方：lazy-load alias 的 event 歷史
  - 5 萬筆 event 時要 470ms，直接 insert 只要 1.2ms
  - 很來回折騰的一個 PR，因為設計不斷在修改
  - 最後這個 PR 還是被關掉了，可能因為作者太 vibe 了（？）最後 TP 親自出馬修理
- [Standardize task run state conflict error response (#69981)](https://github.com/apache/airflow/pull/69981)
  - request changes：回傳格式其實不符合 RFC 9457
  - 而且改到 Execution API，需要補 Cadwyn migration
- [Go SDK: fix operator-precedence bug that could crash the worker process (#70141)](https://github.com/apache/airflow/pull/70141)
- [Fill Taiwanese Mandarin translation gap (#70379)](https://github.com/apache/airflow/pull/70379)

### Issue Triage / RC Testing

- [Task supervisor lingers indefinitely on CeleryExecutor when psutil.Process.wait() returns None (#70117)](https://github.com/apache/airflow/issues/70117)
  - 在本地重現問題
  - 原因是 `_check_subprocess_exit` 沒有處理 `wait(timeout=0)` 回傳 None 的情況
- [Webserver timeout randomly (#64212)](https://github.com/apache/airflow/issues/64212)
  - 把整個出錯的過程整理成流程圖，並標出每一段對應到哪些 PR
- [Pass RFC 9457 compliant error message in HTTPException detail field (#62107)](https://github.com/apache/airflow/issues/62107)
  - 當時同時有好幾個 PR 在改同一件事
  - 建議先統一 Execution API 的錯誤格式，再分批修改
  - 最後 Kaxil 覺得這個 issue 是「雜訊」，所以 close 了
- [UI logs selection issues (Airflow 3.2.2) (#68846)](https://github.com/apache/airflow/issues/68846)
  - 找到原因：log 畫面只 render 看得到的列，捲出畫面的列會被移除，選取範圍就壞掉
  - 用 [#70594](https://github.com/apache/airflow/pull/70594)、[#71148](https://github.com/apache/airflow/pull/71148)、[#71155](https://github.com/apache/airflow/pull/71155)、[#71156](https://github.com/apache/airflow/pull/71156) 修掉
- [Status of testing Providers that were prepared on July 22, 2026 (#70355)](https://github.com/apache/airflow/issues/70355)
  - 幫忙測試 provider RC：postgres 7.0.0rc2、sftp/ssh/airbyte 6.0.0rc1、azure 14.0.0rc1
- [Move template-field validation/transformation out of operator \_\_init\_\_ (#70296)](https://github.com/apache/airflow/issues/70296), [Provider migration for decoupling remote logging from core (#70265)](https://github.com/apache/airflow/issues/70265)

---

## June 2026

**Summary**

- PR: 5 merged
- Issue Triage / RC Testing: 1

### PR

#### Breeze / 本地開發工具

- [Fix inconsistency between generated provider docs and pyproject.toml (#68991)](https://github.com/apache/airflow/pull/68991)：`Bug 修正`
  - 第一個開的 Airflow PR！（但不是第一個 merge 的）

#### Task SDK

- [Add tests for CoordinatorManager configuration error paths (#69184)](https://github.com/apache/airflow/pull/69184)：`測試`

#### Providers

- [Parallelize per-dag auth checks in KeycloakAuthManager (#69107)](https://github.com/apache/airflow/pull/69107)：`效能`
  - multi-team 環境下 `/dags` 頁面載入很慢（[#69041](https://github.com/apache/airflow/issues/69041)），因為每個 Dag 的權限是一個一個去問 Keycloak
  - 改成平行檢查，250 個 Dags 的檢查時間：13.51s → 1.37s（mock，每個 request 模擬 50ms 延遲）

#### 文件

- [Add docker stack docs example for venv scene (#69088)](https://github.com/apache/airflow/pull/69088)：`文件`
- [Fix grammar in PR guidelines (#68992)](https://github.com/apache/airflow/pull/68992)：`文件`
  - 第一個 merge 的 Airflow PR！

### Issue Triage / RC Testing

- [Multi Team: `/dags` screen is very slow to load with multiple teams (#69041)](https://github.com/apache/airflow/issues/69041)
