# Kafka 的真實案例：它在今天的資訊世界扮演什麼角色

[introduction.md](introduction.md) 用訂單 `O1001` 說明 Kafka 怎麼運作；這份講義回答另一個問題：真實的公司拿 Kafka 來做什麼？

以下案例都來自公司自己的工程部落格，或公開的客戶案例。數字以文章發表當時為準，出處列在文末。

## 課程目標

上完這堂課，希望你能：

1. 說出 Kafka 為什麼誕生，以及它想解決的問題。
2. 分辨 Kafka 在系統裡常見的四種角色，並各舉一個公司案例。
3. 理解不同公司為什麼做出不同選擇，例如要平行度還是要順序、自己維運還是交給雲端。
4. 讀到一篇新的案例時，能自己判斷 Kafka 在裡面扮演什麼角色、付出了什麼代價。

---

## 一、從 LinkedIn 開始：Kafka 為什麼誕生

Kafka 是 2010 年前後，由 LinkedIn 的 Jay Kreps、Neha Narkhede、Jun Rao 三位工程師打造的。當時 LinkedIn 的批次導向訊息系統，已經跟不上資料搬運的需求。

### 問題：每個系統都要互相串接

假設公司有 3 個產生資料的系統、3 個需要資料的系統。如果每一對都直接串接，就要 3 × 3 = 9 條管線：

```text
資料庫     ──┬──→ 搜尋索引
應用程式   ──┼──→ 推薦系統
使用者行為 ──┴──→ 資料倉儲
（每個來源都要連到每個目的地）
```

每條管線的格式、出錯方式都不同；任何一端改版，都可能弄壞好幾條。

### 解法：中間放一條共用的 Log

```text
資料庫     ──┐            ┌──→ 搜尋索引
應用程式   ──┼──→ Kafka ──┼──→ 推薦系統
使用者行為 ──┘            └──→ 資料倉儲
```

產生資料的一方只管把 Record 寫進 Kafka，需要資料的一方各自按自己的進度讀。串接數從 N × M 降為 N + M，雙方也不必知道彼此存在。

> **Kafka 最初要解決的，就是「誰要把資料送給誰」這張失控的網。**

### LinkedIn 的成長

| 時間 | 每天處理的訊息量 |
|---|---|
| 2011 年 7 月 | 約 10 億 |
| 2012 年 | 約 200 億 |
| 2013 年 7 月 | 約 2,000 億 |
| 2015 年 | 超過 1 兆 |
| 2019 年 | 超過 7 兆 |

到 2019 年，LinkedIn 維運超過 100 個 Kafka 叢集、4,000 多台 Broker、10 萬個以上的 Topic、700 萬個 Partition。用途包括使用者行為追蹤、服務之間的訊息交換、系統指標收集等。

幾個值得注意的地方：

- Kafka 後來開源，三位作者離開 LinkedIn，創立了 Confluent。
- LinkedIn 在生產環境跑的是自己維護的 release branch，名稱以 `-li` 結尾，並開源在 GitHub 上。他們特別強調這不是 fork，會盡量貼近 upstream，把修改回饋給 Apache Kafka。
- 這是一個典型的開源循環：公司在生產環境遇到問題，修好之後貢獻回社群。後面的 Uber 也是如此。

---

## 二、Kafka 的四種角色

同樣是「用 Kafka」，在不同公司裡做的事可能完全不同。這份講義把常見用法分成四種角色：

| 角色 | Kafka 在做什麼 | 案例 | 對應 introduction.md 的概念 |
|---|---|---|---|
| 服務之間的事件骨幹 | 服務發布事件，其他服務各自訂閱 | Uber、LINE | Consumer Group、Offset、Retry |
| 資料收集與整合管線 | 把資料從產生的地方，持續搬到需要的地方 | Netflix、AO.com | Producer、Topic、Retention |
| 即時串流處理 | 資料流動的同時就完成計算 | BRI（印尼人民銀行） | Key、Partition、Kafka Streams |
| Source of truth | Log 本身就是正本，其他系統是衍生出來的 | The New York Times | Retention、Replay、Partition 順序 |

真實系統常同時扮演好幾種角色。分類只是幫助你在讀案例時抓住重點。

---

## 三、角色一：服務之間的事件骨幹

在 introduction.md 裡，Checkout Service 發布 `OrderPlaced`，Payment、Email、Analytics 各自讀取。這就是「事件骨幹」：服務之間不直接呼叫，而是透過 Kafka 交換事件。

當服務的數量從 5 個變成 1,000 個，會發生什麼事？

### Uber：上千個微服務的非同步佇列

