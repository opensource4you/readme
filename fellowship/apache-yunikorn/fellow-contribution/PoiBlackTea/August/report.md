# PoiBlackTea Monthly Contribution Report

- GitHub: https://github.com/PoiBlackTea
- Slack: https://opensource4you.slack.com/team/U0BBZE02DFF
- ASF JIRA: [weichen lai](https://issues.apache.org/jira/secure/ViewProfile.jspa?name=poiblacktea)
- Period: 2026-08-01 to 2026-08-31

## Summary

PR: 15 (all merged, across 5 YuniKorn repos), Issue Triage: 2 JIRAs filed

YuniKorn committers merge by pushing the patch themselves, so merged PRs show as Closed on GitHub; the landed commits are on master ([core](https://github.com/apache/yunikorn-core/commits/master/?author=PoiBlackTea&since=2026-08-01&until=2026-08-31), [k8shim](https://github.com/apache/yunikorn-k8shim/commits/master/?author=PoiBlackTea&since=2026-08-01&until=2026-08-31), [release](https://github.com/apache/yunikorn-release/commits/master/?author=PoiBlackTea&since=2026-08-01&until=2026-08-31), [site](https://github.com/apache/yunikorn-site/commits/master/?author=PoiBlackTea&since=2026-08-01&until=2026-08-31), [scheduler-interface](https://github.com/apache/yunikorn-scheduler-interface/commits/master/?author=PoiBlackTea&since=2026-08-01&until=2026-08-31)). PRs are listed in the month they were merged.

## PR

### Merged

- [[YUNIKORN-3220] Test setpreemptiontime with different resource types](https://github.com/apache/yunikorn-core/pull/1107): extend `TestQueue_setPreemptionTime` from a single dummy resource to multi-dimensional, subset/superset, disjoint and partially violated quotas.
- [[YUNIKORN-3333] Fix flaky unit test TestQuotaChangeTryPreemption](https://github.com/apache/yunikorn-core/pull/1108): build fresh victims inside each sub-test, because preemption sorts and truncates the victim slice in place and earlier sub-tests corrupted later ones.
- [[YUNIKORN-3334] Fix flaky unit test TestQuotaChangeTryPreemptionWithDifferentResTypes](https://github.com/apache/yunikorn-core/pull/1118): pin victim creation times inside the current hour, since the `-3m`/`-1m` offsets from `time.Now()` crossed an hour boundary in the first minutes of each hour and flipped the hour-truncated victim order.
- [[YUNIKORN-3335] flaky test TestQuotaChangeTryPreemptionForParentQueue](https://github.com/apache/yunikorn-core/pull/1119): make `resetQueue()` reset child queues as well, so allocated resources no longer leaked between sub-tests.
- [[YUNIKORN-3343] Fix flaky Event publisher test TestServiceStartStopInternal](https://github.com/apache/yunikorn-core/pull/1123): count only the publisher's own goroutines and wait for them to drain, replacing a fixed sleep and process-wide `runtime.NumGoroutine()` checks that failed under `-race`.
- [YUNIKORN-3345] Turn on nonamedreturns linter ([core](https://github.com/apache/yunikorn-core/pull/1129), [k8shim](https://github.com/apache/yunikorn-k8shim/pull/1065)): enable the linter in both repos and refactor every function that used named returns.
- [[YUNIKORN-3378] Fix flaky TestSchedulerRecoveryQuotaPreemption](https://github.com/apache/yunikorn-core/pull/1132): remove a pre-restart allocation phase whose assertions raced the asynchronous `UpdateAllocation` and failed whenever the allocation was processed quickly under CI load.
- [[YUNIKORN-3401] Fix nonamedreturns lint error in application_property_test.go](https://github.com/apache/yunikorn-core/pull/1134): fix a named return in `application_property_test.go` that failed the linter enabled in YUNIKORN-3345.
- [[YUNIKORN-3372] Clean up event streams in tests to prevent goroutine leaks](https://github.com/apache/yunikorn-core/pull/1135): add the missing `RemoveStream`/`RemoveEventStream` calls in the `pkg/events` tests so forwarder goroutines no longer outlive the test.
- [YUNIKORN-3359] Run e2e tests with deadlock detection enabled ([release](https://github.com/apache/yunikorn-release/pull/237), [k8shim](https://github.com/apache/yunikorn-k8shim/pull/1064)): add a `deadlockDetection` block to the Helm chart that sets the `DEADLOCK_*` environment variables, then enable it for the e2e runs, which unlike the unit tests ran without it.
- [[YUNIKORN-3354] Recovery pod ordering is not reproducible despite intending to be](https://github.com/apache/yunikorn-k8shim/pull/1075): break `CreationTimestamp` ties by pod UID during recovery and task dispatch, since the timestamp has one-second resolution and informer list order is random.
- [[YUNIKORN-3216] Document unschedulable backoff settings and REST API updates](https://github.com/apache/yunikorn-site/pull/568): document the `application.unschedasks.backoff` and `application.unschedasks.backoff.delay` queue properties and the new `backoffDeadline` REST field.
- [[YUNIKORN-3435] Upgrade go dependencies for CVEs](https://github.com/apache/yunikorn-scheduler-interface/pull/169): bump `golang.org/x/net` and `golang.org/x/text` in scheduler-interface for two high CVEs; the core and k8shim parts landed in September.

## Issue Triage

- Filed [YUNIKORN-3398](https://issues.apache.org/jira/browse/YUNIKORN-3398) Fix unit test failures due to RollbackAllocation behavior conflict: reported two `RollbackAllocation` test failures in `make test`, fixed by aligning the tests in [YUNIKORN-3399](https://issues.apache.org/jira/browse/YUNIKORN-3399).
- Filed [YUNIKORN-3401](https://issues.apache.org/jira/browse/YUNIKORN-3401) Fix nonamedreturns lint error in application_property_test.go, and fixed it in [core#1134](https://github.com/apache/yunikorn-core/pull/1134).
