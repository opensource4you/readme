---
marp: true
theme: default
size: 16:9
paginate: true
title: Kafka 的技術名詞講解與概覽
description: Kafka 技術分享，使用訂單事件理解資料流與故障處理
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

# Kafka 的技術名詞講解與概覽

<p class="lead">用一筆訂單事件，理解 Kafka 的運作</p>

`O1001` 訂單成立之後，資料去哪裡？

<!--
沿用講稿的一至七章順序。先理解 Kafka 是什麼與使用動機，再走過事件旅程、分區、讀取分工、保存與故障，最後回到完整事件流程。訂單與數字是教學示例。
-->
















---
















# 課程目標

1. Kafka 是什麼？系統為什麼會需要它？
2. Producer、Consumer、Broker 分別在做什麼？
3. Topic、Partition、Key、Offset 之間是什麼關係？
4. Consumer Group 如何分工？
5. Kafka 如何保存資料、處理故障？
6. 一筆 Event 從產生到被處理，中間發生了什麼？

<!--
接下來分七章展開，第六章專門討論網路失敗與可靠性，第七章把概念放回整條資料流。
-->
















---
















<!-- header: "一、Kafka 是什麼？為什麼需要 Kafka？" -->
<!-- _class: chapter -->

# 一、Kafka 是什麼？為什麼需要 Kafka？

先拆開「分散式」與「事件串流」，再看下單流程為什麼需要 Kafka。

<!--
先不用急著記 Kafka 名詞。先給定義，再分成分散式系統與事件串流兩個概念，接著給全景圖，最後用同步呼叫與 Kafka 的下單案例說明動機。
-->
















---
















# Kafka 是什麼？

**Kafka 是開源的分散式事件串流平台**

用來擷取、儲存與處理持續產生的資料

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="200" viewBox="0 0 1152 200" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="55" y="25" width="470" height="135" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="290.0" y="83.80000000000001" font-size="29" fill="#182b43" text-anchor="middle"><tspan x="290.0" dy="0">分散式系統</tspan><tspan x="290.0" dy="37.7">多台電腦透過網路協作</tspan></text><rect x="625" y="25" width="470" height="135" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="860.0" y="83.80000000000001" font-size="29" fill="#182b43" text-anchor="middle"><tspan x="860.0" dy="0">事件串流</tspan><tspan x="860.0" dy="37.7">持續產生、保存與處理紀錄</tspan></text></svg>

這是兩個面向：系統如何協作，以及資料如何流動

<!--
先用這句定義介紹 Kafka。第一次聽到分散式事件串流平台，仍可能不清楚它做什麼，因此拆成兩個概念，下一頁先解釋分散式系統。
-->
















---
















# Distributed System：分散式系統

<div class="lanes"><div class="lane"><b>多台電腦協作</b><div>透過網路，共同完成系統的工作</div></div><div class="lane"><b>資料分散保存</b><div>不必把所有資料放在同一台伺服器</div></div><div class="lane"><b>其中一台故障</b><div>透過備份資料與故障切換，盡量維持服務</div></div></div>

後面會遇到：Replication、Leader／Follower、Failure、Retry

<!--
分散式不是單純多開幾台機器，而是透過網路協作。備份與故障切換也有條件與取捨，第五、六章再談。這些問題並非 Kafka 獨有。先理解分散式，再看持續流動的資料是什麼。
-->
















---
















# Event Streaming：事件串流

| 類型 | 訊息 | 意義 |
|---|---|---|
| Command | `PlaceOrder` | 請建立訂單 |
| Event | `OrderPlaced` | 訂單已成立 |

<div class="flow"><div>OrderPlaced</div><span class="arrow">→</span><div>PaymentCompleted</div><span class="arrow">→</span><div>OrderShipped</div></div>

**Event 是對已發生事情的紀錄**

事件持續產生，就形成 Event Stream

<!--
Command 是要求執行操作，Event 是已發生事情的紀錄。這條線描述業務演進，不表示不同 Topic 之間有 Kafka 的順序保證。Kafka Record 也可以承載日誌或其他訊息，不限於業務事件。
-->
















---
















# 先看整張圖

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="355" viewBox="0 0 1152 355" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="120" width="175" height="95" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="87.5" y="160.0" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="87.5" dy="0">Producer</tspan><tspan x="87.5" dy="32.5">寫入 Record</tspan></text><rect x="215" y="0" width="545" height="345" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="487" y="32" font-size="27" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="487" dy="0">orders Topic</tspan></text><polyline points="175,167 215,167" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="870" y="0" width="280" height="345" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="1010" y="32" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="1010" dy="0">Consumer Group</tspan></text><rect x="230" y="55" width="515" height="81" rx="5" fill="#fff" stroke="#90a8bc" stroke-width="1.5"/><text x="250" y="84" font-size="25" fill="#182b43" text-anchor="start"><tspan x="250" dy="0">Partition 0：Record、Record …</tspan></text><text x="250" y="116" font-size="21" fill="#182b43" text-anchor="start"><tspan x="250" dy="0">Leader：Broker A   Follower：B、C</tspan></text><polyline points="745,95 805,95 805,125 890,125" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="230" y="149" width="515" height="81" rx="5" fill="#fff" stroke="#90a8bc" stroke-width="1.5"/><text x="250" y="178" font-size="25" fill="#182b43" text-anchor="start"><tspan x="250" dy="0">Partition 1：Record、Record …</tspan></text><text x="250" y="210" font-size="21" fill="#182b43" text-anchor="start"><tspan x="250" dy="0">Leader：Broker B   Follower：A、C</tspan></text><polyline points="745,189 805,189 805,125 890,125" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="230" y="243" width="515" height="81" rx="5" fill="#fff" stroke="#90a8bc" stroke-width="1.5"/><text x="250" y="272" font-size="25" fill="#182b43" text-anchor="start"><tspan x="250" dy="0">Partition 2：Record、Record …</tspan></text><text x="250" y="304" font-size="21" fill="#182b43" text-anchor="start"><tspan x="250" dy="0">Leader：Broker C   Follower：A、B</tspan></text><polyline points="745,283 805,283 805,280 890,280" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="890" y="82" width="240" height="86" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1010.0" y="117.80000000000001" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="1010.0" dy="0">Consumer A</tspan><tspan x="1010.0" dy="31.200000000000003">讀取 P0、P1</tspan></text><rect x="890" y="239" width="240" height="86" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1010.0" y="274.79999999999995" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="1010.0" dy="0">Consumer B</tspan><tspan x="1010.0" dy="31.200000000000003">讀取 P2</tspan></text></svg>

Topic 包含多個 Partition，Group 內的 Consumer 按 Partition 分工

<!--
先給全景圖，不要求此時理解所有名詞。Topic 是邏輯分類，真正資料在 Partition，副本可跨 Broker 分布。第二章解釋誰寫、誰讀，第三章解釋位置與順序，第四章分工，第五章複寫。接下來先看它為什麼有用。
-->
















---
















# 用下單流程理解 Kafka 的用途

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="345" viewBox="0 0 1152 345" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="133" width="180" height="80" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="90.0" y="181.75" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="90.0" dy="0">Customer</tspan></text><rect x="265" y="125" width="265" height="96" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="397.5" y="165.5" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="397.5" dy="0">Checkout</tspan><tspan x="397.5" dy="32.5">訂單成立</tspan></text><polyline points="180,173 265,173" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="840" y="4" width="290" height="57" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="985.0" y="41.25" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="985.0" dy="0">Payment Service</tspan></text><polyline points="530,173 690,173 690,32 840,32" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="840" y="72" width="290" height="57" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="985.0" y="109.25" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="985.0" dy="0">Inventory Service</tspan></text><polyline points="530,173 690,173 690,100 840,100" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="840" y="140" width="290" height="57" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="985.0" y="177.25" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="985.0" dy="0">Email Service</tspan></text><polyline points="530,173 690,173 690,168 840,168" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="840" y="208" width="290" height="57" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="985.0" y="245.25" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="985.0" dy="0">Analytics Service</tspan></text><polyline points="530,173 690,173 690,236 840,236" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="840" y="276" width="290" height="57" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="985.0" y="313.25" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="985.0" dy="0">Shipping Service</tspan></text><polyline points="530,173 690,173 690,304 840,304" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="675" y="28" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="675" dy="0">逐一呼叫</tspan></text></svg>

下游很慢或故障時，Checkout 必須決定等待、重試或回報錯誤

<!--
用電商下單案例說明。Payment 處理付款，Inventory 保留庫存，Email 通知，Analytics 統計，Shipping 出貨。同步呼叫需要明確結果時仍然有用，這裡重點是下單完成之後的後續作業與失敗處理。下一頁把直接呼叫改成事件發佈。
-->
















---
















# 若使用 Kafka

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="365" viewBox="0 0 1152 365" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="416" y="0" width="320" height="56" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="576.0" y="36.75" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="576.0" dy="0">Checkout Service</tspan></text><polyline points="576,56 576,112" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="600" y="93" font-size="23" fill="#182b43" text-anchor="start"><tspan x="600" dy="0">Publish OrderPlaced</tspan></text><rect x="265" y="112" width="620" height="111" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="576" y="146" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="576" dy="0">Kafka</tspan></text><rect x="310" y="165" width="530" height="45" rx="5" fill="#fff" stroke="#90a8bc" stroke-width="1.5"/><text x="575.0" y="195.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="575.0" dy="0">orders Topic：OrderPlaced</tspan></text><polyline points="576,278 576,223" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="600" y="263" font-size="22" fill="#182b43" text-anchor="start"><tspan x="600" dy="0">Subscribe / 主動讀取</tspan></text><polyline points="100,278 1050,278" fill="none" stroke="#0b7d78" stroke-width="2.5"/><rect x="10" y="310" width="218" height="50" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="119.0" y="343.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="119.0" dy="0">Payment</tspan></text><polyline points="119,310 119,278" fill="none" stroke="#0b7d78" stroke-width="2.5"/><rect x="241" y="310" width="218" height="50" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="350.0" y="343.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="350.0" dy="0">Inventory</tspan></text><polyline points="350,310 350,278" fill="none" stroke="#0b7d78" stroke-width="2.5"/><rect x="472" y="310" width="218" height="50" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="581.0" y="343.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="581.0" dy="0">Email</tspan></text><polyline points="581,310 581,278" fill="none" stroke="#0b7d78" stroke-width="2.5"/><rect x="703" y="310" width="218" height="50" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="812.0" y="343.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="812.0" dy="0">Analytics</tspan></text><polyline points="812,310 812,278" fill="none" stroke="#0b7d78" stroke-width="2.5"/><rect x="934" y="310" width="218" height="50" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1043.0" y="343.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="1043.0" dy="0">Shipping</tspan></text><polyline points="1043,310 1043,278" fill="none" stroke="#0b7d78" stroke-width="2.5"/></svg>

Email 暫停時，其他服務仍可讀取；Email 恢復後可繼續讀取尚未清除的事件

<!--
Checkout 發佈 OrderPlaced，Kafka 將事件寫入 orders Topic 並保存。Payment、Inventory、Email、Analytics、Shipping 訂閱需要的資料，各自主動發出讀取請求，Kafka 才回傳資料。圖中向上的箭頭代表讀取請求，避免誤認為 Broker 主動推送。不同服務透過不同 Group 維護進度，第四章再說明。降低直接呼叫的耦合仍不代表共用資源、資料格式與業務依賴完全不會互相影響。
-->















---
















# Event 處理大致流程

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="395" viewBox="0 0 1152 395" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="15" y="0" width="1120" height="72" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="42" y="44" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="42" dy="0">1. Publish</tspan></text><text x="295" y="44" font-size="24" fill="#182b43" text-anchor="start"><tspan x="295" dy="0">Checkout 發布一筆 OrderPlaced Event</tspan></text><polyline points="576,72 576,97" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="15" y="99" width="1120" height="72" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="42" y="143" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="42" dy="0">2. Store</tspan></text><text x="295" y="143" font-size="24" fill="#182b43" text-anchor="start"><tspan x="295" dy="0">Kafka 把 Event append 到 Topic 中並保存</tspan></text><polyline points="576,171 576,196" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="15" y="198" width="1120" height="72" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="42" y="242" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="42" dy="0">3. Subscribe</tspan></text><text x="295" y="242" font-size="24" fill="#182b43" text-anchor="start"><tspan x="295" dy="0">Payment、Inventory、Email、Analytics、Shipping 訂閱需要的 Event</tspan></text><polyline points="576,270 576,295" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="15" y="297" width="1120" height="72" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="42" y="341" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="42" dy="0">4. Process</tspan></text><text x="295" y="341" font-size="24" fill="#182b43" text-anchor="start"><tspan x="295" dy="0">每個 Service 按自己的進度讀取並處理</tspan></text></svg>

