# Kafka 的技術名詞講解與概覽

## 課程目標

上完這堂課，希望你能理解：

1. Kafka 是什麼？系統為什麼會需要它？
2. Producer、Consumer、Broker 分別在做什麼？
3. Topic、Partition、Key、Offset 之間是什麼關係？
4. Consumer Group 如何分工？
5. Kafka 如何保存資料、處理故障？
6. 一筆 Event 從產生到被處理，中間發生了什麼？

---

## 一、Kafka 是什麼？為什麼需要 Kafka？

> Kafka 是一個開源的分散式事件串流平台，用來擷取、儲存與處理持續產生的資料。

第一次看到「分散式事件串流平台」，可能還是不知道 Kafka 在做什麼。我們先拆成兩個概念。

### Distributed System：分散式系統

分散式系統讓多台電腦透過網路協作，完成同一套系統的工作。

Kafka 不需要把所有資料放在同一台機器上。資料可以分散到不同伺服器；當其中一台故障時，系統也能透過備份資料與故障切換，盡量維持服務。

因此，後面會談到：

- Replication：複寫
- Leader／Follower：主要副本與跟隨副本
- Failure：故障
- Retry：重試

這些都是分散式系統常見的問題與設計，並非 Kafka 獨有。

### Event Streaming：事件串流

**Event 是對已發生事情的紀錄。**

例如：

- `PlaceOrder` 比較像 Command：「請建立這張訂單」。
- `OrderPlaced` 是 Event：「這張訂單已經成立」。

其他 Event 可能有：

```text
OrderPlaced
PaymentCompleted
InventoryReserved
OrderShipped
```

當系統持續產生這類紀錄，就形成 Event Stream。

可以先粗略理解成：

> 不同程式持續把資料寫進 Kafka；Kafka 將資料保存一段時間；其他程式再按自己的進度讀取與處理。

Kafka 中的一筆資料也可以是日誌、狀態變更或其他訊息，不一定都要是業務事件。這堂課主要用 Event 當例子。

### 先看整張圖

先不用理解所有名詞。這堂課會逐步解釋下面這張圖：

```text
Producer
   │ 寫入 Record
   ▼
orders Topic
   ├── Partition 0 ── Leader 在 Broker A，Follower 在 B、C
   ├── Partition 1 ── Leader 在 Broker B，Follower 在 A、C
   └── Partition 2 ── Leader 在 Broker C，Follower 在 A、B
   │
   ▼
Consumer Group 按照 Partition 分工讀取
```

Topic 是資料的邏輯分類；實際資料位於 Partition。每個 Partition 的副本可以分布在多個 Broker 上。

接下來我們會回答：

```text
誰把資料送進來？         → Producer
誰保存資料？             → Broker
資料放在哪裡？           → Topic／Partition
一筆資料是什麼？         → Record
怎麼標示資料的位置？     → Offset
誰來讀資料？             → Consumer
多個 Consumer 怎麼分工？ → Consumer Group／Share Group
Broker 故障怎麼辦？      → Replication
```

### 用下單流程理解 Kafka 的用途

假設電商有一個 Checkout Service。訂單成立後，其他服務可能需要知道這件事：

- Payment Service：處理付款
- Inventory Service：保留庫存
- Email Service：發送通知
- Analytics Service：更新統計

基本作法是 Checkout Service 逐一呼叫這些服務。

如果其中一個服務很慢或暫時故障，Checkout Service 就需要決定如何等待、重試或回報錯誤。

```
                    ┌→ Payment Service
                    ├→ Inventory Service
Customer → Checkout ├→ Email Service
                    ├→ Analytics Service
                    └→ Shipping Service
```

### 若使用 Kafka

訂單成立後，Checkout Service 發佈 `OrderPlaced` Event。

需要這筆事件的服務，各自從 Kafka 讀取。

```text
Checkout
    │
    │ Publish OrderPlaced
    ▼
┌──────── Kafka ────────┐
│                       │
│     orders topic      │
│     OrderPlaced       │
│                       │
└───────────────────────┘
            ▲
            │ Subscribe OrderPlaced
     ┌──────┼─────────┬───────┬─────────┐
   Payment Inventory Email Analytics Shipping
```

1. Publish ： Checkout 發布一筆 `OrderPlaced` Event
   ↓
2. Store ： Kafka 把 Event append 到 Topic 中並保存
   ↓
