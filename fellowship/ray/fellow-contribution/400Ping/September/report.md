# Ray Monthly Contribution Report — September 2026

- **Contributor:** Jie-Kai Chang ([@400Ping](https://github.com/400Ping))
- **Period:** 2026-09-01 – 2026-10-11
- **Repository:** [ray-project/ray](https://github.com/ray-project/ray)

## Summary

| Category | Count |
| --- | ---: |
| PRs merged | 1 |
| PRs opened (open / draft) | 2 |
| PRs reviewed | 1 |

Main focus this month was Ray Data: landing task-based shuffle v2 for `sort()`, migrating the CSV datasource to DataSourceV2, and speeding up Ray Data release tests by removing the custom BYOD image build.

## Pull Requests

### Merged

#### [#64875](https://github.com/ray-project/ray/pull/64875) [Data] Add task-based shuffle v2 support for sort
- **Status:** Merged 2026-09-25 (+1102 / −34, 15 files)
- Adds a `SortSamplingOp -> SortShuffleMapOp -> ShuffleReduceOp` plan for `Dataset.sort()` when `DataContext.shuffle_strategy = HASH_SHUFFLE_V2`, reusing the shared shuffle-map machinery instead of the legacy all-to-all sort.
- September work: split sampling out of the shuffle map into its own operator, derived the output partition count from the input block estimate, kept schemas for empty partitions, opted the sampling op out of resource allocation, and aligned v2 with the legacy sort contracts after shuffle v2 became the default.
- Addressed review feedback from @AarryaSaraf (sampling-size claims and partition-count dependence) and @owenowenisme (final approval).
- Local benchmark on a 11.92 GiB sort: ~35% faster, ~24% lower object-store peak, and zero spilling versus v1.

### Open

#### [#65743](https://github.com/ray-project/ray/pull/65743) [Data] Migrate CSV datasource to DatasourceV2 (draft)
- **Status:** Open draft (+3214 / −63, 25 files); closes [#65651](https://github.com/ray-project/ray/issues/65651)
- Migrates `ray.data.read_csv()` to the DataSourceV2 `ListFiles -> ReadFiles` pipeline while preserving the public API and V1 behavior: record-aligned splitting of large uncompressed CSVs (CRLF and quoted fields), dynamic schema discovery, duplicate-column rejection, order preservation under `preserve_order=True`, and compressed/Snappy whole-file fallbacks.
- September work: re-ported the change on top of the maintainers' DSv2 Reorg stack (`FileChunker` removal, `NonSamplingFileIndexer`, `FileDataSourceV2`) and restructured it into a 5-layer PR stack so each piece can be reviewed independently. Wrote a design doc (TL;DR / problem / design options / decisions / implementation plan) for the Ray Data maintainers.
- Maintainer (@AarryaSaraf, 2026-10-01) asked for a rebase on the latest DSv2 refactor before review; rebase is in progress.
- Two orthogonal DataSourceV2 fixes found during this work are prepared as separate follow-up branches: aligning seeded file shuffle with V1 input-order semantics, and keeping files with unknown sizes in the V2 listing.

#### [#66284](https://github.com/ray-project/ray/pull/66284) [Data] Run release-test cluster health checks from benchmark.py to skip custom BYOD image builds (draft)
- **Status:** Opened 2026-09-18, switched to draft 2026-09-26 pending design discussion (+934 / −177, 17 files)
- Any non-empty `cluster.byod.runtime_env` forces every Ray Data release test to wait on a "Custom images build" step. This PR moves the `RAYTEST_*` guards (worker OOM, unexpected worker failure, dead nodes, object-store utilization, debug progress manager) into the `Benchmark` harness so `runtime_env` can be dropped from most test YAMLs.
- Investigated which env vars must stay in the image (Ray core system-config and worker-side module constants) versus which can move to the script line; the cross-AZ failure-injection test keeps the wrapper guards because it does not use `Benchmark`.

## Reviewed PRs

#### [#66878](https://github.com/ray-project/ray/pull/66878) [data] Improve JSON read error context (by @Yin-1022)
- **Status:** Open (opened 2026-10-09)
- Adds the offending file path to `read_json()` parsing errors for both the Arrow and pandas JSON datasources, so failures across many input files are debuggable.
- Reviewed on 2026-10-10 and 10-11 with inline suggestions: include the file path in the `pyarrow.json.read_json()` fallback warning as well; keep the JSONL error message neutral because the `except ValueError` branch also catches non-syntax errors (e.g. integers beyond int64), so "check that the file contains valid line-delimited JSON" can mislead; and pointed out that the new invalid-UTF-8 regression test still fails during chunk-size estimation rather than the streaming read (pandas decodes in 8 KiB chunks), with a concrete fixture change to exercise the intended branch.

#### [#65278](https://github.com/ray-project/ray/pull/65278) [Data] Fix WebDataset docs and encoder typing (by @viiccwen)
- **Status:** Merged 2026-09-17
- Reviewed and approved. Left inline suggestions on using the `WebDatasetEncoderConfig` type in `Dataset.write_webdataset()`, avoiding documenting the internal output-filename scheme, and pointed to existing Ray Data public APIs (`read_parquet`, `read_kafka`) that import types from `_internal` to settle a style question.
- Note: the review itself was submitted 2026-08-20 – 08-22; the PR merged inside this reporting period.

## Other Contributions

- **Design doc:** "Migrate CSV Datasource to DataSourceV2" design document for the Ray Data maintainers, covering the record-aligned splitting approach, schema-discovery trade-offs, and the 5-PR implementation plan for [#65743](https://github.com/ray-project/ray/pull/65743).
- **Release-test infrastructure investigation:** analyzed the Anyscale release-test job wrapper and the `require_custom_byod_image()` path to confirm that `runtime_env` env vars only reach the cluster via the baked image, which is the basis for [#66284](https://github.com/ray-project/ray/pull/66284).