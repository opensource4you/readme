**Reporting Period: 2026-10-14 to 2026-11-15**

## Total
- 8 Issue/PR Contributions
    (7 issues opened, 6 PRs submitted, including 1 based on an existing issue)
    - 4 PRs/issues merged
    - 2 PRs/issues under review
    - 1 issue awaiting follow-up
    - 1 issue on hold
    ---
    - **<u>1. [Data] Reject duplicate synthesized column names #67164**</u>  
    Synthesized columns in Parquet DataSourceV2 could silently overwrite one another when a generated checkpoint ID collides with `path` or `row_hash`.

        Following PR #67157's move to the plan-time `BindGeneratedIdColumn` rule, I reworked #67164 to distinguish physical Parquet-column conflicts from synthesized-column conflicts, providing more accurate error messages and regression coverage.

        PR: https://github.com/ray-project/ray/pull/67164  
        Issue: https://github.com/ray-project/ray/issues/67002  
        Status: Merged

    ---

    - **<u>2. [Data] Avoid counting terminal waits as output backpressure #67374</u>**  
    Identified a pre-existing metrics accounting issue where exhausted tasks waiting only for terminal confirmation were incorrectly counted as experiencing output backpressure.  

        I opened PR #67374 to distinguish genuine unread-output backpressure from terminal-only waits, with regression tests covering empty tasks, unread output, and pending output after stream exhaustion.  
        PR: https://github.com/ray-project/ray/pull/67374  
        Issue: https://github.com/ray-project/ray/issues/67312  
        Status: Merged

    ---

    - **<u>3. [Data] Dataset.mix does not consistently reject non-finite weights #67387</u>**
    Identified inconsistent validation of non-finite weights in `Dataset.mix()`, where `NaN` and `+inf` are accepted while `-inf` is rejected as a positivity error.

        I reproduced the behavior through the public API, traced the validation through both logical and physical Mix operators, and proposed requiring weights to be both finite and positive.

        Issue: https://github.com/ray-project/ray/issues/67387  
        Status: Held

    ---

    - **<u>4. [Data] Dataset.join(validate_schemas=True) mishandles asymmetric and multi-key joins #67415</u>**  
    Identified two correctness issues in join schema validation: asymmetric keys are checked against the wrong schemas, while multi-key types are collected in schema order rather than configured key order.

        I reproduced both behaviors on master and opened PR #67505 to validate each side against its configured keys, preserve positional key order, and add regression tests for asymmetric and reordered multi-key joins.

        Issue: https://github.com/ray-project/ray/issues/67415  
        PR: https://github.com/ray-project/ray/pull/67505  
        Status: Merged

    ---

    - **<u>5. [Data] repartition() warns that shuffle is ignored before rejecting the same argument #67604</u>**  
    Identified a public API contract inconsistency where `Dataset.repartition()` warns that `shuffle=True` will be ignored before rejecting the same argument combination with a `ValueError`.

        I opened PR #68062 to remove the contradictory warning while preserving existing validation behavior, and added a cluster-independent regression test with successful Red → Green verification.

        Issue: https://github.com/ray-project/ray/issues/67604  
        PR: https://github.com/ray-project/ray/pull/68062  
        Status: Pr waiting to be reviewed

    ---

    - **<u>6. [Data] Redundant schema unification overhead when concatenating Arrow tables with identical schemas #67814</u>**  
    Identified redundant schema unification in `transform_pyarrow.concat()`, accounting for approximately 71% of concatenation time with identical Arrow schemas.

        I opened PR #67935 to introduce an identical-schema fast path while preserving schema reconciliation. Following maintainer feedback, I refined the implementation using `Schema.equals(check_metadata=False)`, with additional benchmarks confirming a **2.87× direct concatenation speedup** and no consistent end-to-end Dataset speedup.

        Issue: https://github.com/ray-project/ray/issues/67814  
        PR: https://github.com/ray-project/ray/pull/67935  
        Status: Merged

    ---

    - **<u>7. [Data] Disk Shuffle V2 leaks detached ShuffleFileServer actors and temporary directories on mid-map cancellation #67958</u>**  
    Identified a resource lifecycle issue where cancelling a shuffle map task before returning its `ShuffleFileServer` handle can leave detached actors and temporary directories untracked by cleanup.

        I developed a controlled cancellation reproducer on Ray 3.0.0.dev0, confirming 3 leaked actors and 3 temporary directories across three interruptions. I traced the likely cause to cleanup relying on completed map handles and proposed lifecycle-aware cleanup with regression coverage.

        Issue: https://github.com/ray-project/ray/issues/67958  
        Status: Waiting for a prior PR to be merged

    ---

    - **<u>8. [Data] Metadata prefetch thread failures are not propagated to the streaming executor #68093</u>**  
    Identified a failure propagation issue where an unhandled exception in `ThreadedMetadataFetcher` terminates the background thread without notifying the streaming executor, leaving deferred metadata and task completion pending.

        I reproduced the issue using cluster-independent fault injection and implemented PR #68171 to propagate unexpected thread failures while preserving the original exception via `__cause__`. Added regression coverage for repeated executor polling and verified the fix against the original reproducer, with 8 threaded executor tests passing.

        Issue: https://github.com/ray-project/ray/issues/68093  
        PR: https://github.com/ray-project/ray/pull/68171  
        Status: PR opened, awaiting maintainer review

