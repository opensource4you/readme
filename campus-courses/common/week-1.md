# 第 1 週：開源入門

講解 1.5 小時，動手 1 小時。這週的目的是讓學生想參與，不是教知識。三個重點：開源是什麼、開源跟軟體圈的關係、參與開源對你的好處。

## 講解

### 1. 什麼是開源（20 分鐘）

- 原始碼公開，任何人可以看、改、散布，條件寫在授權裡
- 不等於免費、不等於沒人負責、不等於業餘。Linux kernel 的貢獻者大多是拿薪水做的
- 一個開源專案長什麼樣：repo、issue、PR、mailing list、release、一群 maintainer
- 授權只要知道兩類
    - 寬鬆：Apache 2.0、MIT。拿去用、改、閉源都行
    - Copyleft：GPL。改了要用同樣授權釋出
    - 看一個專案先看 `LICENSE`
- 基金會：讓專案不屬於單一公司。ASF（Kafka、Airflow、Ozone、YuniKorn）、Linux Foundation / CNCF（Kubernetes、Ray、Flyte）
- ASF 的角色階梯：user → contributor → committer → PMC member → ASF member
    - committer 有 commit 權限，由 PMC 投票產生
    - 所有決定公開在 mailing list，投票 +1 / 0 / -1
- 社群怎麼運作：非同步、公開、信任慢慢累積。maintainer 分散在各時區，等一兩天很正常

### 2. 開源跟軟體圈的關係（25 分鐘）

你用的每一樣東西都站在開源上
- 作業系統：Linux、Android
- 語言與工具：Python、Go、Rust、Node.js、Git、VS Code
- 基礎設施：Kubernetes、Kafka、PostgreSQL、Nginx
- AI：PyTorch、vLLM、SGLang、Hugging Face 上的模型

公司為什麼做開源
- 分攤維護成本：Kafka 由 Confluent、LinkedIn、Apple、Uber 等共同維護，沒有一家付得起全部
- 建立標準：Kubernetes 開源後成為容器調度的事實標準，Google 沒有獨佔但贏了整個生態
- 招人：看得到你的 code 比履歷可信
- 賣周邊：託管服務、支援、企業版。Confluent、Databricks、Red Hat 都是這條路

開源怎麼變成職涯
- 大公司的 infra 團隊很多本身就是開源 maintainer，Kafka、Spark、Ray 的核心團隊都在公司裡
- 面試時 GitHub 上的 PR 是最有說服力的作品集，reviewer 的對話就是推薦信
- 社群成員的例子（README「參與者心得」）
    - 透過貢獻 KubeRay 拿到 Anyscale 美國遠端職缺
    - 從 Flyte 貢獻到加入 Union.ai
    - 透過社群找到 Samsara 工作
    - 歷史系轉職，從開源貢獻走進 Apache
- 台灣的狀況：很會用，很少人參與維護。Kafka、Kubernetes 這些關鍵基礎建設有一大群人在維護，只是裡面很少台灣人。這是問題，也是機會

AI 時代開源更重要
- AI 工具降低了讀大型 codebase 的門檻，以前要花幾週摸熟的專案現在幾天就能上手
- 但 AI 生成的 PR 氾濫，maintainer 更看重「真的理解自己在改什麼」的人
- 會用 AI 又懂得負責的貢獻者，現在是稀缺資源

### 3. 參與開源對你的好處（35 分鐘）

這節要具體，講數字和連結。

#### 直接拿得到的東西

TAIONE 開源基金會 Fellowship（AI 開源新勢力）
- 做開源領獎學金。資格：台灣人、30 歲以下，不限學生
- 由各 track 的 lead 提名，提名寫在 `opensource4you/readme` 的 `fellowship/<track>/<github-id>.md`
- 一般等級每月 NT$20,000，特別強的月份 NT$30,000
- 不是比賽，是「你貢獻到一定程度，mentor 就幫你提」
- 詳見 taione.org 的 Strategic Tracks 頁面

Claude for Open Source
- Anthropic 給開源 maintainer 的免費方案，一段時間的付費等級額度，內容以官網為準：claude.com/contact-sales/claude-for-oss
- Apache committer 用 apache.org 信箱就能申請，非 committer 的 maintainer 也可以，門檻是專案規模
- 社群裡已經有一批人拿到

JetBrains 全產品授權
- ASF committer 可以免費申請 JetBrains All Products Pack（IntelliJ、PyCharm、GoLand 等）
- 很多開源專案本身也可以申請專案授權給 active contributor

