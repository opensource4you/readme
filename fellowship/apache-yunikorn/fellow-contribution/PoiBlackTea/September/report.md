# PoiBlackTea Monthly Contribution Report

- GitHub: https://github.com/PoiBlackTea
- Slack: https://opensource4you.slack.com/team/U0BBZE02DFF
- ASF JIRA: [weichen lai](https://issues.apache.org/jira/secure/ViewProfile.jspa?name=poiblacktea)
- Period: 2026-09-01 to 2026-09-30

## Summary

PR: 14 (all merged, across 4 YuniKorn repos), Code Review: 2, Issue Triage: 3 JIRAs filed

YuniKorn committers merge by pushing the patch themselves, so merged PRs show as Closed on GitHub; the landed commits are on master ([core](https://github.com/apache/yunikorn-core/commits/master/?author=PoiBlackTea&since=2026-09-01&until=2026-09-30), [k8shim](https://github.com/apache/yunikorn-k8shim/commits/master/?author=PoiBlackTea&since=2026-09-01&until=2026-09-30), [release](https://github.com/apache/yunikorn-release/commits/master/?author=PoiBlackTea&since=2026-09-01&until=2026-09-30), [web](https://github.com/apache/yunikorn-web/commits/master/?author=PoiBlackTea&since=2026-09-01&until=2026-09-30)). PRs are listed in the month they were merged.

## Must-read PR

[[YUNIKORN-3236] Inter Queue Preemption: Improve victim selection algorithm](https://github.com/apache/yunikorn-core/pull/1159)

Inter-queue preemption now ranks victims with the same `SortAllocationsBasedOnAsk` ordering as quota preemption, evaluated against the ask queue's max resource, and the legacy `compareAllocationLess`/`scoreAllocation` are removed. During review I found that the comparator returned `true` on ties (`comp == 0`), which breaks the strict weak ordering `sort.SliceStable` requires and also affected `quota_preemptor.go`; we agreed on an `allocationKey` tie-break, which also makes the victim order deterministic. It builds on [#1153](https://github.com/apache/yunikorn-core/pull/1153), which fixed the scoring polarity of the same function.

## PR

### Merged

- [[YUNIKORN-3364] Fix event stream history replay blocking forever on disconnect](https://github.com/apache/yunikorn-core/pull/1133): make the history replay send select on the stop channels, so a client that disconnects during replay no longer leaks the forwarder goroutine.
- [[YUNIKORN-3436] Fix event stream forwarder goroutine leak on slow-consumer eviction](https://github.com/apache/yunikorn-core/pull/1141): apply the same fix to the live event loop, where a full consumer buffer parked the forwarder on a send it could never leave.
- [[YUNIKORN-3365] Shutdown ordering: scheduler allocation notify can block on an RM reply the proxy never drains](https://github.com/apache/yunikorn-core/pull/1152): drain pending RM events on shutdown and buffer the notification reply channels, so an allocation or release in flight during `StopAll` no longer blocks forever.
- [[YUNIKORN-3445] SortAllocationsBasedOnAsk prioritizes originator and opted-out pods for preemption instead of protecting them](https://github.com/apache/yunikorn-core/pull/1153): flip the scoring so originator and opted-out allocations are preempted last instead of first, with a test covering all four combinations.
- [[YUNIKORN-3236] Inter Queue Preemption: Improve victim selection algorithm](https://github.com/apache/yunikorn-core/pull/1159): see [Must-read PR](#must-read-pr).
- [[YUNIKORN-3413] Wildcard limit config read without the manager lock on scheduling paths](https://github.com/apache/yunikorn-core/pull/1165): resolve wildcard user limits once at the manager entrypoint and pass the snapshot down the tracker hierarchy, fixing a race with config reload without inverting the manager-then-tracker lock order.
- [YUNIKORN-3435] Upgrade go dependencies for CVEs ([core](https://github.com/apache/yunikorn-core/pull/1140), [k8shim](https://github.com/apache/yunikorn-k8shim/pull/1080)): bump `golang.org/x/net` and `golang.org/x/text` for two high CVEs and sync k8shim to the new core and scheduler-interface, following the scheduler-interface PR in August.
- [[YUNIKORN-3441] Support podman for building images and running e2e tests](https://github.com/apache/yunikorn-k8shim/pull/1084): detect docker or podman in the `Makefile` and side-load images into kind with `kind load image-archive`, so the e2e tests run without docker.
- [[YUNIKORN-3371] DRA resource-slice tracker goroutine leaks in tests](https://github.com/apache/yunikorn-k8shim/pull/1087): add `Context.Stop()` to stop the DRA resource-slice tracker, whose sync monitor goroutine leaked in every test because the mock informers never sync and its context was never cancelled.
- [[YUNIKORN-3466] Reproducible build fails under podman due to unqualified golang image](https://github.com/apache/yunikorn-k8shim/pull/1100): fully qualify the golang builder image and add `-buildvcs=false`, fixing reproducible builds under podman and as container root.
- [[YUNIKORN-3425] Admission controller serving goroutine reads the server field Shutdown nils](https://github.com/apache/yunikorn-k8shim/pull/1110): hand the server to the serving goroutine as a local variable, fixing a data race on every certificate reload and a possible nil-pointer panic.
- [[YUNIKORN-3443] Support Podman in release tools for building images and validation](https://github.com/apache/yunikorn-release/pull/245): make `build-image.py` and `validate_cluster.sh` work with podman, which were hard-wired to the docker CLI and daemon.
- [[YUNIKORN-3467] Support podman for building web image](https://github.com/apache/yunikorn-web/pull/283): detect docker or podman in the web `Makefile`, overridable with `DOCKER=podman`.

## Code Review

- [[YUNIKORN-3451] replace subprocess call with run](https://github.com/apache/yunikorn-release/pull/243): caught that podman's `manifest push --rm` (docker uses `--purge`) was dropped in a merge from master, and that `@GO_VERSION@` rendered differently from the Node and Angular entries in the release README, which led the author to make all README replacements consistent.
- [[YUNIKORN-3480] Fix the weekly e2e matrix so every K8s version runs](https://github.com/apache/yunikorn-k8shim/pull/1108): approved.

## Issue Triage

- Filed [YUNIKORN-3443](https://issues.apache.org/jira/browse/YUNIKORN-3443) Support Podman in release tools for building images and validation, and fixed it in [release#245](https://github.com/apache/yunikorn-release/pull/245).
- Filed [YUNIKORN-3445](https://issues.apache.org/jira/browse/YUNIKORN-3445) SortAllocationsBasedOnAsk prioritizes originator and opted-out pods for preemption instead of protecting them, and fixed it in [core#1153](https://github.com/apache/yunikorn-core/pull/1153).
- Filed [YUNIKORN-3452](https://issues.apache.org/jira/browse/YUNIKORN-3452) Node Preemption: Fix reversed penalty scores between originator and opted-out pods: the node-selection side of the same polarity bug, where the penalty constants make killing an application's driver cheaper than killing an opted-out worker; open and assigned to me.