<!--
用這四步驟回顧下單流程。訂閱不表示 Broker 主動把資料推送到應用程式。帶到第二章：我們知道為什麼需要 Kafka，現在追蹤 O1001 成立後，一筆事件實際如何穿過這些角色。
-->
















---
















<!-- header: "二、一筆 Event 在 Kafka 內的旅程" -->
<!-- _class: chapter -->

# 二、一筆 Event 在 Kafka 內的旅程

訂單 O1001 已成立，現在追蹤 OrderPlaced 的寫入與讀取。

<!--
承接第一章的用途。先給整筆事件的旅程，再依序介紹 Client／Server、Producer、Serialization、Record、Broker、Topic、Consumer。
-->
















---
















# 一筆 Event 在 Kafka 內的旅程

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="340" viewBox="0 0 1152 340" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="12" y="10" width="355" height="103" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="189.5" y="54.3" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="189.5" dy="0">Checkout Service</tspan><tspan x="189.5" dy="31.200000000000003">建立 OrderPlaced Event</tspan></text><rect x="396" y="10" width="355" height="103" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="573.5" y="54.3" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="573.5" dy="0">Producer</tspan><tspan x="573.5" dy="31.200000000000003">序列化 Key 與 Value</tspan></text><rect x="780" y="10" width="355" height="103" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="957.5" y="54.3" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="957.5" dy="0">Broker</tspan><tspan x="957.5" dy="31.200000000000003">寫入 orders Topic 的 Partition</tspan></text><rect x="12" y="225" width="355" height="103" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="189.5" y="269.29999999999995" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="189.5" dy="0">應用程式</tspan><tspan x="189.5" dy="31.200000000000003">處理 OrderPlaced Event</tspan></text><rect x="396" y="225" width="355" height="103" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="573.5" y="269.29999999999995" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="573.5" dy="0">Consumer</tspan><tspan x="573.5" dy="31.200000000000003">反序列化 Key 與 Value</tspan></text><rect x="780" y="225" width="355" height="103" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="957.5" y="269.29999999999995" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="957.5" dy="0">Consumer</tspan><tspan x="957.5" dy="31.200000000000003">從 Partition 讀取 OrderPlaced</tspan></text><polyline points="367,61 396,61" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="751,61 780,61" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="957,113 957,225" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="980" y="177" font-size="22" fill="#182b43" text-anchor="start"><tspan x="980" dy="0">讀取</tspan></text><polyline points="780,277 751,277" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="396,277 367,277" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

依箭頭從上排左側開始，再往下排由右往左走完事件旅程

<!--
用同一筆 O1001 事件貫穿流程。Producer 指定 orders Topic，將 Key 與 Value 轉成 bytes，Broker 寫入一個 Partition。Consumer 取得資料，反序列化後交給應用程式。接下來逐一解釋這些角色。
-->
















---
















# Client 與 Server 的分工

<div class="flow"><div>Producer<br>Client</div><span class="arrow">→</span><div>Broker / Cluster<br>Server</div><span class="arrow">←</span><div>Consumer<br>Client，主動讀取</div></div>

| 角色 | 責任 |
|---|---|
| Producer | 寫入 Record |
| Broker | 保存與提供 Partition 資料 |
| Consumer | 主動讀取，交給應用程式處理 |

<!--
這張圖的箭頭表示網路請求。Producer 發送寫入請求，Consumer 主動向 Broker 發送讀取請求，Broker 才回傳資料。多個 Broker 組成 Cluster。Java KafkaConsumer 常以 poll(...) 取得資料。
-->
















---
















# Producer 與 Serialization

<div class="flow"><div>Checkout 的事件物件</div><span class="arrow">→</span><div>Producer<br>序列化 Key 與 Value</div><span class="arrow">→</span><div>Bytes<br>送往 Kafka</div></div>

**Producer 是把 Record 寫入 Kafka 的 Client**

Serialization 將程式資料轉成可傳輸的 bytes

<!--
Producer 可以使用 Kafka Producer API 將 OrderPlaced 寫入 orders。先介紹寫入端與序列化，等到 Consumer 再介紹反序列化。JSON 只是資料格式示例，不代表 Kafka 限定 JSON。
-->
















---
















# Record／Event：資料結構與業務意義

<div class="record-layout"><pre class="record-code">寫入位置：orders Topic
Record
├─ Key: "O1001"
├─ Value: {
│    "eventId": "E9001",
│    "eventType": "OrderPlaced",
│    "orderId": "O1001",
│    "amount": 1200
│  }
├─ Timestamp
└─ Headers:
   └─ event-type: "OrderPlaced"</pre><div><p><strong>Event</strong>：描述已發生的事情<br>例如「O1001 訂單已成立」</p><p><strong>Record</strong>：Kafka 傳輸與保存的一筆資料<br>這筆 Event 可表示為左側 Record</p><p><strong>Key</strong> 影響分區，<strong>Value</strong> 是主要資料<br><strong>Timestamp</strong> 是時間，<strong>Headers</strong> 放額外資訊</p></div></div>

<!--
這段 JSON 是 Value 的示例，不是 Kafka 的內部儲存格式。Event 強調業務意義，Record 是 Kafka 傳輸與保存的一筆資料。Topic 是目標位置，Key 常用於分區。Timestamp 可依設定使用建立時間或 Broker 寫入時間。Headers 可放 event type 或 trace ID。讀取後也會得到 Topic、Partition、Offset 等位置資訊。
-->
















---
















# Broker 與 Cluster

**Broker 是 Kafka 叢集中負責資料讀寫與保存的伺服器角色**

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="270" viewBox="0 0 1152 270" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="1150" height="260" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="575" y="32" font-size="28" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="575" dy="0">Kafka Cluster</tspan></text><rect x="22" y="55" width="350" height="180" rx="5" fill="#fff" stroke="#90a8bc" stroke-width="1.5"/><text x="197" y="87" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="197" dy="0">Broker A</tspan></text><text x="40" y="124" font-size="24" fill="#182b43" text-anchor="start"><tspan x="40" dy="0">orders / P0：Leader</tspan></text><text x="40" y="165" font-size="24" fill="#182b43" text-anchor="start"><tspan x="40" dy="0">orders / P1：Follower</tspan></text><text x="40" y="206" font-size="24" fill="#182b43" text-anchor="start"><tspan x="40" dy="0">orders / P2：Follower</tspan></text><rect x="402" y="55" width="350" height="180" rx="5" fill="#fff" stroke="#90a8bc" stroke-width="1.5"/><text x="577" y="87" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="577" dy="0">Broker B</tspan></text><text x="420" y="124" font-size="24" fill="#182b43" text-anchor="start"><tspan x="420" dy="0">orders / P0：Follower</tspan></text><text x="420" y="165" font-size="24" fill="#182b43" text-anchor="start"><tspan x="420" dy="0">orders / P1：Leader</tspan></text><text x="420" y="206" font-size="24" fill="#182b43" text-anchor="start"><tspan x="420" dy="0">orders / P2：Follower</tspan></text><rect x="782" y="55" width="350" height="180" rx="5" fill="#fff" stroke="#90a8bc" stroke-width="1.5"/><text x="957" y="87" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="957" dy="0">Broker C</tspan></text><text x="800" y="124" font-size="24" fill="#182b43" text-anchor="start"><tspan x="800" dy="0">orders / P0：Follower</tspan></text><text x="800" y="165" font-size="24" fill="#182b43" text-anchor="start"><tspan x="800" dy="0">orders / P1：Follower</tspan></text><text x="800" y="206" font-size="24" fill="#182b43" text-anchor="start"><tspan x="800" dy="0">orders / P2：Leader</tspan></text></svg>

多個 Broker 組成 Cluster；Partition 可分散到不同 Broker，同一 Partition 的 Replica 也跨 Broker 保存

<!--
Broker 是伺服器角色。這裡先說資料保存在哪些伺服器，第五章再講 Leader、Follower 與故障接手。下一頁說明資料的邏輯分類 Topic。
-->
















---
















<!-- _class: compact -->

# Topic：資料的邏輯分類

| Topic 名稱 | 可能承載的 Event（示例） |
|---|---|
| `orders` | OrderPlaced、OrderCancelled |
| `payments` | PaymentCompleted、PaymentFailed |
| `shipments` | OrderShipped、ShipmentDelivered |

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="208" viewBox="0 0 1152 208" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="1150" height="203" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="20" y="35" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="20" dy="0">orders Topic</tspan></text><rect x="15" y="52" width="1118" height="62" rx="5" fill="#fff" stroke="#90a8bc" stroke-width="1.5"/><text x="30" y="90" font-size="24" fill="#182b43" text-anchor="start"><tspan x="30" dy="0">Partition 0</tspan></text><rect x="220" y="62" width="440" height="42" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="440.0" y="90.7" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="440.0" dy="0">Record：OrderPlaced (O1001)</tspan></text><rect x="695" y="62" width="420" height="42" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="905.0" y="90.7" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="905.0" dy="0">Record：OrderCancelled (O1003)</tspan></text><rect x="15" y="122" width="1118" height="62" rx="5" fill="#fff" stroke="#90a8bc" stroke-width="1.5"/><text x="30" y="160" font-size="24" fill="#182b43" text-anchor="start"><tspan x="30" dy="0">Partition 1</tspan></text><rect x="220" y="132" width="440" height="42" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="440.0" y="160.7" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="440.0" dy="0">Record：OrderPlaced (O1002)</tspan></text><rect x="695" y="132" width="420" height="42" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="905.0" y="160.7" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="905.0" dy="0">Record：OrderCancelled (O1004)</tspan></text></svg>

<!--
Topic 是邏輯分類名稱。表中的多個事件是補充的命名示例，不是 Kafka 規定的分類。圖展示 orders Topic 包含多個 Partition，每個 Partition 中保存多筆 Record，Record 的 Value 承載業務 Event。每筆寫入選擇其中一個 Partition。不同 Topic 沒有跨 Topic 順序保證。
-->















---
















# Consumer 與 Deserialization

**Consumer 是從 Kafka 讀取資料的 Client**，Java 常透過 `poll(...)` 取得資料

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="248" viewBox="0 0 1152 248" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="10" width="277" height="66" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="138.5" y="51.75" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="138.5" dy="0">Producer：寫入 Record</tspan></text><rect x="436" y="10" width="280" height="66" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="576.0" y="51.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="576.0" dy="0">Broker：保存並提供資料</tspan></text><rect x="870" y="10" width="280" height="66" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1010.0" y="51.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="1010.0" dy="0">Consumer：主動讀取</tspan></text><polyline points="277,43 436,43" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="870,43 716,43" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="799" y="26" font-size="21" fill="#182b43" text-anchor="middle"><tspan x="799" dy="0">讀取請求</tspan></text><rect x="0" y="149" width="280" height="85" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="140.0" y="184.3" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="140.0" dy="0">orders Partition</tspan><tspan x="140.0" dy="31.200000000000003">Key / Value bytes</tspan></text><rect x="436" y="149" width="280" height="85" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="576.0" y="184.3" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="576.0" dy="0">Consumer</tspan><tspan x="576.0" dy="31.200000000000003">反序列化資料</tspan></text><rect x="870" y="149" width="280" height="85" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1010.0" y="184.3" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="1010.0" dy="0">應用程式</tspan><tspan x="1010.0" dy="31.200000000000003">處理 OrderPlaced Event</tspan></text><polyline points="280,192 436,192" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="716,192 870,192" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="797" y="176" font-size="21" fill="#182b43" text-anchor="middle"><tspan x="797" dy="0">還原後的資料</tspan></text></svg>

Producer 與 Consumer 必須約定資料格式；接下來看 Topic 裡的分區與順序

<!--
以 Java KafkaConsumer 為例，poll(...) 取得 Record，透過設定的反序列化器還原 Key 與 Value。格式不一致可能無法解析，或還原出錯誤資料。這裡完成第二章的讀取端旅程。
-->
















---
















<!-- header: "三、Topic、Partition、Key 與 Offset" -->
<!-- _class: chapter -->

# 三、Topic、Partition、Key 與 Offset

資料放在哪裡？同一張訂單的順序與讀取位置如何表示？

<!--
承接第二章資料寫入某個 Partition。依序說明：Partition、順序保證、Key、Offset、Partition 是 Log。
-->
















---
















