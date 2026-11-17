# Ray Monthly Contribution Report — October 2026

Contributor: [Efutrrionpy](https://github.com/Efutrrionpy)

## Reviewed Pull Requests

### [#67146 — Make Prometheus re-read the service discovery file every 5s instead of 5m](https://github.com/ray-project/ray/pull/67146)

Clarified that Prometheus watches the parent directory on Linux, so it misses updates to the symlink target inside the session directory. The author accepted the clarification and updated the PR description.

[Review comment](https://github.com/ray-project/ray/pull/67146#issuecomment-6265837643) · [Author's response](https://github.com/ray-project/ray/pull/67146#issuecomment-6267917043) — October 19, 2026.

## Issues

### [#67318 — read_sql can silently drop rows with NULL shard keys](https://github.com/ray-project/ray/issues/67318)

Reported silent row loss when SQL sharding excludes rows with NULL shard keys. Provided a SQLite reproduction on Ray 2.59.0: reading a 100-row table returned all rows with one block but only 90 with two blocks. Traced the loss to NULL rows not matching any shard predicate.

Opened on October 23, 2026; open as of October 31, 2026.
