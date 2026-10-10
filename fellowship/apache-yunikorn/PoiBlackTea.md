# PoiBlackTea

- GitHub：https://github.com/PoiBlackTea
- Slack：https://opensource4you.slack.com/team/U0BBZE02DFF

## 2026-10-10

提名人: HuangTing-Yao
建議等級：3 萬, 10月取得Apache YuniKorn Committer 的頭銜
2026-07-07（首次貢獻）到 2026-09-30 的重點

- 提出 37 個 PR，全數 merged，橫跨 6 個 YuniKorn repo（月報：[7 月](https://github.com/opensource4you/readme/blob/main/fellowship/apache-yunikorn/fellow-contribution/PoiBlackTea/July/report.md)、[8 月](https://github.com/opensource4you/readme/blob/main/fellowship/apache-yunikorn/fellow-contribution/PoiBlackTea/August/report.md)、[9 月](https://github.com/opensource4you/readme/blob/main/fellowship/apache-yunikorn/fellow-contribution/PoiBlackTea/September/report.md)）
- 改進 inter-queue preemption 的 victim 選擇演算法（[YUNIKORN-3236](https://github.com/apache/yunikorn-core/pull/1159)）
- 主動發現並修正 originator pod 反被優先 preempt 的 bug（[YUNIKORN-3445](https://github.com/apache/yunikorn-core/pull/1153)）
- 修復 [goroutine leak](https://github.com/apache/yunikorn-core/pull/1141)、[shutdown 卡死](https://github.com/apache/yunikorn-core/pull/1152)、[data race](https://github.com/apache/yunikorn-k8shim/pull/1110) 等並行 bug
- 修 6 個 flaky test，皆追到根因（[core#1108](https://github.com/apache/yunikorn-core/pull/1108)、[core#1109](https://github.com/apache/yunikorn-core/pull/1109)、[core#1118](https://github.com/apache/yunikorn-core/pull/1118)、[core#1119](https://github.com/apache/yunikorn-core/pull/1119)、[core#1123](https://github.com/apache/yunikorn-core/pull/1123)、[core#1132](https://github.com/apache/yunikorn-core/pull/1132)）
- 讓 image 建置與 e2e 支援 podman（[k8shim#1084](https://github.com/apache/yunikorn-k8shim/pull/1084)、[k8shim#1100](https://github.com/apache/yunikorn-k8shim/pull/1100)、[release#245](https://github.com/apache/yunikorn-release/pull/245)、[web#283](https://github.com/apache/yunikorn-web/pull/283)）