# Topic、Partition、Record 的層級

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="305" viewBox="0 0 1152 305" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="1150" height="300" rx="5" fill="#f3f6f9" stroke="#718ba1" stroke-width="1.5"/><text x="18" y="31" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="18" dy="0">orders Topic</tspan></text><rect x="15" y="48.0" width="1120" height="77.33333333333333" rx="5" fill="#ffffff" stroke="#90a8bc" stroke-width="1.5"/><text x="30" y="94.66666666666666" font-size="23" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="30" dy="0">Partition 0</tspan></text><rect x="180.0" y="58.0" width="306.6666666666667" height="57.33333333333333" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="333.33333333333337" y="93.66666666666666" font-size="20" fill="#182b43" text-anchor="middle"><tspan x="333.33333333333337" dy="0">Record 0</tspan></text><rect x="493.3333333333333" y="58.0" width="306.6666666666667" height="57.33333333333333" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="646.6666666666666" y="93.66666666666666" font-size="20" fill="#182b43" text-anchor="middle"><tspan x="646.6666666666666" dy="0">Record 1</tspan></text><rect x="806.6666666666666" y="58.0" width="306.6666666666667" height="57.33333333333333" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="960.0" y="93.66666666666666" font-size="20" fill="#182b43" text-anchor="middle"><tspan x="960.0" dy="0">Record 2</tspan></text><rect x="15" y="128.66666666666669" width="1120" height="77.33333333333333" rx="5" fill="#ffffff" stroke="#90a8bc" stroke-width="1.5"/><text x="30" y="175.33333333333334" font-size="23" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="30" dy="0">Partition 1</tspan></text><rect x="180.0" y="138.66666666666669" width="306.6666666666667" height="57.33333333333333" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="333.33333333333337" y="174.33333333333334" font-size="20" fill="#182b43" text-anchor="middle"><tspan x="333.33333333333337" dy="0">Record 3</tspan></text><rect x="493.3333333333333" y="138.66666666666669" width="306.6666666666667" height="57.33333333333333" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="646.6666666666666" y="174.33333333333334" font-size="20" fill="#182b43" text-anchor="middle"><tspan x="646.6666666666666" dy="0">Record 4</tspan></text><rect x="806.6666666666666" y="138.66666666666669" width="306.6666666666667" height="57.33333333333333" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="960.0" y="174.33333333333334" font-size="20" fill="#182b43" text-anchor="middle"><tspan x="960.0" dy="0">Record 5</tspan></text><rect x="15" y="209.33333333333334" width="1120" height="77.33333333333333" rx="5" fill="#ffffff" stroke="#90a8bc" stroke-width="1.5"/><text x="30" y="256.0" font-size="23" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="30" dy="0">Partition 2</tspan></text><rect x="180.0" y="219.33333333333334" width="306.6666666666667" height="57.33333333333333" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="333.33333333333337" y="255.0" font-size="20" fill="#182b43" text-anchor="middle"><tspan x="333.33333333333337" dy="0">Record 6</tspan></text><rect x="493.3333333333333" y="219.33333333333334" width="306.6666666666667" height="57.33333333333333" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="646.6666666666666" y="255.0" font-size="20" fill="#182b43" text-anchor="middle"><tspan x="646.6666666666666" dy="0">Record 7</tspan></text><rect x="806.6666666666666" y="219.33333333333334" width="306.6666666666667" height="57.33333333333333" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="960.0" y="255.0" font-size="20" fill="#182b43" text-anchor="middle"><tspan x="960.0" dy="0">Record 8</tspan></text></svg>

- Topic 是邏輯分類；Partition 是 Topic 裡的有序 Log
- 每筆 Record 只寫入一個 Partition，分區讓資料分散保存與平行讀取

<!--
此圖展示 Topic 分區，沒有展示 Replica。Replica 是同一個 Partition 的副本，稍後另談。Partition 可以分布於不同 Broker，讓保存與讀取分散到多台機器。Consumer 讀過資料不會讓資料立即消失。
-->
















---
















# 順序保證的範圍

<div class="lanes"><div class="lane"><b>Partition 0</b><div><div class="flow"><div>A</div><span class="arrow">→</span><div>B</div><span class="arrow">→</span><div>C</div></div></div></div><div class="lane"><b>Partition 1</b><div><div class="flow"><div>D</div><span class="arrow">→</span><div>E</div><span class="arrow">→</span><div>F</div></div></div></div></div>

**可以依序讀取 A、B、C，無法推論 A 與 D 的先後**

平行工作執行緒也可能改變業務完成順序

<!--
保證的範圍是單一 Topic 的單一 Partition 的 Log 順序。寫入順序、讀取順序與業務完成順序要分開說。圖中的英文字母是不同 Record，不代表全域時間排序。
-->
















---
















# Key：讓同一張訂單進入同一分區

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="330" viewBox="0 0 1152 330" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="75" width="360" height="165" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="180.0" y="133.75" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="180.0" dy="0">Producer</tspan><tspan x="180.0" dy="32.5">以 orderId 作為 Key</tspan><tspan x="180.0" dy="32.5">三筆 Event：Key = O1001</tspan></text><polyline points="360,156 454,156" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="455" y="0" width="695" height="325" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="475" y="32" font-size="27" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="475" dy="0">同一個 orders Topic</tspan></text><rect x="472" y="51" width="660" height="215" rx="5" fill="#fff" stroke="#90a8bc" stroke-width="1.5"/><text x="490" y="82" font-size="25" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="490" dy="0">Partition 0：依寫入順序保存</tspan></text><text x="495" y="124" font-size="25" fill="#182b43" text-anchor="start"><tspan x="495" dy="0">1. O1001  OrderPlaced</tspan></text><text x="495" y="172" font-size="25" fill="#182b43" text-anchor="start"><tspan x="495" dy="0">2. O1001  PaymentCompleted</tspan></text><text x="495" y="220" font-size="25" fill="#182b43" text-anchor="start"><tspan x="495" dy="0">3. O1001  OrderShipped</tspan></text><text x="490" y="303" font-size="23" fill="#182b43" text-anchor="start"><tspan x="490" dy="0">Partition 1、2：其他 Record</tspan></text></svg>

前提：預設 Key 分區方式，Topic 分區數量與分區規則不變

Consumer 可依同一 Partition 的寫入順序，讀取 O1001 的三筆事件

<!--
三個事件必須寫在同一 Topic，才可透過同一 Partition 的 Log 排序。Key 不會把事件重新排成業務期待的先後，也不提供跨 Topic 順序。顯式指定 Partition、自訂 partitioner 或增加 Partition 數都可能改變分區結果。事件若需要嚴格業務順序，Producer 發送方式與應用程式也要一起設計。
-->
















---















# Key 的順序保證與使用條件

| 寫入方式 | 可以依賴的範圍 |
|---|---|
| 同一 Topic、相同 Key，分區數與規則不變 | 進入同一 Partition，依其寫入順序讀取 |
| 分別寫到 orders、payments、shipments | 不提供跨 Topic 順序保證 |

指定 Partition、自訂分區規則，或增加 Partition 數量，都可能改變相同 Key 的分區結果

<!--
Key 決定分區的條件必須說清楚。Producer 不會因為相同 Key 就自動把事件重新排成業務期待的順序。這裡的三筆事件需要實際按預期順序寫入同一個 Topic，再由 Consumer 依 Partition Log 讀取。
-->















---
















# Offset：Partition 內的位置

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="280" viewBox="0 0 1152 280" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="1150" height="275" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="20" y="32" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="20" dy="0">orders Topic / Partition 0：Compaction 清理示例</tspan></text><text x="20" y="101" font-size="25" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="20" dy="0">清理前</tspan></text><rect x="190" y="55" width="217" height="82" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="298.5" y="89.4" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="298.5" dy="0">Offset 0</tspan><tspan x="298.5" dy="28.6">O1001 CREATED</tspan></text><rect x="427" y="55" width="217" height="82" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="535.5" y="89.4" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="535.5" dy="0">Offset 1</tspan><tspan x="535.5" dy="28.6">O1002 CREATED</tspan></text><rect x="664" y="55" width="217" height="82" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="772.5" y="89.4" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="772.5" dy="0">Offset 2</tspan><tspan x="772.5" dy="28.6">O1001 PAID</tspan></text><rect x="901" y="55" width="217" height="82" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1009.5" y="89.4" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="1009.5" dy="0">Offset 3</tspan><tspan x="1009.5" dy="28.6">O1001 SHIPPED</tspan></text><text x="20" y="211" font-size="25" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="20" dy="0">清理後</tspan></text><rect x="190" y="165" width="217" height="82" rx="5" fill="#f8fafc" stroke="#b4c5d3" stroke-width="1.5" stroke-dasharray="6 4"/><text x="298" y="213" font-size="24" fill="#698199" text-anchor="middle"><tspan x="298" dy="0">已移除</tspan></text><rect x="427" y="165" width="217" height="82" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="535.5" y="199.39999999999998" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="535.5" dy="0">Offset 1</tspan><tspan x="535.5" dy="28.6">O1002 CREATED</tspan></text><rect x="664" y="165" width="217" height="82" rx="5" fill="#f8fafc" stroke="#b4c5d3" stroke-width="1.5" stroke-dasharray="6 4"/><text x="772" y="213" font-size="24" fill="#698199" text-anchor="middle"><tspan x="772" dy="0">已移除</tspan></text><rect x="901" y="165" width="217" height="82" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1009.5" y="199.39999999999998" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="1009.5" dy="0">Offset 3</tspan><tspan x="1009.5" dy="28.6">O1001 SHIPPED</tspan></text></svg>

不同 Partition 可各自有 Offset 0：`orders / P0 / 0` 與 `orders / P1 / 0` 是不同位置

清理後只剩 Offset **1、3**，不會重新編成 0、1；Offset 不是訂單 ID 或 Event ID

<!--
這個清理示例是背景 Compaction 的可能結果：同一 Key O1001 只留下較新狀態 SHIPPED，O1002 的 CREATED 仍保留。剩下 Offset 1 與 3，原來的位置不重新編號，所以出現空洞。清理不會立即完成。不同 Partition 各自維護 Offset，例如 P0 的 0 與 P1 的 0 不同。Compaction 第五章再展開。
-->













---
















# Partition 是一條 Log

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="290" viewBox="0 0 1152 290" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="1150" height="118" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="20" y="30" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="20" dy="0">orders / Partition 0：讀取後 Record 仍在 Log 中</tspan></text><rect x="20" y="48" width="355" height="59" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="197.5" y="70.6" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="197.5" dy="0">Offset 0</tspan><tspan x="197.5" dy="29.900000000000002">OrderPlaced O1001</tspan></text><rect x="398" y="48" width="355" height="59" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="575.5" y="70.6" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="575.5" dy="0">Offset 1</tspan><tspan x="575.5" dy="29.900000000000002">OrderPlaced O1002</tspan></text><rect x="776" y="48" width="355" height="59" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="953.5" y="70.6" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="953.5" dy="0">Offset 2</tspan><tspan x="953.5" dy="29.900000000000002">OrderPlaced O1003</tspan></text><rect x="20" y="198" width="475" height="73" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="257.5" y="243.25" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="257.5" dy="0">Email Consumer：已讀過 Offset 0</tspan></text><rect x="656" y="198" width="475" height="73" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="893.5" y="242.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="893.5" dy="0">Analytics Consumer：仍可讀取 Offset 0</tspan></text><polyline points="197,107 197,198" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="197,135 893,135 893,198" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="197,107 197,135" fill="none" stroke="#0b7d78" stroke-width="2.5"/></svg>

**讀取與 Commit 都不會取走 Record**；新 Record 持續附加在 Log 尾端

何時清理？Retention 依時間／容量清理舊資料，Compaction 依 Key 清理較舊值

<!--
Email 已寄出 O1001 通知，並提交自己的進度，Record 仍留在 Kafka，所以不同 Group 的 Analytics 仍能讀取這筆事件。消費進度與資料生命週期分開管理。時間／容量保留通常按 Segment 管理，Compaction 是背景清理，不是對單筆 Record 立即刪除。Topic 可配置 delete、compact 或兩者並用，第五章說明。
-->













---
















<!-- header: "四、Consumer Group：多個 Consumer 怎麼分工？" -->
<!-- _class: chapter -->

# 四、Consumer Group：多個 Consumer 怎麼分工？

有多個 Partition 與多個 Consumer，怎麼分配工作與記錄進度？

<!--
依序介紹 Group 分工、不同 Group、Committed Offset、Rebalance、Lag、Replay。下一章說明 Retention 的設定與儲存管理。
-->
















---
















# Consumer Group 的 Partition 分工

