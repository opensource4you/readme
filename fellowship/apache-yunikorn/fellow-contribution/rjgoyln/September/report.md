# rjgoyln Monthly Contribution Report

- GitHub: https://github.com/rjgoyln
- Slack: https://opensource4you.slack.com/team/U0BMWP51QN5
- ASF JIRA: Dian-Xuan Yang
- Period: 2026-09-02 to 2026-10-05 (since my first YuniKorn contribution)

## Summary

PR: 14 (12 merged, 2 in review, across all 6 YuniKorn repos), Code Review: 5, Issue Triage: 2 (plus 3 JIRAs filed)

YuniKorn committers merge by pushing the patch themselves, so merged PRs show as Closed on GitHub; the landed commits are on master ([core](https://github.com/apache/yunikorn-core/commits?author=rjgoyln), [k8shim](https://github.com/apache/yunikorn-k8shim/commits?author=rjgoyln)).

## PR

### Merged

- [[YUNIKORN-3428] Fix fatal scheduler exit on concurrent taskMap access](https://github.com/apache/yunikorn-k8shim/pull/1091): fix an unlocked `taskMap` read racing with a new pod's write, which made the Go runtime kill the shim.
- [[YUNIKORN-3427] Fix unsynchronised context and task access on deferred release](https://github.com/apache/yunikorn-k8shim/pull/1097): fix two races in `flushReleaseableTasks`, on the context's applications map and on the task's `allocationKey`.
- [[YUNIKORN-3422] Publish the scheduler cache node lists atomically](https://github.com/apache/yunikorn-k8shim/pull/1099): publish the lazily built node lists atomically, since concurrent preemption predicates populated them under a read lock.
- [[YUNIKORN-3358] Remove the unreachable replace-existing-ask branch](https://github.com/apache/yunikorn-core/pull/1146): replace an unreachable branch that left `sortedRequests` inconsistent with an up-front duplicate-key rejection (in the 1.10.0 release branch).
- [[YUNIKORN-3480] Fix the weekly e2e matrix so every K8s version runs](https://github.com/apache/yunikorn-k8shim/pull/1108): fix the weekly e2e, which had failed every week since 1.37 was added because of a missing comma and three nonexistent kind images.
- [[YUNIKORN-3485] Remove unused UpdatePod from the shim KubeClient](https://github.com/apache/yunikorn-k8shim/pull/1112): remove dead code found in the YUNIKORN-3437 audit.
- [YUNIKORN-3484] Update GitHub Actions to latest versions ([core](https://github.com/apache/yunikorn-core/pull/1179), [k8shim](https://github.com/apache/yunikorn-k8shim/pull/1115), [scheduler-interface](https://github.com/apache/yunikorn-scheduler-interface/pull/178), [web](https://github.com/apache/yunikorn-web/pull/284), [release](https://github.com/apache/yunikorn-release/pull/246), [site](https://github.com/apache/yunikorn-site/pull/580)): bump the actions in all 6 repos, checking each major upgrade against the existing workflows and the ASF allowlist.

### In Review

- [[YUNIKORN-3481] Fix orphan allocation when a removal races with scheduling](https://github.com/apache/yunikorn-core/pull/1175): fix an allocation left on its node until restart when its removal races with scheduling, a regression from YUNIKORN-2459 in 1.6.0.
- [[YUNIKORN-3487] Cancel pod condition updates on shim shutdown](https://github.com/apache/yunikorn-k8shim/pull/1114): make pod condition updates cancellable so a slow API server no longer blocks shutdown.

## Code Review

- [[YUNIKORN-3479] Optimize preemption victim selection and trimming](https://github.com/apache/yunikorn-core/pull/1178): found that the new victim ranking ignores priority ordering and scores nondeterministically, and suggested cached, key-sorted vectors that also cut the benchmark from 10.5 ms to 2.3 ms at n=1000.
- [[YUNIKORN-3483] Reinstate Partition state transitions](https://github.com/apache/yunikorn-core/pull/1174): raised a blocking issue where a queue removed from the config could never drain because its pending asks would stop being scheduled.
- [[YUNIKORN-3478] Decouple ask queue quota accounting from victim sizes](https://github.com/apache/yunikorn-core/pull/1172): pointed out that a flipped test assertion hid an over-preemption regression, which the author and I agreed to address in #1178.
- [[YUNIKORN-3465] Add victimAllocationKeys in PreemptionPredicatesResponse](https://github.com/apache/yunikorn-scheduler-interface/pull/176): approved, suggesting the spec define `victimAllocationKeys` as an ordered subset of `preemptAllocationKeys[0..index]`.
- [[YUNIKORN-3421] Fix task release deadlocks in FSM callbacks](https://github.com/apache/yunikorn-k8shim/pull/1101): approved.

## Issue Triage

- [YUNIKORN-3437](https://issues.apache.org/jira/browse/YUNIKORN-3437) Handle shutdown signal during K8s CRUD operations: audited the shim's 8 K8s write call sites for cancellability, mapped them to existing JIRAs, and split out YUNIKORN-3485 and YUNIKORN-3487.
- [YUNIKORN-3231](https://issues.apache.org/jira/browse/YUNIKORN-3231) ForeignAllocations ignored when pods use nodeSelector: reproduced it on 1.8.0 and traced it to informer event ordering, which YUNIKORN-3317 already fixed in 1.9.0.
- Filed [YUNIKORN-3480](https://issues.apache.org/jira/browse/YUNIKORN-3480), [YUNIKORN-3485](https://issues.apache.org/jira/browse/YUNIKORN-3485) and [YUNIKORN-3487](https://issues.apache.org/jira/browse/YUNIKORN-3487), all with PRs.