3. Subscribe ： Payment、Inventory、Email、Analytics、Shipping 各自訂閱需要的 Event
   ↓
4. Process ： 每個 Service 按自己的進度讀取並處理

如此，Checkout Service 跟其他 Service 沒有直接關係，不會互相影響。

假設 Email Service 暫時故障，Payment Service 和 Analytics Service 仍可按自己的進度工作。

只要事件還在 Kafka 的保留範圍內，Email Service 恢復後也能繼續讀取。

---

## 二、一筆 Event 在 Kafka 內的旅程

假設訂單 `O1001` 已成立，Checkout Service 要發佈 `OrderPlaced`：

1. Checkout Service 建立事件資料。
2. Producer 把 Key 和 Value 序列化成 bytes，送往 Kafka。
3. Kafka 把這筆 Record 寫入 `orders` Topic 的某個 Partition。
4. Consumer 從該 Partition 讀取 Record。
5. Consumer 反序列化資料，交給應用程式處理。

```text
Checkout Service
      │ 建立 OrderPlaced
      ▼
Producer
      │ 序列化並送出 Record
      ▼
Kafka Broker
      │ 寫入 orders Topic 的某個 Partition
      ▼
Consumer
      │ 讀取、反序列化
      ▼
應用程式處理
```

### Server Side 與 Client Side

```text
Client Side             Server Side             Client Side

Producer ─────────────→ Kafka Cluster ←──────── Consumer
```

- **Server Side**：Kafka 叢集，負責接收、保存與提供資料，並管理叢集狀態。
- **Client Side**：使用 Kafka Client 的應用程式，負責寫入或讀取資料。

圖中的 Consumer 箭頭指向 Kafka，是因為一般 Kafka Consumer 會**主動向 Broker 取得資料**，而不是等待 Broker 主動推送。

### Producer

> Producer 是把 Record 寫入 Kafka 的 Client。

Checkout Service 可以使用 Kafka Producer API，將 `OrderPlaced` 寫進 `orders` Topic。

### Serialization

> Serialization 是把程式中的資料轉成可傳輸的 bytes。

例如，程式中可能有：

```text
OrderPlaced {
    orderId: "O1001",
    amount: 1200
}
```

Producer 需要按照約定格式，把 Key 和 Value 轉成 bytes。Consumer 之後也必須知道該如何解讀這些 bytes。

```text
程式中的資料 → Serialization → Bytes → Kafka
```

### Record／Event

> Record 是 Kafka 傳輸與保存的一筆資料；Event 則強調這筆資料描述了什麼已發生的事情。

例如，`OrderPlaced` 是業務上的 Event；它可以表示為一筆 Kafka Record：

```text
寫入位置：orders Topic

Record
├── Key: "O1001"
├── Value:
│   {
│     "eventId": "E9001",
│     "eventType": "OrderPlaced",
│     "orderId": "O1001",
│     "amount": 1200
│   }
├── Timestamp
└── Headers:
    └── event-type: "OrderPlaced"
```

幾個重要欄位：

- **Key**：常用來影響 Record 被寫入哪個 Partition，例如 `orderId`。
- **Value**：主要資料，例如訂單內容。
- **Timestamp**：Record 的時間資訊；依設定可以使用建立時間或 Broker 寫入時間。
- **Headers**：額外資訊，例如事件類型或 trace ID。

`orders` 是這筆 Record 的目標 Topic。Producer 建立 Record 時會指定 Topic；Consumer 讀到資料時也能知道它來自哪個 Topic。

### Broker 與 Cluster

> Broker 是 Kafka 叢集中負責資料讀寫與保存的伺服器角色。

多個 Broker 可以組成 Kafka Cluster：

```text
Kafka Cluster
├── Broker A
├── Broker B
└── Broker C
```

一個 Topic 的 Partition 可以分布在不同 Broker；同一個 Partition 的多份 Replica 也會分布在不同 Broker。

### Topic

> Topic 是資料的邏輯分類名稱。

例如：

```text
orders
payments
shipments
```

`OrderPlaced` 可以寫入 `orders` Topic。Topic 下面還會有一個或多個 Partition，真正的 Record 會寫入其中一個 Partition。

### Consumer 與 Deserialization

> Consumer 是從 Kafka 讀取資料的 Client。

以 Java 的 `KafkaConsumer` 為例，常見操作是呼叫 `poll(...)` 取得資料。取得 Record 後，Consumer 會依照約定格式反序列化，再交給應用程式處理：