**Consumer Group 是一群共同分工讀取 Topic 的 Consumer**

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="260" viewBox="0 0 1152 260" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="475" height="255" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="238" y="30" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="238" dy="0">orders Topic：3 個 Partition</tspan></text><rect x="665" y="0" width="485" height="255" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="908" y="30" font-size="24" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="908" dy="0">analytics-group：4 個 Consumer</tspan></text><rect x="16" y="48" width="443" height="53" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="237.5" y="82.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="237.5" dy="0">Partition 0：Record、Record …</tspan></text><rect x="16" y="115" width="443" height="53" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="237.5" y="149.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="237.5" dy="0">Partition 1：Record、Record …</tspan></text><rect x="16" y="182" width="443" height="53" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="237.5" y="216.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="237.5" dy="0">Partition 2：Record、Record …</tspan></text><rect x="682" y="43" width="450" height="44" rx="5" fill="#ffffff" stroke="#90a8bc" stroke-width="1.5"/><text x="907.0" y="73.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="907.0" dy="0">Consumer A：P0、P1</tspan></text><rect x="682" y="95" width="450" height="44" rx="5" fill="#ffffff" stroke="#90a8bc" stroke-width="1.5"/><text x="907.0" y="125.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="907.0" dy="0">Consumer B：P2</tspan></text><rect x="682" y="147" width="450" height="44" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="907.0" y="177.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="907.0" dy="0">Consumer C：Idle</tspan></text><rect x="682" y="199" width="450" height="44" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="907.0" y="229.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="907.0" dy="0">Consumer D：Idle</tspan></text><polyline points="459,74 560,74 560,60 682,60" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="459,141 605,141 605,76 682,76" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="459,208 625,208 625,117 682,117" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

同一 Group 中，Partition 同時只給一個 Consumer；一個 Consumer 可負責多個 Partition

**3 個 Partition + 4 個 Consumer：最多只有 3 個 Consumer 可以同時負責 Partition**

<!--
刻意不平均的教學分配：A 負責 P0、P1，B 負責 P2，C 與 D 閒置。這張圖只有兩個 Active Consumer，不表示最多只能兩個，理論上限仍是三個。實際分配取決於分配策略，一般會盡量均衡。超過 Partition 數量的 Consumer 無法增加這個 Group 對此 Topic 的分區讀取平行度。Consumer 可內含於服務行程，Group 不是額外保存事件的資料副本。
-->













---













# Partition 數量限制消費的平行處理能力

| Partition 數量 | Consumer 數量 | 最多 Active Consumer |
|---|---|---|
| 1 | 4 | 1 |
| 3 | 4 | 3 |
| 6 | 4 | 4 |
| 6 | 8 | 6 |

**Partition 決定平行讀取上限，Consumer 決定實際投入多少處理資源**

分區數要考慮 Throughput、Ordering 與資源成本，更多 Partition 不代表一定更快

<!--
這裡是傳統 Consumer Group 對單一 Topic 的分區讀取並行度，理論上限是 min(Partition 數，Consumer 數)。最多 Active 不等於實際分配一定達到這個數字，例如上一頁刻意不平均時只有 A、B 在工作。Partition 數量變更也可能改變 Key 的分區結果。應用內部可另有工作執行緒，但必須管理順序與 Commit，這裡不把那種並行度當成分區讀取上限。
-->













---
















# 不同 Group，各自讀取同一份資料

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="275" viewBox="0 0 1152 275" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="300" y="0" width="550" height="70" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="575.0" y="43.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="575.0" dy="0">orders / Partition 0：Record 0、1、2、3</tspan></text><rect x="10" y="145" width="355" height="105" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="187.5" y="190.0" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="187.5" dy="0">inventory-group</tspan><tspan x="187.5" dy="32.5">Committed Offset：3</tspan></text><polyline points="575,70 575,105 187,105 187,145" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="394" y="145" width="355" height="105" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="190.0" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">email-group</tspan><tspan x="571.5" dy="32.5">Committed Offset：1</tspan></text><polyline points="575,70 575,105 571,105 571,145" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="778" y="145" width="355" height="105" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="955.5" y="190.0" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="955.5" dy="0">analytics-group</tspan><tspan x="955.5" dy="32.5">Committed Offset：4</tspan></text><polyline points="575,70 575,105 955,105 955,145" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

不同 Group 可獨立消費同一份資料，各自維護消費進度

**一個 Group 的消費，不會改變其他 Group 的進度**

Group 對應獨立的消費需求，Consumer 數量取決於處理能力與平行度需求

<!--
Group 之間不會因為某一 Group 讀走 Record，就讓其他 Group 看不到。例中的進度不同，Email 慢一點仍可讀取保留中的 Record。這與同一 Queue 中一次工作通常由一個 Worker 處理的模式不同。
-->
















---













<!-- _class: compact -->

# Consumer Group、Consumer 與 Downstream Service

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="334" viewBox="0 0 1152 334" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="265" y="0" width="620" height="78" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="575" y="30" font-size="27" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="575" dy="0">orders Topic</tspan></text><rect x="280" y="42" width="185" height="28" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="372.5" y="63.35" font-size="21" fill="#182b43" text-anchor="middle"><tspan x="372.5" dy="0">Partition 0</tspan></text><rect x="479" y="42" width="185" height="28" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="63.35" font-size="21" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">Partition 1</tspan></text><rect x="678" y="42" width="185" height="28" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="770.5" y="63.35" font-size="21" fill="#182b43" text-anchor="middle"><tspan x="770.5" dy="0">Partition 2</tspan></text><rect x="10" y="125" width="355" height="205" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="187" y="154" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="187" dy="0">payment-group</tspan></text><rect x="27" y="171" width="321" height="30" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="187.5" y="193.7" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="187.5" dy="0">Consumer C1：P0</tspan></text><rect x="27" y="207" width="321" height="30" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="187.5" y="229.7" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="187.5" dy="0">Consumer C2：P1</tspan></text><rect x="27" y="243" width="321" height="30" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="187.5" y="265.7" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="187.5" dy="0">Consumer C3：P2</tspan></text><text x="187" y="316" font-size="24" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="187" dy="0">Payment Service</tspan></text><polyline points="187,279 187,292" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="575,78 575,98 187,98 187,125" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="394" y="125" width="355" height="205" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="571" y="154" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="571" dy="0">inventory-group</tspan></text><rect x="411" y="171" width="321" height="30" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="193.7" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">Consumer C1：P0、P1</tspan></text><rect x="411" y="207" width="321" height="30" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="229.7" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">Consumer C2：P2</tspan></text><text x="571" y="316" font-size="24" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="571" dy="0">Inventory Service</tspan></text><polyline points="571,243 571,292" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="575,78 575,98 571,98 571,125" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="778" y="125" width="355" height="205" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="955" y="154" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="955" dy="0">analytics-group</tspan></text><rect x="795" y="171" width="321" height="30" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="955.5" y="193.7" font-size="22" fill="#182b43" text-anchor="middle"><tspan x="955.5" dy="0">Consumer C1：P0、P1、P2</tspan></text><text x="955" y="316" font-size="24" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="955" dy="0">Analytics Service</tspan></text><polyline points="955,207 955,292" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="575,78 575,98 955,98 955,125" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

- Group 數量：需要獨立消費事件的應用或服務
- Consumer 數量：流量、處理能力與部署規模
- Partition 數量：決定單一 Consumer Group 對此 Topic 的最大消費平行度。

<!--
三個 Group 各自讀取 orders 的全部三個 Partition。Payment 配三個 Consumer，Inventory 配兩個，Analytics 配一個。圖中 C1、C2、C3 都是各自 Group 的 Consumer，不是跨 Group 共用成員。通常 Consumer 是下游服務實例裡的 Kafka Client，圖中的 Consumer 到 Service 表示由該服務負責事件處理，不要求 Consumer 額外部署成獨立服務。各 Group 的 Partition 分配與進度互相獨立。
-->













---
















# Committed Offset 指向下一筆

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="190" viewBox="0 0 1152 190" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="12" y="58" width="146" height="93" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="85.0" y="97.30000000000001" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="85.0" dy="0">Offset 0</tspan><tspan x="85.0" dy="31.200000000000003">已處理 ✓</tspan></text><rect x="174" y="58" width="146" height="93" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="247.0" y="97.30000000000001" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="247.0" dy="0">Offset 1</tspan><tspan x="247.0" dy="31.200000000000003">已處理 ✓</tspan></text><rect x="336" y="58" width="146" height="93" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="409.0" y="97.30000000000001" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="409.0" dy="0">Offset 2</tspan><tspan x="409.0" dy="31.200000000000003">已處理 ✓</tspan></text><rect x="498" y="58" width="146" height="93" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.0" y="97.30000000000001" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="571.0" dy="0">Offset 3</tspan><tspan x="571.0" dy="31.200000000000003">已處理 ✓</tspan></text><rect x="660" y="58" width="146" height="93" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="733.0" y="97.30000000000001" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="733.0" dy="0">Offset 4</tspan><tspan x="733.0" dy="31.200000000000003">已處理 ✓</tspan></text><rect x="822" y="58" width="146" height="93" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="895.0" y="97.30000000000001" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="895.0" dy="0">Offset 5</tspan><tspan x="895.0" dy="31.200000000000003">尚未提交</tspan></text><rect x="984" y="58" width="146" height="93" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1057.0" y="97.30000000000001" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="1057.0" dy="0">Offset 6</tspan><tspan x="1057.0" dy="31.200000000000003">尚未提交</tspan></text><polyline points="897,5 897,57" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="680" y="29" font-size="26" fill="#182b43" text-anchor="end" font-weight="600"><tspan x="680" dy="0">Committed Offset = 5</tspan></text></svg>

Commit 5 表示**從 Offset 5 恢復消費**，不表示 Offset 5 已處理完成

Committed Offset 是 Group 針對特定 **Topic／Partition** 記錄的進度

**Commit 只記錄消費進度，不代表業務處理成功。**

<!--
圖中的已處理標示是假設應用先完成 Offset 0–4，再提交 5 的示例。Committed Offset 是 Group 針對特定 Topic／Partition 宣告的讀取進度，不會自動證明業務處理完成。可自動或手動提交。若要讓 commit 表示完成，必須在處理成功後才提交。平行處理時，不能因為較大 Offset 已完成就跨過較小但未完成的 Record。
-->
















---
















<!-- _class: compact -->

# Rebalance：重新分配責任

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="265" viewBox="0 0 1152 265" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="525" height="255" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="263" y="31" font-size="24" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="263" dy="0">故障前：analytics-group</tspan></text><rect x="16" y="57" width="142" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="87.0" y="88.55" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="87.0" dy="0">Partition 0</tspan></text><rect x="293" y="57" width="214" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="400.0" y="88.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="400.0" dy="0">Consumer A</tspan></text><polyline points="158,81 293,81" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="16" y="117" width="142" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="87.0" y="148.55" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="87.0" dy="0">Partition 1</tspan></text><rect x="293" y="117" width="214" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="400.0" y="148.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="400.0" dy="0">Consumer A</tspan></text><polyline points="158,141 293,141" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="16" y="177" width="142" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="87.0" y="208.55" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="87.0" dy="0">Partition 2</tspan></text><rect x="293" y="177" width="214" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="400.0" y="208.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="400.0" dy="0">Consumer B</tspan></text><polyline points="158,201 293,201" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="625" y="0" width="525" height="255" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="888" y="31" font-size="24" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="888" dy="0">Consumer A 故障後：analytics-group</tspan></text><rect x="641" y="57" width="142" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="712.0" y="88.55" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="712.0" dy="0">Partition 0</tspan></text><rect x="918" y="57" width="214" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1025.0" y="88.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="1025.0" dy="0">Consumer B</tspan></text><polyline points="783,81 918,81" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="641" y="117" width="142" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="712.0" y="148.55" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="712.0" dy="0">Partition 1</tspan></text><rect x="918" y="117" width="214" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1025.0" y="148.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="1025.0" dy="0">Consumer B</tspan></text><polyline points="783,141 918,141" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="641" y="177" width="142" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="712.0" y="208.55" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="712.0" dy="0">Partition 2</tspan></text><rect x="918" y="177" width="214" height="47" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1025.0" y="208.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="1025.0" dy="0">Consumer B</tspan></text><polyline points="783,201 918,201" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="528,127 620,127" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="574" y="107" font-size="20" fill="#182b43" text-anchor="middle"><tspan x="574" dy="0">重新分配</tspan></text></svg>

**改變的是誰負責消費，不是 Record 在 Partition 中的儲存順序。**
- Consumer 加入、離開、故障，或 Topic Partition 數量變更，都可能觸發 Rebalance
- Rebalance 可能造成 Record 重複處理，例如 Consumer A 已經處理 Offset 2，卻還沒 Commit，Consumer B 接手後可能重新讀到 Offset 2。



<!--
Consumer 加入、離開、故障，或 Topic Partition 變更，都可能造成重新分配。這張圖是簡化的分配結果，實際取決於協定與分配策略。接手的 Consumer 需要根據有效的已提交進度恢復，未提交的已處理資料可能再次執行。
-->
















---
















