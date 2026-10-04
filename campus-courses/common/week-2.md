# 第 2 週：貢獻流程

講解 1.5 小時，動手 1 小時。內容整理自社群 README 與 Slack 上大家寫過的東西，出處標在各節。

## 講解

### 1. 一次貢獻的完整流程（15 分鐘）

```
找題目 → 認領 → fork / branch → 改 + 測試 → 開 PR → review 來回 → CI 綠 → merge
```

- 每一步都有人在等你，也有你在等人。整個流程一兩週很正常，大專案一兩個月也有
- 題目來源：GitHub issues、JIRA（Apache 專案多半用這個）、mailing list 討論、flaky test、自己讀 code 發現的
- 認領：在 issue 下留言或 assign 給自己。沒人回不代表可以直接做，先問
- 一個 PR 做一件事。混兩件事的 PR 會被要求拆開
- merge 後還沒結束：release note、文件、後續 follow-up issue

### 2. 怎麼讀大型 codebase（20 分鐘）

出處：[貢獻開源專案應有的心態](../../articles/mindset-path-to-committer/README.md)（鄭黃翔、陳楷訓）

- 不要從頭讀。幾十萬行的專案沒人讀得完，maintainer 自己也只熟一部分
- 從入口進：CLI 的 main、server 的啟動類別、一個 public API
- 從測試進：unit test 是最好的使用範例，看完測試大概就知道這個模組在做什麼
- 從 issue 進：拿一個 bug，找到 stack trace 裡的檔案，從那裡往外擴
- 從 git 進：`git log -p <檔案>` 看這個檔案最近為什麼被改，`git blame` 看某一行是誰為了什麼加的，順著 PR 連結去讀當時的討論
- 容忍 black box：不懂的先當成一個函式名字，知道輸入輸出就好，需要時再進去
- 文件像字典，不是拿來背的。遇到場景再查
- 工具：IDE 的 go to definition / find usages 比 grep 快很多，大專案一定要把 IDE 的 index 建起來

### 3. 怎麼找新手題目（15 分鐘）

出處：Slack #apache-kafka、#apache-yunikorn

- 嘉平：cleanup、補測試、修文件大概是最萬用的新手友善題
- label：`good first issue`、`newbie`、`help wanted`、`starter`。Apache 專案在 JIRA 上搜 `labels = newbie`
- flaky test：Kafka 這種大專案常年有一堆，修 flaky 很容易被 PMC 注意到
- 升級帶來的驗證工作：例如 Gradle 大版本升級後有一堆 task 要測，這種事 maintainer 沒空做，很適合新手
- 自己讀 code 時看到的小問題：錯字、過時的註解、沒用到的變數、可以簡化的邏輯
- 不要一開始就挑 feature。楷訓的六階段，先把只有一種解法的事做好
- 挑到題目先在社群 Slack 頻道說一聲，mentor 會告訴你這題適不適合

### 4. 怎麼開一個好的 PR（20 分鐘）

出處：[使用 AI 工具時如何維持貢獻品質](../../articles/mindset-ai-assisted-contribution/README.md)（劉哲佑）、Slack

標題與描述
- 標題照專案慣例，Apache 專案通常是 `KAFKA-12345: 一句話說明`
- 描述寫三件事：為什麼改、改了什麼、怎麼驗證。一句話能講完就不要寫三段
- 假設 reviewer 沒耐心看長篇大論。跑過的測試不用全列，CI 會跑
- 不要有 AI 感。agent 列出的驗證步驟不要原封不動貼上

內容
- 小。能拆就拆，一個 PR 一件事
- 有測試。你必須確認改動真的解決問題，最可靠的方式就是寫測試並跑過
- 每一行 production code 自己看過、理解過。這是你的名字掛在上面
- 照專案的 code style，先跑 `spotlessApply`、`checkstyle`、`pre-commit` 之類的工具再推

review 來回
- 回覆每一個 comment，改了就說改了，不同意就說為什麼
- 不要 force push 蓋掉 reviewer 看過的 commit，加新 commit 上去，merge 前再 squash（看專案習慣）
- 被要求改不是被否定，是 reviewer 願意花時間在你身上
- CI 紅了自己先看 log，不要等 reviewer 幫你看
- 一兩週沒人理可以禮貌地 ping 一次，或貼到社群 Slack 請人幫看

### 5. 溝通禮儀（5 分鐘）

出處：[參與開源社群應有的心態](../../articles/mindset-community-etiquette/README.md)（Tingyao Huang）