```text
Kafka 中的 Bytes → Deserialization → 程式可處理的資料
```

Producer 和 Consumer 必須對資料格式有共同理解；否則 Consumer 可能無法正確還原資料。

到這裡，可以先記住：

> **Producer 寫入資料；Broker 保存並提供資料；Consumer 主動讀取資料。**

---

## 三、Topic、Partition、Key 與 Offset

### Partition

一個 Topic 可以分成多個 Partition：

```text
orders Topic
├── Partition 0：Record → Record → Record → ...
├── Partition 1：Record → Record → ...
└── Partition 2：Record → Record → Record → ...
```

每筆 Record 會寫入其中**一個** Partition。Partition 讓資料可以分散保存，也讓同一個 Consumer Group 能平行讀取。

Kafka 的重要規則是：

> **順序保證以單一 Topic 的單一 Partition 為範圍，不是整個 Topic 的全域順序。**

例如：

```text
Partition 0：A → B → C
Partition 1：D → E → F
```

讀取 Partition 0 時，可以依照 A、B、C 的寫入順序取得資料；但不能因此推論 A 和 D 的先後關係。

也要區分**讀取順序**與**業務完成順序**：如果應用程式把讀到的資料再交給多個工作執行緒平行處理，完成順序仍可能不同。

### Key

Producer 決定 Partition 時，Key 是常見依據。例如把 `orderId` 當作 Key：

```text
Key = "O1001"
```

使用預設的 Key 分區方式，且 Topic 的 Partition 數量與分區規則都不變時，相同 Key 會被送往同一個 Partition。

假設以下三筆事件**都寫入同一個 Topic**：

```text
O1001 OrderPlaced
O1001 PaymentCompleted
O1001 OrderShipped
```

使用相同 Key，就能讓它們進入同一個 Partition，供 Consumer 依該 Partition 的寫入順序讀取。

但相同 Key **不提供跨 Topic 的順序保證**。如果三筆事件分別在 `orders`、`payments`、`shipments`，它們屬於不同的資料流。

指定 Partition、自訂分區規則或增加 Topic 的 Partition 數量，也可能改變相同 Key 的分區結果。

### Offset

> Offset 是 Record 在某個 Partition 中的位置。

```text
orders / Partition 0

offset 0 → O1001 OrderPlaced
offset 1 → O1003 OrderPlaced
offset 2 → O1001 PaymentCompleted
offset 3 → O1001 OrderShipped
```

Offset 不是訂單 ID，也不是 Event ID。它只對**所屬的 Topic／Partition**有意義，所以不同 Partition 都可以有 `offset 0`。

Kafka 清除舊資料或執行 Log Compaction 後，讀到的 Offset 也可能出現空洞；現存 Record 不會因此重新編號。

### Partition 是一條 Log

可以把 Partition 想成一條持續附加資料的 Log：

```text
較舊                                      較新
  │                                         │
  ▼                                         ▼
Record → Record → Record → Record → Record → ...
```

新的 Record 通常附加在尾端。這帶出 Kafka 的重要原則：

> **Kafka 保存一條可供讀取的資料流；Consumer 讀過資料，資料也不會因此立刻被取走或刪除。**

---

## 四、Consumer Group：多個 Consumer 怎麼分工？

### Consumer Group

> Consumer Group 是一群共同分工讀取 Topic 的 Consumer。

假設 `orders` 有三個 Partition：

```text
orders Topic

Partition 0 ──→ Consumer A
Partition 1 ──→ Consumer A
Partition 2 ──→ Consumer B

                 analytics-group
```

在 Consumer Group 中，**同一個 Partition 在同一個 Group 內，同一時間只會分配給一個 Consumer**。

因此，如果一個 Topic 有 3 個 Partition，而 Group 有 4 個 Consumer，最多只有 3 個 Consumer 能同時分到這個 Topic 的 Partition；第 4 個會閒置。

> **Partition 數量會限制 Consumer Group 對該 Topic 的平行讀取程度。**

### 不同 Group 可以各自讀取同一批資料

Inventory、Email 和 Analytics 若都需要 `OrderPlaced`，可以使用不同的 Group：

```text
                    orders Topic
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
   inventory-group   email-group   analytics-group
```

它們各自記錄進度，因此同一筆 Event 可以分別被三個服務處理。