# Consumer Lag：進度與尾端的距離

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="260" viewBox="0 0 1152 260" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><text x="20" y="32" font-size="27" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="20" dy="0">orders Topic / Partition 0</tspan></text><rect x="40" y="118" width="720" height="85" rx="5" fill="#cfe6e2" stroke="#90a8bc" stroke-width="1.5"/><rect x="760" y="118" width="309" height="85" rx="5" fill="#f4e4bd" stroke="#90a8bc" stroke-width="1.5"/><text x="400" y="153" font-size="27" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="400" dy="0">Offset 0–699</tspan></text><text x="400" y="185" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="400" dy="0">analytics-group 已提交進度</tspan></text><text x="914" y="153" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="914" dy="0">Offset 700–999</tspan></text><text x="914" y="185" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="914" dy="0">尚未提交進度</tspan></text><polyline points="760,60 760,118" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="743" y="83" font-size="25" fill="#182b43" text-anchor="end" font-weight="600"><tspan x="743" dy="0">Committed Offset = 700</tspan></text><polyline points="1069,218 1069,203" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="1050" y="247" font-size="25" fill="#182b43" text-anchor="end" font-weight="600"><tspan x="1050" dy="0">Log End Offset = 1000</tspan></text><polyline points="760,99 1069,99" fill="none" stroke="#0b7d78" stroke-width="2.5"/><text x="914" y="68" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="914" dy="0">Consumer Lag = 300</tspan></text></svg>

Lag 反映 Group 的已提交進度距離 Partition 尾端有多遠

- 持續上升，可能代表消費速度跟不上資料產生速度
- 不能直接指出原因，也不一定等於尚未完成的業務筆數

<!--
接續 Rebalance：重新分配或消費暫停可能影響進度，所以用 Lag 觀察一個 Group 落後尾端多少。此圖假設 Log 已寫入連續的 Offset 0–999，Log End Offset 1000 是下一個可寫位置。analytics-group 已提交 700，代表恢復時從 700 開始，因此位置差為300。尚未提交不等於從未讀取或業務未完成，也可能已處理但還沒提交。實際監控工具尾端基準可能不同。若有 Compaction 空洞，Offset 差不等於實際保留的 Record 數。
-->













---
















# Replay：重新讀取保留中的資料

<div class="flow"><div>昨天的 Analytics 有 Bug</div><span class="arrow">→</span><div>修正程式</div><span class="arrow">→</span><div>將讀取位置移回舊 Offset</div><span class="arrow">→</span><div>重新計算</div></div>

修正 Analytics 程式後，重讀 Kafka 仍保留的歷史 Records 並重新計算

Replay 也可用於重新計算新統計，**不只用於故障恢復**

**可能再次觸發寄信、扣款等副作用，必須留意重複處理**

<!--
Replay 是重新讀資料，不表示必須先修改 Committed Offset。可透過調整當前讀取位置或使用新的 Group 等方式進行，具體提交策略按用途決定。除了修復 Bug，也能在統計邏輯改變時，用仍保留的歷史資料重新計算。資料已被清除就無法重讀。寄信、扣款等副作用也可能再次執行，需要業務冪等設計。
-->













---











<!-- header: "五、Kafka 怎麼保存資料、面對故障？" -->
<!-- _class: chapter -->

# 五、Kafka 怎麼保存資料、面對故障？

1. Broker 故障時，Kafka 如何避免資料遺失？
2. Records 持續增加，Kafka 如何管理儲存空間？

<!--
前一章說明 Consumer 讀取與進度，這一章回到保存資料的 Server。先處理可靠性：只有一份資料時 Broker 故障怎麼辦？再處理儲存：Record 不因讀取而刪除，持續增加要如何管理？兩條線最後都回到 orders Topic 的 Partition Log。
-->











---












# 單一 Broker 有什麼風險？

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="250" viewBox="0 0 1152 250" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="68" width="340" height="106" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="170.0" y="113.5" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="170.0" dy="0">Checkout Service / Producer</tspan><tspan x="170.0" dy="32.5">發送 OrderPlaced</tspan></text><rect x="475" y="0" width="520" height="245" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="735" y="35" font-size="27" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="735" dy="0">Broker A：只有一份資料</tspan></text><rect x="495" y="68" width="480" height="106" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="735.0" y="113.19999999999999" font-size="26" fill="#182b43" text-anchor="middle"><tspan x="735.0" dy="0">orders / Partition 0</tspan><tspan x="735.0" dy="33.800000000000004">保存 OrderPlaced Record</tspan></text><polyline points="340,122 495,122" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="1015" y="101" font-size="64" fill="#b74045" text-anchor="middle"><tspan x="1015" dy="0">×</tspan></text><text x="735" y="218" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="735" dy="0">Broker A 故障，Partition 無法正常提供服務</tspan></text></svg>

如果把同一 Partition 複製到多個 Broker，能否降低單點故障的風險？

<!--
只把 Partition 0 放在 Broker A，A 故障時沒有其他副本能接手。圖表示失去存取能力，不表示每次 Broker 暫停都已永久丟失磁碟資料。下一頁用 Replication 回答：讓同一個 Partition 有多份 Replica，分散到不同 Broker。
-->











---












# Replication：一個 Partition 的三份副本

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="287" viewBox="0 0 1152 287" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="105" width="205" height="78" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="102.5" y="153.1" font-size="26" fill="#182b43" text-anchor="middle"><tspan x="102.5" dy="0">Producer</tspan></text><rect x="270" y="0" width="380" height="270" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="460" y="35" font-size="27" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="460" dy="0">Broker A</tspan></text><rect x="290" y="70" width="340" height="165" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="460.0" y="128.75" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="460.0" dy="0">orders / P0</tspan><tspan x="460.0" dy="32.5">Leader Replica</tspan><tspan x="460.0" dy="32.5">Record 0、1、2</tspan></text><polyline points="205,144 290,144" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="860" y="0" width="290" height="125" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="1005" y="30" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="1005" dy="0">Broker B</tspan></text><rect x="876" y="47" width="257" height="60" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1004.5" y="85.05" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="1004.5" dy="0">P0 Follower Replica</tspan></text><polyline points="630,144 750,144 750,77 876,77" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="860" y="145" width="290" height="125" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="1005" y="175" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="1005" dy="0">Broker C</tspan></text><rect x="876" y="192" width="257" height="60" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1004.5" y="230.05" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="1004.5" dy="0">P0 Follower Replica</tspan></text><polyline points="630,144 750,144 750,222 876,222" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="736" y="24" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="736" dy="0">資料複寫</tspan></text></svg>

**Replication Factor = 3**：同一 Partition 有三份 Replica，包含 Leader

**Replication 讓 Partition 不必依賴單一 Broker 保存資料**

<!--
Replica 是同一個 Partition 的副本。A 是 Leader，B、C 是 Follower，從 Leader 複製資料。Replication 提供故障容錯，但不保證任何故障情境都不丟資料。副本是否跟得上、何時確認寫入、故障時能否安全切換，都影響結果。圖中箭頭表示資料複寫方向；Follower 主動向 Leader 取得資料。
-->











---












# Leader／Follower：誰處理讀寫？

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="275" viewBox="0 0 1152 275" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="42" width="273" height="74" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="136.5" y="88.45" font-size="27" fill="#182b43" text-anchor="middle"><tspan x="136.5" dy="0">Producer</tspan></text><rect x="442" y="42" width="285" height="74" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="584.5" y="87.75" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="584.5" dy="0">Broker A / P0 Leader</tspan></text><rect x="876" y="42" width="273" height="74" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1012.5" y="87.75" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="1012.5" dy="0">Follower B、C</tspan></text><polyline points="273,78 442,78" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="352" y="59" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="352" dy="0">寫入</tspan></text><polyline points="876,78 727,78" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="801" y="32" font-size="21" fill="#182b43" text-anchor="middle"><tspan x="801" dy="0">主動取得資料</tspan></text><polyline points="727,101 876,101" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="801" y="147" font-size="21" fill="#182b43" text-anchor="middle"><tspan x="801" dy="0">回傳資料、複寫</tspan></text><rect x="442" y="204" width="285" height="65" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="584.5" y="245.95" font-size="27" fill="#182b43" text-anchor="middle"><tspan x="584.5" dy="0">Consumer</tspan></text><polyline points="584,204 584,116" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="607" y="177" font-size="23" fill="#182b43" text-anchor="start"><tspan x="607" dy="0">讀取請求</tspan></text></svg>

Producer 向 Partition Leader 寫入；Follower 主動從 Leader 取得資料並複寫

Consumer 一般從 Leader 讀取；Leader 故障時，需要選出新 Leader

<!--
Producer 不會同時向三份 Replica 寫入。Follower 發出 fetch 請求，再把取得的資料寫入自己的副本。箭頭區分請求方向與資料回傳方向。Consumer 一般預設向 Leader 讀取，也有從 Follower 讀取的機制，這堂課不展開。接著問：任何 Follower 都能安全接手嗎？
-->











---












# ISR：哪些 Replica 有資格接手？

**ISR（In-Sync Replicas）是符合 Kafka 同步條件的 Replica 集合**

| Broker | P0 Replica 狀態 | ISR |
|---|---|---|
| A | Leader | ✓ |
| B | Follower，持續複寫並符合同步條件 | ✓ |
| C | Follower，落後太久 | ✕ |

Follower 不必每一瞬間完全相同，但要持續複寫、在允許時間內跟上

**ISR = [A, B]：Leader 故障時，通常從合格 ISR 選出新 Leader**

<!--
這裡介紹基本 ISR 模型與一般 clean leader election。replica.lag.time.max.ms 涉及 Follower 持續 fetch 與跟上 Leader 的時間條件，長時間未追上可能移出 ISR；不用背數值。ISR 不表示每個瞬間都完全相同。unclean leader election 有不同的可用性與資料安全取捨，可能丟資料。Kafka 4.0 起還有 Eligible Leader Replicas（ELR），啟用時可追蹤 ISR 外仍能安全當選的副本，因此不能把這個基本模型當成所有版本與設定的唯一選舉條件。來源：https://kafka.apache.org/42/configuration/broker-configs/ 與 https://kafka.apache.org/40/getting-started/upgrade/
-->











---








# Controller：誰管理 Kafka Cluster？

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="312" viewBox="0 0 1152 312" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="1150" height="310" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="20" y="30" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="20" dy="0">Kafka Cluster</tspan></text><rect x="341" y="43" width="470" height="78" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="576.0" y="74.80000000000001" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="576.0" dy="0">Controller（管理角色）</tspan><tspan x="576.0" dy="31.200000000000003">管理 Metadata、協調 Leader Election</tspan></text><rect x="18" y="194" width="355" height="102" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="195.5" y="223.15" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="195.5" dy="0">Broker A</tspan><tspan x="195.5" dy="29.900000000000002">儲存 Partition Records</tspan><tspan x="195.5" dy="29.900000000000002">處理 Producer / Consumer 讀寫</tspan></text><polyline points="576,121 576,166 195,166 195,194" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="397" y="194" width="355" height="102" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="574.5" y="223.15" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="574.5" dy="0">Broker B</tspan><tspan x="574.5" dy="29.900000000000002">儲存 Partition Records</tspan><tspan x="574.5" dy="29.900000000000002">處理 Producer / Consumer 讀寫</tspan></text><polyline points="576,121 576,166 574,166 574,194" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="776" y="194" width="355" height="102" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="953.5" y="223.15" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="953.5" dy="0">Broker C</tspan><tspan x="953.5" dy="29.900000000000002">儲存 Partition Records</tspan><tspan x="953.5" dy="29.900000000000002">處理 Producer / Consumer 讀寫</tspan></text><polyline points="576,121 576,166 953,166 953,194" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="776" y="154" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="776" dy="0">Metadata 更新 / 叢集管理</tspan></text></svg>

**Cluster Metadata**：Topic、Partition、Replica、Leader 等叢集管理資訊

Broker 與 Controller 是 Kafka Server 的兩種角色，可同節點兼任，也可分開部署

<!--
框表示 Kafka Cluster，內含 Controller 與三個 Broker 的角色。箭頭只表示 Metadata 更新及叢集管理，並非 Controller 持續指揮 Broker 的所有工作，也不是一般 Record 資料流。Broker 自己處理 Producer／Consumer 的讀寫，Controller 不經手業務 Record。Controller 與 Broker 可由同一 Kafka Server 兼任，也可分開部署。此頁不引入 Active Controller、Quorum 或部署參數。來源：https://kafka.apache.org/42/operations/kraft/
-->


---












# KRaft：Controller 自己故障怎麼辦？

