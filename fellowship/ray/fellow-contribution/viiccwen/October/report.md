# [Vic] Ray Contribution Report — October 2026

**Period:** 2026-10-12 – 2026-11-15
**GitHub:** [@viiccwen](https://github.com/viiccwen)
**Area:** Ray Data

## Summary

- **6** PRs merged.
- **1** PR open.
- **2** PRs reviewed.
- **5** issues filed.

## Pull Requests

### Merged

| Date | PR | Description |
|---|---|---|
| 2026-11-14 | [#67156 — [Data] Fix Parquet filename suffix detection](https://github.com/ray-project/ray/pull/67156) | A custom `FilenameProvider` name that just contained `parquet` in its stem was treated as if it already ended in `.parquet`. Suffix detection now checks the actual extension. |
| 2026-11-14 | [#67154 — [Data] Make `zero_copy_batch=False` copy default batches](https://github.com/ray-project/ray/pull/67154) | With the default `batch_size=None`, `map_batches(zero_copy_batch=False)` still passed a read-only NumPy view of the whole block. Batches are now copied, as the caller asked. |
| 2026-11-14 | [#66927 — [Data] Fix docstring/behavior mismatches in `read_json` and `Dataset.sort`](https://github.com/ray-project/ray/pull/66927) | Fixes the `read_json(lines=True)` docstring, which said some arguments are ignored when they actually raise `ValueError`, and the `Dataset.sort()` docstring. Adds a test for the `ValueError`, which had no coverage. |
| 2026-11-13 | [#66931 — [Data] Add Top-N execution](https://github.com/ray-project/ray/pull/66931) | Adds an opt-in Top-N path for `sort(...).limit(k)` under `SHUFFLE_V2`, replacing the full range shuffle. Each block contributes at most `k` candidates, and a streaming merge operator keeps the running Top-N. In a reviewer's benchmarks, Top-N was **4–8x** faster at 128+ blocks. For 100M rows with a 128-byte payload, it ran in 3.5s with 0.5 GiB of extra worker memory, versus 21.4s and 16.3 GiB for sort + limit. Closes claimed issue #66831. |
| 2026-10-13 | [#66936 — [Data] Propagate labels to disk SHUFFLE_V2 map/reduce tasks](https://github.com/ray-project/ray/pull/66936) | Disk-based `SHUFFLE_V2` map and reduce tasks ignored `ExecutionOptions.label_selector`, so shuffle work could run outside the Dataset's selected subcluster. The Dataset-level selector is now merged into both phases' task options, and operator-level selectors still take precedence. |
| 2026-10-13 | [#66925 — [Data] Make Polars sort place nulls last](https://github.com/ray-project/ray/pull/66925) | Makes the Polars sort path (`use_polars_sort=True`) put nulls last, like the Arrow and Pandas paths. Before this, turning on Polars sort could change the result order and the rows returned by `sort(...).limit(k)`. |

### Open

| Opened | PR | Description |
|---|---|---|
| 2026-10-18 | [#67135 — [Data] Propagate labels to object-store SHUFFLE_V2 map/reduce tasks](https://github.com/ray-project/ray/pull/67135) | Follow-up to #66936 for the object-store `SHUFFLE_V2` path, so keyed repartition, aggregation, and join shuffles stay in the Dataset's subcluster. The label selector tests now cover both object-store and disk shuffle. |

## Reviewed PRs

| Date | PR | Description |
|---|---|---|
| 2026-11-13 | [#67143 — [Data] Make shuffle reduce operator the owner of flight server](https://github.com/ray-project/ray/pull/67143) by [@owenowenisme](https://github.com/owenowenisme) | Moves ownership of the disk shuffle flight server into the reduce operator, so it no longer needs a detached actor. I pointed out that `.shf` files can be left on disk if execution stops before the reducer has tracked every mapper node, and suggested having the planner pass the row-count source so the reducer doesn't depend on upstream operator internals. |
| 2026-10-22 | [#66909 — [Data][Docs] Clarify task USS metrics and platform limitations](https://github.com/ray-project/ray/pull/66909) by [@Jo-Leo-35](https://github.com/Jo-Leo-35) | Docs update for the task USS metrics I added in #65666. I suggested keeping the OOM-guide note focused on the Linux-only USS requirement, and correcting wording that suggested PyTorch tensors always lead to underestimated USS. Closed without merging. |

## Other Contributions

### Issues filed

| Date | Issue | Description |
|---|---|---|
| 2026-11-13 | [#68117 — [Data] `streaming_split` regular and regular_equal benchmarks incorrectly enable early stopping](https://github.com/ray-project/ray/issues/68117) | Reports that the `streaming_split` `regular` and `regular_equal` release benchmarks always take the early-stop branch: `--early-stop` uses `store_true`, so it defaults to `False`, and the `is not None` check is always true. |
| 2026-10-19 | [#67155 — [Data] Parquet writes treat `parquet` anywhere in a custom filename as a `.parquet` suffix](https://github.com/ray-project/ray/issues/67155) | Bug report for the filename suffix check. Fixed by #67156. |
| 2026-10-19 | [#67153 — [Data] `map_batches(zero_copy_batch=False)` does not copy NumPy batches when `batch_size` is omitted](https://github.com/ray-project/ray/issues/67153) | Bug report for the uncopied whole-block batches. Fixed by #67154. |
| 2026-10-12 | [#66935 — [Data] Object-store SHUFFLE_V2 map/reduce tasks ignore `label_selector`](https://github.com/ray-project/ray/issues/66935) | Reports that object-store shuffle tasks can run outside the selected subcluster. Addressed by #67135. |
| 2026-10-12 | [#66930 — [Data] Disk SHUFFLE_V2 map/reduce tasks ignore `label_selector`](https://github.com/ray-project/ray/issues/66930) | Same problem on the disk shuffle path. Fixed by #66936. |

### Follow-ups from last period

- [#66841](https://github.com/ray-project/ray/issues/66841) (Polars sort null ordering, filed in September) was fixed by #66925.
- [#66831](https://github.com/ray-project/ray/issues/66831) (Top-N execution, claimed in September) was implemented in #66931, merged on 2026-11-13.
- [#66837](https://github.com/ray-project/ray/issues/66837) (plan-level common subtree reuse, claimed in September) has not been started yet.
