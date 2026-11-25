# jeffoodchain

- GitHub：https://github.com/jeffoodchain
- Slack：TODO

## 2026-11-24

提名人 pingsutw · 建議等級 2 萬

- [x] 關係人宣告：提名人與被提名者無親屬、僱傭、財務、業務合作或其他可能影響公正判斷之利害關係

- Shipped action events as Kubernetes Events for Flyte 2, so systems outside Flyte (lineage, alerting, cost accounting) can react to action events again without a broker client in Flyte. Opt-in `executor.k8sEventMode` (`off` / `terminal` / `all`); a deterministic Event name per action event so retried reconciles never create duplicates; DB record → Event → status write ordering so no Event is lost; retryable vs permanent create errors handled separately (+1095 lines with unit and envtest coverage, through 5 review rounds) ([#8116](https://github.com/flyteorg/flyte/pull/8116), [#7829](https://github.com/flyteorg/flyte/issues/7829))
- Verified it end to end on a devbox with kubernetes-event-exporter → Kafka and published a runnable example for users ([flyte-kafka-event-exporter-example](https://github.com/jeffoodchain/flyte-kafka-event-exporter-example)), then filed the follow-up for OTel metrics on Event create failures ([#8473](https://github.com/flyteorg/flyte/issues/8473))
- Fixed a user-reported bug where the run-level service account (`flyte run --service-account`, `with_runcontext(service_account=...)`, `security.service_account` setting) was ignored for task pods, so tasks on EKS had no IRSA role and failed on their first S3 access: added the field to the TaskAction CRD and propagated it to root and child actions ([#8414](https://github.com/flyteorg/flyte/pull/8414), [#8241](https://github.com/flyteorg/flyte/issues/8241))
- Fixed a flyte-sdk bug where `fork()` in a long-lived process silently ran stale source because the code bundle was cached on arguments, not file contents ([flyte-sdk#1508](https://github.com/flyteorg/flyte-sdk/pull/1508), [#7923](https://github.com/flyteorg/flyte/issues/7923))
- Reviews other fellows' Flyte PRs ([#8275](https://github.com/flyteorg/flyte/pull/8275))