如果負責管理 Cluster 的 Controller 自己故障了呢？

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="256" viewBox="0 0 1152 256" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="1150" height="249" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="575" y="31" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="575" dy="0">KRaft Controller Quorum：共同管理 Metadata 的 Controller 節點群</tspan></text><rect x="15" y="53" width="355" height="177" rx="5" fill="#ffffff" stroke="#90a8bc" stroke-width="1.5"/><text x="192" y="90" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="192" dy="0">Controller A：Active</tspan></text><rect x="30" y="119" width="325" height="87" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="192.5" y="155.0" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="192.5" dy="0">Metadata Log 副本</tspan><tspan x="192.5" dy="32.5">保存叢集管理資訊</tspan></text><rect x="394" y="53" width="355" height="177" rx="5" fill="#ffffff" stroke="#90a8bc" stroke-width="1.5"/><text x="571" y="90" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="571" dy="0">Controller B：Standby</tspan></text><rect x="409" y="119" width="325" height="87" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="155.0" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">Metadata Log 副本</tspan><tspan x="571.5" dy="32.5">保存叢集管理資訊</tspan></text><rect x="773" y="53" width="355" height="177" rx="5" fill="#ffffff" stroke="#90a8bc" stroke-width="1.5"/><text x="950" y="90" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="950" dy="0">Controller C：Standby</tspan></text><rect x="788" y="119" width="325" height="87" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="950.5" y="155.0" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="950.5" dy="0">Metadata Log 副本</tspan><tspan x="950.5" dy="32.5">保存叢集管理資訊</tspan></text></svg>

Active Controller 負責目前的管理決策，其他 Controller 可參與選舉與接手

**KRaft 複製 Metadata，並在 Active Controller 故障後選出接手者**

<!--
KRaft Controller Quorum 是共同可靠管理 Metadata 的 Controller 節點群。每個 Controller 各自保存 Metadata Log 副本，圖中不是共用資料庫。Metadata Log 是依序保存叢集管理資訊變更的 Log，與 Broker 中的 Topic Partition Records 不同。某一個 Active Controller 負責當前管理決策；其他 Controller 是 Standby，可在故障後參與選舉與接手。避免用 Controller Leader／Follower 作為標籤，以免混淆 Partition 的資料角色。不深入 Raft、複製流程、多數票或配置。歷史補充：Kafka 4.0 起移除 ZooKeeper 模式，使用 KRaft。來源：https://kafka.apache.org/42/operations/kraft/
-->



---












# Broker 故障後，誰決定新 Leader？

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="280" viewBox="0 0 1152 280" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="10" y="0" width="355" height="112" rx="5" fill="#f5e4e4" stroke="#90a8bc" stroke-width="1.5"/><text x="187.5" y="34.15" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="187.5" dy="0">Broker A</tspan><tspan x="187.5" dy="29.900000000000002">原 Partition Leader</tspan><tspan x="187.5" dy="29.900000000000002">故障 ×</tspan></text><rect x="394" y="0" width="355" height="112" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="34.15" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">Broker B</tspan><tspan x="571.5" dy="29.900000000000002">ISR 成員</tspan><tspan x="571.5" dy="29.900000000000002">可接手 Partition Leader</tspan></text><rect x="778" y="0" width="355" height="112" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="955.5" y="34.15" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="955.5" dy="0">Broker C</tspan><tspan x="955.5" dy="29.900000000000002">不在 ISR</tspan><tspan x="955.5" dy="29.900000000000002">本例不參與正常接手</tspan></text><text x="25" y="150" font-size="25" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="25" dy="0">故障前 ISR = [A, B]</tspan></text><rect x="255" y="187" width="640" height="81" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="575.0" y="220.3" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="575.0" dy="0">Active Controller</tspan><tspan x="575.0" dy="31.200000000000003">協調 B 成為新 Partition Leader，更新 Metadata</tspan></text><polyline points="575,187 575,112" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

**Active Controller 負責叢集管理；Partition Leader 負責特定 Partition 的資料讀寫**

<!--
這是前兩頁角色與容錯概念的實際應用。Broker A 是 orders / P0 原 Partition Leader，發生故障；B 是 ISR 成員，可在本例正常選舉下接手；C 不在 ISR，不參與本例正常接手。Active Controller 協調 B 成為新的 Partition Leader，並更新 Cluster Metadata。Active Controller 與 Partition Leader 是不同職責，前者管理叢集，後者提供特定 Partition 的資料讀寫。沿用基本 ISR／clean election 模型，ELR、unclean election 的例外不在這頁展開。
-->



---








# Kafka 如何決定保留哪些資料？

`cleanup.policy` 決定 Topic 使用哪種資料清理策略

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="255" viewBox="0 0 1152 255" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="72" width="327" height="110" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="163.5" y="104.19999999999999" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="163.5" dy="0">orders Topic</tspan><tspan x="163.5" dy="31.200000000000003">cleanup.policy</tspan><tspan x="163.5" dy="31.200000000000003">資料清理策略</tspan></text><rect x="443" y="13" width="245" height="80" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="565.5" y="45.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="565.5" dy="0">delete</tspan><tspan x="565.5" dy="31.200000000000003">按時間／容量清理</tspan></text><rect x="443" y="158" width="245" height="80" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="565.5" y="190.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="565.5" dy="0">compact</tspan><tspan x="565.5" dy="31.200000000000003">按 Key 清理舊版本</tspan></text><polyline points="327,127 390,127 390,53 443,53" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="327,127 390,127 390,198 443,198" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="779" y="13" width="165" height="80" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="861.5" y="61.75" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="861.5" dy="0">Retention</tspan></text><rect x="985" y="13" width="165" height="80" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1067.5" y="61.75" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="1067.5" dy="0">Segment</tspan></text><polyline points="688,53 779,53" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="944,53 985,53" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="779" y="158" width="371" height="80" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="964.5" y="190.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="964.5" dy="0">Log Compaction</tspan><tspan x="964.5" dy="31.200000000000003">保留每個 Key 的較新狀態</tspan></text><polyline points="688,198 779,198" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

`delete,compact` 可同時啟用：按 Key 整理較新狀態，仍受時間／容量清理限制

<!--
由可靠性主線轉到儲存管理：Records 不斷增加，Kafka 必須先決定資料保留的目的。delete 分支依序談 Retention 決定時間／容量政策，以及 Segment 如何管理與刪除舊 Log。compact 分支談相同 Key 的舊版本清理。圖中的 Retention 到 Segment 是教學關係：政策與清理單位，不是 Record 先經過兩個服務。Segment 也用於 compact 的 Log 管理，並非 delete 專屬。兩者並用時，每個 Key 的最新值也可能被時間／容量政策清除，不能理解為永久保留。來源：https://kafka.apache.org/42/configuration/topic-configs/
-->








---












# Retention：資料保留與清理

先看 `delete` 分支：orders 的歷史資料要保留多久、占用多少空間？

`cleanup.policy=delete`　`retention.ms=604800000`（七天）

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="220" viewBox="0 0 1152 220" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="1150" height="216" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="20" y="31" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="20" dy="0">orders Topic：保留七天（示例）</tspan></text><rect x="15" y="58" width="355" height="122" rx="5" fill="#f5e4e4" stroke="#90a8bc" stroke-width="1.5"/><text x="192.5" y="111.5" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="192.5" dy="0">超過七天的資料</tspan><tspan x="192.5" dy="32.5">符合條件後背景清理</tspan></text><rect x="394" y="58" width="355" height="122" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="111.5" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">最近七天的資料</tspan><tspan x="571.5" dy="32.5">Records 仍保留</tspan></text><rect x="773" y="58" width="355" height="122" rx="5" fill="#cfe6e2" stroke="#90a8bc" stroke-width="1.5"/><text x="950.5" y="111.5" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="950.5" dy="0">新寫入的資料</tspan><tspan x="950.5" dy="32.5">持續附加 Records</tspan></text><polyline points="372,118 393,118" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="750,118 772,118" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

符合條件的資料以 Segment 為單位背景清理，並非到期逐筆立即刪除

**Retention 依時間或容量清理；Replay 的前提是資料仍存在**

<!--
Retention 是資料保留與清理政策。retention.ms 是時間設定，retention.bytes 是容量設定，Topic 可依兩者清理舊 Segment；容量限制可讓資料不再保留到七天。604800000 是七天換成毫秒，不是要學生背這個數字。圖從左到右表示時間較舊到較新，不表示讀取就刪除。超過七天只是示意區域，實際清理由 Segment 條件與背景檢查決定，不是每筆 Record 的精準到期時間。下一頁回答怎麼管理很大的 Log。來源：https://kafka.apache.org/42/configuration/topic-configs/
-->











---












# Segment：為什麼 Partition Log 需要分段？

Retention 決定何時清理；Kafka 如何有效管理、刪除很大的舊 Log？

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="223" viewBox="0 0 1152 223" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="1150" height="215" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="20" y="31" font-size="27" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="20" dy="0">orders / Partition 0 的 Log</tspan></text><rect x="15" y="59" width="355" height="119" rx="5" fill="#f5e4e4" stroke="#90a8bc" stroke-width="1.5"/><text x="192.5" y="111.0" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="192.5" dy="0">Segment A（舊）</tspan><tspan x="192.5" dy="32.5">Records 0、1、2 …</tspan></text><rect x="394" y="59" width="355" height="119" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="111.0" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">Segment B</tspan><tspan x="571.5" dy="32.5">後續 Records …</tspan></text><rect x="773" y="59" width="355" height="119" rx="5" fill="#cfe6e2" stroke="#90a8bc" stroke-width="1.5"/><text x="950.5" y="111.0" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="950.5" dy="0">Segment C（Active）</tspan><tspan x="950.5" dy="32.5">目前新 Record 寫入處</tspan></text></svg>

- Kafka 自動建立與輪替 Segment，以 `segment.bytes`、`segment.ms` 控制條件
- 分段方便管理、索引與清理；Retention 通常刪除符合條件的舊 Segment

**Segment 是管理 Partition Log 的基本單位之一**

<!--
Segment 是 Partition Log 的一段，包含一批 Record，不必每段相同筆數。Active Segment 是目前接受附加寫入的段；達到輪替條件後 Kafka 建立新的 Active Segment，不需要應用程式手動切割。segment.bytes 表示大小條件，segment.ms 表示時間條件。用「整段清理」建立心智模型，細節如背景檢查、Active Segment 與時間戳判斷留在備註，不把 Retention 講成每筆資料精準到期。來源：https://kafka.apache.org/42/configuration/topic-configs/
-->











---












# Log Compaction：需要完整歷史，還是最新狀態？

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="266" viewBox="0 0 1152 266" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="550" height="258" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="275" y="36" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="275" dy="0">需求 A：完整訂單事件歷史</tspan></text><text x="275" y="99" font-size="27" fill="#182b43" text-anchor="middle"><tspan x="275" dy="0">OrderPlaced</tspan><tspan x="275" dy="35.1">PaymentCompleted</tspan><tspan x="275" dy="35.1">OrderShipped</tspan></text><rect x="600" y="0" width="550" height="258" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="875" y="36" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="875" dy="0">需求 B：訂單目前的狀態</tspan></text><text x="875" y="99" font-size="27" fill="#182b43" text-anchor="middle"><tspan x="875" dy="0">O1001 → CREATED</tspan><tspan x="875" dy="35.1">O1001 → PAID</tspan><tspan x="875" dy="35.1">O1001 → SHIPPED</tspan></text></svg>

`delete` 分支：在保留期限內保存事件歷史，才能回顧與重新計算

接著看 `compact` 分支：重建每個 Key 的較新狀態，不必保留所有舊版本

<!--
同一個訂單例子有兩種不同需求。需求 A 需要保留事件演進，不能以 Compact-only 的狀態表取代。需求 B 只需要每張訂單目前的狀態，這就引出 Log Compaction：清理相同 Key 的較舊值。下一頁用相同 Offset 0–3 比較清理前後。兩種需求可使用不同 Topic，不要求所有訂單資料流使用同一清理方式。
-->











---












<!-- _class: policy-choice -->

# Log Compaction：依 Key 保留較新狀態