不同於 Queue 的「工作被一個 Worker 拿走後，其他 Worker 就看不到」設計。

### Committed Offset

Consumer 除了取得 Record，也需要決定故障或重新啟動後從哪裡繼續讀。這個位置稱為 **Committed Offset**。

```text
Partition 0

offset 0 ✓
offset 1 ✓
offset 2 ✓
offset 3 ✓
offset 4 ✓
offset 5 ← 下次讀取的位置

committed offset = 5
```

這裡的 `5` 表示「下次從 offset 5 開始」，不是「已處理完 offset 5」。

> **Committed Offset 只是 Consumer Group 宣告的讀取進度。**

Consumer 可以自動或手動提交 Offset。

如果 Client 希望用它代表「已處理完成」，就需要改成處理成功後才提交 offset。

### Rebalance

假設原本：

```text
Consumer A → Partition 0、1
Consumer B → Partition 2
```

Consumer A 故障後，Group 需要重新分配 Partition，例如：

```text
Consumer B → Partition 0、1、2
```

這個重新決定「哪個 Consumer 負責哪些 Partition」的過程叫做 **Rebalance**。

它不會重新排列 Partition 裡的 Record。

Consumer 加入、離開、故障，或 Topic 的 Partition 發生變化，都可能引發重新分配。

### Consumer Lag

Consumer Lag 用來觀察讀取進度距離資料流尾端有多遠。例如：

```text
Log End Offset    = 1000  ← 下一個可寫入的位置
Committed Offset  =  700  ← Group 下次要讀的位置
Lag               =  300
```

Lag 持續增加，可能代表 Consumer 的讀取或處理速度跟不上資料產生速度。但 Lag 本身**不能直接指出原因**，也不一定等於「尚未完成業務處理的筆數」。

### Replay

Kafka 的 Record 不會因為 Consumer 讀過就立刻消失。只要資料還在保留範圍內，Consumer 就能把讀取位置移回較早的地方，重新讀取。

例如 Analytics Service 的計算程式昨天有 Bug，修好後可以重新讀取昨天仍被保留的資料，再計算一次。這稱為 **Replay**。

Replay 也會讓下游再次收到資料；如果處理會產生外部副作用，例如寄信或扣款，就必須考慮重複執行。

### ShareConsumer／Share Group

前面的 `KafkaConsumer`／Consumer Group 以 Partition 分配作為主要分工方式。

Kafka 也提供 **ShareConsumer／Share Group** ，讓多個 Consumer 可以共同處理來自同一個 Partition 的不同 Record，並對個別 Record 做 acknowledgement。

適合較接近 Queue 的工作分配情境。

```text
同一個 Partition 的 Records
    ├── Record A → ShareConsumer 1
    ├── Record B → ShareConsumer 2
    └── Record C → ShareConsumer 3
```

先記住兩者的取捨：

```text
Consumer Group → 以 Partition 分工，依 Partition 順序讀取
Share Group    → 以 Record 分工，但不保證同樣的處理順序
```

Share Group 在 Kafka 4.2 已可用於正式環境。這裡只會簡單帶過，不會深入其 acknowledgement 與重送規則。

---

## 五、Kafka 怎麼保存資料、面對故障？

### Replication

如果 Partition 只存在 Broker A，Broker A 故障時，這個 Partition 就無法由其他 Broker 接手。因此 Kafka 可以為 Partition 建立多份 Replica（備份）：

```text
Partition 0
├── Broker A：Leader Replica
├── Broker B：Follower Replica
└── Broker C：Follower Replica
```

`Replication Factor = 3` ： 表示這個 Partition 配置了三份 Replica。

### Leader／Follower

一個 Partition 的 Replica 之中：

- **Leader** 主要處理該 Partition 的寫入；一般情況下，Consumer 也是向 Leader 讀取。
- **Follower** 主要向 Leader 取得資料，維持自己的副本。

若 Leader 所在的 Broker 故障，Kafka 可以選出符合條件的 Replica 接手。是否能繼續提供服務、是否有資料遺失風險，取決於副本狀態與相關設定。

### ISR

Follower 不一定永遠跟得上 Leader。Kafka 會追蹤 **ISR（In-Sync Replicas）**，也就是目前符合其同步條件的 Replica 集合。

```text
Leader：Broker A
Follower：Broker B、Broker C
ISR：[A, B]
```

這代表 C 的數據還不是最新的。當 Leader 故障時，下一個可寫入資料的 Leader 只會從 ISR 中選取。

