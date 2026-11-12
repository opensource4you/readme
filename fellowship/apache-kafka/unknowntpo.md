# unknowntpo

- GitHub：https://github.com/unknowntpo
- Slack：https://opensource4you.slack.com/team/U05NXH9131U

## 2026-11-01

提名人 chia7712 · 建議等級 2 萬

- [x] 關係人宣告：提名人與被提名者無親屬、僱傭、財務、業務合作或其他可能影響公正判斷之利害關係

- 社群評分品質分排名第 5，兩個月留言 41 則，review 數量不多但每個都有實質意見，而且作者都照著改了（[reviewed PRs](https://github.com/apache/kafka/pulls?q=is%3Apr+reviewed-by%3Aunknowntpo+updated%3A2026-09-13..2026-11-12)）
- Review NetworkClient 重構時直接點出設計問題：新抽出的 ApiVersionNegotiator 與 NetworkClient 互相依賴，作者因此收回該抽象；接著指出測試裡永遠不會失敗的 assertion、列出 BootstrapResolver 沒被測到的路徑，還提出把狀態收進 ClusterConnectionStates 的替代設計（[#23673](https://github.com/apache/kafka/pull/23673)）
- 自己從 controller 程式碼裡挖出 bug：effective min ISR 一律拿 partition 0 的 replica 數去算，會讓其他 partition 的 ELR 太早被清掉（[#23895](https://github.com/apache/kafka/pull/23895)，KAFKA-21266）；新增 partition 時 replication factor 取決於 hash 順序而不是 partition 0（[#23858](https://github.com/apache/kafka/pull/23858)，KAFKA-21252）
- 追 Streams standby 的 OOM：standby 會一直抓超過 limit offset 的 changelog，restore buffer 無限長大。他一路追到這是 2.5.0 重構時掉的行為，並補上整合測試證明修正前後的差異（[#23815](https://github.com/apache/kafka/pull/23815)，KAFKA-21276）
- 處理 consumer heartbeat 的 busy loop：heartbeat 還在飛而 timer 已過期時，network thread 與 application thread 會空轉。被 review 十幾則後把多餘的註解、測試、1 ms floor 全部砍掉，只留必要的修正（[#23357](https://github.com/apache/kafka/pull/23357)，KAFKA-21031）
- 修 flaky 的 system test 都是找到根因才動手：LogCompactionTester 遇到空 poll 就停（[#23572](https://github.com/apache/kafka/pull/23572)，已 merge，附 24 次全過的結果）、舊版 smoke-test client 的 changelog 被 retention 刪掉（[#23615](https://github.com/apache/kafka/pull/23615)）、share consumer benchmark 讀到 0 筆也算通過（[#23781](https://github.com/apache/kafka/pull/23781)、[#23894](https://github.com/apache/kafka/pull/23894)）
- 清掉測試裡漏關的資源，第一個 PR 修完後主動把同樣的 pattern 掃過其他測試（[#23604](https://github.com/apache/kafka/pull/23604)、[#23929](https://github.com/apache/kafka/pull/23929)）
- Review AGENTS.md 的 PR description 規範時提出具體改寫建議，與作者討論後被採納並 merge（[#23739](https://github.com/apache/kafka/pull/23739)）