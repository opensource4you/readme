# KubeRay Monthly Contribution Report — October 2026

Contributor: [Efutrrionpy](https://github.com/Efutrrionpy)

## Pull Requests

### [ray#66947 — Document scaling from zero with custom resources](https://github.com/ray-project/ray/pull/66947)

Documented how to declare custom resources so KubeRay worker groups can scale from zero, with an accelerator-type example and version requirements. Addresses [kuberay#863](https://github.com/kuberay-project/kuberay/issues/863).

Merged on October 20, 2026 (UTC+8).

### [#5472 — Fix dashboard token login with --proxy-server-url](https://github.com/kuberay-project/kuberay/pull/5472)

Added fallback routes so the local Ray Dashboard can handle token authentication when using History Server as a proxy. Added route tests and a login note, and validated login and stored-session replay with Ray 2.59.0 on kind. Addresses [#5468](https://github.com/kuberay-project/kuberay/issues/5468).

Opened on October 29, 2026; open as of October 31, 2026.

### [#5481 — Preserve maxReplicas: 0 in the RayCluster Helm chart](https://github.com/kuberay-project/kuberay/pull/5481)

Reported [#5454](https://github.com/kuberay-project/kuberay/issues/5454) and fixed explicit zero values being replaced by the fallback for both default and additional worker groups. Added Helm regression tests for zero, positive, missing, and null values; all 123 chart unit tests and local rendering checks passed.

Opened on October 30, 2026; open as of October 31, 2026.