### Controller 與 KRaft

Kafka 除了保存 Record，還需要管理叢集狀態，例如：

- 有哪些 Broker？
- Topic 有哪些 Partition？
- Replica 分布在哪些 Broker？
- 哪個 Replica 是 Leader？

這些資訊稱為 **Cluster Metadata**。負責控制面與 Metadata 管理的是 **Controller 角色**。

現代 Kafka 使用 **KRaft Controller quorum** 管理這些 Metadata。Kafka 4.0 起已移除 ZooKeeper 模式。

```text
Broker     → 主要處理 Topic 資料的讀寫與保存
Controller → 主要管理 Cluster Metadata 與控制面
```

Broker 和 Controller 是**角色**：它們可以部署在不同節點；開發或較小型環境也可能讓同一節點同時扮演兩種角色。Controller 使用的 Metadata Log，與前面討論的 Topic Partition 資料 Log 是不同用途。

### Retention

Kafka 不會因為 Consumer 讀過 Record 就立刻刪除。Topic 可以設定依時間或容量清理舊資料，例如保留七天。

這是 Replay 得以運作的重要原因，但也代表：

> **能重新讀取的前提，是所需資料仍在 Kafka 的保留範圍內。**

### Segment

Partition 的 Log 不會永遠存在一個巨大檔案裡。Kafka 會把它切成多個 **Log Segment**，以便管理與清理資料。

```text
Partition 0
├── Segment A：較舊的 Records
├── Segment B：之後的 Records
└── Segment C：較新的 Records
```

Segment 的切分不表示每個檔案固定包含相同數量的 Record。

### Log Compaction

除了依時間或容量刪除舊資料，Kafka 也支援 **Log Compaction**。它以 Key 為基礎，讓 Log 至少能保留每個 Key 較新的狀態。

```text
O1001 → CREATED
O1002 → CREATED
O1001 → PAID
O1001 → SHIPPED
```

經過背景清理後，較舊的 `O1001` 狀態可以被移除，而較新的 `SHIPPED` 狀態會保留。清理**不是寫入後立即完成**；在完成之前，Consumer 仍可能讀到舊值。Compaction 也不會替留下的 Record 重新編排 Offset。

Topic 可以選擇依時間／容量刪除、依 Key 執行 Compaction，或同時使用兩種清理方式。選擇取決於這條資料流的用途：完整事件歷史與「每個 Key 的較新狀態」是不同需求。

---

## 六、網路失敗、Retry 與可靠性

有 Replica 不代表所有故障都解決了。Producer 和 Broker 透過網路溝通，而網路失敗時，Client 有時無法判斷一個操作究竟有沒有成功。

### 最麻煩的狀態：不知道有沒有成功

假設 Producer 發送 `OrderPlaced`：

```text
Producer ── Request ──→ Broker
                        寫入成功

Producer ←── X ─────── Success Response 遺失
```

Producer 最後只看到 Timeout。它不知道是 Broker 沒收到請求，還是 Broker 已經寫入、只是回覆沒有送達。

### Retry

Producer 可以在符合條件時重試。但如果第一次其實已成功，重試可能造成重複寫入：

```text
第一次送出 → Broker 寫入成功
回覆遺失   → Producer 看見 Timeout
重試       → 可能再次寫入
```

這就是為什麼「重試」與「避免重複」需要一起考慮。

### Acknowledgement／`acks`

Producer 可以用 `acks` 指定何時將寫入視為已獲 Broker 確認：

```text
acks=0
→ Producer 不等待 Broker 確認。

acks=1
→ Leader 寫入自己的 Log 後回覆，不等待所有 ISR 跟上。

acks=all
→ Leader 等待目前 ISR 的 Replica 達成確認條件後回覆。
```

`acks=all` 中的「all」是**目前 ISR 的全部成員**，不一定是這個 Partition 原本配置的所有 Replica。如果 ISR 只剩一份，仍希望拒絕寫入，就需要搭配 `min.insync.replicas` 設定。

例如 `Replication Factor = 3`、`min.insync.replicas = 2`、Producer 使用 `acks=all`，可以要求至少有兩份符合條件的同步副本，寫入才算成功。

### Idempotence：冪等性

冪等性指同一操作重複執行，最終結果仍與執行一次相同。