Uber 的 Kafka 每天處理數兆則訊息、數 PB 的資料，是公司技術架構的基石。超過 300 個微服務用 Kafka 做 pub-sub 佇列，五年內流量從每秒 100 萬則成長到每秒 1,200 萬則。

在這種規模下，直接用 Kafka Client 遇到了幾個問題：

1. **語言太多。** Uber 的服務用 Go、Java、Python、Node.js 寫成，每種語言都要維護一套 Kafka Client；要把 1,000 多個微服務的 Client 全部升級，得花上好幾個月。
2. **平行度被 Partition 數限制。** 很多佇列工作其實不需要順序，但一個 Consumer Group 的平行度上限仍是 Partition 數。如果處理一筆要 1 秒，想達到每秒 1,000 筆，就需要 1,000 個 Partition。
3. **一筆卡住，後面全卡住。** 同一個 Partition 必須依序處理；只要一筆訊息處理很慢，或格式錯誤、永遠失敗（稱為 poison pill），後面的訊息全部都會被擋住。

Uber 的解法是在 Kafka 和服務之間加一層 **Consumer Proxy**：

```text
Kafka ──→ Consumer Proxy ──gRPC──→ 服務實例 1
                         ──gRPC──→ 服務實例 2
                         ──gRPC──→ 服務實例 ...
```

- Proxy 負責從 Kafka 讀取，再透過 gRPC 把每一筆訊息推給服務。服務只要提供一個 gRPC endpoint，不必內建 Kafka Client。
- 一個 Partition 的訊息可以平行推給任意數量的服務實例，平行度不再受 Partition 數限制。
- Proxy 只在「一段連續的訊息都已確認」時才提交 Offset，慢的訊息不會擋住整個進度。

### Uber：Retry Topic 與 Dead Letter Queue

Uber 保險工程團隊另外設計了一套重試機制。回到 introduction.md 的例子：如果 Email Service 處理 `O1001` 一直失敗，該怎麼辦？

```text
orders ──處理失敗──→ retry-1 ──仍失敗──→ retry-2 ──仍失敗──→ DLQ
  │                    │                    │
  └ 立即繼續處理下一筆  └ 延遲一段時間再試   └ 延遲更久再試
```

- 失敗的訊息被轉寫到另一個 retry Topic，原本的 Consumer 就能提交 Offset、繼續處理下一筆。
- 每一層 retry Topic 等待的時間比上一層更久，避免對已經有問題的下游服務瘋狂重試。
- 最後一層仍失敗，就進入 Dead Letter Queue（DLQ），由人工檢查，修好後再送回重試。

> **重點：失敗的訊息不能卡住整條資料流。** 這是所有用 Kafka 做事件骨幹的系統，遲早都要面對的設計問題。

### LINE（LY Corporation）：你每天都在用的服務

LINE 和 Yahoo Japan 合併後的 LY Corporation，擁有全世界最大的 Kafka 部署之一（2026 年的數據）：

- 每天處理超過 1 兆則訊息，I/O 量約 2.6 PB。
- 尖峰時每秒 3,100 萬則訊息。
- 超過 200 台 Broker，服務超過 25,000 台 Client 主機。

用途包括服務之間的 pub-sub、日誌管線、非同步處理、遊戲資料串流，以及用 CDC 同步內部資料庫。他們的工程師形容：只要某個服務會產生資料，它多半就會經過 Kafka。

要注意：公開資料沒有說 LINE 的聊天訊息本身是透過 Kafka 傳送。Kafka 支撐的是服務背後的資料流動，不是取代聊天伺服器。讀案例時，分清楚「公司用 Kafka」和「這個功能用 Kafka」是兩回事。

---

## 四、角色二：資料收集與整合管線

第二種角色是把資料從產生的地方，持續、即時地搬到需要的地方，取代每晚跑一次的批次匯出。

### Netflix：Keystone 資料管線

Netflix 在 2016 年公開了 Keystone，一套統一收集、發布與路由事件的基礎設施，同時供應批次與串流處理：

- 每天平均收進超過 7,000 億則訊息。
- 36 個 Kafka 叢集、超過 4,000 個 Broker 實例。

Netflix 把 Kafka 叢集分成兩層：

```text
幾乎所有應用程式 ──→ Fronting Kafka ──→ 路由層 ──→ Consumer Kafka ──→ 即時處理
                    （只收資料，         │
                     不給一般程式讀）     └──────→ Hive、Elasticsearch
```

- **Fronting Kafka** 只負責接收資料與緩衝，不讓一般應用程式從這裡讀取，讓負載可以預測。
- **Consumer Kafka** 只放需要即時處理的 Topic 子集。