- 公開頻道優先，不要私訊 maintainer 問技術問題
- 問問題帶齊：你想做什麼、你做了什麼、看到什麼結果、你預期什麼。附指令和完整錯誤訊息
- 不要問「可以問一個問題嗎」，直接問
- 用英文。寫不好沒關係，清楚比漂亮重要
- 耐心。社群還不認識你之前，禮貌和耐心是你唯一的信用
- 不要同時認領一堆 issue 然後都不動

### 6. AI 時代你要變成什麼樣的人（15 分鐘）

出處：[使用 AI 工具時如何維持貢獻品質](../../articles/mindset-ai-assisted-contribution/README.md)（劉哲佑）、[參與開源社群應有的心態](../../articles/mindset-community-etiquette/README.md)（Tingyao Huang）、Slack #general

用 AI 沒問題，社群裡大家都在用。問題是 AI 讓寫 code 變便宜之後，你的價值在哪裡。三件事：

#### 你要有能判斷 AI 真假的背景知識，所以你還是要唸書

- AI 會很有自信地給你錯的答案。看不出來的人就是橡皮圖章，其他專案已經有不少反面例子
- 判斷需要底子：作業系統、網路、分散式系統、資料結構。這些課不會因為有 AI 就不用修，反而更重要
- 哲佑：對有點規模的改動，要能判斷它對上層模組、整個 component、甚至整個架構來說合不合理。這個判斷 AI 常常給不出來
- 廷堯：你必須真正理解並掌握 AI 產出的內容，能在出現幻覺或錯誤時即時指出
- 開源是練這個能力最好的地方。每個 PR 都有人幫你驗證你的判斷對不對

#### 你要能做 AI 做不到的事：溝通、安排進度、參與技術決策

- AI 不會幫你在 mailing list 上說服一個不同意你的 PMC
- AI 不會幫你決定這個 feature 該不該做、什麼時候做、要不要先拆成三個 PR
- AI 不會幫你 follow 一個放了兩週沒人理的 PR，也不會幫你跟 reviewer 建立信任
- 這些事在開源社群裡天天發生，而且是公開的。你的每一次討論、每一個 review 都在累積別人對你的印象
- 公司裡資深工程師跟初階工程師的差別，越來越不是誰 code 寫得快，而是誰能把事情推動

#### 你要能善用 AI 給自己加分：code 更乾淨、文字更好讀

- 自己寫完再讓 AI 看一次：命名、重複的邏輯、漏掉的邊界條件。這是第二層防線，不是第一層
- PR description 和 issue 留言用 AI 潤稿，但只留重點。一句話能講完就不要三段，不要有 AI 感
- 英文不好不是藉口了。AI 幫你把意思講清楚，但意思要是你自己的
- 讀 codebase 時用 AI 問「這個函式在整個流程裡的角色是什麼」，比自己 grep 快，但要去原始碼驗證它說的
- 不夠嚴謹的 patch 會造成更長遠的 regression，你改的是很多人在用的基礎建設

#### 實務上

- 標示慣例：ASF 討論過 `Co-authored-by`（不建議用在 AI）、`Generated-by`、`Assisted-by`。看專案規定，Kafka 等專案已經有明確政策
- 很多專案開始禁止或限制 AI 生成的 PR，送之前看 `CONTRIBUTING.md`
- AI review 可以用，但不要只留一個 LGTM。親自掃過每一行還是很容易找到可以更好的地方

## 動手（1 小時）

目標：把上週的 branch 開成 PR，經歷一次 review 來回，被 merge。

### 必做

1. 同步 upstream，確認 branch 是最新的
   ```
   git fetch upstream
   git rebase upstream/main
   git push -f origin add-<你的帳號>
   ```
   這是唯一一次教你 `push -f`，PR 開了之後就不要再用

2. 開 PR 到 `opensource4you/readme`
    - 標題：`add <你的帳號> to <課程>`
    - 描述：一句話說明你是誰、哪間學校
    - 開完到 PR 頁面看 Files changed，確認只有你自己的檔案

3. 等 review，回覆，修改
    - TA 會在你的 PR 留一個 comment（可能是格式、可能是故意挑毛病）
    - 回覆 comment，照建議修改，commit，push（不用 -f）
    - 看 PR 頁面上的 commit 多了一個

4. merge 後清理
   ```
   git checkout main
   git pull upstream main
   git branch -d add-<你的帳號>
   git push origin --delete add-<你的帳號>
   ```

### 選做

5. 到這學期會上的某個專案，用 label 找三個你覺得自己做得來的 issue，把連結記在自己的 md 裡
6. 挑一個該專案最近 merge 的 PR，讀完描述和 review 對話，看看 reviewer 在意什麼

### 簽到

今天的簽到就是你的 PR 被 merge。