背景依 Key 清理舊版本，保留較新值；Offset 不重編號，清理不會立即完成

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="286" viewBox="0 0 1152 286" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><text x="15" y="30" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="15" dy="0">Compaction 前</tspan></text><rect x="15" y="45" width="267" height="78" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="148.5" y="77.1" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="148.5" dy="0">Offset 0</tspan><tspan x="148.5" dy="29.900000000000002">O1001 → CREATED</tspan></text><rect x="300" y="45" width="267" height="78" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="433.5" y="77.1" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="433.5" dy="0">Offset 1</tspan><tspan x="433.5" dy="29.900000000000002">O1002 → CREATED</tspan></text><rect x="585" y="45" width="267" height="78" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="718.5" y="77.1" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="718.5" dy="0">Offset 2</tspan><tspan x="718.5" dy="29.900000000000002">O1001 → PAID</tspan></text><rect x="870" y="45" width="267" height="78" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1003.5" y="77.1" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="1003.5" dy="0">Offset 3</tspan><tspan x="1003.5" dy="29.900000000000002">O1001 → SHIPPED</tspan></text><polyline points="575,126 575,156" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="598" y="148" font-size="23" fill="#182b43" text-anchor="start"><tspan x="598" dy="0">Background Compaction / 背景清理</tspan></text><text x="15" y="183" font-size="26" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="15" dy="0">Compaction 後（示意）</tspan></text><rect x="15" y="199" width="267" height="78" rx="5" fill="#f3f6f9" stroke="#a9bdce" stroke-width="1.5" stroke-dasharray="6 4"/><text x="149" y="230" font-size="23" fill="#698199" text-anchor="middle"><tspan x="149" dy="0">Offset 0</tspan><tspan x="149" dy="29.900000000000002">已清理</tspan></text><rect x="300" y="199" width="267" height="78" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="433.5" y="231.10000000000002" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="433.5" dy="0">Offset 1</tspan><tspan x="433.5" dy="29.900000000000002">O1002 → CREATED</tspan></text><rect x="585" y="199" width="267" height="78" rx="5" fill="#f3f6f9" stroke="#a9bdce" stroke-width="1.5" stroke-dasharray="6 4"/><text x="719" y="230" font-size="23" fill="#698199" text-anchor="middle"><tspan x="719" dy="0">Offset 2</tspan><tspan x="719" dy="29.900000000000002">已清理</tspan></text><rect x="870" y="199" width="267" height="78" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="1003.5" y="231.10000000000002" font-size="23" fill="#182b43" text-anchor="middle"><tspan x="1003.5" dy="0">Offset 3</tspan><tspan x="1003.5" dy="29.900000000000002">O1001 → SHIPPED</tspan></text></svg>

| 需要保存的資料 | cleanup.policy |
|---|---|
| 保留期限內的完整事件歷史 | `delete` |
| 重建每個 Key 的最新狀態 | `compact` |
| 時間／容量限制與最新狀態整理 | `delete,compact` |

<!--
Log Cleaner 是執行背景 Compaction 的清理機制。O1001 的 CREATED、PAID 可被清理，SHIPPED 是較新值；O1002 的 CREATED 仍保留。圖是可能的背景清理結果，不是立即清理保證。Topic 的 cleanup.policy 可為 delete、compact，或 delete,compact 兩者並用；並用時保留資料也受時間／容量限制，不能理解為每個 Key 永久存在。進階還有 tombstone 與清理條件，本課不展開。來源：https://kafka.apache.org/42/configuration/topic-configs/
-->











---











<!-- header: "六、網路失敗、Retry 與可靠性" -->
<!-- _class: chapter reliability -->

# 六、網路失敗、Retry 與可靠性

可靠性要分清三層責任：

1. **Producer**：寫入 Kafka，等待確認與避免重試造成重複 Record
2. **Consumer**：讀取 Record，提交與恢復消費進度
3. **應用程式／寄信服務**：決定寄信是否成功、如何避免重複寄送

<!--
本章順序維持原樣：Timeout、Retry、acks、min.insync.replicas、Idempotence，再看寄信與 Commit 的故障窗口。Producer 的寫入去重不等於 Consumer 不會重讀，Consumer Commit 也不代表外部寄信成功。Consumer 的讀取進度與業務處理的協調，需要應用程式按處理結果正確提交。
-->











---












# Timeout：這筆 OrderPlaced 有寫入嗎？

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="315" viewBox="0 0 1152 315" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="50" y="0" width="300" height="57" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="200.0" y="37.95" font-size="27" fill="#182b43" text-anchor="middle"><tspan x="200.0" dy="0">Producer</tspan></text><rect x="780" y="0" width="320" height="57" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="940.0" y="37.95" font-size="27" fill="#182b43" text-anchor="middle"><tspan x="940.0" dy="0">Broker</tspan></text><polyline points="200,57 200,305" fill="none" stroke="#90a8bc" stroke-width="2.5"/><polyline points="940,57 940,305" fill="none" stroke="#90a8bc" stroke-width="2.5"/><polyline points="200,110 940,110" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="570" y="95" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="570" dy="0">發送 OrderPlaced</tspan></text><text x="960" y="155" font-size="23" fill="#182b43" text-anchor="start"><tspan x="960" dy="0">寫入成功</tspan></text><polyline points="940,197 415,197" fill="none" stroke="#0b7d78" stroke-width="2.5" stroke-dasharray="7 5"/><text x="585" y="182" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="585" dy="0">Success Response 遺失</tspan></text><text x="387" y="206" font-size="34" fill="#b74045" text-anchor="middle"><tspan x="387" dy="0">×</tspan></text><text x="217" y="281" font-size="25" fill="#182b43" text-anchor="start"><tspan x="217" dy="0">只看到 Timeout，無法判斷是否寫入</tspan></text></svg>

**Producer 只看到 Timeout，無法判斷 Broker 沒收到，還是回覆丟失**

<!--
圖中 Broker 已寫入，但回覆在網路中遺失。也可能請求根本沒到 Broker，Producer 在這兩種情況下都可能看到 Timeout。結果未知是重試設計的起點，不能把 Timeout 等同於寫入失敗。
-->











---












# Retry：再送一次會不會多一筆？

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="265" viewBox="0 0 1152 265" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="80" width="320" height="98" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="160.0" y="121.5" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="160.0" dy="0">Producer 重試</tspan><tspan x="160.0" dy="32.5">同一次 OrderPlaced 發送</tspan></text><rect x="430" y="0" width="720" height="254" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="790.0" y="135.75" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="790.0" dy="0"></tspan></text><text x="455" y="32" font-size="24" fill="#182b43" text-anchor="start" font-weight="600"><tspan x="455" dy="0">orders / Partition 0：未採取重試去重時的可能結果</tspan></text><rect x="452" y="57" width="675" height="66" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="789.5" y="98.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="789.5" dy="0">Offset 0：OrderPlaced O1001（第一次其實成功）</tspan></text><rect x="452" y="157" width="675" height="66" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="789.5" y="198.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="789.5" dy="0">Offset 1：OrderPlaced O1001（重試又寫入）</tspan></text><polyline points="320,129 400,129 400,190 452,190" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

重試可能讓未成功的請求完成，也可能重複寫入已成功的事件

**重試與避免重複，需要一起考慮**

<!--
這是沒有有效的 Producer Idempotence 時，或應用重送相同業務事件時的示意風險，不表示現代 Kafka Producer 預設必然寫入兩次。第一次成功但回覆遺失，Client 重試會遇到結果未知的問題。先介紹 acks 是什麼，再介紹 Idempotence 處理機制涵蓋的重試。
-->











---












# acks：Broker 何時回覆寫入確認？

**Acknowledgement 是寫入確認，Producer 透過 `acks` 指定等待條件**

| 設定 | 這筆 OrderPlaced 等到什麼程度？ |
|---|---|
| `acks=0` | 不等待 Broker 確認 |
| `acks=1` | Leader 寫入自己的 Log 就回覆 |
| `acks=all` | 等待目前 ISR 達成確認條件 |

**all 指目前 ISR，不等於最初配置的所有 Replica**

如果 ISR 只剩 A 一份，`acks=all` 是否仍可成功？

<!--
acks 控制寫入確認條件，不代表 Consumer 已處理，也不等於立即 fsync 或絕對不會遺失。acks=1 不等 Follower 全部跟上，Leader 在複寫前故障可能影響資料。acks=all 等待目前 ISR 的確認條件，ISR 可能只有一份。即使 Broker 已滿足條件，回覆也可能遺失，Producer 仍看到 Timeout。下一頁用 min.insync.replicas 回答最低副本數。來源：https://kafka.apache.org/42/configuration/producer-configs/
-->











---












# min.insync.replicas：只剩一份時還接受寫入嗎？

示例：`Replication Factor=3`，Producer 使用 `acks=all`

**`min.insync.replicas=2`：成功寫入至少要有兩份符合條件的 ISR**

| 當前 ISR | 寫入結果 |
|---|---|
| A、B、C | 可接受，等待目前 ISR 確認 |
| A、B | 可接受，等待目前 ISR 確認 |
| A | 拒絕寫入 |

**用最低同步副本要求，避免只剩一份時仍確認成功**

<!--
這是靜態簡化情境。min.insync.replicas 搭配 acks=all 指定最低 ISR 要求，不是只等待兩份：若 ISR 三份，all 仍等待目前 ISR 的確認條件。真實 ISR 可在操作間變動，也可能產生錯誤或 Timeout。拒絕寫入讓可用性降低，以維持所要求的副本條件。來源：https://kafka.apache.org/42/configuration/topic-configs/
-->











---












# Idempotence：重試如何避免重複寫入？

**Producer Idempotence 是 Kafka 內建的功能，現代 Java Producer 預設啟用**

`enable.idempotence=true`，前提是沒有衝突設定

<div class="flow"><div>同一次發送<br>因 Timeout 重試</div><span class="arrow">→</span><div>Producer Idempotence<br>識別重試</div><span class="arrow">→</span><div>Kafka Log<br>避免新增重複 Record</div></div>

| 去重層次 | 責任與範圍 |
|---|---|
| Kafka Log 的寫入去重 | Producer Idempotence 處理涵蓋的發送重試 |
| 寄信等業務操作的去重 | 應用程式／外部 API 的冪等與去重設計 |

**Log 只有一筆 Record，Consumer 重讀仍可能再寄一次信**

<!--
冪等性指同一操作重複執行，最終結果仍與執行一次相同。Apache Kafka 現代 Java Producer 的 enable.idempotence 預設為 true，但若未明確指定且有衝突設定，功能會停用；明確啟用又設定衝突時會拋出設定錯誤。需要 acks=all、retries>0、max.in.flight.requests.per.connection<=5。不要把這個 Java Client 預設套用到所有第三方 Client 或舊版。Producer 以識別與序號涵蓋發送重試，不是以業務 eventId 或 Key 在全域去重，也不防止應用主動發出兩次相同業務事件。來源：https://kafka.apache.org/42/configuration/producer-configs/
-->











---












# Event Processing：處理成功，但 Commit 前故障

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="268" viewBox="0 0 1152 268" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="0" y="0" width="355" height="88" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="177.5" y="36.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="177.5" dy="0">Consumer 讀取</tspan><tspan x="177.5" dy="31.200000000000003">OrderPlaced</tspan></text><rect x="394" y="0" width="355" height="88" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="36.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">Process Event</tspan><tspan x="571.5" dy="31.200000000000003">例：呼叫寄信 API</tspan></text><rect x="788" y="0" width="355" height="88" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="965.5" y="52.4" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="965.5" dy="0">處理成功</tspan></text><rect x="170" y="171" width="355" height="88" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="347.5" y="207.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="347.5" dy="0">重新讀取</tspan><tspan x="347.5" dy="31.200000000000003">並處理 Event</tspan></text><rect x="670" y="171" width="355" height="88" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="847.5" y="207.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="847.5" dy="0">Consumer 故障</tspan><tspan x="847.5" dy="31.200000000000003">尚未 Commit</tspan></text><polyline points="355,44 394,44" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="749,44 788,44" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="966,88 966,131 848,131 848,171" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="670,215 525,215" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

**同一筆 Event 可重新處理；Producer Idempotence 無法防止重複業務操作**

<!--
Process Event 是應用程式執行業務邏輯，例如寄信、更新統計或保留庫存，不是 Consumer 只是讀到 Record。例子中業務處理已成功，但 Commit 前故障，重啟後同一筆 Event 可能重新處理。Producer Idempotence 涵蓋寫入重試去重，不能防止 Consumer 重讀後再執行業務操作。寄信服務若支援 Idempotency Key，可使用 eventId 識別同一次操作；去重細節不放在主畫面。單純查表、寄信、再寫表仍有故障窗口，不能直接視為只寄一次。
-->



---












# Event Processing 與 Offset Commit：順序有什麼影響？

**Process Event 是應用程式執行業務邏輯，不等於 Consumer 只是讀到 Event**

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="266" viewBox="0 0 1152 266" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="12" y="0" width="390" height="84" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="207.0" y="51.45" font-size="27" fill="#182b43" text-anchor="middle"><tspan x="207.0" dy="0">Offset Commit</tspan></text><rect x="749" y="0" width="390" height="84" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="944.0" y="51.45" font-size="27" fill="#182b43" text-anchor="middle"><tspan x="944.0" dy="0">Process Event</tspan></text><polyline points="402,42 749,42" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="575" y="29" font-size="24" fill="#b74045" text-anchor="middle"><tspan x="575" dy="0">× 故障</tspan></text><text x="575" y="118" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="575" dy="0">故障可能漏處理：At-most-once</tspan></text><rect x="12" y="136" width="390" height="84" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="207.0" y="187.45" font-size="27" fill="#182b43" text-anchor="middle"><tspan x="207.0" dy="0">Process Event</tspan></text><rect x="749" y="136" width="390" height="84" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="944.0" y="187.45" font-size="27" fill="#182b43" text-anchor="middle"><tspan x="944.0" dy="0">Offset Commit</tspan></text><polyline points="402,178 749,178" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><text x="575" y="165" font-size="24" fill="#b74045" text-anchor="middle"><tspan x="575" dy="0">× 故障</tspan></text><text x="575" y="254" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="575" dy="0">故障可能重複處理：At-least-once</tspan></text></svg>

