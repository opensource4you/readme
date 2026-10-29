# Parkerhiphop

- GitHub：https://github.com/Parkerhiphop
- Slack：https://opensource4you.slack.com/team/U0855NXK291

## 2026-10-01

提名人 chia7712 · 建議等級 2 萬

- [x] 關係人宣告：提名人與被提名者無親屬、僱傭、財務、業務合作或其他可能影響公正判斷之利害關係

- 社群評分排名第 5，兩個月 review 12 個 PR、留言 83 則，其中 9 個是非 committer 的 PR，常常是第一個留實質意見的人（[reviewed PRs](https://github.com/apache/kafka/pulls?q=is%3Apr+reviewed-by%3AParkerhiphop+updated%3A2026-08-08..2026-10-07)）
- 參與「PR 換學分」校園課程（[#pr2學分](https://opensource4you.slack.com/archives/C0C5U1370ES)），準備擔任大學講師，Kafka 教材已經動工：從「Kafka 是什麼」一路講到 Consumer Group、資料保存與故障、Retry 與可靠性共七章，並用 Marp 產出簡報，作業設計中（[readme#27](https://github.com/opensource4you/readme/pull/27)，3500 多行）
- 幫 4.4 release 打下手：負責 trunk bump 到 4.5.0-SNAPSHOT 並處理 MetadataVersion 的連帶問題，發現漏洞後自己開 follow-up 收尾（[#23125](https://github.com/apache/kafka/pull/23125)、[#23244](https://github.com/apache/kafka/pull/23244)、[#23254](https://github.com/apache/kafka/pull/23254)），並把 unstable MetadataVersion 的規則寫成註解留給後人（[#23522](https://github.com/apache/kafka/pull/23522)）
- 啃大骨頭：重構 NetworkClient，抽出 ApiVersionNegotiator 把 API version 協商邏輯獨立出來，改動 1600 多行（[#23673](https://github.com/apache/kafka/pull/23673)，KAFKA-20855）
- 把文件裡寫死的版本號全部改成 `{version}`，本地用 kafka-site 逐一驗證連結（[#23524](https://github.com/apache/kafka/pull/23524)、[#23671](https://github.com/apache/kafka/pull/23671)），review 新手的 Quickstart 文件 PR 時也要求照同樣規則修（[#23598](https://github.com/apache/kafka/pull/23598)、[#23526](https://github.com/apache/kafka/pull/23526)、[#23536](https://github.com/apache/kafka/pull/23536)）
- 遇到別人搶走自己 JIRA 的 PR，沒有情緒，先把社群禮儀講清楚，再因為 patch 本身沒問題直接 approve（[#23308](https://github.com/apache/kafka/pull/23308)）
- 把 Scala 整合測試搬到 clients-integration-tests，被來回 review 二十幾則仍耐心逐條改完（[#22907](https://github.com/apache/kafka/pull/22907)）
