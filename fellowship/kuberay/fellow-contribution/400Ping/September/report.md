# KubeRay Monthly Contribution Report — September 2026

- **Contributor:** Jie-Kai Chang ([@400Ping](https://github.com/400Ping))
- **Period:** 2026/09/01 – 2026/10/11
- **Repository:** [ray-project/kuberay](https://github.com/ray-project/kuberay)

## Summary

| Category | Count |
| --- | --- |
| Authored PRs actively worked on | 1 (open, in review) |
| Reviewed PRs | 2 |

## Pull Requests

### [#5215 [Feature] Add IPv6 support](https://github.com/ray-project/kuberay/pull/5215) — Open, under review (label: `1.8.0`)

Adds IPv6-only cluster support to KubeRay (+654 / −163, 19 files). Related to [ray-project/ray#54660](https://github.com/ray-project/ray/issues/54660).

- Ray head and worker Pods advertise their Kubernetes Pod IP, with IPv6-aware dashboard binding and endpoint formatting.
- Auto-generated mTLS certificates now include IPv6 SANs.
- New IPv6-only E2E coverage for RayCluster, RayService, and mTLS.
- Fixes Ray start command expansion and IPv6 E2E log collection.

Work done in this period (opened on 2026/08/30, iterated throughout September and October):

- 09/02: Narrowed scope from dual-stack to IPv6 single-stack after review feedback.
- 09/13: Fixed the IPv6 E2E Buildkite job by dropping the host DNS search domain from the kind node; switched log collection to `kind export logs` so CoreDNS/kube-proxy failures are captured, not only operator logs.
- 09/24: Verified on an IPv6-only kind cluster with Ray 2.58.0 that Ray auto-detects the Pod IPv6 address correctly, and removed the default `--node-ip-address` injection accordingly.
- 09/26: Converted the mTLS controller test into a table-driven test over both IPv4 and IPv6; switched the certificate check to `openssl -verify_ip` for semantic IP comparison (the previous `grep` could never match IPv6 SANs).
- 10/09: The controller now derives `dashboard-host` (`::` vs `0.0.0.0`) from the head Service's `spec.ipFamilies`; seeded the head Service in unit tests and rebased onto master.

Reviewers engaged: @machichima, @justinyeh1995 (10 review-thread exchanges resolved).

## Reviewed PRs

### [#5171 [HistoryServer] List Ray event files recursively](https://github.com/ray-project/kuberay/pull/5171) by @vincent-626 — reviewed 2026/09/05

Reduces Ray event discovery from `1 + N + N*M` storage listing calls to one recursive listing per session, implemented across S3, Azure Blob, GCS, Aliyun OSS, and the local reader.

- Pointed out that the S3 page size was capped at 100 objects, which would still cause `ceil(K / pageSize)` round trips for log-heavy sessions and partly defeat the goal of the PR.
- Requested table-driven unit tests for `classifyRayEventFile` to pin down rejection of non-canonical and malformed paths.

### [#5322 [Bug] logStdoutEncoder and logFileEncoder setting from `--config` are ignored](https://github.com/ray-project/kuberay/pull/5322) by @Onionssss — merged 2026/09/22

Fixes the operator ignoring `logStdoutEncoder` / `logFileEncoder` from the config file because `main.go` read the raw flag defaults. Asked the author to include test results in the PR description before merge (2026/09/19).