例如寄信：先 Commit 可能漏寄；先 Process 再 Commit，可能重複呼叫寄信 API

<!--
上頁說明處理成功但未 Commit 的故障，這頁比較順序。Commit 再 Process，Commit 之後處理之前故障，恢復時會跳過事件，對應 At-most-once 的風險；Process 再 Commit，成功處理後提交前故障，恢復時可能重做，對應 At-least-once 的風險。寄信是具體例子，主概念適用於各種事件處理。At-least-once 還需正確成功提交、持續重試、資料仍保留等前提，不表示 Kafka 自動保證外部信件送達。
-->



---












<!-- _class: policy-choice -->

# Exactly-once：Kafka-to-Kafka 的處理保證

如果 Consumer 處理後還要寫回 Kafka，如何避免故障重試造成重複輸出？

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="335" viewBox="0 0 1152 335" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="100" y="5" width="310" height="80" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="255.0" y="37.5" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="255.0" dy="0">orders Topic</tspan><tspan x="255.0" dy="32.5">OrderPlaced</tspan></text><rect x="620" y="5" width="430" height="80" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="835.0" y="37.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="835.0" dy="0">Analytics Service</tspan><tspan x="835.0" dy="31.200000000000003">讀取 OrderPlaced，計算統計</tspan></text><polyline points="410,45 620,45" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="140" y="145" width="870" height="180" rx="5" fill="#e4f0ed" stroke="#0b7d78" stroke-width="1.5"/><text x="575" y="181" font-size="26" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="575" dy="0">Kafka Transaction：一起 Commit 或 Abort</tspan></text><rect x="160" y="204" width="395" height="100" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="357.5" y="246.5" font-size="25" fill="#182b43" text-anchor="middle"><tspan x="357.5" dy="0">輸出 Records</tspan><tspan x="357.5" dy="32.5">寫入 stats Topic</tspan></text><rect x="595" y="204" width="395" height="100" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="792.5" y="246.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="792.5" dy="0">輸入 Offsets</tspan><tspan x="792.5" dy="31.200000000000003">提交 orders Topic 消費進度</tspan></text><polyline points="835,85 835,113 575,113 575,145" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

**Exactly-once 讓 Kafka 交易範圍內的處理結果只生效一次，不代表程式只執行一次。**

外部寄信、付款 API 不會自動包含在 Kafka Transaction 內。

<!--
orders Topic 到 Analytics Service，再寫入 stats Topic 是 Kafka-to-Kafka 的例子。Consumer 讀取 OrderPlaced，應用執行 Event Processing 計算統計，再寫出 Records 與提交輸入 Offset。圖中的綠色 Transaction 框只涵蓋 Kafka 輸出與輸入進度的原子提交，不包住程式碼執行本身。若 Abort，輸出不應被 read_committed Consumer 看作已提交結果，已提交的輸入進度也不會被這筆中止交易推進；應用仍需正確恢復與重新處理。需要正確交易使用、Offset 協調、read_committed、關閉自動 Commit 等消費設定，這堂課不介紹 API。Exactly-once 不表示程式碼只跑一次，適當機制讓指定範圍結果只生效一次。外部寄信、付款需要外部服務冪等／去重或其他一致性機制，不受 Kafka Transaction 自動保護。來源：https://kafka.apache.org/42/design/design/
-->



---
















<!-- header: "七、重新複習一筆 Event 會怎麼被處理" -->
<!-- _class: chapter -->

# 七、重新複習一筆 Event 會怎麼被處理

把所有名詞放回同一筆 OrderPlaced，重新走一次完整流程。

<!--
最後回到完整流程。回顧寫入、保存、複寫、讀取、業務處理與提交，再回顧不同 Group 與問題對應概念。
-->
















---
















# O1001 的完整旅程

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="398" viewBox="0 0 1152 398" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="10" y="5" width="355" height="112" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="187.5" y="53.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="187.5" dy="0">Checkout</tspan><tspan x="187.5" dy="31.200000000000003">建立 OrderPlaced</tspan></text><rect x="394" y="5" width="355" height="112" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="38.199999999999996" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">Producer</tspan><tspan x="571.5" dy="31.200000000000003">Topic=orders，Key=O1001</tspan><tspan x="571.5" dy="31.200000000000003">序列化 Key / Value</tspan></text><rect x="778" y="5" width="355" height="112" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="955.5" y="53.8" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="955.5" dy="0">orders / Partition 0</tspan><tspan x="955.5" dy="31.200000000000003">Leader 寫入 Record Log</tspan></text><rect x="10" y="270" width="355" height="112" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="187.5" y="318.79999999999995" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="187.5" dy="0">Commit Offset</tspan><tspan x="187.5" dy="31.200000000000003">提交下一個讀取位置</tspan></text><rect x="394" y="270" width="355" height="112" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="571.5" y="318.79999999999995" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="571.5" dy="0">應用程式</tspan><tspan x="571.5" dy="31.200000000000003">處理 OrderPlaced</tspan></text><rect x="778" y="270" width="355" height="112" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="955.5" y="303.2" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="955.5" dy="0">Consumer</tspan><tspan x="955.5" dy="31.200000000000003">從 Partition 讀取</tspan><tspan x="955.5" dy="31.200000000000003">反序列化 Key / Value</tspan></text><polyline points="365,61 394,61" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="749,61 778,61" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="955,117 955,270" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="778,325 749,325" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><polyline points="394,325 365,325" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="420" y="153" width="330" height="65" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="585.0" y="193.9" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="585.0" dy="0">Follower Replica 複寫</tspan></text><polyline points="955,151 790,151 790,186 750,186" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

**Kafka 保存資料與進度，應用程式負責業務結果與重複執行的處理**

<!--
請聽眾用這張圖回顧：Producer 在哪裡？Offset 在哪裡？如果 Email 暫停，哪裡仍保存資料？如果 Broker 故障，哪個 Replica 可以接手？如果寄信後才故障，哪個機制仍不足以保證只寄一次？圖為資料旅程，不是所有網路請求的方向。
-->
















---
















# 同一筆 Event，供不同 Group 各自處理

<svg class="diagram" xmlns="http://www.w3.org/2000/svg" width="1152" height="250" viewBox="0 0 1152 250" role="img"><defs><marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="#0b7d78"/></marker></defs><rect x="245" y="0" width="660" height="66" rx="5" fill="#eaf0f5" stroke="#90a8bc" stroke-width="1.5"/><text x="575.0" y="42.1" font-size="26" fill="#182b43" text-anchor="middle"><tspan x="575.0" dy="0">orders Topic：同一筆 OrderPlaced Record</tspan></text><rect x="10" y="145" width="355" height="94" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="188" y="178" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="188" dy="0">inventory-group</tspan></text><text x="188" y="213" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="188" dy="0">Inventory Service 處理事件</tspan></text><polyline points="575,66 575,101 188,101 188,145" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="394" y="145" width="355" height="94" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="572" y="178" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="572" dy="0">email-group</tspan></text><text x="572" y="213" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="572" dy="0">Email Service 處理事件</tspan></text><polyline points="575,66 575,101 572,101 572,145" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/><rect x="778" y="145" width="355" height="94" rx="5" fill="#f3f6f9" stroke="#90a8bc" stroke-width="1.5"/><text x="956" y="178" font-size="25" fill="#182b43" text-anchor="middle" font-weight="600"><tspan x="956" dy="0">analytics-group</tspan></text><text x="956" y="213" font-size="24" fill="#182b43" text-anchor="middle"><tspan x="956" dy="0">Analytics Service 處理事件</tspan></text><polyline points="575,66 575,101 956,101 956,145" fill="none" stroke="#0b7d78" stroke-width="2.5" marker-end="url(#arr)"/></svg>

**各 Group 分別讀取同一批 Record，維護自己的進度**

Kafka 保存資料與進度，業務是否成功與是否重複仍需要服務設計

<!--
這張圖中的各 Group 是平行讀取，並非 Inventory 把事件傳給 Email、Email 再傳給 Analytics。用分岔的資料流說明不同 Group 的獨立性。
-->
















---
















# 一套理解 Kafka 的心智模型

遇到新的 Kafka Concept，先問：

1. **在哪裡？** 它位於 Event Flow 的哪個環節？
2. **為什麼？** 它想解決什麼問題？
3. **有什麼限制？** 它能保證什麼，又不能保證什麼？

**把技術名詞放回 Event Flow，理解問題、機制與限制，而不只是背誦定義。**

<!--
這是一套學習與回顧方法，不再引入新技術。先定位資料流環節，再理解問題與機制，最後確認保證範圍與限制。接下來用兩張問題對照表練習這三個提問，不要求背出名詞。主要教材：[[v1]]。
-->


---
















<!-- _class: dense -->

# 問題與概念：寫入、位置與讀取

| 實際問題 | Kafka Concept |
|---|---|
| 誰把 OrderPlaced 寫進 Kafka？ | Producer |
| 哪個 Server 儲存資料並提供讀寫？ | Broker |
| 訂單事件要寫入哪一類資料？ | Topic |
| 資料如何分散保存與平行讀取？ | Partition |
| 同一張訂單的事件如何依序讀取？ | Key + Partition |
| 一筆 Record 在 Partition 的哪個位置？ | Offset |
| 同一服務的多個 Consumer 怎麼分工？ | Consumer Group |

<!--
用前三個提問練習定位問題。相同 Key 的事件依序讀取，須寫入同一 Topic，分區數量及分區規則不變，並按預期順序寫入。Offset 是特定 Partition 內的位置，不等同 Group 已完成的業務進度。Broker 是處理資料讀寫的 Kafka Server 角色，不包含所有 Server 角色。
-->


---
















<!-- _class: dense -->

# 問題與概念：進度、保存與可靠性

| 實際問題 | Kafka Concept |
|---|---|
| Group 故障後從哪個位置恢復？ | Committed Offset |
| Group 的進度距離 Partition 尾端多遠？ | Consumer Lag |
| 修正統計後如何重算歷史資料？ | Replay + Retention |
| Broker 故障，如何保存資料並接手？ | Replication + ISR + Leader Election |
| 誰管理叢集？管理者故障怎麼辦？ | Controller + KRaft |
| 寫入逾時，如何重試並等待確認？ | Retry / acks |
| 寫入重試如何避免新增重複 Record？ | Producer Idempotence |

<!--
Committed Offset 記錄 Group 針對 Topic Partition 的恢復位置，不是 Kafka 對業務處理成功的確認。Lag 是已提交進度與 Log 尾端的距離，不一定等於未完成的業務筆數。Replay 必須有仍保留的 Records，也可能重複副作用。Replication 與 ISR 支援本課正常 Leader Election 案例，Active Controller 協調接手。逾時不能單憑 Client 判定是否寫入，Retry 與 acks 分別處理重送及確認條件，Producer Idempotence 只涵蓋符合條件的寫入重試，不防止 Consumer 重複業務操作。
-->


---














# 延伸閱讀

- **[Kafka Streams](https://kafka.apache.org/43/streams/)**：Stream Processing、Aggregation、Windowing
- **[Kafka Connect](https://kafka.apache.org/43/kafka-connect/)**：Kafka 與 Database、外部系統的資料整合
- **[KRaft](https://kafka.apache.org/43/operations/kraft/)**：Raft Consensus、Controller Quorum、Metadata Replication
- **[Share Group](https://kafka.apache.org/43/javadoc/org/apache/kafka/clients/consumer/KafkaShareConsumer.html)**：Record-level Acknowledgement、工作分配模式
- **[Reliability & Operations](https://kafka.apache.org/43/design/design/)**：Consumer Lag 監控、Transactions、故障復原

<!--
每個主題是後續學習方向，不在 Introduction 深入展開。點選主題可開啟 Kafka 4.3 官方文件。Streams 可學串流計算、聚合與時間視窗；Connect 是資料整合；KRaft 延伸至共識與 Metadata 容錯；Share Group 可探索按 Record 分工與確認；可靠性與維運可接著看 Lag 監控、交易及故障恢復。監控參考：https://kafka.apache.org/43/operations/monitoring/ 。若想讀程式，可在 Apache Kafka codebase 找 streams/、connect/、raft/、metadata/、clients/ 相關模組。各主題需回到 Event Flow，問位置、問題、保證與限制。
-->