另一個值得討論的數字：當時每天的資料遺失率低於 0.01%。這表示 Netflix 接受極少量的遺失。對使用者行為紀錄來說，這是合理的取捨；但對付款資料，就不能這樣設計。introduction.md 第六章談的 `acks`、Replication 等可靠性設定，在真實世界就是這樣依資料的重要性做選擇。

### AO.com：從 CDC 到即時個人化

AO.com 是英國的家電電商，在英國和德國銷售超過 9,000 種商品。他們把 Kafka 用在兩個階段：

**第一階段：用 CDC 把舊系統的資料變成事件流。** Change Data Capture（CDC）讀取資料庫自己的交易紀錄，把每一次 INSERT、UPDATE、DELETE 轉成 Record 寫進 Kafka。這樣不必改動訂單系統的程式，也不會對資料庫多下查詢。產生的 raw 與 enriched Topic 再透過 Connector 送到下游服務，以及給資料科學家研究用的 S3。

**第二階段：用網站的點擊流做個人化。**

```text
網站點擊 ──→ raw Topic ──Kafka Streams 補上客戶資料──→ enriched Topic
                                                         │
                       ┌─────────────────────────────────┤
                       ▼                                 ▼
             後端服務（Lambda／MongoDB／S3）        送回網站，即時調整頁面
```

「補上客戶資料」在技術上是把點擊事件和客戶資料表做 join：每來一筆點擊，就用客戶 ID 去查客戶的歷史資訊，附加上去。根據 AO 的 A/B 測試，這套個人化讓轉換率最多提升 30%。

這個案例有兩個重點：

- **保留 raw Topic。** 如果之後發現 enrichment 的邏輯有錯，可以修好程式後從 raw Topic 重跑。這正是 introduction.md 說的 Replay。
- **資料一旦變成事件流，其他團隊就會加入。** AO 提到，資料變成事件流之後，又有兩三個團隊開始使用。這就是第一章 N + M 的實際效果。

---

## 五、角色三：即時串流處理

前兩種角色裡，Kafka 主要負責「搬」。第三種角色是在資料流動的同時就完成計算，讓系統在事件發生的當下做出反應。

### BRI（印尼人民銀行）：即時詐欺偵測與信用評分

BRI 是印尼最大的銀行，約有 7,500 萬名客戶。他們以 Kafka 為基礎建立事件驅動架構，用在：

- 即時信用評分
- 詐欺偵測，例如標記異常交易、凍結被盜用的卡片
- 商家評估
- 預警系統：在違約發生之前，找出可能還不出錢的客戶

詐欺偵測是用 KSQL 搭配 Kafka Streams 做即時處理。BRI 表示，詐欺偵測過去要兩週到一個月，現在可以即時完成。

### 串流處理在算什麼

- **無狀態運算**一次只看一筆，例如「金額超過 10 萬就標記」。
- **有狀態運算**需要記住過去，例如「同一張卡 1 分鐘內刷了 5 次以上」。這需要時間視窗、聚合與狀態儲存，通常交給 Kafka Streams、ksqlDB 或 Apache Flink 這類框架。

以 Kafka Streams 為例，「同一張卡 1 分鐘內超過 5 筆交易就送出警示」大致長這樣：

```java
StreamsBuilder builder = new StreamsBuilder();

builder.stream("transactions", Consumed.with(Serdes.String(), Serdes.String())) // Key = 卡號
       .groupByKey()
       .windowedBy(TimeWindows.ofSizeWithNoGrace(Duration.ofMinutes(1)))
       .count()
       .toStream()
       .filter((windowedCard, count) -> count > 5)
       .map((windowedCard, count) -> KeyValue.pair(windowedCard.key(), count + " 筆交易"))
       .to("fraud-alerts", Produced.with(Serdes.String(), Serdes.String()));
```

用卡號當 Key，同一張卡的交易就會進同一個 Partition（introduction.md 第三章），計算才會正確。

### 讀客戶案例要注意的事

BRI 的案例是 Confluent 發布的客戶故事，內容大多引述 BRI 主管的說法，數字是自行回報、沒有經過第三方驗證。這不代表內容不可信，但讀這類文章時，要分清楚「技術架構的描述」和「行銷性質的成效數字」。

---

## 六、角色四：Source of truth（The New York Times）

前三種角色裡，資料的正本都在別處，Kafka 只是傳遞或加工的通道。第四種角色反過來：**Kafka 本身就是正本**，其他系統只是由它衍生出來的視圖。

### 紐約時報的 Publishing Pipeline

紐約時報有好幾套內容管理系統，還有 161 年的報導存檔。搜尋、個人化推薦、網站與 App 前端，都需要讀取這些已發布的內容。

