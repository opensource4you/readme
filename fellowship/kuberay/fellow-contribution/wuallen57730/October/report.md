# wuallen57730 Monthly Contribution Report

- GitHub: https://github.com/wuallen57730
- Slack: https://opensource4you.slack.com/team/U0B8264S5TM
- Period: 2026-10-12 to 2026-11-15

## Summary

PR: 4 (1 merged, 3 in review; 2 new this period), Code Review: 0, Issue Discussion: 0 new

Changes since the September report:

- #5363 got a third approval and was merged.
- New PRs: #5395 (from the plan in #4994) and #5410 (closes #5279, approved by CheyuWu).
- #5238 and #5256 had no updates.

## PR

### Merged

- [[History Server] Bump Ray to 2.58.0 in examples and e2e test data #5363](https://github.com/kuberay-project/kuberay/pull/5363) (carried over from September)
  - September: in review. Approved by win5923 and machichima, with the `time.sleep(2)` workaround already removed.
  - This period: approved by rueian and merged on 2026-10-15. The History Server examples, e2e test data, and `DEVELOPMENT.md` now use Ray 2.58, which fixes the missing task logs on the task detail page ([ray#65288](https://github.com/ray-project/ray/pull/65288)). With the docs side ([ray#66630](https://github.com/ray-project/ray/pull/66630)) merged last month, both of my parts of [#5356](https://github.com/kuberay-project/kuberay/issues/5356) are done.

### In Review

- [[RayService] Set worker serve label from Serve proxy status #5395](https://github.com/kuberay-project/kuberay/pull/5395) (new, from the plan in [#4994](https://github.com/kuberay-project/kuberay/issues/4994))
  - Change: worker Pods always got `ray.io/serve=true`, so if a user overrode the readiness probe, a worker without a Serve proxy received traffic and requests failed. The label is now `true` only when a `HEALTHY` proxy runs on the worker, based on the Serve details that `reconcileServe` already fetches. The readiness probe is not changed.
  - Testing: unit tests, plus manual tests on kind with Ray 2.58. In the HeadOnly case, successful requests went from 23/40 to 138/138.
  - Status: requested review from machichima on 2026-10-15 and pinged again on 2026-11-08. No review yet.
- [[CI][History Server] Move historyserver lint false positives out of the burn-down TODO #5410](https://github.com/kuberay-project/kuberay/pull/5410) (new, closes [#5279](https://github.com/kuberay-project/kuberay/issues/5279), which I took in September)
  - Change: [#5247](https://github.com/kuberay-project/kuberay/pull/5247) put five `historyserver/` lint false positives under the burn-down TODO. This PR fixes G703 and G117 in code, replaces the G101 and G704 rules with inline `//nolint:gosec` and a reason, and moves the `cancelled` misspell exclusion to the section that is meant to stay.
  - Result: `make lint` in `historyserver/` reports 0 issues.
  - Status: addressed three rounds of review from CheyuWu (2026-10-17, 10-19, 10-24) and was approved on 2026-10-24. Waiting for win5923's review.
- [[History Server][CI] add unit tests for the list API filters #5238](https://github.com/kuberay-project/kuberay/pull/5238) (carried over from September)
  - The PR adds table-driven unit tests for `historyserver/pkg/utils/filter.go`, raising its coverage from 0% to 98.8%. Part of [#5222](https://github.com/kuberay-project/kuberay/issues/5222).
  - This period: no update. It has had no review since it was opened on 2026-09-03.

## Code Review

None in this period.

## Issue Discussion

No new comments from me in this period. Updates on the issues from the September report:

- [#4994](https://github.com/kuberay-project/kuberay/issues/4994) RayService worker readiness with `proxy_location=HeadOnly`
  - September: proposed the fix, and the maintainer approved the plan.
  - This period: opened PR #5395 on 2026-10-11. On 2026-11-11, machichima said in the issue that the readiness probe should stay as it is to keep the HA behavior from [#1808](https://github.com/kuberay-project/kuberay/pull/1808). #5395 does not change the probe, only the `ray.io/serve` label, so it matches that direction.
- [#5256](https://github.com/kuberay-project/kuberay/issues/5256) Make webhook and reconciliation validation consistent
  - September: posted the implementation plan and a question about `deletionTimestamp`. The issue is assigned to me.
  - This period: no update. The plan is still waiting for a maintainer reply.
