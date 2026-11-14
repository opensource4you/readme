# Ray Monthly Contribution Report — October 2026

- **Contributor:** [@rayo5o](https://github.com/rayo5o)
- **Period:** 2026-10-12 – 2026-11-14
- **Repository:** [ray-project/ray](https://github.com/ray-project/ray)
- **Area:** Ray Data

## Highlights

- **Found, fixed and back-ported pyarrow 26 failures on Arrow-native tensor columns.** Reviewing [#67579](https://github.com/ray-project/ray/pull/67579), I found its null-row fix raised on pyarrow 26, which Ray CI did not test. My fix [#67806](https://github.com/ray-project/ray/pull/67806) merged within a day, and my review of the 2.60.1 cherry-pick got it shipped in 2.60.1 and 2.59.2 ([#68019](https://github.com/ray-project/ray/pull/68019), [#68026](https://github.com/ray-project/ray/pull/68026)). When [#68005](https://github.com/ray-project/ray/pull/68005) merged with another pyarrow 26 failure I had pointed out, I found that `sort()`, `map_groups()`, `repartition()`, `join()` and `zip()` all failed on these columns, and opened the fix [#68072](https://github.com/ray-project/ray/pull/68072).
- **Cut worker memory in batching.** Found that the batcher copies each multi-chunk input block into worker memory before the first batch, and proposed reading only the needed rows (peak 391 to 14 MiB) in [#67527](https://github.com/ray-project/ray/issues/67527). Another contributor implemented it in [#67672](https://github.com/ray-project/ray/pull/67672); my end-to-end check (extra memory per task 118 to 1 MiB, identical batches on 180 edge cases) is in its description, and a maintainer is reviewing it. My [#67192](https://github.com/ray-project/ray/pull/67192) halves peak memory for NumPy batches with `zero_copy_batch=False` (501 to 245 MiB).
- **Reviews that changed the PR before merge** (23 Ray Data PRs reviewed, each checked against its merge base):
  - [#67579](https://github.com/ray-project/ray/pull/67579): my cost numbers (one null block made a 294 MiB batch take 0.5 s instead of 0.025 s) led the maintainer to change the design.
  - [#67590](https://github.com/ray-project/ray/pull/67590): the maintainer made my second-round docs fix the condition for merging.
  - [#67891](https://github.com/ray-project/ray/pull/67891): found that the fix turned an error into a silently wrong `std()` (1.42e18 against a true 2.45e9); the author adopted my one-line fix.
  - [#68017](https://github.com/ray-project/ray/pull/68017): found that the DataSource V2 migration fails on a shard master reads; fixed the next day.
  - [#66931](https://github.com/ray-project/ray/pull/66931): found a `ValueError` with default configs, then re-measured the maintainer's rework (100M rows: 21.37 to 3.45 s).

## Summary

| Category | Count |
| --- | ---: |
| PRs merged | 1 |
| PRs opened (open / draft) | 4 |
| Issues opened | 6 |
| PRs reviewed | 23 |
| Issues investigated | 1 |

## Opened PRs

#### [#67192](https://github.com/ray-project/ray/pull/67192) [Data] Skip the redundant block copy for NumPy batches with `zero_copy_batch=False` (open)
- Follow-up from the #67154 review: I opened [#67185](https://github.com/ray-project/ray/issues/67185) for the double copy and implemented the fix. NumPy batches now skip the Arrow block copy when every column converts to a non-object array.
- Halves peak memory for single-chunk fixed-width blocks (501 vs 245 MiB on a 244 MiB block); multi-chunk blocks stay the same, as a reviewer found and I confirmed. Found that dropping the copy for every column lets a UDF modify the source data of variable-shaped tensors, so those columns keep the old path.

#### [#67266](https://github.com/ray-project/ray/pull/67266) [Data] Fix unifying a fixed-shape tensor array with a lower-ndim variable-shaped one (open)
- Found that a tensor column mixing fixed-shape 2-D and ragged 1-D blocks always crashes on concat, e.g. `union()` then `iter_batches()`. Reported it in [#67265](https://github.com/ray-project/ray/issues/67265) and fixed it.
- Traced it to two bugs in one method that no test reached. A stale-closed PR (#60389) had the same fix, so master still crashed. Added regression tests over both orders and all three tensor formats, and fixed the all-null and empty cases a reviewer raised.

#### [#67283](https://github.com/ray-project/ray/pull/67283) [Data] Join dense chunks once when converting multi-chunk tensor columns to pandas (open)
- Follow-up to #66122, whose zero-copy pandas path skipped columns with more than one chunk, which is what a batch spanning two blocks produces. Reported it in [#67282](https://github.com/ray-project/ray/issues/67282) and fixed it.
- With every batch spanning two blocks, `iter_batches(batch_format="pandas")` went from 346 to 179 ms for small rows and 172 to 147 ms for image-sized rows. Checked against the old path on 15 edge cases.

#### [#67806](https://github.com/ray-project/ray/pull/67806) [Data] Fix native tensor null rows on pyarrow 26 (merged)
- Follow-up to my review of #67579, which merged with a regression I had pointed out: on pyarrow 26, any batch with a null row in an Arrow-native tensor column raised `ArrowInvalid` instead of returning `None`.
- Traced it to pyarrow 26 rejecting nulls in the tensor conversion, which Ray CI can't see because it tests pyarrow 17 and 23 only. The fix converts the storage without its null bitmap; 19 failing tests pass on pyarrow 26, with results identical to before on older versions across 12 edge cases. Merged within a day of opening.

#### [#68072](https://github.com/ray-project/ray/pull/68072) [Data] Fix shuffle and join on native tensor columns with pyarrow 26 (open)
- Follow-up to my review of #68005, which merged with a pyarrow 26 failure I had pointed out; it still failed on master.
- Found it was wider than the one sort test: on pyarrow 26, `sort()`, `groupby().map_groups()`, `repartition()`, `join()` and `zip()` all failed on native tensor columns, including Parquet written with pyarrow's own tensor type under default settings, because Ray built empty blocks with `Schema.empty_table()`. The fix builds them column by column, matching the old result for every other type compared.

## Opened issues

#### [#67459](https://github.com/ray-project/ray/issues/67459) [Data] Copy batches only once with `zero_copy_batch=False` in the remaining cases (open)
- Follow-up plan for #67192, opened to ask maintainers before any PR. Lists the three cases that still copy each batch twice: multi-chunk columns, pandas batches, and batches with one string or other object column.
- A local patch that copies once cut peak memory from 513 to 257 MiB on multi-chunk blocks and from 543 to 287 MiB with one string column; for default pandas batches the extra copy is 260 MiB and protects nothing.
- Another contributor opened draft PR #67528 on 10-29 covering the same three cases. Measured it against its base and replied: with 5000-chunk blocks and `zero_copy_batch=False` it is 20x slower (1.9 s to 38.6 s), because it skips the chunk combining that #62579 added; asked maintainers to decide the scope. No maintainer has replied yet.

#### [#67527](https://github.com/ray-project/ray/issues/67527) [Data] Batcher copies the whole multi-chunk input block into worker memory before the first batch (open)
- Found that the batcher copies each multi-chunk input block out of the object store into worker memory before the first batch. An earlier fix (#62295) was closed as stale, and a maintainer's change (#62579) went the other way for speed.
- Measured both sides: the earlier fix is 20x slower with 5000 chunks (16.2 vs 0.79 s). Proposed splitting by chunk instead, which cut peak memory from 391 to 14 MiB and was as fast or faster in most cases.

#### [#68089](https://github.com/ray-project/ray/issues/68089) [Data] write_json fails on date columns with pandas 3 (open)
- Found while reviewing #68007: with pandas 3, `write_json` fails on any `date32` or `date64` column with the default arguments, so `read_parquet` or `read_csv` followed by `write_json` breaks. The cause is in pandas (`to_json` calls `.dt.as_unit` on Arrow date columns, which only timestamps and durations support); Ray hits it because `to_pandas` returns Arrow-backed columns by default since 2.56, and Ray Data CI only runs pandas 2.3.3.
- Linked a working fix on a fork branch and asked whether it should live in Ray or pandas. Across 660 compared cases it writes the same output on pandas 3 as pandas 2.3.3 does, and dates after 2262, which overflow on pandas 2.3.3, now work too.

## Reviewed PRs

#### [#66931](https://github.com/ray-project/ray/pull/66931) [data] Add Top-N execution (by @viiccwen, merged)
- Default-off Top-N path for `sort(...).limit(k)`. The PR had timing only up to 5M rows, so I benchmarked memory and swept k, input size, and block count ([comment](https://github.com/ray-project/ray/pull/66931#issuecomment-6289194854)).
- Found that with default configs, k=10000 on 256 blocks raised `ValueError` instead of falling back, and that the 5.8x speedup in the description mostly came from worker startup.
- Re-measured after the maintainer's rework ([follow-up](https://github.com/ray-project/ray/pull/66931#issuecomment-6462840086)): the error is gone, Top-N is 4–8x faster with 128+ blocks, and at 100M rows it takes 3.45 s vs 21.37 s and 0.5 vs 16.3 GiB of worker memory, though its object store peak rose from 6.2 to 9.0 GiB.

#### [#67154](https://github.com/ray-project/ray/pull/67154) [Data] Make `zero_copy_batch=False` copy default batches (by @viiccwen)
- Fixes `map_batches(zero_copy_batch=False)` passing a read-only batch when `batch_size=None`.
- Answered @Yin-1022's double-copy question with measurements: the extra Arrow copy doubles peak memory (501 vs 245 MiB on a 244 MiB block). Showed it already exists on master's integer `batch_size` path and proposed a format-aware follow-up issue ([comment](https://github.com/ray-project/ray/pull/67154#discussion_r4321292360)).

#### [#66700](https://github.com/ray-project/ray/pull/66700) fix(data): bound output block stats retention in streaming execution (by @SIDDARTHAREDDY8)
- Stops operators from keeping one stats object per output block on the driver for the whole run ([#66016](https://github.com/ray-project/ray/issues/66016)). Measured it against its parent commit: with 40,000 blocks, driver Python growth went from 36.4 to 4.8 MiB, and `ds.stats()` output was unchanged.
- Found that the PR's new test fails on the current head, that it breaks an existing shuffle stats test, and that the rebase must carry over #66694's wall time fix ([review](https://github.com/ray-project/ray/pull/66700#pullrequestreview-5623969650)). The author fixed both tests the same day, the second one with the approach I suggested.

#### [#67267](https://github.com/ray-project/ray/pull/67267) [Data] Proactively scale down actor pools for backpressured operators to reduce resource waste (by @dragongu)
- Opt-in policy that shrinks an actor pool whose output a slow consumer is not reading. The PR had no workload numbers, so I measured it with a slow `iter_batches` consumer that later speeds up.
- Over a 180 s slow phase, mean alive actors dropped from 4.09 to 1.7-1.9 with no visible cost once the consumer sped up. Found that the policy reads a smaller pool as recovery and grows it back, so reaching 1 actor took 10-51 s ([review](https://github.com/ray-project/ray/pull/67267#pullrequestreview-5666028478)). The author fixed the log I flagged; re-checked on the new head, the pool still grew back from 4 to 5 actors while the consumer stayed slow ([second review](https://github.com/ray-project/ray/pull/67267#pullrequestreview-5888063117)).

#### [#67528](https://github.com/ray-project/ray/pull/67528) [Data] Avoid redundant scalar block copies before pandas conversion (by @Himanshu658820)
- Skips the protective block copy for scalar columns before pandas conversion, the pandas case from my series issue #67459.
- Checked a bot report that this lets a UDF modify the source block: it did not hold on pandas 2 or 3. The 20x many-chunk slowdown I had reported is gone. A batch covering a 128 MiB block now needs 0 instead of 128 MiB extra; smaller batches save about one batch, since the batcher still combines the block first (#67527) ([review](https://github.com/ray-project/ray/pull/67528#pullrequestreview-5712674470)).

#### [#67579](https://github.com/ray-project/ray/pull/67579) [Data] Keep tensor columns with null rows mergeable across blocks (by @marwan116, merged)
- Lets a batch merge blocks where one column is a tensor in some blocks and null or a Python object in others, by turning the column into pickled objects. The author asked reviewers to confirm that choice.
- Measured what it costs: one null block makes a 294 MiB image batch take about 0.5 s to merge and convert instead of 0.025 s, and batches alternate between float32 and object. `iter_torch_batches` still failed on them with a misleading "ragged tensor" hint. The author changed the design based on these numbers: the column now stays a tensor with null rows, at no extra cost, and the torch error names the null rows ([review](https://github.com/ray-project/ray/pull/67579#pullrequestreview-5714345472)). The author then requested my review; I approved after confirming the cost was gone and that null rows survive eight read paths that previously failed ([approval](https://github.com/ray-project/ray/pull/67579#pullrequestreview-5715631855)).
- Pointed out that the null-row support overlaps two open PRs and an issue: the repro from #67200 passes on the PR and fails on master ([comment](https://github.com/ray-project/ray/pull/67579#issuecomment-6528517815)). The author linked the PR to #67200, explained how it relates to the other two, and fixed one more null-row case found while comparing with #67236. After discussion with another maintainer the PR was narrowed to null rows only; mixing tensors with pickled objects still raises.
- Re-checked the author's fix for two bugs another maintainer found: the slice bug is fixed, but native-format null rows still fail on pyarrow 26 (a regression from master) because the fix relies on a conversion that returns zeros on pyarrow 23–25 and raises on 26; CI pins 23, so it passes there ([review](https://github.com/ray-project/ray/pull/67579#pullrequestreview-5746465923)). The PR was merged without that change.

#### [#67672](https://github.com/ray-project/ray/pull/67672) [Data] Avoid whole-block copies when batching multi-chunk Arrow inputs (by @OneSizeFitsQuorum)
- Implements the fix proposed in my issue #67527: batches read only the rows they need instead of first copying the whole multi-chunk input block.
- Confirmed the whole-input copy is gone end to end (per-task extra memory 118/64/64 MiB to 1/0/0 MiB) with batches identical to master on 180 edge cases. Found the one slower layout, wide tables with misaligned chunks and NumPy batches (0.71 to 1.00 s), and showed the author's large-batch slowdown figure holds under Arrow's default allocator ([comment](https://github.com/ray-project/ray/pull/67672#issuecomment-6570667995)). The author added these results to the PR description and cited the comparison with the simpler split as evidence for keeping the per-column design.

#### [#66224](https://github.com/ray-project/ray/pull/66224) [Data] Fix from_pandas block splits on pandas 3 (by @RainieLLM)
- Fixes `from_pandas(override_num_blocks=...)` on pandas 3 by splitting with contiguous row slices instead of `np.array_split`.
- Measured the two split styles under review: the maintainer's index-array version adds a full extra copy (driver peak 824 to 1630 MiB for a 1 GiB frame, 1.25 to 2.0 s at 200 blocks) with identical output on 56 edge cases. Found the one case where slices are slower (tensor casting off: 1.7 vs 0.7 s) and that the PR also halves the peak on pandas 2 ([comment](https://github.com/ray-project/ray/pull/66224#discussion_r4439661641)).

#### [#67566](https://github.com/ray-project/ray/pull/67566) [Data] Keep every element of nested-null columns in Arrow-backed to_pandas (by @marwan116, merged)

- Found that master's failure is worse than the dropped elements the PR describes: when a pandas UDF gets a slice of a `list<null>` block, Arrow aborts the worker and Ray retries the task forever, so the job never finishes. Confirmed the PR fixes this and every other nested-null input tested, on both supported pandas majors, and traced a remaining `read_json` failure to an invalid array from pyarrow's JSON reader ([comment](https://github.com/ray-project/ray/pull/67566#issuecomment-6582462997)). The author added the sliced case as a regression test before merge.

#### [#64740](https://github.com/ray-project/ray/pull/64740) [Data] Fix normalize_block_types picking the count instead of the block type (by @vineethsaivs)
- Fixes a swapped tuple unpack that made every mixed Arrow/pandas input fall back to pandas instead of the majority block type.
- Confirmed the new test catches the bug, then traced which callers actually reach it: default `groupby` never does, and mixed-type `sort` crashes earlier in sampling on master. The only reachable caller is `zip`, whose output now follows the left dataset (pandas to Arrow), a user-visible change missing from the description. Also pinned the failing lint step to one blank line ([review](https://github.com/ray-project/ray/pull/64740#pullrequestreview-5740214128)). The author documented and tested the tie case, fixed the lint, and added the `zip` change to the description.

#### [#67640](https://github.com/ray-project/ray/pull/67640) [Data] Estimate a Parquet row group's decoded Arrow bytes from its footer [2/5] (by @AarryaSaraf, closed; continued in #67837)
- Replaces the fixed "uncompressed size x5" guess for a Parquet row group's in-memory size with one computed from the file footer.
- Checked it on TPC-H lineitem and four harder layouts written by PyArrow, DuckDB and Polars: exact with size statistics and within about 2% without them, where x5 was up to 2x over or 50x under. Found that when a dictionary page header can't be read, the estimate jumps to thousands of times the real size, and the caller only logs that at debug level ([review](https://github.com/ray-project/ray/pull/67640#pullrequestreview-5746114289)).

#### [#67650](https://github.com/ray-project/ray/pull/67650) [Data] Estimate PyArrow's peak memory decoding a Parquet row group from its footer [5/5] (by @AarryaSaraf, closed; continued in #67839)
- Estimates how much memory PyArrow needs while decoding a Parquet row group, so read tasks can be sized before they run out of memory.
- Found that the PR measured one row group read on its own, while Ray's reader reads 8 batches ahead: a 4-row-group file read with Ray's settings peaked at 2.2-3.4x the estimate. Confirmed newer PyArrow does not underestimate on the PR's own cases ([review](https://github.com/ray-project/ray/pull/67650#pullrequestreview-5779799669)). Re-checked on the restacked #67839: per-task summing now covers readahead ([comment](https://github.com/ray-project/ray/pull/67839#issuecomment-6701200387)). My second point, that many similar columns decoded on several threads go under the estimate, does not apply to the default read task, which pins PyArrow to one CPU thread; the author made the estimate follow that setting, and on re-running at 1 and 12 threads the lowest ratio was 1.05x.

#### [#67590](https://github.com/ray-project/ray/pull/67590) [Data] Add `condition` to `Dataset.join` and extract equi-join keys from it (by @400Ping, merged)
- Lets `join()` take a condition and turns each cross-side equality into an extra hash key, instead of filtering after the join.
- Confirmed the ~25x claim the PR cited but did not measure: in TPC-H q5's shape at sf1, the last join emits 184,082 rows on one key and 7,243 with the nation key added (25.4x). Checked null keys across all six join types and both shuffle paths against a SQL `ON` reference (all identical), and found the docs say the left column is kept, which is wrong for `right_outer` ([review](https://github.com/ray-project/ray/pull/67590#pullrequestreview-5816483726)). After the rework kept both `condition` columns, re-ran the checks and found the same `right_outer` error in the rewritten docs; the maintainer made that fix the condition for merging, and it merged with it ([second review](https://github.com/ray-project/ray/pull/67590#pullrequestreview-5887855282)).

#### [#67891](https://github.com/ray-project/ray/pull/67891) [Data] Fix std() on integer columns with values beyond 2**53 (by @wguo7)
- Stops `std()` from raising `ArrowInvalid` on integer columns above 2**53, such as nanosecond timestamps.
- Found that those timestamps now return a silently wrong value instead of an error: 8 rows gave 1.42e18 against a true 2.45e9, because each block's int64 sum overflows and `combine` uses the wrong block means. The PR's equal-size test blocks cancel the error out. The author adopted my one-line fix with a test across five shuffle strategies ([comment](https://github.com/ray-project/ray/pull/67891#issuecomment-6736298885)).

#### [#67896](https://github.com/ray-project/ray/pull/67896) [Data] Fix WebDataset expand_json key collision, 1-element list unwrapping, and .jpeg decoding (by @deep1blue)
- Fixes four crashes and wrong types in `read_webdataset`, mostly around `expand_json`.
- Confirmed each fix against the merge base (exactly the four new tests fail there), traced the only CI failure to a missing blank line in the new test, and found that on a key collision the JSON value is now silently dropped, so I suggested documenting it. Also checked archives where a whole read chunk has no `.json` ([review](https://github.com/ray-project/ray/pull/67896#pullrequestreview-5829263011)). The author adopted both points within an hour; I re-ran the tests on the new commit and approved.

#### [#67999](https://github.com/ray-project/ray/pull/67999) [cherry-pick][2.60.1][Data] Keep tensor columns with null rows mergeable across blocks (#67579) (by @sai-miduthuri, merged)
- Cherry-picks #67579 into the 2.60.1 patch release.
- Found that it leaves out my follow-up fix #67806, so on pyarrow 26, which a fresh `pip install "ray[data]"` gets, two native-tensor null-row cases that work in 2.60.0 would start raising `ArrowInvalid`. Showed that #67806 picks cleanly on top and fixes them, and asked to ship both together ([review](https://github.com/ray-project/ray/pull/67999#pullrequestreview-5835879487)). The suggestion was adopted: #67806 was picked into 2.60.1 ([#68019](https://github.com/ray-project/ray/pull/68019)) and 2.59.2 ([#68026](https://github.com/ray-project/ray/pull/68026)), and both merged together with this PR.

#### [#68005](https://github.com/ray-project/ray/pull/68005) [Data] Raise minimum supported pyarrow to 23.0.0 and test up to 26.0.1 (by @goutamvenkat-anyscale, merged)
- Raises Ray Data's pyarrow floor to 23, deletes about 160 version gates and skips, and adds a pyarrow 26 CI lane.
- Found that the new lane is skipped on premerge, so it first runs after merge. Rebuilt its environment locally: the PR and its merge base fail identically, but one sort test fails on pyarrow 26, which I traced to a pyarrow bug with a three-line repro ([review](https://github.com/ray-project/ray/pull/68005#pullrequestreview-5843808043)). It merged without a reply; the failure still reproduced on master, so `sort()` on a native tensor column failed on pyarrow 26. I opened the fix in [#68072](https://github.com/ray-project/ray/pull/68072).

#### [#67955](https://github.com/ray-project/ray/pull/67955) [Data] Fix `SizeEstimator.add_block` sampling indices and 100-row block condition (by @deep1blue)
- Fixes three sampling bugs so `SizeEstimator.add_block` matches calling `add` row by row.
- Confirmed the fix (the new tests fail on the merge base and pass on the PR; 20,000 random block-size sequences match row-by-row sampling), but found that nothing has called `add_block` since simple blocks were removed in #36477 (2023), which I also checked at runtime across five pipeline types. So the fix changes no size estimate today, and I suggested deleting the method instead ([comment](https://github.com/ray-project/ray/pull/67955#issuecomment-6773173061)).

#### [#67988](https://github.com/ray-project/ray/pull/67988) [Data] Retry FooterReader actor deaths with Ray Core and yield footers in listing order (by @ayushk7102)
- Lets Ray Core restart a dead Parquet footer-reader actor and retry its calls, and makes footers come back in listing order so the retry cannot drop or duplicate files.
- Ran the retry end to end through `read_parquet` while killing actors mid-listing: 30 runs with 50 kills read every file exactly once, the merge base fails, and restoring completion order loses files in 5 of 6 runs. Also measured that a file that crashes the reader now restarts forever (13 crashes in 180 s) instead of failing after 19 s, with no setting to cap it ([review](https://github.com/ray-project/ray/pull/67988#pullrequestreview-5854147455)).

#### [#68017](https://github.com/ray-project/ray/pull/68017) [Data] Migrate read_webdataset to DataSource V2 (by @Hyunoh-Yeo)
- Moves `read_webdataset` onto the new DataSource V2 read path, which becomes the default, by copying the V1 tar and decoding helpers.
- Compared the same archives on master, on the open fix #67896, and on V2. The copy misses all of #67896's fixes, and one of them breaks V2 outright: a shard whose JSON lists change from one to two elements after the first 512 samples now fails to read, while master reads it ([review](https://github.com/ray-project/ray/pull/68017#pullrequestreview-5867967056)). The author fixed it the next day; I re-ran the cases, confirmed the shard now reads as a list column, and approved.

#### [#68007](https://github.com/ray-project/ray/pull/68007) [Data] Fix write_json for non-nanosecond timestamps and durations (by @hzmsh)
- On pandas < 3, converts non-nanosecond timestamp and duration columns to the output unit before `to_json()`, so a microsecond timestamp from Parquet is no longer written 1,000x too small (2024 read back as 1970).
- Used pandas 3 as the reference, since the fix follows it: 396 outputs (12 tables, three block types, 11 `to_json` argument sets) on pandas 2.3.3 and 3.0.6, each on the PR and its merge base. On pandas 2.3.3, 96 outputs go from wrong to matching pandas 3 and none that matched before changes; on pandas 3 the PR changes nothing. Approved ([review](https://github.com/ray-project/ray/pull/68007#pullrequestreview-5870302254)).

#### [#68118](https://github.com/ray-project/ray/pull/68118) [Data] Adaptively skip map-side partial aggregation for high-cardinality keys (by @sampan-s-nayak)
- Lets each `SHUFFLE_V2` map task skip partial aggregation when its group keys are nearly unique, sending raw rows that the reducer merges with other tasks' partial results. On by default.
- The PR's benchmark only measured speed, so I checked the mixed raw/partial reduce for correctness: every mix matches across 29 input shapes, and end-to-end output equals master. Also showed that the dictionary-key failure a bot flagged already happens on master. Approved ([review](https://github.com/ray-project/ray/pull/68118#pullrequestreview-5901798942)).

#### [#68083](https://github.com/ray-project/ray/pull/68083) [Data] Handle null list slots that span child values in list.sort and list.flatten (by @dundysm)
- Fixes `list.sort()` raising and `list.flatten()` silently shifting rows when a null list row still has child values underneath (e.g. a list field taken from a null struct row).
- Checked 32 edge-case inputs (slices around the null row, chunked columns, `large_list`, `fixed_size_list`, `list_view`, nesting) on the PR and its merge base: all correct on the PR, 19 wrong or raising before. Found that the fix also covers a null inner list, which crashes flatten on master, but no test reaches that line; suggested a test that fails without it ([review](https://github.com/ray-project/ray/pull/68083#pullrequestreview-5903435988)).

## Issue investigations

#### [#65544](https://github.com/ray-project/ray/issues/65544) Streaming Parquet read holds 2-5x the dataset's size as RSS (by @satyarao-epi)
- Found where the extra memory goes with memray and in-worker sampling ([comment](https://github.com/ray-project/ray/issues/65544#issuecomment-6423037499)). Part of the gap is measurement: summed RSS counts object store pages once per process, and the pyarrow baseline itself grows to about the file size.
- Traced the rest to the read worker, which holds the pre-buffered raw row groups and the decoded block at once: 265 MiB per task against the 127.7 MiB Ray budgets. Turning off `pre_buffer` cut it to 145 MiB.