過去的做法是每套系統各自提供 API。這些 API 由不同團隊在不同時期開發，欄位名稱與格式都不一致；想一次讀取全部歷史內容，也非常困難。

2017 年，他們公開了改以 Kafka 為核心的新架構：

```text
各內容管理系統 ──→ Gateway（檢查格式）──→ Monolog（單一 Partition）
                                            │
                                            ▼
                               Denormalizer（Kafka Streams）
                                            │
                                            ▼
                               Denormalized log（多個 Partition）
                                            │
                          ┌─────────────────┼─────────────────┐
                          ▼                 ▼                 ▼
                    搜尋（Elasticsearch）   網站前端         個人化推薦
```

- **Monolog** 收錄自 1851 年以來發表的所有內容，依發布時間排序，**永久保存**。
- Monolog 只有**一個 Partition**。因為文章會引用圖片、作者等其他內容，必須保證讀者一定先讀到被引用的內容，才讀到引用它的文章。單一 Partition 只能放在一顆磁碟上，但他們的全部內容不到 100 GB，不成問題。
- 不需要全域順序的用途，由 Denormalizer 把每篇文章和它引用的內容打包，寫進另一條**有多個 Partition** 的 log，讓下游平行讀取。

### 為什麼可行

新的系統上線時，從 Offset 0 開始讀，就能建出完整的歷史資料。搜尋索引要改格式時，不必修改正在運作的索引，而是建一個新的，從頭 Replay 一次，追上即時資料後再切換。

紐約時報也說明了為什麼當時不選 Google Pub/Sub、AWS SQS 或 Kinesis：他們需要 **永久保存** 與 **有順序的讀取**，當時只有 Kafka 同時滿足這兩點。

> **需要順序的地方犧牲平行度，不需要的地方再把平行度拿回來。**

這個設計的思想來自 Martin Kleppmann 的〈Turning the database inside-out〉，在他的著作 *Designing Data-Intensive Applications* 中有更完整的討論。

---

## 七、自己維運，還是交給雲端？

讀到這裡，你可能注意到兩類公司：

| | LinkedIn、Uber、LINE、Netflix | Michelin、AO.com |
|---|---|---|
| 怎麼運作 Kafka | 自己維運大型叢集 | 從自己維運改用雲端託管 |
| 原因 | 規模極大，基礎設施本身就是核心能力 | 想把人力放在業務上，而不是叢集維運 |
| 和社群的關係 | 常把改進貢獻回 Apache Kafka | 主要是使用者 |

### Michelin：從批次報表到即時庫存

米其林原本的庫存報表不可靠、資料過時，影響全球供應鏈。米其林的主管形容舊系統「非常批次、非常 monolithic」。

他們先在自己的機房架設 Kafka，但需要三名全職員工維護叢集，仍然難以擴展。2021 年，他們遷移到雲端託管的 Kafka 服務，估計營運成本比自己維運省下 35%。他們也用 Kafka 和 Kafka Streams 改寫了原本的流程編排系統，把 monolith 拆成可以各自演進的服務。

AO.com 也有類似經驗：自己維運時，一次 Broker 故障最多要三天才能解決。

### 大公司把改進貢獻回社群：Tiered Storage

自己維運的大公司，常會遇到別人還沒遇到的問題。Uber 發現，Broker 要把所有資料存在本機磁碟，想保留更久就只能加機器。他們提出 **KIP-405 Tiered Storage**：較新的資料留在 Broker 本機，較舊的搬到 S3、HDFS 這類便宜的遠端儲存。

這項設計和 Apache Kafka 社群一起完成，在 Kafka 3.6.0 以 early access 形式釋出，之後正式可用。現在所有 Kafka 使用者都能受益。這就是課程第 1 週講的開源循環：公司遇到問題、在社群提出設計、一起實作，最後回到每個人手上。

### Kafka 今天的位置

- **Kafka 的通訊協定已經成為業界標準介面。** 除了 Apache Kafka 本身，許多雲端廠商與公司提供相容的託管服務或實作。
- **和其他系統結合。** 搭配 Apache Flink 做複雜的串流計算；把 Topic 直接轉成 Apache Iceberg 這類開放表格格式，讓分析工具查詢串流資料。
- **AI 需要即時資料。** BRI 的詐欺偵測就是一例：串流處理即時算出特徵，交給模型判斷。推薦系統與 AI agent 也需要知道「現在正在發生什麼」，而不只是昨天的報表。

不管用在哪裡，Kafka 做的事其實和 2010 年一樣：讓需要資料的系統，即時拿到來源系統產生的事件。

