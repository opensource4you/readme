# Ray Contribution Report — October 2026

- **Contributor:** Hao-Ting Ho ([@Jadiouo](https://github.com/Jadiouo))
- **Period:** 2026-10-12 – 2026-11-14
- **Repository:** [ray-project/ray](https://github.com/ray-project/ray)
- **Areas:** Ray Data, RLlib, Tune, Serve, Core

## Summary

| Category | Count |
| --- | ---: |
| PRs opened (all open; 4 draft) | 5 |
| Issues opened | 3 |
| Issues investigated (repro / root cause posted) | 4 |
| PRs reviewed | 54 (6 merged, 2 closed) |

Most of this period went into reviewing Ray Data and Tune PRs. For each review I ran the base and the PR through the public API and posted the before/after results. Four of my own PRs came out of these reviews and issues.

## Highlights

- **Fixed a shared-state leak a reviewer found in my PR.** In [#67739](https://github.com/ray-project/ray/pull/67739), hikaru-212 found that forcing `preserve_order` changed the `ExecutionOptions` shared by sibling datasets. I fixed it by copying the options and extended the test to catch it.
- **Silent LSTM state reset in multi-agent sampling.** While working on [#67740](https://github.com/ray-project/ray/pull/67740) (issue [#56771](https://github.com/ray-project/ray/issues/56771)), I found that with agents acting in turns, `MultiAgentEpisode.cut()` also makes the env runner restart each agent from the initial state after every `sample()`. The learner crash was only part of it. I prototyped a fix in `cut()` and asked the maintainers which direction they prefer ([comment](https://github.com/ray-project/ray/pull/67740)).
- **Reviews that changed the PR:**
  - [#67407](https://github.com/ray-project/ray/pull/67407): the fix made `ray.init(ignore_reinit_error=True)` raise after a successful client connect, which library code calls unconditionally. The author narrowed the check.
  - [#67324](https://github.com/ray-project/ray/pull/67324): found that the loop kept sending after an early failure and that retries bypassed the qps limit. The author fixed both.
  - [#67266](https://github.com/ray-project/ray/pull/67266): found that all-null or empty variable-shaped arrays still failed. The author added the fix and a test.
  - [#67376](https://github.com/ray-project/ray/pull/67376): measured that `/` already differs between pandas and Arrow blocks on the base. The maintainers used this to decide to keep `/` as it is.
  - [#67862](https://github.com/ray-project/ray/pull/67862): found that the base overwrote the caller's DataFrame in `transform_batch`. The author documented the fix.
- **Two PRs that break each other.** [#67618](https://github.com/ray-project/ray/pull/67618) and [#67171](https://github.com/ray-project/ray/pull/67171) merge cleanly, but together the new test fails with `KeyError: 'trial_id'`.
- **Picked up an 8-month-old PR.** [#61444](https://github.com/ray-project/ray/pull/61444) had no Ray Data maintainer review. I verified it (787 KB → 14 KB for a 1000-row slice) and found it needs a rebase (`NameError` on current master).

## Pull Requests

| PR | Status | Description |
| --- | --- | --- |
| [#67739](https://github.com/ray-project/ray/pull/67739) [Data] Keep row order in split_at_indices and train_test_split | Open (draft) | Non-shuffled splits now follow row order. Out-of-order runs: 5/5 → 0/5 with one slow block. Issue [#67181](https://github.com/ray-project/ray/issues/67181). |
| [#67740](https://github.com/ray-project/ray/pull/67740) [RLlib] Fix stateful module crash in learner connector for multi-agent episodes with agents acting in turns | Open (draft) | The second `train()` no longer crashes for TicTacToe with `use_lstm=True`. Based on the closed [#64081](https://github.com/ray-project/ray/pull/64081), with credit. Issue [#56771](https://github.com/ray-project/ray/issues/56771). |
| [#67742](https://github.com/ray-project/ray/pull/67742) [Data] Only serialize referenced bytes of string/binary view slices | Open (draft, stacked on [#67409](https://github.com/ray-project/ray/pull/67409)) | Object store size for the issue's script with `batch_size=100`: 2.4 GB → 4.2 MB. Issue [#67368](https://github.com/ray-project/ray/issues/67368). |
| [#67743](https://github.com/ray-project/ray/pull/67743) [Data] Make zero_copy_batch=False batches writable in map_groups and for list columns | Open (draft) | `zero_copy_batch=False` now gives writable NumPy batches in `map_groups` and for list columns. Issue [#67367](https://github.com/ray-project/ray/issues/67367). |
| [#67161](https://github.com/ray-project/ray/pull/67161) [Data] Deflake test_train_test_split by preserving block order | Open | Applies the fix from [#57113](https://github.com/ray-project/ray/pull/57113) to the one order-dependent test it missed. |

## Issues

| Issue | Description |
| --- | --- |
| [#67181](https://github.com/ray-project/ray/issues/67181) | Should `train_test_split(shuffle=False)` / `split_at_indices` keep row order by default? Repro: wrong 10/10 times with the default settings. |
| [#67367](https://github.com/ray-project/ray/issues/67367) | `zero_copy_batch=False` doesn't give writable NumPy batches in `map_groups` and for list columns. Found while reviewing [#67154](https://github.com/ray-project/ray/pull/67154). |
| [#67368](https://github.com/ray-project/ray/issues/67368) | `map_batches` output with string_view columns grows with the number of batches (4 MB input → 2.4 GB). Found while reviewing [#67148](https://github.com/ray-project/ray/pull/67148). |
| [#56771](https://github.com/ray-project/ray/issues/56771) (investigated) | Reproduced the RLlib multi-agent + LSTM crash on master and traced the root cause to `MultiAgentEpisode.cut()`. Led to [#67740](https://github.com/ray-project/ray/pull/67740). |
| [#54314](https://github.com/ray-project/ray/issues/54314) (investigated) | Reproduced on master that the module-to-env pipeline is built from the env's spaces, with a public-API repro and two possible directions. |
| [#67606](https://github.com/ray-project/ray/issues/67606) (investigated) | Proposed a narrower fix for null Hive partitions written as null-typed Parquet columns, with results on the author's branch. |
| [#67312](https://github.com/ray-project/ray/issues/67312) (investigated) | Offered to take it; the author's fix [#67374](https://github.com/ray-project/ray/pull/67374) was merged after my review. |

## Reviewed PRs

Result = my review state. Status = PR state on 2026-11-13.

### Ray Data

| PR | Result | Status | What I checked / found |
| --- | --- | --- | --- |
| [#67154](https://github.com/ray-project/ray/pull/67154) `zero_copy_batch=False` copies default batches | Approved | Open | Reproduced the read-only error; found the `map_groups` and list-column gaps (→ [#67367](https://github.com/ray-project/ray/issues/67367)). |
| [#67156](https://github.com/ray-project/ray/pull/67156) Parquet filename suffix detection | Approved | Open | Reproduced the missing `.parquet` suffix; 4 new cases fail on base. |
| [#67135](https://github.com/ray-project/ray/pull/67135) Labels for object-store SHUFFLE_V2 tasks | Approved | Open | `object_store` cases fail on base, pass with the PR; `disk` cases pass on both. |
| [#66997](https://github.com/ray-project/ray/pull/66997) `TensorArray.take` row corruption | Approved | Open | Reproduced the wrong rows; showed a bot's suggestion would break `take([-1, -1])`. |
| [#67148](https://github.com/ray-project/ray/pull/67148) string-view / binary-view serialization | Commented | Open | Found that view slices keep whole buffers (→ [#67368](https://github.com/ray-project/ray/issues/67368)). |
| [#67409](https://github.com/ray-project/ray/pull/67409) string-view / binary-view serialization (supersedes #67148) | Commented | Open | Every view array fails to deserialize on pyarrow 17/18; suggested keeping the IPC fallback below 19. |
| [#67192](https://github.com/ray-project/ray/pull/67192) Skip redundant block copy for NumPy batches | Approved | Open | 168 combinations, no shared memory; peak extra memory 501 MB → 244 MB. |
| [#67266](https://github.com/ray-project/ray/pull/67266) Unify fixed-shape with lower-ndim variable-shaped tensors | Commented | Open | Found the all-null / empty edge case; the author added it. |
| [#67363](https://github.com/ray-project/ray/pull/67363) Hive null partitions read as `None` | Approved | Open | Verified parquet / CSV / multi-level; reproduced the bot finding on the first commit and its fix. |
| [#67293](https://github.com/ray-project/ray/pull/67293) `None` for null rows in Arrow tensor columns | Approved | Open | Null rows: zeros → `None`; the bot's vectorized version was only 1.05–1.2x faster. |
| [#67374](https://github.com/ray-project/ray/pull/67374) Don't count terminal waits as output backpressure | Approved | Merged 10/28 | Reported backpressure for the issue's repro: 10 s → 0. |
| [#67376](https://github.com/ray-project/ray/pull/67376) True division for `/` on integer columns | Commented | Closed | Measured that pandas and Arrow blocks already disagree; maintainers kept `/` as it is. |
| [#67324](https://github.com/ray-project/ray/pull/67324) HttpRequestUDF sends requests as they are created | Commented | Open | 5 rows on a 0.5 s server: 2.52 s → 0.51 s; found 2 behavior changes, both fixed. |
| [#67355](https://github.com/ray-project/ray/pull/67355) Stop the block prefetcher when iteration ends early | Approved | Merged 11/4 | 24 leaked prefetcher threads → 0 after early `break`. |
| [#67437](https://github.com/ray-project/ray/pull/67437) SQL sharding falls back to `%` | Commented | Open | Emulated a DB without `MOD()`; noted NULL keys are still dropped and the tests could be parametrized. |
| [#67506](https://github.com/ray-project/ray/pull/67506) Reject non-finite weights in `Dataset.mix` | Commented | Open | NaN / inf weights hung on base, now raise at `mix()`. |
| [#67420](https://github.com/ray-project/ray/pull/67420) Processor stages with colliding names | Approved | Open | Base silently dropped a stage (output `13` instead of `123`). |
| [#67283](https://github.com/ray-project/ray/pull/67283) Join dense chunks once for multi-chunk tensor columns | Approved | Open | 40 cases identical to base; conversion 273 ms → 60 ms. |
| [#67528](https://github.com/ray-project/ray/pull/67528) Avoid redundant scalar block copies before pandas conversion | Approved | Open | Peak RSS 436 → 186 MiB; in-place UDF writes still don't touch the source. |
| [#67672](https://github.com/ray-project/ray/pull/67672) Avoid whole-block copies when batching multi-chunk Arrow inputs | Approved | Open | Peak Arrow memory 68 → 7 MiB; found a conflict with #67192. |
| [#67806](https://github.com/ray-project/ray/pull/67806) Native tensor null rows on pyarrow 26 | Approved | Merged 11/5 | `ArrowInvalid` on pyarrow 26 fixed; noted CI doesn't run pyarrow 26. |
| [#67736](https://github.com/ray-project/ray/pull/67736) Backpressure when downstream capacity is zero | Commented (2 rounds) | Open | Upstream queue peak 132 → 45 MB with a slow-starting actor; asked for an e2e test, which the author added. |
| [#61444](https://github.com/ray-project/ray/pull/61444) Dense union arrays in custom serialization | Approved | Open | 787 KB → 14 KB per 1000-row slice; needs a rebase (`NameError`). |
| [#67855](https://github.com/ray-project/ray/pull/67855) Prune Parquet row groups for mixed OR filters | Commented (2 rounds) | Open | Bytes read 6.7 → 4.2 MB, same rows in 21 queries; my earlier points on e2e tests were addressed. Shared my script as a [gist](https://gist.github.com/Jadiouo/35fcb95a0aee582953449dc44c65648f). |
| [#67868](https://github.com/ray-project/ray/pull/67868) Wait for `iter_threaded` workers to exit | Approved | Closed | Up to 3 live threads after `break` → 0; the author moved the fix to core. |
| [#67862](https://github.com/ray-project/ray/pull/67862) Avoid full-DataFrame apply in KBinsDiscretizer | Approved | Open | Found that base overwrote the caller's DataFrame; 80 → 52 ms. |
| [#67845](https://github.com/ray-project/ray/pull/67845) Vectorize LeRobot delta segments | Commented | Open | The two "vectorized" tests also pass on base; the author rewrote them. |
| [#67949](https://github.com/ray-project/ray/pull/67949) TableBlockBuilder compaction accounting | Commented | Open | Blocks `[7,7,7,7,7,5]` → `[8,8,8,8,8]`; the new tests fail under the `tests/unit` conftest. |

### RLlib

| PR | Result | Status | What I checked / found |
| --- | --- | --- | --- |
| [#67914](https://github.com/ray-project/ray/pull/67914) Keep truncated agents truncated | Approved | Open | Truncated agent: `(T, T)` → `(F, T)`; found that `slice()` drops the truncated flag on base too. |
| [#67961](https://github.com/ray-project/ray/pull/67961) Non-Discrete action spaces in PrevActionsPrevRewards | Approved | Open | Box / MultiDiscrete / Tuple / Dict crashed on base; values encoded correctly with the PR. |

### Tune

| PR | Result | Status | What I checked / found |
| --- | --- | --- | --- |
| [#67171](https://github.com/ray-project/ray/pull/67171) BayesOptSearch.register_analysis misaligning configs | Approved | Open | New test fails on base with the pinned `bayesian-optimization` 1.4.0. |
| [#67425](https://github.com/ray-project/ray/pull/67425) resolve_placeholders crash | Approved | Open | Mixed search spaces crashed on base; `Tuner.restore` now restores the original objects. |
| [#67482](https://github.com/ray-project/ray/pull/67482) Record `sample_from` values in `evaluated_params` | Approved | Open | Values now recorded and survive `Tuner.restore`. |
| [#67618](https://github.com/ray-project/ray/pull/67618) Nested `points_to_evaluate` in BayesOptSearch | Approved | Open | Found it breaks together with #67171 (`KeyError: 'trial_id'`). |
| [#67712](https://github.com/ray-project/ray/pull/67712) MedianStoppingRule without `mode` | Approved | Open | Base silently minimized and stopped the best trial first. |
| [#67711](https://github.com/ray-project/ray/pull/67711) ASHA rung at `max_t` | Approved | Open | Base dropped the 243 rung; asked for a guard, which the author added. |
| [#67830](https://github.com/ray-project/ray/pull/67830) Report a returned or yielded zero | Approved | Open | Noted that `return False` is now reported too. |
| [#67872](https://github.com/ray-project/ray/pull/67872) DistributeResources fractional increments | Approved | Open | 0.1-GPU steps stopped at 0.9 on base, reach 1.0 with the PR. |
| [#67829](https://github.com/ray-project/ray/pull/67829) `max_concurrent_trials` for a user BasicVariantGenerator | Approved (2 rounds) | Open | Peak concurrency 4 → 2; asked about the new error, and the author kept a lower limit. |

### Serve

| PR | Result | Status | What I checked / found |
| --- | --- | --- | --- |
| [#67434](https://github.com/ray-project/ray/pull/67434) Busy-loop drain with `graceful_shutdown_wait_loop_s=0` | Approved | Merged 11/5 | ~100k log lines at 100% CPU → 4 lines. |
| [#67405](https://github.com/ray-project/ray/pull/67405) `@serve.batch` mixing up argument order | Approved | Open | Base gave one caller another caller's value. |
| [#67675](https://github.com/ray-project/ray/pull/67675) Reject `target_ongoing_requests=None` | Approved | Merged 11/5 | Base logged `TypeError` hundreds of times; now a validation error. |
| [#67210](https://github.com/ray-project/ray/pull/67210) Drain buffered gRPC stream messages | Approved | Open | Replica killed mid-stream: 0 → all chunks delivered before the error. |
| [#67844](https://github.com/ray-project/ray/pull/67844) ASGI lifespan state in each request | Approved | Merged 11/10 | 500 → 200 with the lifespan's object, no per-request leaks. |

### Core, Autoscaler, Train

| PR | Result | Status | What I checked / found |
| --- | --- | --- | --- |
| [#67407](https://github.com/ray-project/ray/pull/67407) `ray.init()` after a failed client connect | Commented, then approved | Open | Found the `ignore_reinit_error=True` regression; re-verified the narrowed fix. |
| [#67658](https://github.com/ray-project/ray/pull/67658) `Queue` put/get with `timeout=0` | Approved | Open | Base raised `Empty` / `Full` even when it could proceed. |
| [#67582](https://github.com/ray-project/ray/pull/67582) Pool `map_async` callback order | Approved | Open | Callback now gets input order, matching `.get()`. |
| [#67586](https://github.com/ray-project/ray/pull/67586) Keep `default_tags` when pickling metrics | Approved | Open | `ValueError` in remote tasks on base. |
| [#67861](https://github.com/ray-project/ray/pull/67861) colorama in `inspect_serializability` | Approved | Open | `RecursionError` at call 249 on base; 600 calls fine with the PR. |
| [#67871](https://github.com/ray-project/ray/pull/67871) Conflicting `=` / `!=` state API filters | Approved | Open | Same rows; the PR skips the request and warns. |
| [#67686](https://github.com/ray-project/ray/pull/67686) autoscaler v2 `KeyError` for a removed node type | Approved | Open | Reproduced through the scheduler the way the reconciler calls it. |
| [#67685](https://github.com/ray-project/ray/pull/67685) autoscaler v2 `max_nodes` vs `min_workers` | Approved | Open | Base cancelled both launches and made empty-ID terminations. |
| [#67807](https://github.com/ray-project/ray/pull/67807) autoscaler v2 repeated scheduling work | Approved | Open | 423 scenarios identical to base; 1.9 s → 0.5 s CPU. |
| [#67651](https://github.com/ray-project/ray/pull/67651) Shut down failed worker group before sizing replacement | Approved | Open | Reproduced the leaked CPU in 1 of 4 base runs; noted it depends on timing. |

## Learn

- Ray Data often hands a UDF a zero-copy slice of the block instead of a copy. That explains why `zero_copy_batch=False` still gave read-only arrays in `map_groups`, and why a string-view slice kept all of the parent's buffers. I check what a slice references before trusting its size.
- In my own PR, setting `preserve_order` changed the `ExecutionOptions` that sibling datasets share, and a reviewer caught it. I didn't know datasets shared that object until then.
- `MultiAgentEpisode.cut()` counts its lookback in env steps, so an agent that acts in turns can lose its last state. Tracing this taught me how episodes, connectors and env runners pass state around.
- A new test that also passes on base doesn't test the fix. I run new tests on base first, and a few times that was the main thing I reported.
- I ask about the direction on the issue first, with a repro and a plan, before writing a fix. I also try to keep comments short, since long ones cost reviewers time.
