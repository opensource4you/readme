# Ray Monthly Contribution Report — October 2026

- **Contributor:** Jie-Kai Chang ([@400Ping](https://github.com/400Ping))
- **Period:** 2026-10-12 – 2026-11-14
- **Repository:** [ray-project/ray](https://github.com/ray-project/ray)

## Summary

| Category | Count |
| --- | ---: |
| PRs merged | 2 |
| PRs opened, still open (open / draft) | 3 (2 / 1) |
| Issues filed | 1 |

Main focus this period was the Ray Data query optimizer for the TPC-H benchmark suite ([#66829](https://github.com/ray-project/ray/issues/66829)): predicate pushdown through joins and a `condition` argument for `Dataset.join` both landed, and projection pushdown through joins is in draft. On the side, `read_text` was moved onto DataSource v2, and a scale-from-zero bug for GPUs requested through Kubernetes DRA was fixed in both the Ray autoscaler and KubeRay.

## Pull Requests

### Merged

#### [#67122](https://github.com/ray-project/ray/pull/67122) [Data] Predicate pushdown through joins: split conjuncts, derive one-sided OR weakenings
- **Status:** Opened 2026-10-17, merged 2026-11-11 (+1200 / −194, 10 files); closes [#66832](https://github.com/ray-project/ray/issues/66832)
- A `Filter` above a `Join` was routed as a single predicate, so `filter(left_pred & right_pred)` pushed nothing, and a disjunction such as TPC-H q7's France/Germany `OR` never reached the scans even though it implies a one-sided `IN` on each nation table.
- Splits the top-level `AND` chain at the join and routes each conjunct to every side that accepts it, leaving the rest as a residual `Filter`.
- Adds `DeriveJoinSidePredicates`, which derives the weaker one-sided predicate a residual `OR` implies and pushes it with the regular machinery, recursing through nested joins; the `OR` stays above the join to reject cross combinations. Runs once from `_post_optimize` with an enforcement check so re-optimizing an optimized plan is a no-op.
- Replaces name-only side routing in `Join` with column ownership that mirrors the physical join's naming (suffixes, key coalescing, semi/anti). This also fixes a pre-existing wrong-rows case where `col("val") == 1` under `RIGHT_OUTER` with a right suffix was pushed to the wrong input.
- TPC-H q7 evidence (sf1): both nation scans now receive `column1 == 'FRANCE' | column1 == 'GERMANY'` through `select_columns` and two `rename_columns`, rows out of each nation scan drop from 25 to 2, rows into the last join drop from 6,001,215 to 478,523, and the last shuffle map goes from 1.14 s to 0.10 s.
- Addressed review from @owenowenisme: moved helpers into `pushdown_utils.py`, replaced `getattr` with `SupportsFilterPushdown`, used `replace` so `ray_remote_args` are kept, trimmed docstrings, added a schema-less fallback for non-Arrow inputs, and replaced `dag_str` comparison in tests with a structural plan walk. @owenowenisme contributed a simplification commit before approving.

#### [#67590](https://github.com/ray-project/ray/pull/67590) [Data] Add `condition` to `Dataset.join` and extract equi-join keys from it
- **Status:** Opened 2026-10-30, merged 2026-11-13 (+849 / −33, 12 files); closes [#66834](https://github.com/ray-project/ray/issues/66834)
- `Dataset.join` only accepted hash keys through `on` / `right_on`, so a second equality between the two sides had to be written as a `filter` above the join. TPC-H q5 did exactly that, materializing every `l_suppkey` match before the nation check dropped most of it.
- Adds `condition: Expr` to `Dataset.join` and the `Join` logical operator, with `ON`-clause semantics: column references name input columns, an ambiguous name raises, and `on` stays required so a theta join is rejected.
- Adds `ExtractEquiJoinKeys`, which splits the condition's `AND` chain and turns every cross-side `col(a) == col(b)` into a condition key pair. Condition keys are hashed and matched like `on` keys but not coalesced, so the output columns match `join().filter()`. Non-equality conjuncts stay on the operator as the residual for [#66835](https://github.com/ray-project/ray/issues/66835).
- A column can be a key only once per side; a condition that reuses an `on` column raises with a pointer to `filter()`.
- Switched the TPC-H q2 and q5 release scripts from a filter above the join to `condition`, and verified both against DuckDB's reference SQL on sf1 data (q2: 100 identical rows including order; q5: identical revenue).
- Review: @rayo5o independently confirmed the ~25x probe-match reduction claim (184,082 vs 7,243 rows at sf1) and verified `ds.schema()` matches the output for all eight join types on both shuffle strategies. Addressed @sampan-s-nayak's requests (single `resolve_condition()` call, docstring with example, q2 script switched to `condition`) and @rayo5o's doc fix for the `right_outer` coalesced key name. Approved by @sampan-s-nayak.

### Open

#### [#68063](https://github.com/ray-project/ray/pull/68063) [Data] Read text files on DataSource v2
- **Status:** Opened 2026-11-11, in review (+1453 / −7, 9 files)
- `read_text` always used the V1 `TextDatasource`, which reads each file whole in one task with no column, limit or partition pushdown. With `use_datasource_v2` on, it now builds `TextDatasourceV2` and runs `ListFiles -> ReadFiles`, keeping V1 for `arrow_open_stream_args` and HTTP paths.
- `TextReader` implements `Reader.read` directly since `pyarrow.dataset` has no text format: decompress by extension, decode, `splitlines()`, same semantics as V1. Partition columns come from the path; files outside the partition directories get nulls. Supports column pruning, limit and partition pruning; task sizing uses bytes on disk instead of a sampled read.
- Addressed review from @abhishekverma-ray: preserve file order under `preserve_order=True` when there are more files than read tasks (round-robin wrapped around), wrap the filesystem in `RetryingPyFileSystem` so listing retries transient S3 errors, and reject a partition key named `text` at planning time instead of silently overwriting the column. Explained why an unknown partition key on a root file follows the existing `field_types` contract shared with V1 and the ORC V2 reader.
- Follow-up noted in the PR: splitting one text file across tasks once the line chunker from [#67725](https://github.com/ray-project/ray/pull/67725) lands.

#### [#67681](https://github.com/ray-project/ray/pull/67681) [Core][Autoscaler][Kuberay] Recognize DRA implicit extended resource names as GPUs
- **Status:** Opened 2026-11-02, awaiting review (+85 / −2, 2 files); related to [kuberay-project/kuberay#5488](https://github.com/kuberay-project/kuberay/issues/5488)
- Kubernetes Dynamic Resource Allocation (DRA) exposes every DeviceClass as `deviceclass.resource.kubernetes.io/<class>` ([KEP-5004](https://github.com/kubernetes/enhancements/tree/master/keps/sig-scheduling/5004-dra-extended-resource)). Classes without `extendedResourceName`, such as the NVIDIA GPU Operator's `gpu.nvidia.com`, can only be requested through that name, and the KubeRay autoscaler's `*gpu` suffix rule parsed such a group as 0 GPUs, so `minReplicas: 0` groups never scaled up for `num_gpus=1` work.
- Reproduced on a real RTX 4080 SUPER with the NVIDIA DRA driver 0.5.1. Adds `_is_gpu_resource_key`, which keeps the suffix rule and also accepts `deviceclass.resource.kubernetes.io/gpu.*`; both autoscaler v1 and v2 go through `AutoscalingConfigProducer`.

#### [#68158](https://github.com/ray-project/ray/pull/68158) [Data] Derive required columns through Join in ProjectionPushdown (draft)
- **Status:** Opened 2026-11-14 as draft (+828 / −61, 9 files); closes [#66833](https://github.com/ray-project/ray/issues/66833)
- `ProjectionPushdown` only reached the Parquet reader when the projection was adjacent to the read, so every TPC-H script opened with hand-written `select_columns` on each table.
- Adds a required-column interface on `LogicalOperator` and implements it for `Join`, map, one-to-one, n-ary and all-to-all operators so the rule can propagate the required-column set top-down through joins (split by side, add each side's join keys) and reach each scan. Test suite in `test_projection_fusion.py` (+508 lines).

## Issues

#### [#67127](https://github.com/ray-project/ray/issues/67127) [Data] `Dataset.join` fails with "unjoinable types" when one input is an empty pandas-backed block
- **Status:** Filed 2026-10-17, open
- Any join whose left or right input contains only empty pandas-backed blocks fails in `JoinShuffleReduce._preprocess` for all four join types, while the same join with an empty Arrow-backed input works. Found while testing [#67122](https://github.com/ray-project/ray/pull/67122): predicate pushdown moves a filter below the join, so a plain `join(...).filter(...)` on `from_pandas` data can hit this without the user writing the filter first.

## Reviewed PRs

No new reviews in this period.

## Other Contributions

- **KubeRay companion fix:** [kuberay-project/kuberay#5487](https://github.com/kuberay-project/kuberay/pull/5487) [Bug] Recognise the DRA implicit extended resource name in GPU detection (opened 2026-11-02, approved by @HsiaoHungKai, open) with the bug report [kuberay-project/kuberay#5488](https://github.com/kuberay-project/kuberay/issues/5488). `utils.IsGPUResourceKey` now accepts the DRA name, fixing `--num-gpus` rendering, `status.desiredGPU` and group-level `resources` in one place. Reproduced on kind with dra-example-driver and on real hardware with the NVIDIA DRA driver.
- **TPC-H optimizer series:** the three PRs above ([#67122](https://github.com/ray-project/ray/pull/67122), [#67590](https://github.com/ray-project/ray/pull/67590), [#68158](https://github.com/ray-project/ray/pull/68158)) are the first three items of the Ray Data TPC-H optimizer roadmap ([#66829](https://github.com/ray-project/ray/issues/66829))
