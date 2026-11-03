# wuallen57730 Monthly Contribution Report

- GitHub: https://github.com/wuallen57730
- Slack: https://opensource4you.slack.com/team/U0B8264S5TM
- Period: 2026-09-01 to 2026-10-11 (last updated 2026-10-07)

## Summary

PR: 6 (4 merged, 2 in review, including 1 KubeRay docs PR in ray-project/ray), Code Review: 0, Issue Discussion: 2

## PR

### Merged

- [[Helm][Feat] Expose enableIngress and ingressOptions in the RayCluster chart #5195](https://github.com/ray-project/kuberay/pull/5195): expose `head.enableIngress` and `head.ingressOptions` in the `ray-cluster` Helm chart, so Helm users can create and customize the head dashboard Ingress that KubeRay 1.7 added to the CRD. Both values are optional and the default rendering is unchanged. Merged 2026-09-15, closes [#5189](https://github.com/ray-project/kuberay/issues/5189).
- [[RayCluster][CI] add e2e test for autoscaler scale-up with mTLS #5066](https://github.com/ray-project/kuberay/pull/5066): add an e2e test that scales an mTLS cluster up and down with the autoscaler and checks that the shared cert-manager worker certificate adds and drops pod IPs in its SANs, a path that had no e2e coverage. Merged 2026-09-22.
- [[History Server][CI] add unit tests for the lineage task summary #5128](https://github.com/ray-project/kuberay/pull/5128): add unit tests for `ToSummaryByLineage`, covering tree construction, actor grouping, sibling merging into `GROUP` nodes, state count aggregation, and sort order, as a follow-up promised in [#4469](https://github.com/ray-project/kuberay/pull/4469). Merged 2026-09-22, closes [#5081](https://github.com/ray-project/kuberay/issues/5081).
- [[Docs][History Server] Require Ray 2.58 for task logs ray#66630](https://github.com/ray-project/ray/pull/66630): update the KubeRay History Server guide in the Ray docs to require Ray 2.58, which fixes missing task logs. This is the docs side of [#5356](https://github.com/ray-project/kuberay/issues/5356), paired with #5363. Merged 2026-10-02.

### In Review

- [[History Server] Bump Ray to 2.58.0 in examples and e2e test data #5363](https://github.com/ray-project/kuberay/pull/5363): bump the History Server examples and e2e test data to Ray 2.58, which fixes missing task logs on the task detail page, and remove the `time.sleep(2)` workaround from the e2e test data. Verified on kind that `taskLogInfo` in `TASK_LIFECYCLE_EVENT` is now complete and that the task and log e2e tests pass on 2.58 but fail on 2.56. Approved by two reviewers (`lgtm`), part of [#5356](https://github.com/ray-project/kuberay/issues/5356).
- [[History Server][CI] add unit tests for the list API filters #5238](https://github.com/ray-project/kuberay/pull/5238): add table-driven unit tests for query parsing and filtering in `historyserver/pkg/utils/filter.go`, raising its coverage from 0% to 98.8%. Part of [#5222](https://github.com/ray-project/kuberay/issues/5222).

## Code Review

None in this period.

## Issue Discussion

- [#4994](https://github.com/ray-project/kuberay/issues/4994) RayService worker readiness with `proxy_location=HeadOnly`: reproduced both cases on kind. In one, a worker without a Serve proxy kept the `ray.io/serve=true` label. In the other, 17 of 40 requests failed when such a worker was Ready. Also showed that the spec-based approach in [#4997](https://github.com/ray-project/kuberay/pull/4997) does not work, because Ray only applies `proxy_location` when Serve starts. Proposed setting the label from the proxy status that the Serve API reports, and the maintainer approved the plan. PR in progress.
- [#5256](https://github.com/ray-project/kuberay/issues/5256) Make webhook and reconciliation validation consistent: posted an implementation plan to reuse the reconcilers' `Validate*Spec` functions in the RayCluster, RayJob, and RayService webhooks. Raised whether validation should be skipped when `deletionTimestamp` is set, so invalid objects can still be deleted.
