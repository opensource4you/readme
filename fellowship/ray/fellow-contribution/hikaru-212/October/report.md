# Ray Open Source Contributions — October 11 to November 14, 2026

## Summary

**Contributor:** `hikaru-212`

- **Authored work:** 4 authored PRs, 3 authored issues, and 1 substantial carry-over PR revision.

- **Reviews:** 37 external PRs reviewed: 24 findings across 21 PRs, plus 16 independent validations.

- **Results:** 22 findings accepted or acknowledged by authors; 20 followed by code or test changes.

- **Merges:** 6 reviewed PRs merged; none of the authored PRs merged.

Of the six merged review targets, #67505 included a fix for the reported finding. #67554 and #67850 merged with their reported concerns unresolved; the other three were independent validations. #67382 is listed as authored work and referenced under #67229, but its finding is counted once.

Work covered Ray Core, GCS, Data, Serve, Autoscaler, Tune, SQL sharding, and regression tests.

## Authored Pull Requests

### Ray [#67061](https://github.com/ray-project/ray/pull/67061) — Leader-Renewal Watchdog Revalidation

- **Contribution:** Implemented mutex-protected renewal revalidation so the GCS watchdog cannot step down using a stale timestamp after a newer renewal. Filed [#67060](https://github.com/ray-project/ray/issues/67060) and added deterministic interleaving regressions.
- **Outcome:** Reported 16 focused tests passing, including repeated regression runs. The draft PR was automatically closed as stale on November 13 without merge; the original issue remained open.

### Ray [#67381](https://github.com/ray-project/ray/pull/67381) — Autoscaler Transition Details

- **Contribution:** Implemented forwarding of instance-transition details into status history and downstream SDK/CLI output, addressing [#67380](https://github.com/ray-project/ray/issues/67380). Added regressions for allocation failure, allocation success, and Ray installation.
- **Outcome:** Three regressions failed on the baseline and passed after correction; 124 focused tests passed. The PR remained open and ready for review; live-cluster validation was not run.

### Ray [#67382](https://github.com/ray-project/ray/pull/67382) — HashShuffle Logical GPU Accounting

- **Contribution:** Implemented current/base GPU accounting from effective aggregator configuration after the separate finding on #67229. Added fractional-allocation tests and [revised shutdown semantics](https://github.com/ray-project/ray/pull/67382#issuecomment-6408092484) in response to another review, including explicit `num_gpus=None` handling.
- **Outcome:** Reported focused tests and real-actor probes passing, with logical accounting matching GCS allocations. The PR remained open, pending conversion from draft to ready for review; testing covered logical GPU allocations, not physical CUDA hardware.

### Ray [#67384](https://github.com/ray-project/ray/pull/67384) — Redundant Actor-Lease Cancellation

- **Contribution:** Implemented cancellation of redundant replacement leases when an earlier compatible actor grant is adopted, addressing [#67383](https://github.com/ray-project/ray/issues/67383). Added lease-ID fencing and GCS/Raylet ownership and resource-release tests.
- **Outcome:** Reported 71 GCS Actor tests and the Raylet lease-manager test passing. The PR remained open, pending conversion from draft to ready for review; the official Bazel baseline comparison and live-cluster test were not completed.

## Code Reviews — Findings

### Ray [#66876](https://github.com/ray-project/ray/pull/66876#discussion_r4240304401) — Placement-Group Cleanup Ownership

- **Contribution:** Demonstrated lost cleanup obligations after retry exhaustion. A [follow-up](https://github.com/ray-project/ray/pull/66876#discussion_r4310157938) revalidated the correction and separately reproduced delayed old cleanup releasing a newer reservation in controlled components.

- **Outcome:** The author accepted the first finding and changed code, tests, and cleanup ownership. The second fencing concern received no author response or fix and was not reproduced in a full RPC run. The PR remained open.

### Ray [#66094](https://github.com/ray-project/ray/pull/66094#discussion_r4253375311) — Interpreter Paths Containing Spaces

- **Contribution:** Reproduced a worker-launch regression in which an unquoted default Python interpreter path containing spaces was split by `bash -c`. The comparison used production runtime-environment and launcher paths with a fake Nsight executable.

- **Outcome:** The author accepted the finding, corrected production code, and added a regression test. The PR remained open; I did not rerun the fix.

### Ray [#66921](https://github.com/ray-project/ray/pull/66921#discussion_r4277772702) — NIXL Metadata-Generation Synchronization

- **Contribution:** Reproduced a metadata-generation race allowing stale metadata to reach `add_remote_agent()` after a newer version was imported. Identified the synchronization gap between version checking, cache updates, and reader acquisition.

- **Outcome:** The author accepted and reproduced the race, then fixed it in #66921. That PR closed without merge, but the redesigned [#67389](https://github.com/ray-project/ray/pull/67389) merged a per-agent locking fix for the same problem. I did not rerun the original race on the merged revision.

### Ray [#67210](https://github.com/ray-project/ray/pull/67210#pullrequestreview-5599025909) — Explicit Streaming Cancellation

- **Contribution:** Demonstrated that explicit cancellation incorrectly drained buffered Serve messages before raising, using a real streaming deployment. [Revalidated the author's correction](https://github.com/ray-project/ray/pull/67210#issuecomment-6391640558).

- **Outcome:** The author accepted the regression and changed code and tests. The PR remained open; my revalidation covered the October revision, before later cancellation-race changes prompted by another reviewer.

### Ray [#67229](https://github.com/ray-project/ray/pull/67229#pullrequestreview-5598113507) — HashShuffle GPU Resource Semantics

- **Contribution:** Established three distinct findings: understated maximum GPU requirements, a separate [current-usage accounting gap](https://github.com/ray-project/ray/pull/67229#issuecomment-6391882619), and a public `Dataset.join()` failure with explicit [`num_gpus=None`](https://github.com/ray-project/ray/pull/67229#discussion_r4370505252).

- **Outcome:** The author accepted all three. Maximum/None corrections gained code and tests and were [revalidated](https://github.com/ray-project/ray/pull/67229#discussion_r4373836376); I implemented the current-usage correction separately in #67382. Both PRs remained open; the original contention finding did not establish deadlock.

### Ray [#67242](https://github.com/ray-project/ray/pull/67242#pullrequestreview-5640333964) — Unordered-Queue Watermark Reachability

- **Contribution:** Traced production submitter/client paths and used loopback gRPC to show that supported unordered requests supplied watermark `-1`, rather than the nonnegative watermark assumed by the queue-level regression.

- **Outcome:** The author accepted the correction and clarified the issue. No supported producer path using the test's nonnegative watermark was found; the PR remained open.

### Ray [#67406](https://github.com/ray-project/ray/pull/67406#discussion_r4394831411) — Composite SQL Shard-Key Row Preservation

- **Contribution:** Reproduced PostgreSQL composite-NULL sharding loss: public `read_sql` returned 131 of 200 rows on the reviewed head. [Post-fix validation](https://github.com/ray-project/ray/pull/67406#discussion_r4409006232) recovered every ID exactly once.

- **Outcome:** The author accepted the finding, corrected the predicate complement, and updated SQL-generation assertions. The PR remained open; committed coverage was a predicate test, not a live PostgreSQL integration regression.

### Ray [#67437](https://github.com/ray-project/ray/pull/67437#discussion_r4395360421) — SQLite Fallback Test Validity

- **Contribution:** Showed that the SQLite authorizer checked the wrong callback argument, allowing the fallback test to pass without disabling `MOD()`. [Revalidation](https://github.com/ray-project/ray/pull/67437#discussion_r4409072881) confirmed `%` shard queries and all 500 rows after correction.

- **Outcome:** The author accepted the finding and changed the test only. The PR remained open; this was a false-positive test, not a production fallback defect.

### Ray [#67455](https://github.com/ray-project/ray/pull/67455#pullrequestreview-5693561901) — Cross-Selector Resource Packing

- **Contribution:** Demonstrated that grouping requests by label selectors left feasible demand unscheduled under a node cap or planned unnecessary capacity. [Revalidated](https://github.com/ray-project/ray/pull/67455#issuecomment-6525358242) revised scheduling with 20 focused cases.

- **Outcome:** The author accepted the finding, changed per-node tier selection, and added packing coverage. The PR remained open; I did not rerun later caching changes.

### Ray [#67505](https://github.com/ray-project/ray/pull/67505#discussion_r4411640115) — Distributed Join Row Loss

- **Contribution:** Demonstrated silent row loss when repeated join keys exposed inconsistent pandas/Polars partition hashing: a valid match appeared with one partition but disappeared with two.

- **Outcome:** The author accepted the finding, rejected duplicate keys, and added regressions for both operands. The corrected PR received independent approval and merged; this guarded the exposed configuration rather than repairing every underlying hashing path.

### Ray [#67580](https://github.com/ray-project/ray/pull/67580#pullrequestreview-5713207056) — Latest-Attempt Test Assertions

- **Contribution:** Validated latest-attempt sorting and separately found that an integration test evaluated `state == "FINISHED"` without asserting it. Demonstrated that incorrect states could therefore pass the check.

- **Outcome:** The author accepted the test gap and added assertions for the latest attempt number and finished state. The PR remained open; live-cluster execution was left to CI, not reported as a completed local run.

### Ray [#67554](https://github.com/ray-project/ray/pull/67554#discussion_r4426035603) — Same-ID Reservation Ownership

- **Contribution:** Reproduced stale cleanup freeing a later same-ID reservation using real Dynamo with controlled callback timing. A [Ray Serve deployment follow-up](https://github.com/ray-project/ray/pull/67554#discussion_r4435201130) confirmed the production cancellation path, although the complete stale-free interleaving was not reached in deployment.

- **Outcome:** The merged code documents the request-ID uniqueness assumption after another reviewer requested a comment. The code still relies on that assumption; the full same-ID race was not reproduced in a Serve deployment.

### Ray [#67654](https://github.com/ray-project/ray/pull/67654#discussion_r4442719161) — Optuna Error-Trial Imports

- **Contribution:** Reproduced an errored-trial import failing on fixed configuration values absent from Optuna's distributions, preventing a `FAIL` record and interrupting subsequent batch imports.

- **Outcome:** The author accepted the finding, normalized parameters to the search space, added real-trial regressions, and reported 110 related tests passing. The PR remained open; those fix results were author-reported, not my post-fix revalidation.

### Ray [#67711](https://github.com/ray-project/ray/pull/67711#discussion_r4460114025) — ASHA Rungs Beyond the Training Limit

- **Contribution:** Demonstrated that epsilon-based rung calculation admitted a rung just above `max_t`, changing stopping decisions for a near-integer reduction factor in the actual scheduler.

- **Outcome:** The author accepted and reproduced the finding, excluded out-of-range rungs, and added regression coverage. A corrected revision received independent community approval; the PR remained open; I did not rerun the fix.

### Ray [#67739](https://github.com/ray-project/ray/pull/67739#discussion_r4460257865) — Dataset Execution-Option Isolation

- **Contribution:** Reproduced `split_at_indices()` mutating shared execution options: the input and sibling Datasets inherited `preserve_order=True`, including the sibling's later materialization.

- **Outcome:** The author accepted the finding, copied options before applying split-specific ordering, and added a regression. The author reported 419 split tests and 25 zip tests passing; the PR remained open without my post-fix revalidation.

### Ray [#67751](https://github.com/ray-project/ray/pull/67751#discussion_r4460335975) — Large-Integer Tune Metrics

- **Contribution:** Showed that a new NaN guard rejected previously valid `2**64` metrics, changing stopper behavior and causing public `Tuner.fit()` to fail where the parent completed.

- **Outcome:** The author accepted the finding, replaced the NaN check, and added regressions for both optimization modes. The PR remained open; I did not rerun the fix.

### Ray [#67861](https://github.com/ray-project/ray/pull/67861#discussion_r4487183835) — Nested Serializability Stream Ownership

- **Contribution:** Reproduced nested `inspect_serializability()` calls restoring stdout to a closed inner buffer, causing subsequent output to fail or be redirected silently.

- **Outcome:** The author accepted the finding, introduced per-call stream wrapping, and committed the nested-call regression. The design also addressed other reviewers' feedback; a corrected revision received community approval, but I did not rerun the fix.

### Ray [#67862](https://github.com/ray-project/ray/pull/67862#discussion_r4494316201) — Duplicate-Label Discretizer Transforms

- **Contribution:** Reproduced duplicate pandas labels breaking public `transform_batch()` for Custom and Uniform discretizers. Dataset ingestion rejected these labels on both revisions, so this was not a Dataset pipeline regression.

- **Outcome:** The author accepted the finding and added a per-column fallback and regression. [Revalidation](https://github.com/ray-project/ray/pull/67862#discussion_r4503122412) confirmed all four discretizer/copy-on-write combinations and input preservation; the PR remained open.

### Ray [#67850](https://github.com/ray-project/ray/pull/67850#discussion_r4503240066) — Disk-Sort Streaming Retry Integrity

- **Contribution:** Reproduced data corruption in a controlled `Dataset.sort()` run using real Arrow Flight and native Ray Core worker-crash retry. The run completed with 40 output rows, but 10 IDs were missing and 10 duplicated. The output matched the first attempt's retained block followed by the retry's remaining blocks.

- **Outcome:** The [author acknowledged the issue](https://github.com/ray-project/ray/pull/67850#discussion_r4532081725) and planned a separate fix. The PR merged; the failure was not reproduced without instrumentation on a matching build, and no follow-up fix was independently verified.

### Ray [#67883](https://github.com/ray-project/ray/pull/67883#discussion_r4503418337) — Canceled Model-Destructor Cleanup

- **Contribution:** Reproduced a file-descriptor cleanup leak when cancellation stopped a queued synchronous destructor and a class replacement disabled its garbage-collection fallback. [Independently revalidated](https://github.com/ray-project/ray/pull/67883#discussion_r4507267606) the shielding fix.

- **Outcome:** The author accepted the finding and changed cleanup lifetime, regression tests, and later exception logging. The PR remained open; later logging changes were not revalidated, and no full HTTP-disconnection reproduction was established.

### Ray [#67923](https://github.com/ray-project/ray/pull/67923#discussion_r4502928824) — Reconfiguration Latency Ownership

- **Contribution:** Demonstrated duplicate latency samples when a superseding user-config reconfiguration inherited a stale pending-rank entry. [Post-fix validation](https://github.com/ray-project/ray/pull/67923#discussion_r4507285676) restored one observation.

- **Outcome:** The author accepted the finding, transferred observation ownership, and added a handoff regression. The PR remained open; the test still lacked a direct assertion that latency was recorded exactly once.

## Code Reviews — Independent Validations

### Ray [#65314](https://github.com/ray-project/ray/pull/65314#pullrequestreview-5496581686) — Generator-Recovery Task Accounting

- **Validation:** Compared the unchanged recovery regression on the pre-fix base and patch, then exercised pending-task accounting through the real actor-submitter reply callback.

- **Outcome:** The base retained count 2; the patch kept 1 during recovery and returned to 0 on completion. No additional accounting defect was found; the PR remained open.

### Ray [#67031](https://github.com/ray-project/ray/pull/67031#pullrequestreview-5566530047) — Serve Callback and Cancellation Ordering

- **Validation:** Compared parent and head using identical regressions and four additional callback/cancellation interleavings, checking stale consume-wait handles and early callback delivery.

- **Outcome:** The early-callback regression failed on parent and passed on the patch; controls also passed. The PR received independent approval and merged, although available native-extension support limited full integration validation.

### Ray [#67117](https://github.com/ray-project/ray/pull/67117#pullrequestreview-5566731868) — Wrapped Batch Receiver and Cache Ownership

- **Validation:** Compared baseline and head across four batching regressions and two multiplexing cases, checking receiver selection, per-instance cache separation, and cached-model reuse.

- **Outcome:** All six cases passed on the tested head, alongside 95 batching tests. The PR remained open; later changes responding to another reviewer were not revalidated by me.

### Ray [#67146](https://github.com/ray-project/ray/pull/67146#pullrequestreview-5569477879) — Prometheus Symlink Refresh

- **Validation:** Tested Prometheus 2.48.1 on Linux/arm64 with an unchanged service-discovery symlink whose target was atomically replaced, comparing the existing and proposed refresh intervals.

- **Outcome:** The five-minute setting remained stale during a 20-second observation window; the proposed five-second interval discovered the update in 6.8 seconds. The PR remained open; validation was specific to that environment.

### Ray [#67135](https://github.com/ray-project/ray/pull/67135#pullrequestreview-5569706652) — Shuffle Label-Selector Propagation

- **Validation:** Compared object-store Shuffle V2 behavior on base and head, retained disk-shuffle controls, and verified actual labeled-node execution for map and reduce tasks.

- **Outcome:** Object-store cases failed on base and passed on the patch; disk controls stayed green. The reviewed implementation received independent community approval, and the PR remained open.

### Ray [#67137](https://github.com/ray-project/ray/pull/67137#pullrequestreview-5595453689) — HyperBand NaN Ranking

- **Validation:** Compared base and head across trial-order permutations in minimize/maximize modes, including finite ties and infinity/NaN controls for successive halving.

- **Outcome:** All 320 bracket executions passed on the reviewed implementation. The author clarified stable insertion-order handling for all-NaN ties; that untested policy boundary was not a new defect. The PR remained open.

### Ray [#67164](https://github.com/ray-project/ray/pull/67164#pullrequestreview-5581006956) — Generated-Column Name Collisions

- **Validation:** Compared parent and head using collision regressions and public `read_parquet` cases, retaining distinct-name and physical-column-replacement controls.

- **Outcome:** The reported validation covered the original reviewed revision. The author later substantially redesigned handling from constructor time to planning time; that version received independent approval and merged, without my revalidation of the redesigned implementation.

### Ray [#66998](https://github.com/ray-project/ray/pull/66998#pullrequestreview-5626468659) — Runtime-Environment Cache Accounting

- **Validation:** Compared pre-fix, initial, and revised implementations using real `URICache` and `WorkingDirPlugin`, including deletion failures, permission errors, partial deletion, and recovery.

- **Outcome:** The baseline reproduced the accounting failure; all five focused cases passed on the reviewed head. The author acknowledged the independent coverage, but the PR remained open; logical eviction did not guarantee physical cleanup after deletion failure.

### Ray [#67013](https://github.com/ray-project/ray/pull/67013#pullrequestreview-5626637992) — Repeater Mean Preservation

- **Validation:** Compared parent and head across four deterministic errored-repeat cases, inspecting actual BayesOptSearch and HyperOptSearch state, callback identity, and group completion.

- **Outcome:** All four cases failed on parent and passed on the reviewed head. The PR remained open; later warning-handling changes were not revalidated, and the existing all-NaN completion-order policy remained unchanged.

### Ray [#67192](https://github.com/ray-project/ray/pull/67192#pullrequestreview-5625301489) — NumPy Batch Copy Isolation

- **Validation:** Compared parent and head across numeric, tensor, nullable, ragged, mixed-schema, and multi-chunk batches under two PyArrow versions; checked source isolation and focused peak-memory behavior.

- **Outcome:** No source-mutation regression appeared; single-chunk memory improved, while multi-chunk gains were limited and acknowledged by the author. The PR remained open with community approval; its later rebase was not revalidated.

### Ray [#66953](https://github.com/ray-project/ray/pull/66953#pullrequestreview-5629521395) — Push RPC Deadline Recovery

- **Validation:** Used production ObjectManager/PushManager and real gRPC with a controlled receiver, comparing enabled and disabled deadlines in the same binary while a second request waited for the only slot.

- **Outcome:** The finite deadline released the slot and dispatched the second request before the first handler returned; the disabled control remained queued at the checkpoint. The PR remained open.

### Ray [#67275](https://github.com/ray-project/ray/pull/67275#pullrequestreview-5640264410) — uv Argument Parsing

- **Validation:** Compared parent and head through the production runtime-environment hook and both Client bootstrap command-construction paths, intercepting process execution.

- **Outcome:** The patch preserved the module boundary for explicit `uv run` options where parent raised `SystemExit(2)`; automatic-bootstrap controls passed on both. The PR remained open, and full Ray Client end-to-end execution was not validated.

### Ray [#67315](https://github.com/ray-project/ray/pull/67315#pullrequestreview-5640578228) — Actor-Lease Worker Cleanup

- **Validation:** Compared cancellation and same-node restart behavior using the production GCS actor manager/scheduler with controlled RPC replies, checking unused versus adopted worker ownership.

- **Outcome:** Both cleanup regressions failed on parent and passed on the patch: unused workers were released while the adopted worker and current attempt remained intact. The PR remained open; these were component checks, not full-cluster validation.

### Ray [#67360](https://github.com/ray-project/ray/pull/67360#pullrequestreview-5640802471) — Autoscaler Retry History

- **Validation:** Validated bounded-history retry accounting across 16 cases, then [revalidated the author's redesign](https://github.com/ray-project/ray/pull/67360#issuecomment-6469387650) after lifetime summaries moved into `InstanceManager` following another reviewer's request.

- **Outcome:** The initial parent failed three cases and the patch passed all 16; the revised implementation preserved tested behavior without a new regression. The PR remained open; these were Python-component checks, not full-cluster or restart-safety validation.

### Ray [#67399](https://github.com/ray-project/ray/pull/67399#pullrequestreview-5682355508) — RaySyncer Reactor Ownership

- **Validation:** Audited ownership captures in all eight dispatched handlers and the timer's weak-pointer guard, then rebuilt and ran `ray_syncer_test` on Linux ARM64 with caching disabled.

- **Outcome:** All 24 tests passed, including real-gRPC and production-factory authentication cases; no additional ownership gap was found. The PR remained open, and the original ASAN use-after-free was not directly reproduced.

### Ray [#67935](https://github.com/ray-project/ray/pull/67935#pullrequestreview-5814032675) — Arrow Schema Fast-Path Equivalence

- **Validation:** Compared 40 Arrow fixtures across 160 checks covering metadata, extension/tensor types, nulls, chunking, promotion, and expected errors; also measured focused identical-schema performance.

- **Outcome:** Outputs and expected exceptions matched, with improved identical-schema performance. The author subsequently revised the implementation; that version received approval and merged, but my validation covered the earlier head rather than the merged revision.

## Existing PR Follow-up

### Ray [#66818](https://github.com/ray-project/ray/pull/66818) — Queue-Owned Retry Eligibility Index

- **Contribution:** [Revised restart processing](https://github.com/ray-project/ray/pull/66818#discussion_r4244832025) in response to another reviewer's quadratic-scanning finding, adding a queue-owned eligibility index and lifecycle/invariant tests.

- **Outcome:** The October 12 revision made restart-specific eligibility work linear across callbacks; total queue work retained existing O(N log N) operations. Opened before this period, the PR closed without merge on November 11; only its substantial revision is counted.
