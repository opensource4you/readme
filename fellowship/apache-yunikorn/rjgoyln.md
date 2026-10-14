# rjgoyln

- GitHub：https://github.com/rjgoyln
- Slack：https://opensource4you.slack.com/team/U0BMWP51QN5

## 2026-10-10

提名人: HuangTing-Yao, chenyulin0719
建議等級：2 萬
2026-09-02（首次貢獻）到 2026-10-10 的重點

- review 11 個 PR，9 個有實質意見，7 個來自其他 fellow
- review [YUNIKORN-3479](https://github.com/apache/yunikorn-core/pull/1178) 時抓到 victim 排序忽略 priority，並提出讓 benchmark 從 10.5 ms 降到 2.3 ms 的寫法
- 另抓到被測試改動掩蓋的 regression、被移除的 queue 無法 drain、orphan pod 漏重試等問題（[core#1185](https://github.com/apache/yunikorn-core/pull/1185)、[core#1174](https://github.com/apache/yunikorn-core/pull/1174)、[k8shim#1117](https://github.com/apache/yunikorn-k8shim/pull/1117)）
- 提出 18 個 PR，16 個已 merged，含 6 個 repo 的 GitHub Actions 升級（[月報](https://github.com/opensource4you/readme/blob/main/fellowship/apache-yunikorn/fellow-contribution/rjgoyln/September/report.md)；10-05 後另有 [core#1182](https://github.com/apache/yunikorn-core/pull/1182)、[site#582](https://github.com/apache/yunikorn-site/pull/582)、[k8shim#1118](https://github.com/apache/yunikorn-k8shim/pull/1118)、[core#1190](https://github.com/apache/yunikorn-core/pull/1190)）
- 修復 k8shim 多個 race，含會讓 Go runtime 直接終止 shim 的並行存取（[k8shim#1091](https://github.com/apache/yunikorn-k8shim/pull/1091)、[k8shim#1097](https://github.com/apache/yunikorn-k8shim/pull/1097)、[k8shim#1099](https://github.com/apache/yunikorn-k8shim/pull/1099)）
- 修好自 K8s 1.37 加入後每週都失敗的 weekly e2e（[YUNIKORN-3480](https://github.com/apache/yunikorn-k8shim/pull/1108)）
- 修正 1.6.0 起的 orphan allocation regression（[YUNIKORN-3481](https://github.com/apache/yunikorn-core/pull/1175)，review 中）