- 27 Review
    - **<u>1. [Serve] Surface terminal deployment actor failures outside UPDATING #66910</u>**  
    This PR improves Ray Serve deployment status handling by surfacing terminal deployment actor failures across more deployment states instead of leaving deployments indefinitely marked as retrying.  

        I identified a recovery edge case where, after recreation retries are exhausted, redeploying the exact same deployment configuration returns no change and does not reset the exhausted retry state, leaving the deployment unable to recover unless its configuration or version changes.  
        PR: https://github.com/ray-project/ray/pull/66910  
        Review: https://github.com/ray-project/ray/pull/66910#discussion_r4267141271

    ---

    - **<u>2. [Data] Make zero_copy_batch=False copy default batches #67154</u>**  
    This PR fixes writable batch behavior when `zero_copy_batch=False` and the default `batch_size=None` is used.  
        
        I traced and locally verified that the NumPy path performs two full-batch copies, while the second copy alone is sufficient. I raised the performance concern and suggested handling the broader optimization as a format-aware follow-up rather than blocking this PR.   
        PR: https://github.com/ray-project/ray/pull/67154  
        Review: https://github.com/ray-project/ray/pull/67154#discussion_r4320981519

    ---

    - **<u>3. [Data] Deflake test_train_test_split by preserving block order #67161</u>**  
    This PR stabilizes `train_test_split` tests by enabling deterministic block ordering with `preserve_order=True`.  

        I raised a concern that forcing ordered execution may cause the test to exercise behavior different from the default `preserve_order=False` setting, and questioned whether the flake instead exposes ambiguity in the ordering contract of `train_test_split(shuffle=False)`, and that ended up becoming a separate follow-up issue.  
        PR: https://github.com/ray-project/ray/pull/67161  
        Review: https://github.com/ray-project/ray/pull/67161#discussion_r4322072170

    ---

    - **<u>4. [Data] Finalize exhausted tasks without output-read budget #67180</u>**  
    This PR fixes a Ray Data execution issue where exhausted tasks could remain active when the output-read budget was zero, delaying task finalization and resource release.  

        I tested terminal completion through the real executor, confirming the expected ACTIVE → DRAINED → FINISHED lifecycle and correct ordering of deferred final outputs and terminal errors. During the review, I also discovered a separate output-backpressure accounting issue, leading to issue #67312 and PR #67374.  
        PR: https://github.com/ray-project/ray/pull/67180  
        Review: https://github.com/ray-project/ray/pull/67180#issuecomment-6355775805

    ---

    - **<u>5. [Data] Account for HashShuffle aggregator GPU usage in logical resource accounting #67382</u>**  
    This PR updates HashShuffle resource accounting so aggregator GPUs configured through aggregator_ray_remote_args are reflected in operator and global logical GPU usage instead of being hardcoded to zero.  

        I raised a non-blocking question about GPU accounting after aggregator shutdown, since the existing GPU Shuffle implementation reports zero usage after its actor pool shuts down. I noted that the proposed behavior is consistent with HashShuffle's CPU/memory accounting and asked which post-shutdown semantics should be preserved.

        PR: https://github.com/ray-project/ray/pull/67382  
        Review: https://github.com/ray-project/ray/pull/67382#issuecomment-6394644010
    
    
    ---

    - **<u>6. [Data] Fix SQL sharding with null keys #67406</u>**  
    This PR fixes silent row loss in ray.data.read_sql() when shard-key values are NULL, routing rows containing NULL shard keys to task 0 while preserving hash-based sharding for non-null rows.

        I independently tested multi-key sharding with NULL values in either or both keys, confirming that every row was preserved exactly once. I also suggested adding direct multi-key regression coverage, since the existing test only covers a single shard key.

        PR: https://github.com/ray-project/ray/pull/67406  
        Review: https://github.com/ray-project/ray/pull/67406#issuecomment-6416646511
    
    ---

    - **<u>7. [Data] Preserve Pandas extension dtypes for all-null columns #67373</u>**  
    This PR changes PandasBlockAccessor.to_arrow() so explicitly typed Pandas extension columns retain their concrete Arrow type when every value is null, instead of being coerced to Arrow null.

        I locally tested nullable numeric, categorical, timezone-aware datetime, and period dtypes, and verified a mixed-block `map_batches()` case with all-null and non-null Int64 blocks. The resulting Dataset preserved a consistent int64 schema, supporting the broader extension-dtype handling.

        PR: https://github.com/ray-project/ray/pull/67373  
        Review: https://github.com/ray-project/ray/pull/67373#issuecomment-6416938166

    ---

    - **<u>8. [Data][LLM] Fix max_pending_requests<=0 crash and empty prompt in vLLM error rows #67460</u>**  
    This PR fixes Ray Data LLM inference failures when request limiting is disabled and prevents the original prompt from being lost in error rows after request preparation mutates the input.  

        I verified the async nullcontext() path and locally exercised an error occurring after the prompt had already been removed from the input row, confirming that the original prompt is still preserved correctly.  

        PR: https://github.com/ray-project/ray/pull/67460  
        Review: https://github.com/ray-project/ray/pull/67460#issuecomment-6446564315

    ---

    - **<u>9. [Core] Deflake _run_hanging_command tests #67461</u>**  
    This PR replaces a log-based subprocess readiness check in Ray Jobs tests with an atomically published PID file to remove a startup race.

        I traced the entrypoint launch sequence and confirmed that the old "Waiting..." check could succeed before the shell actually started, while the new PID-file signal exposes a complete PID belonging to a live process and remains compatible with the affected callers.

        PR: https://github.com/ray-project/ray/pull/67461  
        Review: https://github.com/ray-project/ray/pull/67461#issuecomment-6447258630

    ---

    - **<u>10. [Serve] Don't busy-loop the replica drain when graceful_shutdown_wait_loop_s=0 #67434</u>**  
    This PR prevents Ray Serve replica shutdown from busy-looping at graceful_shutdown_wait_loop_s=0 by introducing a 10ms minimum polling interval and rate-limiting drain logs.

        I verified that immediate HAProxy checks and minimum draining-period semantics remain intact, and identified a non-blocking edge case where very small graceful_shutdown_timeout_s values can expire before the next 10ms polling check observes request completion.  

        PR: https://github.com/ray-project/ray/pull/67434  
        Review: https://github.com/ray-project/ray/pull/67434#issuecomment-6449026658

    ---

    - **<u>11. [Data] build row-group sub-fragments lazily so footer reads run in the scan threads #67469</u>**  
    This PR replaces eager fragment.subset() construction with make_fragment() for grouped Parquet row groups, allowing footer reads to be deferred until the parallel scan stage.

        I verified that non-contiguous row-group selection, partition expressions, and scan results remain equivalent, and confirmed that fragment construction no longer accesses the file until scanning begins.  

        PR: https://github.com/ray-project/ray/pull/67469  
        Review: https://github.com/ray-project/ray/pull/67469#issuecomment-6476275533

    ---

    - **<u>12. [Data] Avoid per-feature Series concat and float upcasting in Vectorizers and zero-norm NaNs in Normalizer #67492</u>**  
    This PR improves HashingVectorizer and CountVectorizer by avoiding intermediate Pandas concatenations and fixes Normalizer so all-zero rows no longer produce NaNs.

        I verified the behavior through the public transform_batch() path, covering all three normalization modes, append mode, multiple vectorized columns, integer count preservation, and missing values represented by None, np.nan, and pd.NA. The updated behavior matched the intended semantics.  

        PR: https://github.com/ray-project/ray/pull/67492  
        Review: https://github.com/ray-project/ray/pull/67492#issuecomment-6485554264

    ---

    - **<u>13. [Data] Fix train_test_split sizes lost to float truncation and stratified counts #67465</u>**  
    This PR fixes floating-point truncation and uses largest-remainder allocation to preserve the requested size of stratified train/test splits.

        I identified and reproduced a remaining correctness issue with NaN stratification keys. Because normalized NaN values still do not compare equal, the precomputed allocation can be missed and fall back to a floored per-group count, causing train_test_split() to return fewer test rows than requested.  

        PR: https://github.com/ray-project/ray/pull/67465  
        Review: https://github.com/ray-project/ray/pull/67465#issuecomment-6486452102

    ---

    - **<u>14. [Data] Cache PyArrow vocabulary arrays and inverse label mapping in encoders #67524</u>**  
    This PR caches encoder lookup structures to avoid rebuilding PyArrow vocabulary arrays and inverse label mappings on every batch.

        I identified and reproduced a stale-cache correctness issue in OrdinalEncoder, OneHotEncoder, and LabelEncoder. The cache only checks dictionary identity and length, so same-size in-place changes to stats_ are not detected, causing stale ordinal mappings, zeroed one-hot outputs for updated categories, and outdated inverse labels.  

        PR: https://github.com/ray-project/ray/pull/67524  
        Review: https://github.com/ray-project/ray/pull/67524#issuecomment-6486585464

    ---

    - **<u>15. [Data] Keep pandas metadata on Arrow blocks parsable by pandas #67576</u>**  
    This PR rewrites Pandas metadata dtype names that Pandas cannot parse, allowing Arrow-backed columns to convert back to Pandas without failing while preserving already valid metadata.

        I verified the behavior beyond the original list-column case using Arrow-backed struct, map, and dictionary columns, all of which round-tripped successfully with their values preserved. I also checked nullable integers, booleans, strings, categoricals, and timezone-aware datetimes as controls, and validated both the public map_batches(...).to_pandas() path and write_parquet() → pd.read_parquet() interoperability.  

        PR: https://github.com/ray-project/ray/pull/67576  
        Review: https://github.com/ray-project/ray/pull/67576#issuecomment-6525885326

    ---

    - **<u>16. [Data] Skip the S3 HEAD request on each Parquet file open by passing listed file sizes #67645</u>**  
     This PR reduces unnecessary S3 requests by reusing listed file sizes when opening Parquet files through PyArrow, bypassing `RetryingPyFileSystem` for sized S3 opens.  

        I identified and reproduced a retry regression in schema inference, where unwrapping `RetryingPyFileSystem` without `call_with_retry()` bypasses configured Ray-level retries. A fail-once filesystem confirmed that master recovered on the second attempt, while the PR immediately propagated the first failure.  

        PR: https://github.com/ray-project/ray/pull/67645  
        Review: https://github.com/ray-project/ray/pull/67645#issuecomment-6544259452

    ---

    - **<u>17. [Data] Add fill_null expression method #67661</u>**  
    This PR introduces `fill_null()` for Ray Data expressions, including support for PyArrow evaluation and predicate pushdown.
  
        I validated the implementation across Parquet and in-memory Arrow execution paths, covering floating-point precision, type promotion, all-null columns, and comparison/arithmetic predicates. I also compared PyArrow Dataset scanner filtering against in-memory expression evaluation with independently specified expected results, including dictionary-encoded strings, decimal128, and timestamp columns. All 25 test cases passed, with no correctness inconsistencies reproduced.  

        PR: https://github.com/ray-project/ray/pull/67661  
        Review: https://github.com/ray-project/ray/pull/67661#issuecomment-6593229531

    ---

    - **<u>18. [Data] Push AND conjuncts on passthrough columns below a Project #67656</u>**  
    This PR improves predicate pushdown by moving eligible AND conjuncts below projection operators while retaining predicates that depend on computed columns.

        I verified the optimization across Parquet and in-memory Arrow paths, testing column overwrites, multi-stage renaming, nested predicates, and null preservation. I also confirmed that non-idempotent projections using `monotonically_increasing_id()` prevent unsafe predicate movement while deterministic projections remain eligible for optimization. 

        PR: https://github.com/ray-project/ray/pull/67656  
        Review: https://github.com/ray-project/ray/pull/67656#issuecomment-6596330457

    ---

    - **<u>19. [Data] Add a shared byte-range line chunker for CSV and JSONL on DataSource V2 #67725</u>**  
    This PR introduces shared byte-range chunking for CSV and JSONL files in Ray DataSource V2, allowing large files to be divided into independent read units.  

        I validated 72 reconstruction cases and four bin-packing scenarios, then identified a batching issue where `LineChunkFileIndexer` can exceed `max_paths_per_output`. A minimal reproduction produced a 10-row manifest despite a configured limit of 3, and I reported the finding with reproducible code.  

        PR: https://github.com/ray-project/ray/pull/67725  
        Review: https://github.com/ray-project/ray/pull/67725#issuecomment-6596711584

    ---

    - **20. <u>[Data] Vectorize LeRobot delta segment preparation and validate index contiguity #67845</u>**  
    This PR optimizes LeRobot's temporal-window processing by replacing per-row Python operations with vectorized NumPy and PyArrow operations.

        I independently tested 24 cases covering episode IDs, index contiguity, boundaries, and anchor selections. I also reproduced a behavioral difference with duplicate episode IDs: the original dictionary lookup selects the last occurrence, while `pc.index_in()` selects the first, and raised a question about upstream uniqueness guarantees.  

        PR: https://github.com/ray-project/ray/pull/67845  
        Review: https://github.com/ray-project/ray/pull/67845#issuecomment-6648268333

    ---

    - **21. <u>[Data] Fix std() on integer columns with values beyond 2\*\*53 #67891</u>**  
    This PR fixes an `ArrowInvalid` exception in Ray Data's standard deviation aggregation by allowing large integer columns to be cast to Float64 before computing squared differences.

        I independently tested six large-integer edge cases, including adjacent integers, negative values, and uint64 boundary values. Direct accessor-level testing revealed significant precision loss, including zero variance for distinct integers, and exposed a potential overflow issue in the existing integer-sum-based mean calculation. I prepared a minimal reproduction and review feedback to clarify the intended numerical accuracy guarantees.

        PR: https://github.com/ray-project/ray/pull/67891  
        Review: https://github.com/ray-project/ray/pull/67891#issuecomment-6702250542

    ---

    - **22. <u>[Data] Fix WebDataset expand_json key collision, 1-element list unwrapping, and .jpeg decoding #67896</u>**  
    This PR addresses multiple correctness issues in Ray Data's WebDataset datasource, including JSON key collisions, missing JSON fields, unintended single-element list unwrapping, and image extension handling.  

        I independently tested JSON expansion with nested structures, missing fields, key collisions, and mixed data types. I also verified list preservation and Arrow conversion for custom decoder outputs, along with 10 image encoding/decoding cases across different extensions and capitalization. The tested scenarios behaved as expected.  

        PR: https://github.com/ray-project/ray/pull/67896  
        Review: https://github.com/ray-project/ray/pull/67896#issuecomment-6708105669

    ---

    - **23. <u>[Data] Fix Concatenator missing-column handling when raise_if_missing=False #67912</u>**  
    This PR fixes a correctness issue in Ray Data's `Concatenator` preprocessor, where missing input columns could trigger a `KeyError` despite `raise_if_missing=False`.  

        I independently reviewed and tested the updated implementation, covering partial and complete missing-column scenarios, `flatten=True`, non-default Pandas indices, and empty DataFrames. I also validated end-to-end Ray Dataset execution, including Arrow Tensor schema generation, `take_all()`, `to_pandas()`, and repartitioning.  

        PR: https://github.com/ray-project/ray/pull/67912  
        Review: https://github.com/ray-project/ray/pull/67912#issuecomment-6708442794

    ---

    - **24. <u>[Data] Fix TableBlockBuilder compaction row/size accounting and interleaved add_block ordering #67949</u>**  
        This PR fixes row/size accounting during compaction and preserves insertion order when interleaving add() and add_block() operations.  

        I independently validated both Arrow and Pandas builders through two rounds of testing, covering interleaved operations, memory-estimation cursor consistency, continued appends after build(), automatic threshold-based compaction, and schema evolution with reordered or missing columns. All tested scenarios passed, preserving row counts, insertion order, and expected values without observable correctness regressions.  

        PR: https://github.com/ray-project/ray/pull/67949  
        Review: https://github.com/ray-project/ray/pull/67949#issuecomment-6724224899

    ---

    - **25. <u>[Data] Add partition_filters to read_delta and take file sizes from the log #67900</u>**  
    This PR introduces partition filtering for Delta Lake reads and optimizes file-size retrieval by using Delta Log metadata instead of per-file filesystem lookups.  

        I independently reviewed and benchmarked metadata processing across 1K, 5K, and 20K active files, finding no consistent benefit from disabling metadata flattening. I also validated 13 partition values covering special characters, Unicode, and multiple URL-encoding levels, confirming correct file-size mappings without path collisions or mismatches.  

        PR: https://github.com/ray-project/ray/pull/67900  
        Review: https://github.com/ray-project/ray/pull/67900#issuecomment-6744803123

    ---

    - **26. <u>[Data] (Data fault tolerance) Stop a lineage re-trace at a pending reconstruction task #68094</u>**  
    This PR improves Ray Data lineage reconstruction by stopping redundant upstream tracing when a queued or running reconstruction task can already reproduce the required outputs.  

        I independently reviewed the reconstruction logic and passed 72 existing lineage tracker tests, along with 4 adversarial cases covering overlapping reconstruction attempts, different failure orders, and independent reconstruction plans. I also tested late-added child dependencies during streaming reconstruction, confirming that their release callbacks are deferred until task completion.  

        PR: https://github.com/ray-project/ray/pull/68094  
        Review: https://github.com/ray-project/ray/pull/68094#issuecomment-6860153557

    ---

    - **27. <u>[Data] Handle null list slots that span child values in list.sort and list.flatten #68083</u>**  
    This PR fixes incorrect parent-index alignment in Ray Data's `list.sort()` and `list.flatten()` when PyArrow null list slots span underlying child values, preventing length mismatches and incorrectly shifted output rows.  

        I independently reviewed the fix using 1,000 randomized differential cases (500 List and 500 LargeList), covering sliced arrays and nested null-list scenarios against an independent Python reference. I also conducted 3 additional UDF-level checks involving chunked list sorting, null struct-field flattening, and chunked nested-list flattening.  

        PR: https://github.com/ray-project/ray/pull/68083  
        Review: https://github.com/ray-project/ray/pull/68083#issuecomment-6882751187  