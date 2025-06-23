# 2025.06.12 Kafka 相關饅頭營

https://www.facebook.com/share/p/16nCK8H4Rr/
https://www.facebook.com/share/p/16q7im9pCY/
https://www.facebook.com/share/p/16UZnUkSKy/

---
## what is opensource4you

#### 講者
Chia-Ping Tsai

#### 投影片
https://github.com/opensource4you/readme/blob/main/slides/os4y.pdf

---
## Beyond complexity: introducing Strimzi for Simplified Kafka Operations

#### 講者
Luke

#### 投影片
https://docs.google.com/presentation/d/1nhcXox_NIUuKk3q2bk40wgvDBNsMNTa-lp7TptdbdnI/edit?slide=id.g365a1d17877_0_5#slide=id.g365a1d17877_0_5

#### Q&A 紀錄

| 序列 | 提問者 | 問題 | 答覆 |
|---|---|---|---|
| 0 | 嘉平 | 為啥捐給 CNCF？選基金會的依據？ | 因為主要是瞄準 Kafka over K8S，所以選 **CNCF**。商業面的原因不清楚。 |
| 1 | Robin Han | Strimizi 和 Bitnami Helm Chart 的定位有什麼差異嗎？ | 目標都一樣，都是要把 Kafka 跑在 K8S 上。但感覺對方好像只是一個人，差別的定位不太清楚。現況是雙方都有各自的客戶，沒有多去做比較。 |
| 2 | 嘉平 | Strimizi 要在 CNCF 畢業怎麼這麼難？ | **CNCF Landscape** 中畢業的專案真的不多，只要看大圈圈就好。細節不清楚，但就 Luke 觀察下來，**CNCF 畢業門檻真的很高**。詳細可參考：[https://landscape.cncf.io/](https://landscape.cncf.io/) |
| 3 | 嘉平 | 畢業有什麼特殊意義嗎？ | 專案畢業能讓使用者更容易知道，**未畢業前不會被 Highlight 出來**。另一方面，企業要採用也會更安心。 |
| 4 | 嘉平 | 客戶會因為還沒畢業覺得 not good 嗎？ | Luke 沒有聽到過類似說法，但不確定老闆們的想法。 |
| 5 | 嘉平 | 轉到 incubating phase 有什麼條件？ | 會需要列出一些數字，其中一項是要證明**成員來自不同的公司**、**Adopter 至少要有幾間**、**Committer 要分散在不同公司**。 |
| 6 | 嘉平 | 最難達成的是哪個？ | 最難的是 **Contributor**，要讓他們一直活躍，但又能來自不同公司，非常困難。 |
| 7 | 楊承昊 | Adopter 跟 Contributor 差異在哪？ | **Powered by** 就是 **Adopter** 的意思，他們不會貢獻，只會使用。參考：[https://kafka.apache.org/powered-by](https://kafka.apache.org/powered-by) |
| 8 | 楊承昊 | 不會 Kafka 的有機會嗎？ | 當然會是最好，不過不會也有機會，我們不會講死，只要你是 Contributor 我們就不會在意這麼多。 |
| 9 | 嘉平 | 貢獻到成為資深 Maintainer 的話對求職有幫助嗎？ | 目前團隊中有人是懂 Strimizi 就來了。在 **KubeCon** 也會有 **Maintainer Section**，會比較容易被選上去發表。補充：還有 Co-located Event。 |
| 10 | 嘉平 | 如果進 Red Hat 就讓畢業更困難了？ | 先達成第一步再想後面的。 |
| 11 | Jay Chang | 學生沒經驗推薦嗎？ | 不論年齡階段都歡迎。之前有個梗圖是「有一隻貓在打字。」可以先解 **good first issue**，在 GitHub Issues 應該可以找到初學者適用的 Label。 |
| 12 | Chung En Lee | K8S 有一些虛擬層（ex: volume），Storage 相關的有耳聞會很挑戰，Strimizi 會有什麼困難嗎？ | Kafka 現在只支援 **Block Storage**。Strimizi 也鼓勵說基本的 Disk 是最好用的，目前沒有多著墨，就是用 K8S 基本的去使用。更進一步的設定，團隊上沒有專攻這方面的，也沒有花太多時間在上面。 |
| 13 | Chung En Lee | 虛擬機是隨機 Mount 還是指定？ | 都可以設定，由使用者去選擇。 |

---
## Streaming Files through Kafka

#### 講者
Mark

#### 投影片
https://github.com/opensource4you/readme/blob/main/slides/File%20Streaming%20-%20Streamsend%20Uploader%20%26%20Downloader.pdf

#### Q&A 紀錄

| 序列 | 提問者 | 問題 | 答覆 |
|---|---|---|---|
| 0 | Stan Hsu | How does the producer convert txt or pdf into message? Line by line or bytes by bytes? (Producer 怎麼把 txt 或 pdf 轉換成 Kafka Message？逐行還是逐位元組？) | **DGAF**, it doesn't care about the content. It just **chunks the file and sends** it. |
| 1 | Stan Hsu | How can the consumer avoid OOM when merging files? (Consumer 如何在合併檔案時避免 OOM？) | **Allocate swap**. |
| 2 | Robin Han | How about upload the file to S3 and send message with the object path to Kafka? (如果把檔案上傳到 S3，然後再把 S3 物件路徑作為訊息發送到 Kafka 呢？) | This is a **locker room pattern**. If you can do it this way, it makes sense. This scenario applies if you already have an object store. |
| 3 | 致遠 | How does the downloader handle duplicated chunks? (Downloader 如何處理重複消費的 chunk？) | Uses a **Chunk ID**; this serves for de-duplication. |
| 4 | 嘉平 | How do you handle big size files? (如何處理大檔案？) | The downloader will know the size of the file. As the downloader writes files, the **read chunk will be deleted** at the same time. |
| 5 | 嘉平 | All chunks in the same partition? (所有 chunk 都在同一個 Partition 嗎？) | For **single-thread mode**, all chunks will be in the **only one partition**. For **multi-thread**, there will be a chunk set in many partitions. |
| 6 | inayathulla khan | Is batch size is set to 1 to avoid duplicate issues? (Batch Size 是設定成 1 來避免重複性的問題嗎？) | No specific answer was recorded for this question. |
| 7 | inayathulla khan | What will happen if I add many files to a single partition? Does it cause rebalancing issue? | It always uses the **first partition**, it doesn't rely on a key, and the mechanism will **guarantee there will be no rebalancing issue**. |
| 8 | inayathulla khan | Why Rust? (為什麼選擇 Rust？) | My friend said it is cool. |

---
## The challenges of building Kafka on top of S3

#### 講者
韩旭

#### 投影片
https://github.com/opensource4you/readme/blob/main/slides/automq_challenges_on_s3.pdf

#### Q&A 紀錄

| 序列 | 提問者 | 問題 | 答覆 |
|---|---|---|---|
| 0 | 龔宣璋 | Broker 是完全無狀態的嗎？我的理解 AutoMQ 的分層和 RemoteStorageManager 沒關係，在這點上有考慮嗎？ | 是完全 **Stateless**。寫入後資料會**馬上到 S3**。 |
| 1 | 嘉平 | 流量管理是動態做調整的嗎？ | 會提供網路代管的**限流器**。 |
| 2 | 嘉平 | 限流器是否使用 Kafka Quota？ | **獨立於 Kafka 外**的機制。 |
| 3 | 嘉平 | 寫入 Parquet File，寫入 Kafka Topic 後多久寫入 Parquet File？如果產生很多 Small File 該如何處理？Schema Registry 是哪個版本？ | 簡單版本：設定 **1 分鐘**後，廣播到 Worker，開始寫入數據到 Parquet File。即時性高但 Small File 很多，後續再合併，客戶負擔最佳化的成本。**Schema Registry 是 Confluent 的版本**，使用者自備。 |
| 4 | 嘉平 | AutoMQ 對 KIP-1150，如果接受的話對公司發展有什麼影響？同步 Upstream 的 Code 麻煩嗎？Cross AZ 流量問題上，Tier Storage 是否已解決？ | 影響不大，KIP-1150 沒有徹底解決 Stateless 問題。因為是另一套，只關注 Log 的調整，KIP-1150 技術上還有很多挑戰。並未解決，資料已複製完畢，流量已產生。**跨區流量費用遠遠高於計算成本**，這並非 Kafka 架構問題，而是 Cloud Provider 的計費問題。 |
| 5 | 嘉平 | AutoMQ 除了 S3 還有其他賣點嗎？ | 商業版的部分沒有聽到相關說明。 |
| 6 | Stan Hsu | Metadata 會不會很難實作？畢竟有多層 Cache，Consumer 來讀的時候必須知道在哪個 Broker Memory 或 SSD 或 Object Path？ | Metadata 相容 S3。Consumer 讀 Log Cache，**冷讀才需要讀取 Metadata**。 |
| 7 | 龔宣璋 | 最開始設計的時候就完全沒考慮 RemoteStorageManager 這個接口嗎？ | 最早設計就是要用 **Object Storage**。Tier Storage 的限制是最新資料在本地。 |
| 8 | Lan Ding | AutoMQ 的 Compaction 邏輯和 Compact Topic 的 Compact 邏輯有做什麼兼容優化嗎？對於 Compact Topic 是需要 Compaction 時讀寫 S3 一次，做 Compact 時又讀寫 S3 一次，會帶來三倍的讀寫放大嗎？ | 沒有修正，不需特別優化，Kafka 是刪除再創建，AutoMQ 把 Object 合併。**讀寫放大可接受**。 |
| 9 | Lan Ding | AutoMQ 是如何解決高基數問題的？例如現在有 10000 分區，AutoMQ 定期做一次 Compaction，數據可能在 S3 內的組織仍然比較鬆散，會有讀放大嗎？預讀是不是也不生效了？就等於限制了單節點承載的分區數上限？ | 一個節點不超過 **4-5 萬個分區**，有 Index 去找數據，放大不用擔心。其實沒有限制，但有建議。分區過多會影響 Compaction 效能。 |
| 10 | Lan Ding | 最後一個問題，Broker Fail-over 的時候，會利用磁碟的多掛載能力把盤掛載到其他 Broker 上，進行數據恢復，這中間應該是需要調用雲廠商的卸載、掛載接口，這個 RTO 會不會比較高？ | Kafka Fail-over 大約 **40-50 秒**。S3 大約 **20 秒**。依照設計的面向，Timeout 過短也有缺點。 |
| 11 | 嘉平 | Broker Fail 是否有更好的偵測方法？在 Client 的 Code 是否能更新與調整？ | 在 **Client 上偵測才有效**。Kafka 面向大流量但延遲較不影響的場景。 |

---
## Building a Custom Linux CPU Scheduler with sched_ext

#### 講者
Eric Chou, Po-Ying Chiu

#### 投影片
https://docs.google.com/presentation/d/1eUl2n3l1ebOyGZUol6a9-mgg0EP2nrobF3tOphjPiVg/edit?usp=sharing