# HsiaoHungKai

- GitHub：https://github.com/HsiaoHungKai
- Slack：https://opensource4you.slack.com/team/U0B83TWUNQY

## 2026-11-19

提名人 CheyuWu · 建議等級 2 萬

- [x] 關係人宣告：提名人與被提名者無親屬、僱傭、財務、業務合作或其他可能影響公正判斷之利害關係

- [Contribution](https://github.com/opensource4you/readme/blob/main/fellowship/kuberay/fellow-contribution/HsiaoHungKai/October/report.md)
- Found a bug: a RayJob with `clusterSelector` waits forever if the selected RayCluster is suspended. Reported it with steps to reproduce ([#5476](https://github.com/kuberay-project/kuberay/issues/5476)). Sent a fix that fails the RayJob instead, as a maintainer suggested at the community sync. It is in review ([#5491](https://github.com/kuberay-project/kuberay/pull/5491)). When a reviewer proposed that the RayJob resume an idle-suspended RayCluster by itself, explained why a RayJob should not change a RayCluster it does not own, and the reviewer withdrew that proposal ([discussion](https://github.com/kuberay-project/kuberay/pull/5491#issuecomment-6623069791))
- Found that `reconcilePods` deletes Pods in five places, and each place handles `NotFound` differently ([#5446](https://github.com/kuberay-project/kuberay/issues/5446)). Sent a fix that uses one shared function for all five and treats `NotFound` as success. It is in review ([#5464](https://github.com/kuberay-project/kuberay/pull/5464))
- Fixed a broken build in the History Server tests on master. Two PRs each passed CI on their own, but the build broke after both were merged ([#5519](https://github.com/kuberay-project/kuberay/pull/5519))
- Reviewed 7 KubeRay PRs ([#5409](https://github.com/kuberay-project/kuberay/pull/5409), [#5416](https://github.com/kuberay-project/kuberay/pull/5416), [#5427](https://github.com/kuberay-project/kuberay/pull/5427), [#5465](https://github.com/kuberay-project/kuberay/pull/5465), [#5481](https://github.com/kuberay-project/kuberay/pull/5481), [#5487](https://github.com/kuberay-project/kuberay/pull/5487), [#5530](https://github.com/kuberay-project/kuberay/pull/5530)). For example, tested #5530 on a kind cluster before and after the fix before approving it, and asked in #5427 whether a RayCluster should be `Ready` when no worker Pod can be created
- Fixed two mistakes in the RayService docs: an upgrade step that never finishes, and a wrong Service name. Both are in review ([ray#68064](https://github.com/ray-project/ray/pull/68064), [ray#68079](https://github.com/ray-project/ray/pull/68079))
- Growth: earlier work followed what others asked for: a fix for an issue someone else filed ([#5053](https://github.com/kuberay-project/kuberay/pull/5053)), and a follow-up to a review comment that made the mTLS e2e test reuse one RayCluster for all checks ([#5097](https://github.com/kuberay-project/kuberay/pull/5097), opened in August, merged in this period). September was mostly spent reading and learning the code ([September report](https://github.com/opensource4you/readme/blob/main/fellowship/kuberay/fellow-contribution/HsiaoHungKai/September/report.md)). In this period, started finding controller bugs by reading the code and reporting them as issues ([issues](https://github.com/kuberay-project/kuberay/issues?q=is%3Aissue+author%3AHsiaoHungKai)), and went from one review before this period ([#4894](https://github.com/kuberay-project/kuberay/pull/4894)) to seven
