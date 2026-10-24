---
marp: true
theme: default
size: 16:9
paginate: true
title: Kafka 的真實案例
description: Kafka 在真實公司中的四種角色與案例
lang: zh-TW
style: |
  section { background: #f8fafc; color: #182b43; font-family: "PingFang TC", "Noto Sans CJK TC", sans-serif; font-size: 28px; padding: 52px 64px; justify-content: flex-start; line-height: 1.5; }
  h1 { color: #102b4d; font-size: 42px; line-height: 1.25; margin: 0 0 34px; border-bottom: 4px solid #0f928b; padding-bottom: 15px; }
  p { margin: 15px 0; }
  strong { color: #0b7d78; }
  code { background: #e8eef4; color: #213a55; font-size: 0.86em; }
  pre { background: #e8eef4; padding: 22px 28px; font-size: 25px; line-height: 1.45; }
  table { width: 100%; font-size: 25px; margin: 10px 0 22px; border-collapse: collapse; }
  th { background: #173b60; color: white; }
  td, th { padding: 14px 18px; border: 1px solid #c7d4e0; }
  tr:nth-child(even) { background: #eaf0f5; }
  ul { margin-top: 18px; }
  li { margin: 10px 0; }
  .flow { display: flex; align-items: center; justify-content: center; gap: 16px; width: 100%; margin: 22px 0; }
  .flow > div { border: 1px solid #a9bdce; background: #eaf0f5; padding: 17px 12px; text-align: center; flex: 1; font-size: 27px; }
  .arrow { color: #0f928b; font-size: 38px; }
  .lanes { margin: 16px 0 24px; }
  .lane { display: flex; align-items: center; border-bottom: 1px solid #b8c8d8; min-height: 70px; padding: 12px 0; gap: 25px; }
  .lane > b { width: 245px; flex-shrink: 0; color: #0b7d78; font-size: 25px; }
  .lane > div { flex: 1; font-size: 26px; }
  .lane .flow { margin: 0; }
  .lane .flow > div { padding: 7px 12px; }
  .metric { font-size: 48px; padding: 24px 0; color: #173b60; text-align: center; }
  section.cover { background: #102b4d; color: #edf4fa; justify-content: center; }
  section.cover h1 { color: white; font-size: 54px; border: none; max-width: 1000px; }
  section.cover .lead { color: #77dad0; font-size: 35px; }
  section.cover code { background: #204666; color: white; }
  section::after { font-size: 18px; color: #698199; right: 34px; bottom: 22px; }
  header { position: absolute; top: 14px; left: 64px; right: 64px; color: #698199; font-size: 17px; }
  section.chapter { background: #102b4d; color: #edf4fa; }
  section.chapter h1 { color: white; font-size: 44px; border-color: #77dad0; }
  section.chapter header { display: none; }
  section.chapter p { color: #c5e7e3; font-size: 30px; }
  section.dense table { font-size: 24px; }
  section.dense td, section.dense th { padding: 9px 14px; }
  .broadcast { margin: 25px 0; text-align: center; }
  .origin { color: #0b7d78; font-weight: bold; border: 1px solid #a9bdce; padding: 15px; }
  .branches { display: flex; justify-content: space-around; padding: 15px 0; }
  .branches > div { flex: 1; font-size: 27px; }

  .diagram { width: 100%; flex-shrink: 0; display: block; margin: 8px 0 14px; overflow: visible; font-family: "PingFang TC", "Noto Sans CJK TC", sans-serif; }
  .record-layout { display: grid; grid-template-columns: 52% 48%; gap: 24px; width: calc(100% - 24px); }
  .record-layout .record-code { margin: 0; font-size: 23px; line-height: 1.36; padding: 15px 20px; white-space: pre; }
  .record-layout p { font-size: 25px; line-height: 1.55; margin: 6px 0 28px; }
  section.compact table { font-size: 24px; margin-bottom: 12px; }
  section.compact td, section.compact th { padding: 9px 16px; }

  section.policy-choice h1 { margin-bottom: 14px; padding-bottom: 10px; }
  section.policy-choice p { font-size: 25px; line-height: 1.35; margin: 7px 0; }
  section.policy-choice .diagram { margin: 5px 0; }
  section.policy-choice table { font-size: 24px; line-height: 1.2; margin: 5px 0; }
  section.policy-choice td, section.policy-choice th { padding: 6px 14px; }

  section.reliability strong { color: #77dad0; }

---

<!-- _class: cover -->

# Kafka 的真實案例

<p class="lead">它在今天的資訊世界扮演什麼角色</p>

LinkedIn · Uber · LINE · Netflix · AO.com · BRI · The New York Times · Michelin

<!--
introduction.md 講 Kafka 怎麼運作；這堂課講真實公司拿它來做什麼。案例都來自公司的工程部落格或公開客戶案例，數字以文章發表當時為準。
-->

---

# 課程目標

1. 說出 Kafka 為什麼誕生，以及它想解決的問題
2. 分辨 Kafka 在系統裡常見的**四種角色**，並各舉一個公司案例
3. 理解不同公司為什麼做出不同選擇
    - 要平行度，還是要順序？
    - 自己維運，還是交給雲端？
4. 讀到新的案例時，能判斷 Kafka 的角色與代價

<!--
重點不是背公司名稱，而是學會讀案例：痛點是什麼、Kafka 扮演什麼角色、付出了什麼代價。最後一章會給一套讀案例的方法。
-->

---

<!-- _class: chapter -->

# 一、從 LinkedIn 開始

Kafka 為什麼誕生？

<!--
第一章回到起點：Kafka 是為了解決什麼問題而誕生的。
-->

---

# 問題：每個系統都要互相串接

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="330" viewBox="0 0 1152 330" role="img" aria-label="直接串接 vs 透過 Kafka"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><text x="250" y="30" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600">直接串接：3 × 3 = 9 條</text><line x1="180" y1="100" x2="330" y2="100" stroke="#90a8bc" stroke-width="1.5"/><line x1="180" y1="100" x2="330" y2="185" stroke="#90a8bc" stroke-width="1.5"/><line x1="180" y1="100" x2="330" y2="270" stroke="#90a8bc" stroke-width="1.5"/><line x1="180" y1="185" x2="330" y2="100" stroke="#90a8bc" stroke-width="1.5"/><line x1="180" y1="185" x2="330" y2="185" stroke="#90a8bc" stroke-width="1.5"/><line x1="180" y1="185" x2="330" y2="270" stroke="#90a8bc" stroke-width="1.5"/><line x1="180" y1="270" x2="330" y2="100" stroke="#90a8bc" stroke-width="1.5"/><line x1="180" y1="270" x2="330" y2="185" stroke="#90a8bc" stroke-width="1.5"/><line x1="180" y1="270" x2="330" y2="270" stroke="#90a8bc" stroke-width="1.5"/><rect x="0" y="72" width="180" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="90.0" y="108.0" font-size="23">資料庫</tspan></text><rect x="330" y="72" width="180" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="420.0" y="108.0" font-size="23">搜尋索引</tspan></text><rect x="0" y="157" width="180" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="90.0" y="193.1" font-size="23">應用程式</tspan></text><rect x="330" y="157" width="180" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="420.0" y="193.1" font-size="23">推薦系統</tspan></text><rect x="0" y="242" width="180" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="90.0" y="278.1" font-size="23">使用者行為</tspan></text><rect x="330" y="242" width="180" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="420.0" y="278.1" font-size="23">資料倉儲</tspan></text><text x="870" y="30" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600">透過 Kafka：3 + 3 = 6 條</text><rect x="600" y="72" width="170" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="685.0" y="108.0" font-size="23">資料庫</tspan></text><polyline points="770,100 812,100" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="930,100 972,100" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="982" y="72" width="170" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="1067.0" y="108.0" font-size="23">搜尋索引</tspan></text><rect x="600" y="157" width="170" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="685.0" y="193.1" font-size="23">應用程式</tspan></text><polyline points="770,185 812,185" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="930,185 972,185" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="982" y="157" width="170" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="1067.0" y="193.1" font-size="23">推薦系統</tspan></text><rect x="600" y="242" width="170" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="685.0" y="278.1" font-size="23">使用者行為</tspan></text><polyline points="770,270 812,270" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="930,270 972,270" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="982" y="242" width="170" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="1067.0" y="278.1" font-size="23">資料倉儲</tspan></text><rect x="812" y="72" width="118" height="226" rx="5" fill="#d9efed" stroke="#0b7d78" stroke-width="2"/><text fill="#182b43" text-anchor="middle"><tspan x="871.0" y="177.5" font-size="25" font-weight="600">Kafka</tspan><tspan x="871.0" y="210.0" font-size="22">共用 Log</tspan></text></svg>

**中間放一條共用的 Log，串接數從 N × M 降為 N + M**

<!--
2010 年前後，LinkedIn 的 Jay Kreps、Neha Narkhede、Jun Rao 發現批次導向的訊息系統跟不上資料搬運需求。左邊每多一個系統，就要和另一側每個系統都串一次；右邊只要接上 Kafka。產生資料的一方只管寫入，需要資料的一方按自己的進度讀，雙方不必知道彼此存在。
-->

---

# LinkedIn 的成長：八年七千倍

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="365" viewBox="0 0 1152 365" role="img" aria-label="LinkedIn 每天訊息量成長"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><text x="170" y="51" font-size="24" fill="#182b43" text-anchor="end">2011 年 7 月</text><rect x="190" y="20" width="4.0" height="44" rx="3" fill="#90a8bc"/><text x="208" y="51" font-size="24" fill="#182b43" text-anchor="start">約 10 億</text><text x="170" y="113" font-size="24" fill="#182b43" text-anchor="end">2012 年</text><rect x="190" y="82" width="4.0" height="44" rx="3" fill="#90a8bc"/><text x="208" y="113" font-size="24" fill="#182b43" text-anchor="start">約 200 億</text><text x="170" y="175" font-size="24" fill="#182b43" text-anchor="end">2013 年 7 月</text><rect x="190" y="144" width="22.3" height="44" rx="3" fill="#90a8bc"/><text x="226.28571428571428" y="175" font-size="24" fill="#182b43" text-anchor="start">約 2,000 億</text><text x="170" y="237" font-size="24" fill="#182b43" text-anchor="end">2015 年</text><rect x="190" y="206" width="111.4" height="44" rx="3" fill="#90a8bc"/><text x="315.42857142857144" y="237" font-size="24" fill="#182b43" text-anchor="start">超過 1 兆</text><text x="170" y="299" font-size="24" fill="#182b43" text-anchor="end">2019 年</text><rect x="190" y="268" width="780.0" height="44" rx="3" fill="#0b7d78"/><text x="984.0" y="299" font-size="24" fill="#0b7d78" text-anchor="start" font-weight="600">超過 7 兆</text><text x="190" y="352" font-size="21" fill="#698199" text-anchor="start">每天處理的訊息量（線性刻度：前三年的量小到幾乎看不見）</text></svg>

<!--
八年成長七千倍。2019 年的用途包括使用者行為追蹤、服務之間的訊息交換、系統指標收集。
-->

---

# 2019 年的 LinkedIn

<div class="flow"><div><b>100+</b><br>叢集</div><div><b>4,000+</b><br>Broker</div><div><b>10 萬+</b><br>Topic</div><div><b>700 萬</b><br>Partition</div></div>

- Kafka 開源後，三位作者離開 LinkedIn，創立了 Confluent
- LinkedIn 跑自己維護的 `-li` release branch，開源在 GitHub
    - 強調不是 fork，盡量貼近 upstream，把修改回饋給 Apache Kafka
- **開源循環**：公司在生產環境遇到問題，修好後貢獻回社群

<!--
這個循環和第 1 週講的「公司為什麼做開源」接得上。後面的 Uber 也會看到同樣的模式：Tiered Storage 就是 Uber 遇到問題後貢獻回社群的。
-->

---

<!-- _class: dense -->

# Kafka 的四種角色

| 角色 | Kafka 在做什麼 | 案例 |
|---|---|---|
| 服務之間的事件骨幹 | 服務發布事件，其他服務各自訂閱 | Uber、LINE |
| 資料收集與整合管線 | 把資料持續搬到需要的地方 | Netflix、AO.com |
| 即時串流處理 | 資料流動的同時就完成計算 | BRI |
| Source of truth | Log 本身就是正本 | The New York Times |

真實系統常同時扮演好幾種角色，分類只是幫助你讀案例時抓住重點

<!--
接下來四章，每章一種角色。每個案例都會對回 introduction.md 的概念：Consumer Group、Offset、Retention、Replay、Partition 順序。
-->

---

<!-- _class: chapter -->

# 三、角色一：服務之間的事件骨幹

當服務從 5 個變成 1,000 個，會發生什麼事？

<!--
introduction.md 的 Checkout 例子就是事件骨幹。這章看規模放大一千倍之後的問題。
-->

---

<!-- _class: compact -->

# Uber：上千個微服務的非同步佇列

<div class="flow"><div><b>數兆則</b><br>每天訊息量</div><div><b>300+</b><br>微服務用 Kafka 做佇列</div><div><b>100 萬 → 1,200 萬</b><br>五年內每秒訊息量</div></div>

直接用 Kafka Client 遇到三個問題：

1. **語言太多**：Go、Java、Python、Node.js，升級 1,000 多個服務要好幾個月
2. **平行度被 Partition 數限制**：處理一筆要 1 秒，每秒 1,000 筆就要 1,000 個 Partition
3. **一筆卡住，後面全卡住**：慢訊息或 poison pill 擋住整個 Partition

<!--
第二點對回 introduction.md 第四章：一個 Consumer Group 的平行度上限是 Partition 數。很多佇列工作其實不需要順序，卻被這個限制綁住。第三點是同一個 Partition 必須依序處理的副作用。
-->

---

# Uber 的解法：Consumer Proxy

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="330" viewBox="0 0 1152 330" role="img" aria-label="Uber Consumer Proxy"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="110" width="230" height="110" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="115.0" y="157.5" font-size="25" font-weight="600">Kafka</tspan><tspan x="115.0" y="190.0" font-size="22">orders / P0</tspan></text><polyline points="230,165 330,165" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="280" y="150" font-size="21" fill="#698199" text-anchor="middle">讀取</text><rect x="330" y="75" width="300" height="180" rx="5" fill="#d9efed" stroke="#0b7d78" stroke-width="2"/><text fill="#182b43" text-anchor="middle"><tspan x="480.0" y="141.2" font-size="25" font-weight="600">Consumer Proxy</tspan><tspan x="480.0" y="173.8" font-size="22">平行推送</tspan><tspan x="480.0" y="206.2" font-size="22">連續確認後才提交 Offset</tspan></text><polyline points="630,165 720,165 720,40 840,40" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="840" y="8" width="310" height="64" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="995.0" y="48.4" font-size="24">服務實例 1</tspan></text><polyline points="630,165 720,165 720,165 840,165" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="840" y="133" width="310" height="64" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="995.0" y="173.4" font-size="24">服務實例 2</tspan></text><polyline points="630,165 720,165 720,290 840,290" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="840" y="258" width="310" height="64" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="995.0" y="298.4" font-size="24">服務實例 3</tspan></text><text x="780" y="26" font-size="21" fill="#698199" text-anchor="middle">gRPC</text></svg>

服務只要提供 gRPC endpoint，不必內建 Kafka Client；平行度不再受 Partition 數限制

<!--
Proxy 從 Kafka 讀取，再把每一筆訊息平行推給任意數量的服務實例。只有一段連續的訊息都確認後，才提交 Offset，所以慢的訊息不會擋住整體進度。代價是多維護一層系統。
-->

---

# Uber：Retry Topic 與 Dead Letter Queue

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="270" viewBox="0 0 1152 270" role="img" aria-label="Retry Topic 與 Dead Letter Queue"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="40" width="252" height="90" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="126.0" y="94.5" font-size="27">orders</tspan></text><text x="126" y="175" font-size="22" fill="#698199" text-anchor="middle">正常處理</text><rect x="300" y="40" width="252" height="90" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="426.0" y="94.5" font-size="27">retry-1</tspan></text><text x="426" y="175" font-size="22" fill="#698199" text-anchor="middle">延遲一段時間再試</text><rect x="600" y="40" width="252" height="90" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="726.0" y="94.5" font-size="27">retry-2</tspan></text><text x="726" y="175" font-size="22" fill="#698199" text-anchor="middle">延遲更久再試</text><rect x="900" y="40" width="252" height="90" rx="5" fill="#f7e6df" stroke="#b4532a" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="1026.0" y="94.5" font-size="27">DLQ</tspan></text><text x="1026" y="175" font-size="22" fill="#698199" text-anchor="middle">人工檢查後送回</text><polyline points="252,85 300,85" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="552,85 600,85" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="852,85 900,85" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="276" y="25" font-size="20" fill="#698199" text-anchor="middle">失敗</text><text x="576" y="25" font-size="20" fill="#698199" text-anchor="middle">仍失敗</text><text x="876" y="25" font-size="20" fill="#698199" text-anchor="middle">仍失敗</text><text x="576" y="245" font-size="23" fill="#182b43" text-anchor="middle">原本的 Consumer 轉寫到 retry Topic 後就提交 Offset，繼續處理下一筆</text></svg>

**失敗的訊息不能卡住整條資料流**

<!--
這是 Uber 保險工程團隊的設計。想像 Email Service 處理 O1001 一直失敗：失敗的訊息轉寫到 retry Topic，原本的 Consumer 就能提交 Offset 繼續往前。每一層等待更久，避免對有問題的下游瘋狂重試。最後一層仍失敗就進 DLQ，人工檢查後送回重試。
-->

---

# LINE（LY Corporation）：你每天都在用的服務

<div class="flow"><div><b>1 兆+</b><br>每天訊息量</div><div><b>2.6 PB</b><br>每天 I/O</div><div><b>3,100 萬</b><br>尖峰每秒訊息</div><div><b>200+</b><br>Broker</div></div>

- 用途：pub-sub、日誌管線、非同步處理、遊戲資料、CDC 同步資料庫
- 「只要服務會產生資料，多半就會經過 Kafka」

公開資料**沒有**說聊天訊息本身透過 Kafka 傳送

分清楚「公司用 Kafka」和「這個功能用 Kafka」

<!--
數字來自 2026 年 ClickHouse 部落格對 LY Corporation 的介紹，超過 25,000 台 Client 主機連到這些 Broker。提醒同學：Kafka 支撐的是服務背後的資料流動，不是取代聊天伺服器。
-->

---

<!-- _class: chapter -->

# 四、角色二：資料收集與整合管線

把資料從產生的地方，持續搬到需要的地方

<!--
取代每晚跑一次的批次匯出。
-->

---

# Netflix：Keystone 資料管線

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="330" viewBox="0 0 1152 330" role="img" aria-label="Netflix Keystone"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="55" width="185" height="100" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="92.5" y="97.8" font-size="24">幾乎所有</tspan><tspan x="92.5" y="129.0" font-size="24">應用程式</tspan></text><polyline points="185,105 235,105" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="235" y="40" width="240" height="130" rx="5" fill="#d9efed" stroke="#0b7d78" stroke-width="2"/><text fill="#182b43" text-anchor="middle"><tspan x="355.0" y="82.2" font-size="24" font-weight="600">Fronting Kafka</tspan><tspan x="355.0" y="113.4" font-size="21">只收資料、緩衝</tspan><tspan x="355.0" y="144.6" font-size="21">不給一般程式讀</tspan></text><polyline points="475,105 525,105" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="525" y="55" width="150" height="100" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="600.0" y="113.4" font-size="24">路由層</tspan></text><polyline points="675,105 725,105" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="725" y="40" width="210" height="130" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="830.0" y="82.2" font-size="24" font-weight="600">Consumer Kafka</tspan><tspan x="830.0" y="113.4" font-size="21">需即時處理的</tspan><tspan x="830.0" y="144.6" font-size="21">Topic 子集</tspan></text><polyline points="935,105 982,105" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="982" y="55" width="170" height="100" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="1067.0" y="113.4" font-size="24">即時處理</tspan></text><polyline points="600,155 600,240 725,240" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="725" y="205" width="427" height="70" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="938.5" y="248.4" font-size="24">批次處理：Hive、Elasticsearch</tspan></text><text x="576" y="318" font-size="23" fill="#698199" text-anchor="middle">36 個叢集、4,000+ Broker、每天 7,000 億則訊息（2016）</text></svg>

<!--
Keystone 是統一收集、發布與路由事件的基礎設施，同時供應批次與串流處理。Fronting Kafka 不讓一般程式讀取，讓負載可以預測；Consumer Kafka 只放需要即時處理的 Topic。這是用架構隔離寫入與讀取的例子。
-->

---

<!-- _class: compact -->

# Netflix 接受極少量遺失

當時每天的資料遺失率**低於 0.01%**

| 資料 | 遺失一點點可以嗎？ |
|---|---|
| 使用者行為紀錄 | 可以，統計結果幾乎不受影響 |
| 付款、訂單 | 不行 |

introduction.md 第六章的 `acks`、Replication、`min.insync.replicas`，在真實世界就是依資料的重要性來選擇

<!--
這頁的重點是可靠性有成本。不是每份資料都需要最高等級的保證；更高的保證通常代表更高的延遲或更低的可用性。設計時要先問這份資料丟了會怎樣。
-->

---

# AO.com：從 CDC 到即時個人化

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="345" viewBox="0 0 1152 345" role="img" aria-label="AO.com 的兩個階段"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><text x="0" y="26" font-size="23" fill="#0b7d78" text-anchor="start" font-weight="600">第一階段：CDC</text><rect x="0" y="45" width="190" height="70" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="95.0" y="88.4" font-size="24">訂單系統</tspan></text><polyline points="190,80 250,80" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="250" y="45" width="230" height="70" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="365.0" y="73.1" font-size="23" font-weight="600">CDC</tspan><tspan x="365.0" y="103.0" font-size="20">讀交易紀錄</tspan></text><polyline points="480,80 540,80" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="540" y="45" width="260" height="70" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="670.0" y="88.0" font-size="23">raw / enriched Topic</tspan></text><polyline points="800,80 862,80" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="862" y="45" width="290" height="70" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="1007.0" y="88.0" font-size="23">下游服務、S3（研究）</tspan></text><text x="0" y="176" font-size="23" fill="#0b7d78" text-anchor="start" font-weight="600">第二階段：點擊流個人化</text><rect x="0" y="195" width="190" height="70" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="95.0" y="238.4" font-size="24">網站點擊</tspan></text><polyline points="190,230 230,230" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="230" y="195" width="160" height="70" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="310.0" y="238.1" font-size="23">raw Topic</tspan></text><polyline points="390,230 430,230" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="430" y="185" width="230" height="90" rx="5" fill="#d9efed" stroke="#0b7d78" stroke-width="2"/><text fill="#182b43" text-anchor="middle"><tspan x="545.0" y="223.1" font-size="23" font-weight="600">Kafka Streams</tspan><tspan x="545.0" y="253.0" font-size="20">補上客戶資料</tspan></text><polyline points="660,230 700,230" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="700" y="195" width="180" height="70" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="790.0" y="238.1" font-size="23">enriched Topic</tspan></text><polyline points="880,230 920,230 920,210 950,210" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="880,230 920,230 920,300 950,300" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="950" y="182" width="202" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="1051.0" y="217.7" font-size="22">後端服務</tspan></text><rect x="950" y="272" width="202" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="1051.0" y="307.7" font-size="22">送回網站</tspan></text></svg>

<!--
AO.com 是英國的家電電商，在英國和德國銷售超過 9,000 種商品。CDC 讀資料庫自己的交易紀錄，把每次 INSERT、UPDATE、DELETE 轉成 Record，不必改動訂單系統。第二階段用 Kafka Streams 把點擊事件和客戶資料 join，enriched Topic 餵給後端服務，也送回網站即時調整頁面。A/B 測試顯示轉換率最多提升 30%。
-->

---

# AO.com 的兩個重點

**保留 raw Topic**

enrichment 邏輯有錯，修好後從 raw Topic 重跑：這就是 **Replay**

**資料變成事件流之後，其他團隊就會加入**

AO 提到又有兩三個團隊開始使用這些資料，這就是第一章 **N + M** 的實際效果

<!--
第一個重點連回 Retention 與 Replay；第二個重點連回 Kafka 誕生的理由。資料一旦以事件流的形式存在，新的使用者只要接上去，不必再去跟來源系統談一次。
-->

---

<!-- _class: chapter -->

# 五、角色三：即時串流處理

在資料流動的同時就完成計算

<!--
前兩種角色裡，Kafka 主要負責搬；這種角色是在事件發生的當下做出反應。
-->

---

# BRI（印尼人民銀行）：即時詐欺偵測

印尼最大的銀行，約 **7,500 萬**名客戶

- 即時信用評分
- 詐欺偵測：標記異常交易、凍結被盜用的卡片
- 商家評估
- 預警系統：在違約發生前，找出可能還不出錢的客戶

詐欺偵測用 KSQL 搭配 Kafka Streams

<div class="flow"><div>過去<br>兩週到一個月</div><span class="arrow">→</span><div>現在<br><b>即時</b></div></div>

<!--
等到隔天的批次報表才發現盜刷，損失早已造成。這是串流處理最典型的用途。
-->

---

<!-- _class: compact -->

# 串流處理在算什麼

- **無狀態**：一次只看一筆，例如「金額超過 10 萬就標記」
- **有狀態**：需要記住過去，例如「同一張卡 1 分鐘內刷超過 5 次」

```java
builder.stream("transactions", Consumed.with(Serdes.String(), Serdes.String())) // Key = 卡號
       .groupByKey()
       .windowedBy(TimeWindows.ofSizeWithNoGrace(Duration.ofMinutes(1)))
       .count()
       .toStream()
       .filter((windowedCard, count) -> count > 5)
       .map((windowedCard, count) -> KeyValue.pair(windowedCard.key(), count + " 筆交易"))
       .to("fraud-alerts", Produced.with(Serdes.String(), Serdes.String()));
```

用卡號當 **Key**，同一張卡的交易才會進同一個 Partition

<!--
程式碼只是概念示意，看得懂大意即可。有狀態運算需要時間視窗、聚合與狀態儲存，通常交給 Kafka Streams、ksqlDB 或 Apache Flink。Key 的選擇對回 introduction.md 第三章。
-->

---

<!-- _class: compact -->

# 讀客戶案例要注意的事

BRI 的案例是 Confluent 發布的客戶故事

- 內容大多引述 BRI 主管的說法
- 數字是**自行回報**，沒有經過第三方驗證

分清楚兩件事：

| 技術架構的描述 | 行銷性質的成效數字 |
|---|---|
| 用了哪些元件、資料怎麼流 | 「從一個月變成即時」「成本省 35%」 |
| 通常比較可信 | 要保留判斷 |

<!--
這不代表內容不可信，而是讀這類文章時要有意識。公司自己的工程部落格通常技術細節較多；廠商的客戶案例偏重成效。
-->

---

<!-- _class: chapter -->

# 六、角色四：Source of truth

Kafka 本身就是正本，其他系統是衍生出來的

<!--
前三種角色裡，正本都在別處；這種角色反過來。
-->

---

# 紐約時報原本的問題

- 好幾套內容管理系統，加上 **161 年**的報導存檔
- 搜尋、個人化推薦、網站與 App 都需要讀取已發布的內容
- 過去每套系統各自提供 API
    - 不同團隊在不同時期開發，欄位名稱與格式都不一致
    - 想一次讀取全部歷史內容非常困難

<!--
這和第一章 LinkedIn 的問題很像：每個需要內容的系統，都要認識每一套 API 的脾氣。
-->

---

# 紐約時報的 Publishing Pipeline

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="345" viewBox="0 0 1152 345" role="img" aria-label="The New York Times Publishing Pipeline"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="25" width="175" height="90" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="87.5" y="63.1" font-size="23">各內容</tspan><tspan x="87.5" y="93.0" font-size="23">管理系統</tspan></text><polyline points="175,70 215,70" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="215" y="25" width="170" height="90" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="300.0" y="63.1" font-size="23" font-weight="600">Gateway</tspan><tspan x="300.0" y="93.0" font-size="20">檢查格式</tspan></text><polyline points="385,70 425,70" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="425" y="15" width="300" height="110" rx="5" fill="#d9efed" stroke="#0b7d78" stroke-width="2"/><text fill="#182b43" text-anchor="middle"><tspan x="575.0" y="48.1" font-size="23" font-weight="600">Monolog</tspan><tspan x="575.0" y="78.0" font-size="20">單一 Partition</tspan><tspan x="575.0" y="107.9" font-size="20">1851 年至今，永久保存</tspan></text><polyline points="725,70 765,70" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="765" y="25" width="387" height="90" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="958.5" y="63.1" font-size="23" font-weight="600">Denormalizer</tspan><tspan x="958.5" y="93.0" font-size="20">Kafka Streams，打包文章與引用</tspan></text><polyline points="958,115 958,150 575,150 575,180" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="375" y="180" width="400" height="80" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="575.0" y="213.1" font-size="23" font-weight="600">Denormalized log</tspan><tspan x="575.0" y="243.0" font-size="20">多個 Partition，可平行讀</tspan></text><polyline points="575,260 575,285 130,285 130,300" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="0" y="300" width="260" height="45" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="130.0" y="330.2" font-size="22">搜尋 Elasticsearch</tspan></text><polyline points="575,260 575,285 576,285 576,300" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="446" y="300" width="260" height="45" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="576.0" y="330.2" font-size="22">網站與 App 前端</tspan></text><polyline points="575,260 575,285 1022,285 1022,300" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="892" y="300" width="260" height="45" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="1022.0" y="330.2" font-size="22">個人化推薦</tspan></text></svg>

<!--
Monolog 只有一個 Partition，因為文章會引用圖片、作者等內容，必須保證先讀到被引用的內容。全部內容不到 100 GB，單一 Partition 放在一顆磁碟上不成問題。不需要全域順序的用途，由 Denormalizer 打包後寫進有多個 Partition 的 log，讓下游平行讀取。
-->

---

# 為什麼可行

- 新系統上線：從 **Offset 0** 開始讀，就能建出完整的歷史資料
- 搜尋索引要改格式：建一個新的，從頭 **Replay**，追上後再切換
- 不選 Pub/Sub、SQS、Kinesis：需要**永久保存**與**有序讀取**，只有 Kafka 都做到

**需要順序的地方犧牲平行度，不需要的地方再把平行度拿回來**

<!--
這個設計的思想來自 Martin Kleppmann 的 Turning the database inside-out，在 Designing Data-Intensive Applications 有更完整的討論。和 Uber 對照：Uber 想擺脫 Partition 對平行度的限制，紐約時報刻意只用一個 Partition。
-->

---

<!-- _class: chapter -->

# 七、自己維運，還是交給雲端？

<!--
讀到這裡，可以看到兩類公司。
-->

---

<!-- _class: dense -->

# 兩類公司

| | LinkedIn、Uber、LINE、Netflix | Michelin、AO.com |
|---|---|---|
| 怎麼運作 Kafka | 自己維運大型叢集 | 從自己維運改用雲端託管 |
| 原因 | 規模極大，基礎設施是核心能力 | 想把人力放在業務上 |
| 和社群的關係 | 常把改進貢獻回 Apache Kafka | 主要是使用者 |

<!--
沒有哪一種比較好，取決於規模與基礎設施是不是公司的核心能力。
-->

---

# Michelin：從批次報表到即時庫存

- 庫存報表不可靠、資料過時，影響全球供應鏈
- 舊系統「非常批次、非常 monolithic」
- 先在機房自架 Kafka：需要**三名全職員工**維護，仍難以擴展
- 2021 年改用雲端託管，估計營運成本省下 **35%**
- 用 Kafka Streams 改寫流程編排系統，把 monolith 拆成可各自演進的服務

AO.com 也有類似經驗：自己維運時，一次 Broker 故障最多要**三天**才能解決

<!--
35% 是估計值，不是量測結果，這也是讀客戶案例時要注意的地方。
-->

---

# 大公司把改進貢獻回社群：Tiered Storage

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="320" viewBox="0 0 1152 320" role="img" aria-label="Tiered Storage"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="10" width="620" height="300" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"></text><text x="310" y="46" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600">Broker</text><rect x="30" y="70" width="560" height="95" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="310.0" y="110.3" font-size="24" font-weight="600">本機磁碟（local tier）</tspan><tspan x="310.0" y="141.5" font-size="21">最近幾小時的熱資料</tspan></text><rect x="30" y="185" width="560" height="95" rx="5" fill="#d9efed" stroke="#0b7d78" stroke-width="2"/><text fill="#182b43" text-anchor="middle"><tspan x="310.0" y="225.3" font-size="24" font-weight="600">遠端儲存（remote tier）</tspan><tspan x="310.0" y="256.5" font-size="21">S3、HDFS 等，保留數天到數月</tspan></text><polyline points="310,165 310,185" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="780" y="80" width="372" height="75" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="966.0" y="110.6" font-size="23" font-weight="600">低延遲 Consumer</tspan><tspan x="966.0" y="140.5" font-size="20">讀本機的新資料</tspan></text><rect x="780" y="195" width="372" height="75" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text fill="#182b43" text-anchor="middle"><tspan x="966.0" y="225.6" font-size="23" font-weight="600">回補、復原作業</tspan><tspan x="966.0" y="255.5" font-size="20">讀遠端的舊資料</tspan></text><polyline points="780,117 592,117" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="780,232 592,232" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

Uber 提出 **KIP-405**，和社群一起完成，3.6.0 以 early access 釋出

<!--
Uber 發現 Broker 要把所有資料存在本機磁碟，想保留更久就只能加機器。Tiered Storage 把舊資料搬到便宜的遠端儲存，保留時間不再受機器數量限制。現在所有 Kafka 使用者都能受益，這就是開源循環。
-->

---

# Kafka 今天的位置

- **通訊協定成為業界標準介面**：許多雲端廠商與公司提供相容的服務或實作
- **和其他系統結合**：搭配 Flink 做串流計算；Topic 轉成 Iceberg 表格供分析
- **AI 需要即時資料**：詐欺偵測、推薦系統、AI agent 都需要知道「現在正在發生什麼」

**Kafka 做的事和 2010 年一樣：讓需要資料的系統，即時拿到來源系統產生的事件**

<!--
收尾回到第一章：不管技術怎麼演進，Kafka 解決的核心問題沒有變。
-->

---

# 八、怎麼讀一篇 Kafka 案例

1. **原本的痛點是什麼？** 批次太慢、串接太多、服務耦合、還是維運成本？
2. **Kafka 扮演哪一種角色？** 事件骨幹、整合管線、串流處理、source of truth？
3. **付出了什麼代價？** 紐約時報放棄平行度、Netflix 接受少量遺失、Uber 多一層 Proxy
4. **這篇文章是誰寫的？** 工程部落格偏技術細節，客戶案例偏成效

這四個問題也可以直接用在作業的「**專案在解什麼**」

<!--
對應 introduction.md 最後的三個問題：在哪裡、為什麼、有什麼限制。
-->

---

<!-- _class: compact -->

# 討論題

1. Uber 讓平行度不受 Partition 數限制，紐約時報卻刻意只用一個 Partition。需求差在哪？各自放棄了什麼？
2. 舉一種可以接受遺失的資料，以及一種絕對不行的資料。Kafka 的設定應該有什麼不同？
3. 學校選課系統在開放第一分鐘湧入大量請求。用 Kafka 重新設計，你會建立哪些 Topic？Key 選什麼？

<!--
可以分組討論，每組挑一題報告。
-->

---

<!-- _class: compact -->

# 討論題（續）

4. 紐約時報永久保存所有內容。如果用同樣方式保存使用者個資，會遇到什麼問題？（提示：刪除權）
5. 挑一個你常用的服務（外送、叫車、社群、串流影音），推測它背後有哪些事件流，各是哪一種角色？
6. 為什麼 Michelin 選擇託管服務，而 Uber 選擇自己維運，還把 Tiered Storage 貢獻回社群？

<!--
第 5 題適合當作業「專案在解什麼」的暖身。
-->

---

<!-- _class: compact -->

# 延伸閱讀與資料來源

- 完整講義與所有出處：`campus-courses/kafka/real-world-cases.md`
- Jay Kreps，*The Log: What every software engineer should know about real-time data's unifying abstraction*
- Martin Kleppmann，*Turning the database inside-out*、*Designing Data-Intensive Applications*
- KIP-405: Kafka Tiered Storage

案例來源：LinkedIn、Uber、Netflix 工程部落格；ClickHouse 部落格（LY Corporation）；Confluent 部落格（The New York Times）與客戶案例（AO.com、BRI、Michelin）

<!--
所有連結列在講義文末。
-->
