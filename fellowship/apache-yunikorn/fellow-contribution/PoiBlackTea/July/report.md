# PoiBlackTea Monthly Contribution Report

- GitHub: https://github.com/PoiBlackTea
- Slack: https://opensource4you.slack.com/team/U0BBZE02DFF
- ASF JIRA: [weichen lai](https://issues.apache.org/jira/secure/ViewProfile.jspa?name=poiblacktea)
- Period: 2026-07-07 to 2026-07-31 (since my first YuniKorn contribution)

## Summary

PR: 8 (all merged, across 4 YuniKorn repos), Issue Triage: 1 JIRA filed

YuniKorn committers merge by pushing the patch themselves, so merged PRs show as Closed on GitHub; the landed commits are on master ([core](https://github.com/apache/yunikorn-core/commits/master/?author=PoiBlackTea&since=2026-07-01&until=2026-07-31), [k8shim](https://github.com/apache/yunikorn-k8shim/commits/master/?author=PoiBlackTea&since=2026-07-01&until=2026-07-31), [release](https://github.com/apache/yunikorn-release/commits/master/?author=PoiBlackTea&since=2026-07-01&until=2026-07-31), [site](https://github.com/apache/yunikorn-site/commits/master/?author=PoiBlackTea&since=2026-07-01&until=2026-07-31)). PRs are listed in the month they were merged.

## PR

### Merged

- [[YUNIKORN-3247] Update troubleshooting statedump collection](https://github.com/apache/yunikorn-site/pull/565): point the troubleshooting guide at the current `/debug/fullstatedump` endpoint on port 9080 instead of the removed `/ws/v1/fullstatedump` on 9889, and add port-forward instructions.
- [[YUNIKORN-3287] Support Gateway API HTTPRoute in Helm chart](https://github.com/apache/yunikorn-release/pull/231): add an optional `HTTPRoute` template so the web UI and REST API can be exposed through the Kubernetes Gateway API, tested end to end on Minikube behind an NGINX gateway.
- [[YUNIKORN-3328] Fix repository order in release-configs.json](https://github.com/apache/yunikorn-release/pull/232): load scheduler-interface before core and k8shim, which depend on it, so `build-release.py` can update their dependencies.
- [[YUNIKORN-3327] Replace deprecated distutils with shutil in release script](https://github.com/apache/yunikorn-release/pull/233): switch `build-release.py` to `shutil.copytree`, since `distutils` was removed in Python 3.12.
- [[YUNIKORN-3326] Change BackoffDeadline field in REST DAO to int64](https://github.com/apache/yunikorn-core/pull/1105): encode the application `backoffDeadline` as UnixNano `int64` like the other REST DAO timestamps, instead of `*time.Time`.
- [[YUNIKORN-3337] Fix race condition in webservice unit tests](https://github.com/apache/yunikorn-core/pull/1109): wait until the test web server accepts connections before sending requests, fixing intermittent connection-refused failures.
- [[YUNIKORN-3324] Remove pod "ignore-application" annotation](https://github.com/apache/yunikorn-k8shim/pull/1055): remove the annotation and its admission controller handling, which only existed for the removed plugin mode.
- [[YUNIKORN-3315] Remove old placeholder flags and processing](https://github.com/apache/yunikorn-k8shim/pull/1056): remove the placeholder label and annotation flags that were deprecated for removal in 1.7.0, along with their fallback code.

## Issue Triage

- Filed [YUNIKORN-3337](https://issues.apache.org/jira/browse/YUNIKORN-3337) Fix connection refused error in webservice unit tests, and fixed it in [core#1109](https://github.com/apache/yunikorn-core/pull/1109).