---

## 八、怎麼讀一篇 Kafka 案例

introduction.md 最後給了三個問題：在哪裡、為什麼、有什麼限制。讀案例時，可以換成這四個問題：

1. **原本的痛點是什麼？** 批次太慢、串接太多、服務耦合、還是維運成本？
2. **Kafka 扮演哪一種角色？** 事件骨幹、整合管線、串流處理，還是 source of truth？
3. **付出了什麼代價？** 例如紐約時報放棄平行度換順序、Netflix 接受極少量遺失、Uber 多維護一層 Proxy。
4. **這篇文章是誰寫的？** 公司自己的工程部落格通常有較多技術細節；廠商的客戶案例則偏重成效，數字多為自行回報。

這四個問題也可以直接用在作業的「專案在解什麼」。

---

## 九、討論題

1. Uber 加了 Consumer Proxy 讓平行度不受 Partition 數限制，紐約時報卻刻意只用一個 Partition。兩者的需求差在哪裡？各自放棄了什麼？
2. Netflix 接受每天低於 0.01% 的資料遺失。請舉一種可以接受遺失的資料，以及一種絕對不行的資料，並說明 Kafka 的設定應該有什麼不同。
3. 你的學校選課系統在開放選課的第一分鐘會湧入大量請求。如果用 Kafka 重新設計，你會建立哪些 Topic？Key 要選什麼？它主要扮演哪一種角色？
4. 紐約時報永久保存所有內容。如果一間公司用同樣的方式保存使用者的個人資料，會遇到什麼問題？提示：想想個資法規要求的「刪除權」。
5. 挑一個你常用的服務（外送、叫車、社群、串流影音），推測它背後可能有哪些事件流，分別對應這份講義的哪一種角色。
6. 為什麼 Michelin 選擇託管服務，而 Uber 選擇自己維運，還把 Tiered Storage 貢獻回社群？

---

## 延伸閱讀

- Jay Kreps，[The Log: What every software engineer should know about real-time data's unifying abstraction](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying)：Kafka 作者之一對 Log 抽象的經典說明，是第一章的理論基礎。
- Martin Kleppmann，[Turning the database inside-out with Apache Samza](https://martin.kleppmann.com/2015/03/04/turning-the-database-inside-out.html)：第六章 source of truth 模式的思想來源。
- [KIP-405: Kafka Tiered Storage](https://cwiki.apache.org/confluence/display/KAFKA/KIP-405%3A+Kafka+Tiered+Storage)：看一個真實的 KIP 長什麼樣子，從動機、設計到相容性討論。

## 資料來源

- LinkedIn：[How LinkedIn customizes Apache Kafka for 7 trillion messages per day](https://engineering.linkedin.com/blog/2019/apache-kafka-trillion-messages)（2019）、[How We're Improving and Advancing Kafka at LinkedIn](https://engineering.linkedin.com/apache-kafka/how-we_re-improving-and-advancing-kafka-linkedin)（2015）、[Kafka Tops 1 Trillion Messages Per Day at LinkedIn](https://bigdatawire.com/2015/09/02/kafka-tops-1-trillion-messages-per-day-at-linkedin)（BigDATAwire，2015）
- Uber：[Enabling Seamless Kafka Async Queuing with Consumer Proxy](https://www.uber.com/blog/kafka-async-queuing-with-consumer-proxy/)、[Building Reliable Reprocessing and Dead Letter Queues with Apache Kafka](https://www.uber.com/blog/reliable-reprocessing/)、[Introduction to Kafka Tiered Storage at Uber](https://www.uber.com/blog/kafka-tiered-storage/)
- LINE（LY Corporation）：[How LY Corporation uses ClickHouse to observe one of the largest Kafka deployments on earth](https://clickhouse.com/blog/line-yahoo)（ClickHouse，2026）
- Netflix：[Kafka Inside Keystone Pipeline](https://netflixtechblog.com/kafka-inside-keystone-pipeline-dd5aeabaf6bb)（2016）
- AO.com：[Confluent 客戶案例](https://www.confluent.io/customers/ao/)、[How Confluent data in motion tech is giving AO.com the personal touch](https://www.computing.co.uk/sponsored/4036055/confluent-motion-tech-giving-ao-com-personal-touch)（Computing）
- BRI：[Confluent 客戶案例](https://www.confluent.io/customers/bri/)
- The New York Times：[Publishing with Apache Kafka at The New York Times](https://www.confluent.io/blog/publishing-apache-kafka-new-york-times/)（2017）
- Michelin：[Confluent 客戶案例](https://www.confluent.io/customers/michelin/)
