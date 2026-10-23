## Apache Airflow 開源貢獻紀錄 by henry3260（2026-04 ～ 2026-10-15）

- GitHub：[@henry3260](https://github.com/henry3260)
- 專案：[apache/airflow](https://github.com/apache/airflow)
- 日期：2026 年 4 月 ～ 2026 年 10 月 15 日
- Total: PR: 85 merged + 9 open, Code Review: 224 PRs + 2 comment-only (97 inline threads), Merged as committer: 88 (since Jul 2026), Issue Triage: 7, RC Testing: 10, Dev List: 13

---

## Month: October 2026（10/01 ～ 10/15）

Summary: PR: 4 merged, Code Review: 20 (6 inline threads), Merged as committer: 10

### PR

- [Go SDK: vendor the schemas it generates from](https://github.com/apache/airflow/pull/74047) (+6135/−55, ~5.8k is vendored JSON schema): sync script + prek hook + tests
- [Go SDK: order tasks with Before, After and Label](https://github.com/apache/airflow/pull/74119) (+1105/−7): task dependency API for Go Dags
- [Stop rejecting valid non-ASCII Anthropic batches as too large](https://github.com/apache/airflow/pull/74662) (+77/−4): size estimate counted `\uXXXX` escapes, inflating CJK payloads ~2x vs. the real UTF-8 bytes
- [Let LLMBatchOperator request_params be templated](https://github.com/apache/airflow/pull/74751) (+37/−1)
- Also landed this month (opened earlier): [#64523 REST API metrics](https://github.com/apache/airflow/pull/64523), [#72862](https://github.com/apache/airflow/pull/72862), [#73805](https://github.com/apache/airflow/pull/73805), [#73963](https://github.com/apache/airflow/pull/73963)

### Code Review

- [Fix clearing with upstream and downstream selecting unrelated tasks](https://github.com/apache/airflow/pull/74548): found the same bug still exists in the loop-aware clear path; asked to pass mutable sets explicitly; test naming no longer matched behavior (3 inline)
- [Java SDK: Declare a Dag's task graph with @Builder.Deps](https://github.com/apache/airflow/pull/71189): caught that `RESERVED_VIEW_NAMES` rejects `notify`, which the ADR's own example uses
- [Add missing Taiwanese Mandarin translations](https://github.com/apache/airflow/pull/74545): zh-TW wording review
- Java/Go SDK series, technical review to unblock: [#74230](https://github.com/apache/airflow/pull/74230), [#71190](https://github.com/apache/airflow/pull/71190), [#74096](https://github.com/apache/airflow/pull/74096), [#74342](https://github.com/apache/airflow/pull/74342), [#74341](https://github.com/apache/airflow/pull/74341)
- Approved / minor comments: [#74003](https://github.com/apache/airflow/pull/74003), [#73442](https://github.com/apache/airflow/pull/73442), [#73596](https://github.com/apache/airflow/pull/73596), [#74122](https://github.com/apache/airflow/pull/74122), [#74077](https://github.com/apache/airflow/pull/74077), [#73813](https://github.com/apache/airflow/pull/73813), [#74384](https://github.com/apache/airflow/pull/74384), [#74247](https://github.com/apache/airflow/pull/74247), [#73743](https://github.com/apache/airflow/pull/73743), [#74437](https://github.com/apache/airflow/pull/74437), [#74486](https://github.com/apache/airflow/pull/74486), [#74294](https://github.com/apache/airflow/pull/74294)

---

## Month: September 2026

Summary: PR: 22 merged + 4 open (8 are backports), Code Review: 65 (19 inline threads, 1 changes requested), Merged as committer: 39, Issue Triage: 2, RC Testing: 2, Dev List: 2, Issues opened: 3

### PR

- Asset permission scoping (security):
  - [Scope asset API responses to the assets a user may read](https://github.com/apache/airflow/pull/72682) (+592/−17)
  - [Hide unreadable assets in the asset expression served by next_run_assets](https://github.com/apache/airflow/pull/72864) (+290/−21)
  - [Keep the Dag asset schedule the same shape for every caller](https://github.com/apache/airflow/pull/73805) (+198/−7)
  - [Scope UI next_run_assets endpoint to assets the caller may read](https://github.com/apache/airflow/pull/72862) (+80/−0)
  - [Authorize POST /assets/events on the asset named in the body](https://github.com/apache/airflow/pull/73007) (+71/−9)
  - Open: [#72924](https://github.com/apache/airflow/pull/72924) upstream asset events, [#73008](https://github.com/apache/airflow/pull/73008) UI dependencies graph
- Go SDK:
  - [Generate DagSpec and TaskSpec from the serialization schema](https://github.com/apache/airflow/pull/73936) (+2272/−59): codegen + drift-check prek hook
  - [Add SetVariable and DeleteVariable to the Go SDK task client](https://github.com/apache/airflow/pull/73084) (+344/−8)
  - [Bring the coordinator-protocol models back to the supervisor schema](https://github.com/apache/airflow/pull/73963) (+334/−238)
  - [Update Go SDK docs for the airflow.Bundle authoring surface](https://github.com/apache/airflow/pull/73528) (+206/−116)
  - [Reject invalid Go SDK task handler registrations up front](https://github.com/apache/airflow/pull/73549) (+65/−1), [Document that registration closes at Serve](https://github.com/apache/airflow/pull/73614) (+13/−4)
  - Open: [#73820](https://github.com/apache/airflow/pull/73820) log attributes displacing standard fields
- [Fix KubernetesPodOperator XCom loss when container_logs is a string](https://github.com/apache/airflow/pull/72502) (+26/−1)
- [Fix XCom sidecar helper mutating the caller's pod volumes](https://github.com/apache/airflow/pull/72522) (+20/−3)
- [Fix collection error and timeout assertion in Snowpark container tests](https://github.com/apache/airflow/pull/72709) (+3/−1)
- Open: [Allow deleting queued partitioned asset events via REST API](https://github.com/apache/airflow/pull/73119)
- Backports (8): [#72742](https://github.com/apache/airflow/pull/72742), [#73100](https://github.com/apache/airflow/pull/73100), [#73102](https://github.com/apache/airflow/pull/73102), [#73214](https://github.com/apache/airflow/pull/73214), [#73293](https://github.com/apache/airflow/pull/73293), [#73505](https://github.com/apache/airflow/pull/73505), [#73603](https://github.com/apache/airflow/pull/73603), [#73988](https://github.com/apache/airflow/pull/73988)

### Code Review

- [Java SDK: Honor TaskFlow arg bindings sent by the supervisor](https://github.com/apache/airflow/pull/71188): `from_default` not read, so a defaulted param breaks the bound == declared check; binding rules diverge from Python/Go; primitive vs boxed fields fail differently; suggested rejecting `register()` after `serve()` like the Go SDK (5 inline)
- [Go SDK: introduce the airflow package with airflow.Context](https://github.com/apache/airflow/pull/73277): caught a test that always passes regardless of branch; `Logger()` falls back but `Client()` returns nil
- [Multi-Team: Add teams inspect CLI command](https://github.com/apache/airflow/pull/72036): `print_as` mapper derived rows from closures instead of the element; check `multi_team` is enabled first
- [Add asset state store commands to airflowctl](https://github.com/apache/airflow/pull/70771): literal `null` becomes None and is then rejected with 422 by the core validator; description missed a command
- [API: Translate domain ValueError in Task State Store route](https://github.com/apache/airflow/pull/73753): requested changes — the test's 404 came from `_validate_scope`, not from `set()`
- [Stop airflow standalone leaking components when one fails to start](https://github.com/apache/airflow/pull/72464): the "regression test" did not drive the real shutdown loop
- [UI: Clarify active Dag runs in the header](https://github.com/apache/airflow/pull/72371): queued runs also count toward `max_active_runs`, so the new wording could mislead
- [Keep asset-event Dag run start_date non-null for older Task SDK clients](https://github.com/apache/airflow/pull/71862): asked to cover both by-asset and by-alias endpoints
- [Add Go SDK capability manifest and compatibility matrix](https://github.com/apache/airflow/pull/71753): proposed a runtime dimension for stub-task argument binding
- [Fix #73432: preserve last_finish_time …](https://github.com/apache/airflow/pull/73771): asked for reproduction; duplicate of #73433
- [Fix DAG.cli() crashing on dags pause and unpause](https://github.com/apache/airflow/pull/72109): applied the requested change and merged
- Approved / minor comments (incl. backports and SDK follow-ups): [#72365](https://github.com/apache/airflow/pull/72365), [#70945](https://github.com/apache/airflow/pull/70945), [#72523](https://github.com/apache/airflow/pull/72523), [#72565](https://github.com/apache/airflow/pull/72565), [#72568](https://github.com/apache/airflow/pull/72568), [#70943](https://github.com/apache/airflow/pull/70943), [#72551](https://github.com/apache/airflow/pull/72551), [#72573](https://github.com/apache/airflow/pull/72573), [#72548](https://github.com/apache/airflow/pull/72548), [#72583](https://github.com/apache/airflow/pull/72583), [#72346](https://github.com/apache/airflow/pull/72346), [#72601](https://github.com/apache/airflow/pull/72601), [#72579](https://github.com/apache/airflow/pull/72579), [#72615](https://github.com/apache/airflow/pull/72615), [#72621](https://github.com/apache/airflow/pull/72621), [#72684](https://github.com/apache/airflow/pull/72684), [#72722](https://github.com/apache/airflow/pull/72722), [#72677](https://github.com/apache/airflow/pull/72677), [#72744](https://github.com/apache/airflow/pull/72744), [#72707](https://github.com/apache/airflow/pull/72707), [#72832](https://github.com/apache/airflow/pull/72832), [#70991](https://github.com/apache/airflow/pull/70991), [#72890](https://github.com/apache/airflow/pull/72890), [#70400](https://github.com/apache/airflow/pull/70400), [#70619](https://github.com/apache/airflow/pull/70619), [#71054](https://github.com/apache/airflow/pull/71054), [#73066](https://github.com/apache/airflow/pull/73066), [#73098](https://github.com/apache/airflow/pull/73098), [#71333](https://github.com/apache/airflow/pull/71333), [#73139](https://github.com/apache/airflow/pull/73139), [#73035](https://github.com/apache/airflow/pull/73035), [#72796](https://github.com/apache/airflow/pull/72796), [#72891](https://github.com/apache/airflow/pull/72891), [#72810](https://github.com/apache/airflow/pull/72810), [#73212](https://github.com/apache/airflow/pull/73212), [#73216](https://github.com/apache/airflow/pull/73216), [#73282](https://github.com/apache/airflow/pull/73282), [#71871](https://github.com/apache/airflow/pull/71871), [#73314](https://github.com/apache/airflow/pull/73314), [#73372](https://github.com/apache/airflow/pull/73372), [#73421](https://github.com/apache/airflow/pull/73421), [#72462](https://github.com/apache/airflow/pull/72462), [#72251](https://github.com/apache/airflow/pull/72251), [#73453](https://github.com/apache/airflow/pull/73453), [#73599](https://github.com/apache/airflow/pull/73599), [#73643](https://github.com/apache/airflow/pull/73643), [#73792](https://github.com/apache/airflow/pull/73792), [#73665](https://github.com/apache/airflow/pull/73665), [#73648](https://github.com/apache/airflow/pull/73648), [#71929](https://github.com/apache/airflow/pull/71929), [#73595](https://github.com/apache/airflow/pull/73595), [#70999](https://github.com/apache/airflow/pull/70999), [#72675](https://github.com/apache/airflow/pull/72675), [#73980](https://github.com/apache/airflow/pull/73980)

### Issue Triage

- [#72756](https://github.com/apache/airflow/issues/72756): asked the reporter to follow the issue template with error details
- [#73240](https://github.com/apache/airflow/issues/73240): pointed to the existing fix in #68842

### Issues Opened

- [#73803](https://github.com/apache/airflow/issues/73803) asset schedule depends on which assets the caller may read (fixed by #73805), [#73954](https://github.com/apache/airflow/issues/73954) Go SDK models behind the schema (fixed by #73963), [#73951](https://github.com/apache/airflow/issues/73951) Go SDK pointer spec field

### RC Testing

- [Providers prepared on September 09, 2026](https://github.com/apache/airflow/issues/72902): verified cncf.kubernetes 10.22.0rc1 (#72522, #72502) and other items
- [Apache Airflow 3.3.2rc1](https://github.com/apache/airflow/issues/73089)

### Dev List

- [[LAZY CONSENSUS] Limiting number of opened PRs for users without write access](https://lists.apache.org/thread/kowwrbxrt7dt8mk0nnmnoyt6955yg6fc): proposed a trust list as a middle ground, then agreed to start with a cap of 5 ([follow-up](https://lists.apache.org/thread/bc4m1346pbhrzd9zdxowzjhow46fbw8l))

---

## Month: August 2026

Summary: PR: 2 merged (1 backport) + 1 open, Code Review: 37 (9 inline threads), Merged as committer: 19, RC Testing: 1

### PR

- [Fix misleading error for malformed version in airflow db downgrade](https://github.com/apache/airflow/pull/72331) (+36/−1)
- Backport: [#71119](https://github.com/apache/airflow/pull/71119) airflowctl list offset fix
- Open: [Remove unreachable break in get_bagged_dag](https://github.com/apache/airflow/pull/72332)

### Code Review

- [Stop variables export/import from silently corrupting values](https://github.com/apache/airflow/pull/70944): caught that the export change regresses the UI import path, since CLI and UI use different unwrap rules (3 inline)
- [Show each Dag only once in airflow dags list](https://github.com/apache/airflow/pull/70934): spotted the same latest-version bug in `DBDagBag.iter_all_latest_version_dags()`
- [Fail Glue job tasks stopped mid-run in deferrable verbose mode](https://github.com/apache/airflow/pull/71495): ERROR state still disagreed with the `job_complete` waiter used by the non-verbose branch
- [airflowctl: Fix params value in execute_list()](https://github.com/apache/airflow/pull/69627): proposed the pagination stop condition using `total_entries`
- [Rename airflowctl assets get-by-alias to get-alias](https://github.com/apache/airflow/pull/70453): naming and consistency review
- [Collect the test classes pytest silently skipped](https://github.com/apache/airflow/pull/71643): follow-up idea on existing `PytestCollectionWarning` config
- Approved / minor comments: [#70905](https://github.com/apache/airflow/pull/70905), [#70903](https://github.com/apache/airflow/pull/70903), [#70868](https://github.com/apache/airflow/pull/70868), [#70841](https://github.com/apache/airflow/pull/70841), [#70939](https://github.com/apache/airflow/pull/70939), [#70989](https://github.com/apache/airflow/pull/70989), [#71168](https://github.com/apache/airflow/pull/71168), [#68534](https://github.com/apache/airflow/pull/68534), [#70501](https://github.com/apache/airflow/pull/70501), [#71301](https://github.com/apache/airflow/pull/71301), [#71334](https://github.com/apache/airflow/pull/71334), [#71342](https://github.com/apache/airflow/pull/71342), [#71370](https://github.com/apache/airflow/pull/71370), [#71429](https://github.com/apache/airflow/pull/71429), [#71435](https://github.com/apache/airflow/pull/71435), [#71481](https://github.com/apache/airflow/pull/71481), [#71559](https://github.com/apache/airflow/pull/71559), [#71570](https://github.com/apache/airflow/pull/71570), [#71626](https://github.com/apache/airflow/pull/71626), [#70977](https://github.com/apache/airflow/pull/70977), [#71482](https://github.com/apache/airflow/pull/71482), [#71708](https://github.com/apache/airflow/pull/71708), [#72154](https://github.com/apache/airflow/pull/72154), [#72160](https://github.com/apache/airflow/pull/72160), [#72190](https://github.com/apache/airflow/pull/72190), [#72192](https://github.com/apache/airflow/pull/72192), [#72261](https://github.com/apache/airflow/pull/72261), [#72291](https://github.com/apache/airflow/pull/72291), [#72320](https://github.com/apache/airflow/pull/72320), [#72314](https://github.com/apache/airflow/pull/72314), [#72340](https://github.com/apache/airflow/pull/72340)

### RC Testing

- [Apache Airflow 3.3.1rc2](https://github.com/apache/airflow/issues/71274): verified 7 of my items (#70304, #70442, #70460, #70463, #70733, #70734, #70738) with per-item notes

---

## Month: July 2026

Summary: PR: 17 merged (8 backports), Code Review: 59 + 1 comment-only (20 inline threads, 2 changes requested), Merged as committer: 20, Issue Triage: 2, RC Testing: 1, Dev List: 5

### Other

- Became an Apache Airflow committer (announced on dev list, 2026-07-01); took code-owner areas for the FastAPI API and airflowctl ([#69215](https://github.com/apache/airflow/pull/69215))

### PR

- [Keep Dag run execution API endpoints working for older Task SDK clients](https://github.com/apache/airflow/pull/70138) (+117/−0)
- [Fix Hive metastore partition sensor initialization](https://github.com/apache/airflow/pull/69612) (+79/−25)
- [Simplify airflowctl query parameter handling](https://github.com/apache/airflow/pull/69475) (+35/−21)
- [Fix incorrect asset queued-event endpoint paths in airflowctl](https://github.com/apache/airflow/pull/70461) (+34/−3): bug found while reviewing #70376
- [Document that connection secrets without conn_type remain supported](https://github.com/apache/airflow/pull/69780) (+22/−0)
- [Add regression test for SERVER_TERMINATED precedence over retry](https://github.com/apache/airflow/pull/70774) (+17/−0)
- [Correct supervisor comments on kill escalation and finish() states](https://github.com/apache/airflow/pull/70572), [Fix ClickHouse test_connection test](https://github.com/apache/airflow/pull/70114), [Remove obsolete committer onboarding steps](https://github.com/apache/airflow/pull/69201)
- Backports of AIP-94 / airflowctl work (8): [#69232](https://github.com/apache/airflow/pull/69232), [#69258](https://github.com/apache/airflow/pull/69258), [#69263](https://github.com/apache/airflow/pull/69263), [#69412](https://github.com/apache/airflow/pull/69412), [#70311](https://github.com/apache/airflow/pull/70311), [#70442](https://github.com/apache/airflow/pull/70442), [#70460](https://github.com/apache/airflow/pull/70460), [#70463](https://github.com/apache/airflow/pull/70463)

### Code Review

- [Add IMPORT_ERRORS_ALL permission for import errors of files with no registered Dag](https://github.com/apache/airflow/pull/69790): caught a permission bypass — without matching `bundle_name`, a same-named file in another bundle skips the check; suggested the join fix
- [Route SQLAlchemyError through global exception handler](https://github.com/apache/airflow/pull/69267): match `_DatabaseErrorHandler` stacktrace behavior, add route test for `/run`, move handler next to common handlers (3 inline)
- [Add last_asset_event_timestamp sort option to GET /assets](https://github.com/apache/airflow/pull/70552): test could not distinguish the new sort key from the PK fallback; query fixes (4 inline)
- [Fix google gke system dag deprecations](https://github.com/apache/airflow/pull/69876): requested changes — Autopilot clusters don't support custom node pools, so the system Dags would fail
- [Reject zero step in mapped XCom slices](https://github.com/apache/airflow/pull/70124): requested changes — no API-version migration needed since the old behavior is the bug
- [Detect stale metrics registry entries in the metrics sync prek hook](https://github.com/apache/airflow/pull/69578): also validate the hardcoded list against the registry
- [Remove no-op ServerResponseError re-raise blocks in airflowctl](https://github.com/apache/airflow/pull/70376): flagged pre-existing wrong endpoint paths (fixed in #70461)
- [airflowctl: fix datetime CLI parameters rejecting all input](https://github.com/apache/airflow/pull/70249): proposed an end-to-end parser test
- [Allow slashes in connection IDs fetched via the Execution API](https://github.com/apache/airflow/pull/69228): noted it covers only one of the two problems in #57864
- [Add has_note key to the Grid Runs API response](https://github.com/apache/airflow/pull/69121): UI test fixtures missing the new field
- [Change log level for Edge job fetch](https://github.com/apache/airflow/pull/68736): docs also need updating
- [Mark dags list-import-errors as migrated to airflowctl](https://github.com/apache/airflow/pull/68602) (comment): suggested deprecating all `dags` commands together
- Approved / minor comments (incl. zh-TW translations, backports, CI/dependabot): [#69199](https://github.com/apache/airflow/pull/69199), [#69210](https://github.com/apache/airflow/pull/69210), [#69215](https://github.com/apache/airflow/pull/69215), [#69220](https://github.com/apache/airflow/pull/69220), [#68046](https://github.com/apache/airflow/pull/68046), [#69236](https://github.com/apache/airflow/pull/69236), [#68958](https://github.com/apache/airflow/pull/68958), [#69223](https://github.com/apache/airflow/pull/69223), [#69246](https://github.com/apache/airflow/pull/69246), [#69322](https://github.com/apache/airflow/pull/69322), [#69320](https://github.com/apache/airflow/pull/69320), [#69314](https://github.com/apache/airflow/pull/69314), [#69339](https://github.com/apache/airflow/pull/69339), [#69341](https://github.com/apache/airflow/pull/69341), [#69350](https://github.com/apache/airflow/pull/69350), [#69410](https://github.com/apache/airflow/pull/69410), [#68650](https://github.com/apache/airflow/pull/68650), [#69369](https://github.com/apache/airflow/pull/69369), [#69455](https://github.com/apache/airflow/pull/69455), [#69445](https://github.com/apache/airflow/pull/69445), [#69470](https://github.com/apache/airflow/pull/69470), [#69505](https://github.com/apache/airflow/pull/69505), [#69549](https://github.com/apache/airflow/pull/69549), [#68985](https://github.com/apache/airflow/pull/68985), [#69489](https://github.com/apache/airflow/pull/69489), [#69708](https://github.com/apache/airflow/pull/69708), [#69707](https://github.com/apache/airflow/pull/69707), [#69305](https://github.com/apache/airflow/pull/69305), [#69694](https://github.com/apache/airflow/pull/69694), [#69714](https://github.com/apache/airflow/pull/69714), [#69497](https://github.com/apache/airflow/pull/69497), [#69949](https://github.com/apache/airflow/pull/69949), [#69731](https://github.com/apache/airflow/pull/69731), [#70073](https://github.com/apache/airflow/pull/70073), [#70162](https://github.com/apache/airflow/pull/70162), [#70195](https://github.com/apache/airflow/pull/70195), [#70212](https://github.com/apache/airflow/pull/70212), [#70304](https://github.com/apache/airflow/pull/70304), [#70293](https://github.com/apache/airflow/pull/70293), [#70321](https://github.com/apache/airflow/pull/70321), [#70379](https://github.com/apache/airflow/pull/70379), [#70153](https://github.com/apache/airflow/pull/70153), [#70490](https://github.com/apache/airflow/pull/70490), [#70369](https://github.com/apache/airflow/pull/70369), [#70577](https://github.com/apache/airflow/pull/70577), [#70785](https://github.com/apache/airflow/pull/70785), [#70776](https://github.com/apache/airflow/pull/70776), [#70806](https://github.com/apache/airflow/pull/70806)

### Issue Triage

- [#64880](https://github.com/apache/airflow/issues/64880): closed as fixed by #67343
- [#70271](https://github.com/apache/airflow/issues/70271): coordinated assignment

### RC Testing

- [Providers prepared on July 22, 2026](https://github.com/apache/airflow/issues/70355): verified #69612

### Dev List

- [[LAZY CONSENSUS] Configuration design for REST API & UI metrics tagging (PR #64523)](https://lists.apache.org/thread/hbzpxq4533gmrmv4kd7v58t9c0ypv97c): called the vote, [clarified route-template tagging](https://lists.apache.org/thread/8n9m5pknt0ldc37s14d6mr08trv2dh1v), [closed with consensus](https://lists.apache.org/thread/tnwrbo7bx3l2tz2wy6d4xfmkxc1q4sd4)
- [[DISCUSS] Next steps in our triage](https://lists.apache.org/thread/ht8l9fdn6lxwb2p7vfcnfnb82d53n6p7): +1 on the cap
- [Re: New committer](https://lists.apache.org/thread/63lmvzkblrqomscn46374qds37oq7qxx)

---

## Month: June 2026

Summary: PR: 11 merged + 1 open, Code Review: 15 (18 inline threads, 1 changes requested), Issue Triage: 1, RC Testing: 3, Dev List: 6

### PR

- [Add airflowctl dags clear command](https://github.com/apache/airflow/pull/68706) (+654/−73)
- [Improve SQLite test DB health checks](https://github.com/apache/airflow/pull/68021) (+651/−6, mostly tests)
- [Add airflowctl dags state command](https://github.com/apache/airflow/pull/68564) (+347/−70)
- [Add partition date filters to Dag run API](https://github.com/apache/airflow/pull/68682) (+217/−27)
- [[AIP-94] Add filters and pagination to airflowctl jobs list](https://github.com/apache/airflow/pull/68616) (+131/−18)
- [Fix TaskInstance duration calculation with SQLite](https://github.com/apache/airflow/pull/68142) (+38/−4)
- [Refactor latest DagRun load options for consistency](https://github.com/apache/airflow/pull/67911) (+14/−26)
- AIP-94 migrations: [#68525](https://github.com/apache/airflow/pull/68525) providers command, [#68529](https://github.com/apache/airflow/pull/68529) dags details, [#68444](https://github.com/apache/airflow/pull/68444) dags list
- [Fix Celery worker JSON logging config for teams](https://github.com/apache/airflow/pull/69139) (+3/−3)
- Open: [[AIP-94] Add airflowctl providers get](https://github.com/apache/airflow/pull/68854)

### Code Review

- [API: Return 503 when SQLite locks during backfill creation](https://github.com/apache/airflow/pull/67900): caught that the change widens a race — concurrent requests can both pass the `num_active` check and create multiple active backfills; suggested locking the DagModel row
- [Add task instance management commands to airflowctl CLI](https://github.com/apache/airflow/pull/67947): requested changes — the new None-filtering doesn't protect boolean fields; unrelated test change; regenerate help image
- [Guard transport.send() so IAM/gRPC errors don't crash supervised components](https://github.com/apache/airflow/pull/68250): questioned the failure path (default transport only enqueues); use `create_autospec`, avoid `caplog`
- [Avoid N+1 team-name queries in bulk Dag run authorization](https://github.com/apache/airflow/pull/68286): asked for a team-owned Dag test (3 inline)
- [Reduce redundant Dag team lookups in authorization checks](https://github.com/apache/airflow/pull/68020): a query-count test still asserted the old exact counts
- [Add bundle_name to Dag](https://github.com/apache/airflow/pull/68583): test on empty `tmp_path` made the loop assert nothing
- [Add Calendar Deadlines API and Data Models](https://github.com/apache/airflow/pull/68604): query/signature fixes, asked for unit tests
- [Add airflowctl tasks clear command](https://github.com/apache/airflow/pull/66926), [Mark connections commands as migrated](https://github.com/apache/airflow/pull/68972): help texts / pointed out `connections import` now exists in airflowctl
- Approved / minor comments: [#67848](https://github.com/apache/airflow/pull/67848), [#69083](https://github.com/apache/airflow/pull/69083), [#69084](https://github.com/apache/airflow/pull/69084), [#69102](https://github.com/apache/airflow/pull/69102), [#69072](https://github.com/apache/airflow/pull/69072), [#69186](https://github.com/apache/airflow/pull/69186)

### Issue Triage

- [#68083](https://github.com/apache/airflow/issues/68083): pointed to #67494 which already addresses it

### RC Testing

- [Airflow CTL 0.1.5rc1](https://github.com/apache/airflow/issues/67655), [Helm Chart 1.22.0rc1](https://github.com/apache/airflow/issues/67821)
- [Providers prepared on June 19, 2026](https://github.com/apache/airflow/issues/68751): microsoft-azure 13.5.0rc1, verified the PowerBI `wait_for_completion=False` regression path; +1 on the [vote thread](https://lists.apache.org/thread/78vx099tw7rcncvxklrp19xcs253fkxs)

### Dev List

- [[DISCUSS] Configuration design for REST API & UI metrics tagging](https://lists.apache.org/thread/2o3g4yvr0pzr64fzjwf9xd78obd4dhnt): started the design discussion for #64523
- Votes: [June PR of the Month](https://lists.apache.org/thread/ghgqb5rx06qyszqpghskovsxs129q5zt), [June Rookie PR of the Month](https://lists.apache.org/thread/8fsz9rvrv2y2sbhmh9dhmvxgysktqh7c)
- Replies: [New Community Provider: Anthropic](https://lists.apache.org/thread/6k5m8kpx4kp7yd5g8z885x0wzp4hjk62), [Rebase your PRs for faster CI](https://lists.apache.org/thread/r5jbv9g7cg1wsxos1fg8t7ox19czy4sg)

---

## Month: May 2026

Summary: PR: 10 merged + 2 open, Code Review: 18 + 1 comment-only (18 inline threads, 3 changes requested), Issue Triage: 1, RC Testing: 2

### PR

- [Fix triggerer KEDA database connection rendering](https://github.com/apache/airflow/pull/67538) (+108/−4)
- [Fix config lint warnings for remove_if_equals rules](https://github.com/apache/airflow/pull/66370) (+88/−11)
- [Fix launcher RBAC for fully-qualified executors](https://github.com/apache/airflow/pull/66208) (+46/−4)
- [Avoid lazy-loading timetable fields for latest DagRuns](https://github.com/apache/airflow/pull/66488) (+44/−4)
- [Fix SecretsMasker merge round-trip for Kubernetes env vars](https://github.com/apache/airflow/pull/67122) (+43/−0)
- [Fix smtp connection test for noop tuple response](https://github.com/apache/airflow/pull/66406) (+26/−2)
- [Update Weaviate examples for client 4.16 API](https://github.com/apache/airflow/pull/67343) (+25/−21)
- [Fix Breeze dry-run commands executing in quiet mode](https://github.com/apache/airflow/pull/67132), [Prevent durable storage tests from leaking hook lineage](https://github.com/apache/airflow/pull/67252), [Remove stray debug output](https://github.com/apache/airflow/pull/66434)
- Open: [Reject invalid TaskInstance state update payloads](https://github.com/apache/airflow/pull/67160), [Fix MySQL HITL responded-by filters](https://github.com/apache/airflow/pull/67323)

### Code Review

- [Fix import errors total_entries count with multiple DAGs per file](https://github.com/apache/airflow/pull/67550): requested changes — the data query still applied `limit` to joined rows before grouping (limit=1 should return one import error)
- [Add incremental refresh support to TableauOperator](https://github.com/apache/airflow/pull/67340): requested changes — `incremental` exists only in tableauserverclient ≥0.35 but the provider allows ≥0.27
- [Fix Celery worker liveness probe hostname lookup](https://github.com/apache/airflow/pull/67471): requested changes, approved after update
- [Compute historical metrics percentages from uncapped counts](https://github.com/apache/airflow/pull/67549): percentages still misleading while per-state counts stay capped
- [airflowctl add tasks clear command](https://github.com/apache/airflow/pull/66179): move logic into operations.py, parametrize tests, help texts (5 inline)
- [Fix N+1 query pattern in bulk pool delete endpoint](https://github.com/apache/airflow/pull/66222): simpler loop; compare query counts across two pool sizes instead of a hardcoded number
- [Load USFederalHolidayCalendar lazily](https://github.com/apache/airflow/pull/66303): asked for an ImportError fallback test
- [Add tasks state command to airflowctl](https://github.com/apache/airflow/pull/66202), [Handle no next run in dags next-execution](https://github.com/apache/airflow/pull/67395): tests, error handling, wording consistency
- [fix(scheduler): ignore stale executor success after defer reschedule](https://github.com/apache/airflow/pull/66431) (comment): raised the case where the scheduler re-queues the TI quickly
- Approved / minor comments: [#66188](https://github.com/apache/airflow/pull/66188), [#66261](https://github.com/apache/airflow/pull/66261), [#66574](https://github.com/apache/airflow/pull/66574), [#67382](https://github.com/apache/airflow/pull/67382), [#67592](https://github.com/apache/airflow/pull/67592), [#66200](https://github.com/apache/airflow/pull/66200), [#65875](https://github.com/apache/airflow/pull/65875), [#67143](https://github.com/apache/airflow/pull/67143), [#67445](https://github.com/apache/airflow/pull/67445)

### Issue Triage

- [#66176](https://github.com/apache/airflow/issues/66176): spotted a likely wrong endpoint in the AIP-94 task; suggested `POST /clearTaskInstances`

### RC Testing

- [Providers prepared on May 19, 2026](https://github.com/apache/airflow/issues/67213): verified #65955, #66406
- [Apache Airflow 3.2.2rc3](https://github.com/apache/airflow/issues/67282)

---

## Month: April 2026

Summary: PR: 19 merged + 1 open, Code Review: 10 (7 inline threads), Issue Triage: 1, RC Testing: 1

### PR

- Helm chart: [Fix Airflow 3 task log access with NetworkPolicies](https://github.com/apache/airflow/pull/65754) (+49/−3), [cleanup pod lifecycle hooks](https://github.com/apache/airflow/pull/65764) (+48/−0), [database cleanup lifecycle hooks](https://github.com/apache/airflow/pull/65881) (+45/−0), [cleanup RBAC only for KubernetesExecutor](https://github.com/apache/airflow/pull/65539) (+34/−3), [false-green applyCustomEnv check](https://github.com/apache/airflow/pull/65495), [duplicate fallback branch](https://github.com/apache/airflow/pull/65558)
- [Fix XCom prior-dates lookup for duplicate run_id across Dags](https://github.com/apache/airflow/pull/65227) (+53/−13)
- [Fix N+1 queries in trigger asset event submission](https://github.com/apache/airflow/pull/65367) (+27/−1)
- [Fix list-envs auth status for env names containing .json](https://github.com/apache/airflow/pull/64677) (+36/−7)
- airflowctl: [Send backfill payloads as JSON](https://github.com/apache/airflow/pull/65158), [fallback for failed constrained installs](https://github.com/apache/airflow/pull/65013)
- [Fix kubernetes cleanup-pods ignoring --verbose](https://github.com/apache/airflow/pull/65955) (+11/−5)
- CI reliability: [Stop masking quarantined unit test failures](https://github.com/apache/airflow/pull/65500), [Avoid false recovery alerts](https://github.com/apache/airflow/pull/64863), [constraints diff status handling](https://github.com/apache/airflow/pull/65456), [monotonic clock for prek timing](https://github.com/apache/airflow/pull/65481), [system test failure logging](https://github.com/apache/airflow/pull/65410), [#65381](https://github.com/apache/airflow/pull/65381), [#64645](https://github.com/apache/airflow/pull/64645)
- Open: [Fix in-process Execution API secrets routing in client contexts](https://github.com/apache/airflow/pull/65587)

### Code Review

- [Add API endpoint to re-parse a single DAG](https://github.com/apache/airflow/pull/65839): status code (422 vs 404) for a Dag without file location, error messages (3 inline)
- [Handle IMAP attachment filename collisions](https://github.com/apache/airflow/pull/65995): code changes didn't match the PR description (wrong branch pushed)
- [Pass try_number to extra links API](https://github.com/apache/airflow/pull/65661): optimization suggestion
- [Fix REST API pools: unlimited open_slots as -1](https://github.com/apache/airflow/pull/65466), [Stringify task_instance.id for OTEL](https://github.com/apache/airflow/pull/66053), [Drop Airflow 2 Support in Chart](https://github.com/apache/airflow/pull/65866): nits
- Approved / minor comments: [#64582](https://github.com/apache/airflow/pull/64582), [#64520](https://github.com/apache/airflow/pull/64520), [#65065](https://github.com/apache/airflow/pull/65065), [#65072](https://github.com/apache/airflow/pull/65072)

### Issue Triage

- [#66047](https://github.com/apache/airflow/issues/66047): asked to close as fixed by #66045

### RC Testing

- [Apache Airflow 3.2.1rc3](https://github.com/apache/airflow/issues/65336): verified #64416