例如： `SET order.status = "SHIPPED"` 若重複執行，最後狀態仍是 `SHIPPED`；但 `order_count += 1` 若重複執行兩次，Order 的數量就會多一筆。

Kafka Producer 有 Idempotence 機制，可避免特定 Producer 重試造成重複寫入。

但 Producer 沒有重複寫入 Event，不代表 Consumer 不會重複讀取並執行，例如：

```text
Consumer 讀取 OrderPlaced
        ↓
寄信成功
        ↓
Consumer 故障，Offset 尚未提交
        ↓
重新讀取 OrderPlaced
        ↓
可能再寄一次信
```

Producer Idempotence 無法替外部寄信服務判斷「這封信是否已寄過」。

### At-most-once、At-least-once、Exactly-once

假設 Consumer 需要「處理 Event」與「提交 Offset」，兩件事的順序會產生不同風險。

**先提交，再處理：** 故障後 Event 未處理

```text
Commit Offset → 故障 → 尚未完成處理
```

**先處理，再提交：** 重新啟動後可能再次處理 Event

```text
處理完成 → 故障 → Offset 尚未提交
```

因此會產生三種 Event 的處理方式：

- **At-most-once**：最多處理一次，但可能漏掉。
- **At-least-once**：至少處理一次，但可能重複。
- **Exactly-once**：讓指定範圍內的處理結果只生效一次；必須明確說明系統邊界與使用的機制，不能直接套用到外部寄信、付款等操作。

不用死背，只需要思考：

> **故障可能發生在任何兩個步驟之間。設計可靠流程時，要逐一問：此時重新啟動會漏掉什麼，又會重複什麼？**

---

## 七、重新複習一筆 Event 會怎麼被處理

上面介紹很多名詞，不用一次全部背起來。

比較重要的是先知道它們在整條 Event Flow 裡的位置。

```text
Checkout Service
      │ 訂單成立，建立 OrderPlaced Event
      ▼
Producer
      │ 指定 Topic／Key，序列化 Key 與 Value
      ▼
orders Topic 的某個 Partition
      │
      ▼
Leader Broker 將 Record 寫入 Log
      │
      ├── Follower Replica 複寫
      └── Follower Replica 複寫
      │
      ▼
Consumer 主動讀取 Record
      │ 反序列化
      ▼
應用程式處理
      │
      ▼
依處理結果提交下一次讀取的 Offset
```

如果多個服務都需要同一筆 Event，它們可以使用不同的 Consumer Group，各自讀取並維護進度：

```text
                         orders Topic
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
       inventory-group   email-group   analytics-group
```

把整個流程濃縮成一段話：

> 外部服務透過 **Producer** 寫入 **Event**，內部表示為 **Record**，序列化後寫入 **Topic** 的某個 **Partition**。**Partition** 是有順序的 **Log**，由 **Broker** 保存，並透過 **Replica** 提高容錯能力。不同 **Consumer Group** 可以各自讀取同一批 **Record**，按自己的進度處理，並以 **Committed Offset** 記錄下次讀取的位置。
> Kafka 保存資料與讀取進度，但業務處理是否成功、會不會重複執行，仍需要外部服務自己設計。

學 Kafka 不需要把每個名詞當成獨立知識背起來。
遇到新的 Kafka Concept，可以先問兩個問題：

1. 它出現在一筆 Event / Record 的哪個環節？
2. 它想解決什麼問題，又有什麼使用條件？

再往下找到對應的 Kafka Concept：

1. 誰把資料寫進 Kafka？→ **Producer**
2. Kafka Server 是誰？→ **Broker**
3. 資料要放哪裡？ → **Topic**
4. 資料太多，怎麼平行處理？ → **Partition**
5. 同一張訂單需要順序？ → **Key** + **Partition**
6. 讀到哪裡了？ → **Offset** + **Commit**
7. 多個 Consumer 怎麼分工？ → **Consumer Group**
8. Consumer 處理到哪裡？ → **Committed Offset**
9. Consumer 跟不上？ → **Consumer Lag**
10. 需要重新處理資料？ → **Replay** / **Retention**
11. Broker 掛掉？ → **Replication** + **Leader / Follower** + **ISR**
12. 誰管理 Cluster Metadata？ → **Controller** + **KRaft**
13. 網路 Timeout？ → **Retry** / **acks**
14. Retry 造成重複？ → **Idempotence**

**這堂課希望讓你們對 Kafka 有一套心智模型，深入細節時才不會迷失。**
