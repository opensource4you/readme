# F64116045

- GitHub：https://github.com/F64116045
- Slack：https://opensource4you.slack.com/team/U0BNT0YPCDT

## 2026-10-15

提名人 chungen0126 · 建議等級 2 萬

- 近三個月提出 24 個 PR（ozone 21、ozone-site 3），其中 16 個已 merged（5 個 open）
- Review 19 個非自己開的 PR，包含 S3 Object Lock（[#11053](https://github.com/apache/ozone/pull/11053)、[#11293](https://github.com/apache/ozone/pull/11293)）、Datanode StorageType（[#11223](https://github.com/apache/ozone/pull/11223)）等功能
- 讓 `PendingContainerTracker` 依 StorageType 計算 pending allocation，支援 storage policy（[#10928](https://github.com/apache/ozone/pull/10928)，48 個檔案）
- 修復多個 S3 Gateway 相容性問題，以 MinIO Mint / ceph s3-tests 找出問題：錯誤回應 Content-Type（[#11031](https://github.com/apache/ozone/pull/11031)）、簽章中的空 Content-Type（[#11088](https://github.com/apache/ozone/pull/11088)）、lifecycle rule Status 驗證（[#10980](https://github.com/apache/ozone/pull/10980)）
- SCM/OM 直接使用設定的 DNS-to-switch mapping（[#10911](https://github.com/apache/ozone/pull/10911)），後續移除 MiniOzoneCluster 的 workaround（[#11158](https://github.com/apache/ozone/pull/11158)）並更新文件（[ozone-site#538](https://github.com/apache/ozone-site/pull/538)）
- 修 flaky test 時追到根因（[#11319](https://github.com/apache/ozone/pull/11319)），並加速慢測試，`TestSstFileSetReader` 由 131.9s 降到 4.8s（[#11469](https://github.com/apache/ozone/pull/11469)）

