# hedger9487

- GitHub：https://github.com/hedger9487
- Slack：https://opensource4you.slack.com/team/U0BUKKVB7DF

## 2026-10-10

- 提名人：HuangTing-Yao , 建議等級：2 萬

- 提出 25 個 PR，16 個已 merged；提報 23 張 JIRA，20 張屬 YUNIKORN-3186 preemption 強化（[月報](https://github.com/opensource4you/readme/pull/29)）
- 修正 preemption 的多資源比較錯誤、predicate 所需 victim 被截掉、shortfall 誤判、配額誤算 victim 大小等問題（[core#1148](https://github.com/apache/yunikorn-core/pull/1148)、[core#1156](https://github.com/apache/yunikorn-core/pull/1156)、[core#1157](https://github.com/apache/yunikorn-core/pull/1157)、[core#1163](https://github.com/apache/yunikorn-core/pull/1163)、[core#1170](https://github.com/apache/yunikorn-core/pull/1170)、[core#1172](https://github.com/apache/yunikorn-core/pull/1172)）
- 修正 k8shim PreemptionFilter 未更新 CycleState、導致 affinity／topology 搶佔一律失敗的問題（[YUNIKORN-3464](https://github.com/apache/yunikorn-k8shim/pull/1098)）
- 找出 guaranteed 下限檢查自 2024-06 起形同虛設，victim queue 會被搶到 guaranteed 以下（[YUNIKORN-3492](https://github.com/apache/yunikorn-core/pull/1185)，修正 review 中）
- 另找出 DaemonSet 搶佔過量、quota preemption 分配算錯等獨立 bug（[core#1188](https://github.com/apache/yunikorn-core/pull/1188)、[core#1189](https://github.com/apache/yunikorn-core/pull/1189)，review 中）
- 修復 [race condition](https://github.com/apache/yunikorn-core/pull/1154)、[scheduler panic](https://github.com/apache/yunikorn-core/pull/1150)、[quota leak](https://github.com/apache/yunikorn-core/pull/1147) 等穩定性問題