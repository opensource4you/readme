# Ray Monthly Contribution Report — October 2026

Contributor: [Efutrrionpy](https://github.com/Efutrrionpy)

## Reviewed Pull Requests

### [#67146 — Make Prometheus re-read the service discovery file every 5s instead of 5m](https://github.com/ray-project/ray/pull/67146)

Clarified that Prometheus watches the parent directory on Linux, so it misses updates to the symlink target inside the session directory. The author accepted the clarification and updated the PR description.

[Review comment](https://github.com/ray-project/ray/pull/67146#issuecomment-6265837643) · [Author's response](https://github.com/ray-project/ray/pull/67146#issuecomment-6267917043) — October 19, 2026.
