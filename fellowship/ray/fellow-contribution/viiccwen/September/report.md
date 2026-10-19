# [Vic] Ray Contribution Report — September–October 2026

**Period:** 2026-09-01 – 2026-10-11
**GitHub:** [@viiccwen](https://github.com/viiccwen)
**Area:** Ray Data

## Summary

- **10** PRs merged.
- **1** PR closed.
- **8** issues filed and **2** optimizer issues claimed.

## Pull Requests

### Merged

| Date | PR | Description |
|---|---|---|
| 2026-10-08 | [#66828 — [Data] Add limit to TPC-H Q18 benchmark](https://github.com/ray-project/ray/pull/66828) | Adds the spec-required `LIMIT 100` after the final sort in the TPC-H Q18 benchmark, so the benchmark matches the reference SQL. |
| 2026-09-24 | [#65512 — [Data] Warn when configured memory is below 1.25x max USS](https://github.com/ray-project/ray/pull/65512) | Detects map operators whose configured `memory` is below 125% of the observed max task USS, and logs a warning with a suggested setting. |
| 2026-09-17 | [#65750 — [Data] Make distribution metric export directly testable](https://github.com/ray-project/ray/pull/65750) | Refactors how Prometheus distribution metrics are created and recorded so the exported Gauges can be unit-tested directly. |
| 2026-09-17 | [#66080 — [Data] Re-enable stale partition field mismatch tests](https://github.com/ray-project/ray/pull/66080) | Re-enables two skipped partition field mismatch tests and updates them to match current behavior. |
| 2026-09-17 | [#66078 — [Data] Raise a descriptive partition value cast error](https://github.com/ray-project/ray/pull/66078) | Converts PyArrow cast failures on partition values into a `ValueError` that names the field, the value, and the target type. |
| 2026-09-17 | [#66040 — [Data] Make OOM warning messages easier to read](https://github.com/ray-project/ray/pull/66040) | Shortens OOM and unexpected worker failure warnings. Instead of dumping full Prometheus samples, they now show only the metric name, type, and latest cumulative value. |
| 2026-09-17 | [#66044 — [Data] Add logical memory usage chart](https://github.com/ray-project/ray/pull/66044) | Exports the logical heap memory allocated to running operators and adds a memory usage chart to the Ray Data Grafana "Resource Budget / Usage" section. |
| 2026-09-17 | [#65278 — [Data] Fix WebDataset docs and encoder typing](https://github.com/ray-project/ray/pull/65278) | Fixes the `Dataset.write_webdataset()` documentation and the type contract of the WebDataset encoder. |
| 2026-09-15 | [#66126 — [Data] Preserve anti-join rows with an empty unknown-schema side](https://github.com/ray-project/ray/pull/66126) | Fixes a hash-shuffle (V2) anti join bug that dropped rows from the preserved side when the other side was empty and its schema couldn't be inferred. |
| 2026-09-15 | [#66124 — [Data] Raise `NotImplementedError` in `BlockColumnAccessor.sum`](https://github.com/ray-project/ray/pull/66124) | Fixes `BlockColumnAccessor.sum()`, which returned `NotImplementedError` instead of raising it. |

### Closed

| Date | PR | Description |
|---|---|---|
| 2026-09-17 | [#65450 — [Data] Dereference ObjectRefs passed through map UDF `fn_args`](https://github.com/ray-project/ray/pull/65450) | Proposed passing `ObjectRef`s in `fn_args` as top-level task arguments so that the scheduler waits on them and UDFs no longer need to call `ray.get()`. Closed without merging because of backward-compatibility concerns. |

### Open

_None._

## Reviewed PRs

_None this period._

## Other Contributions

### Issues filed

| Date | Issue | Description |
|---|---|---|
| 2026-10-08 | [#66841 — [Data] Make null ordering consistent between Arrow/Pandas and Polars sort](https://github.com/ray-project/ray/issues/66841) | Reports that the Polars sort path (`use_polars_sort=True`) orders nulls differently from the Arrow and Pandas paths, which put nulls last. |
| 2026-09-12 | [#66125 — [Data] Anti joins drop rows when the empty side has an unknown schema](https://github.com/ray-project/ray/issues/66125) | Bug report for the anti-join row loss. Fixed by #66126. |
| 2026-09-12 | [#66123 — [Data] `BlockColumnAccessor.sum()` returns `NotImplementedError` instead of raising it](https://github.com/ray-project/ray/issues/66123) | Bug report for the returned exception. Fixed by #66124. |
| 2026-09-11 | [#66079 — [Data] Re-enable stale partition field mismatch tests](https://github.com/ray-project/ray/issues/66079) | Tracks the skipped partition tests. Resolved by #66080. |
| 2026-09-11 | [#66077 — [Data] Raise a descriptive error when a partition value cannot be cast](https://github.com/ray-project/ray/issues/66077) | Requests a clear error message for partition cast failures. Addressed by #66078. |
| 2026-08-16 | [#65511 — [Data] Warn when configured memory is below 1.25x max USS](https://github.com/ray-project/ray/issues/65511) | Proposes warning when an operator's configured memory is below 125% of the observed max USS. Resolved by #65512. |
| 2026-08-13 | [#65449 — [Data] ObjectRefs passed to map UDFs through `fn_args` are not dereferenced](https://github.com/ray-project/ray/issues/65449) | Reports that `ObjectRef`s in `fn_args` reach UDFs without being dereferenced and aren't treated as scheduling dependencies. Explored in #65450. |
| 2026-08-07 | [#65277 — [Data] Correct `write_webdataset` documentation and encoder type contract](https://github.com/ray-project/ray/issues/65277) | Reports mismatches between the `write_webdataset()` docs and the encoder's types. Fixed by #65278. |

### Issues claimed

| Date | Issue | Description |
|---|---|---|
| 2026-10-08 | [#66831 — [data] Push Limit through Sort (Top-N execution)](https://github.com/ray-project/ray/issues/66831) | Logical optimizer rule that turns `sort().limit(k)` into a Top-N, so the full global sort is skipped. This matters for the TPC-H queries that end in `ORDER BY ... LIMIT`. |
| 2026-10-08 | [#66837 — [data] Plan-level common subtree reuse (CSE on the logical DAG)](https://github.com/ray-project/ray/issues/66837) | Reuses a logical subtree that is consumed more than once instead of running it twice. This would remove manual workarounds such as the one in TPC-H Q2. |