其他
- GitHub Student Developer Pack：Copilot 等一堆工具免費，學生身分就能拿
- Apache 的 Community Over Code 研討會，committer 免費入場，有 Travel Assistance 補助機票住宿
- LFX Mentorship、Google Summer of Code：有薪的開源實習，三個月幾千美元，社群裡有人做過 LFX
- IT Matters Awards 有開源貢獻獎，社群每年有人得獎

#### 正在醞釀的

ALC Community Recognition
- ALC Taipei 正在向 ASF ComDev 提案一個官方頁面，由各地 ALC 分會每年推薦「幫助新人變成真正貢獻者」的人，列在 apache.org 上
- 不是給 commit 最多的人，是給帶人的人。你如果在社群裡幫忙帶新手、review 新手的 PR，這就是你的
- ALC Taipei 會是第一批推薦，提案通過後細節會在社群公告

#### 不是錢但更值錢的

- 一個全世界都看得到的作品集。你的 PR、你的 review、你跟 maintainer 的對話，面試官看得到
- 跟世界級工程師一起工作。review 你 code 的人可能是這個領域寫教科書的人，這在學校和多數公司都碰不到
- 真實的工程訓練：讀大型 codebase、寫測試、被 review、處理 CI、跨時區溝通。這些課堂教不了
- 社群人脈。源來適你兩年內出了 5 位 Kafka committer，Airflow、Ozone、Flyte、YuniKorn、Gravitino 都有 committer 甚至 PMC。這些人就在 Slack 上
- 一條不用刷題的路。這條路人少，但走通的人都走得很遠

#### 誠實的部分

嘉平在 README 寫的
- 開源不是顯學。對學生而言，乖乖刷題面試才是最常見的路
- 這裡有成功的案例，也有更多放棄的案例。適不適合，最後取決於你
- 社群很被動。你很積極 mentor 才會很積極
- 社群的目標不是讓眾人成就社群，而是讓社群幫助個人

楷訓 mentor 過 20+ 人的經驗
- 從修 doc、加 test 開始，品質要高，慢慢累積信任
- 真的自己從零走到 committer 的只有一位，其他人是 mentor 幫忙跳過前面幾步。這就是社群存在的意義
- 「我要先學完 XXX 才能開始」是學生習慣，對開源沒用。容忍 black box，找你現在就能貢獻的地方

### 4. 這門課怎麼上（10 分鐘）

- 16 週：前兩週通用，之後每個技術兩週，最後兩週期末報告
- 每堂 1.5 小時講解、1 小時動手
- 所有繳交都是維護自己的一個 md，用 PR 交
- 評分：簽到 20%、作業 60%、期末 20%
- 不強迫貢獻。想繼續的進 Slack 頻道，有人接。上面講的好處，門就在那裡
- 環境自己課前裝好，課堂上不處理

## 動手（1 小時）

目標：每個人有一個能用的 GitHub 帳號和本機 git 環境，並且在自己的 fork 上完成第一個 commit。

### 必做

1. GitHub 帳號與 SSH key
   ```
   ssh-keygen -t ed25519 -C "你的 email"
   cat ~/.ssh/id_ed25519.pub
   ```
   貼到 GitHub Settings → SSH and GPG keys，然後測試：
   ```
   ssh -T git@github.com
   Hi <你的帳號>! You've successfully authenticated
   ```

2. git 基本設定
   ```
   git config --global user.name "你的名字"
   git config --global user.email "你的 email"
   ```
   email 要跟 GitHub 帳號的一致，commit 才會算到你頭上

3. Fork `opensource4you/readme`，clone 到本機
   ```
   git clone git@github.com:<你的帳號>/readme.git
   cd readme
   git remote add upstream git@github.com:opensource4you/readme.git
   ```

4. 開 branch，建立自己的檔案，commit，push 到自己的 fork
   ```
   git checkout -b add-<你的帳號>
   mkdir -p campus-courses/submissions/<課程>
   printf '# <你的帳號>\n\n學校：<你的學校>\n' > campus-courses/submissions/<課程>/<你的帳號>.md
   git add .
   git commit -m "add <你的帳號>"
   git push -u origin add-<你的帳號>
   ```
   push 完到 GitHub 上看自己的 fork 有沒有這個 branch。今天不開 PR，下週開。

### 選做

5. 加入源來適你 Slack（opensource4you.tw/slack/join），在 #general 自我介紹一句
6. 挑一個這學期會上的專案，去 GitHub 看它最近 merge 的三個 PR 是誰開的、哪間公司的人 review 的
7. 申請 GitHub Student Developer Pack

### 簽到

今天的簽到不用 PR，TA 直接看你 fork 上有沒有 `add-<你的帳號>` branch。