# Ray Monthly Contribution Report — September 2026

**Contributor:** [@GNITOAHC](https://github.com/GNITOAHC) · **Repository:** [ray-project/ray](https://github.com/ray-project/ray) · **Area:** Ray Serve

## Summary

Three bug fixes merged in Ray Serve and one PR under review.

| PR | Title | Status | Diff |
|----|-------|--------|------|
| [#66358](https://github.com/ray-project/ray/pull/66358) | [serve] Drop dead replicas from the multiplexed model ID mapping | Merged 2026-09-22 | +50 / -0 |
| [#66438](https://github.com/ray-project/ray/pull/66438) | [serve] Fix KeyError that stalls the application control loop when a deployment is removed | Merged 2026-09-24 | +118 / -0 |
| [#66616](https://github.com/ray-project/ray/pull/66616) | [serve] Fix TypeError when recording a custom metric with a route tag | Merged 2026-10-03 | +97 / -14 |
| [#66910](https://github.com/ray-project/ray/pull/66910) | [serve] Surface terminal deployment actor failures outside UPDATING | Open (in review) | +119 / -78 |

## Details

### #66358 — Dead replicas in the multiplexed model mapping
- **Problem:** `on_replica_actor_died` did not clear `MultiplexMixin._multiplexed_model_id_to_replica_ids`. Until the controller sent a new replica set, the router could still pick a dead replica. That caused a `KeyError` in `pow_2_router.py`, which ended the routing task without notice and left requests unfulfilled.
- **Fix:** Also remove the dead replica from the multiplexed mapping, the same way locality sets are already handled. Unit tests added.

### #66438 — Controller stall after a deployment is removed
- **Problem:** `ApplicationAutoscalingState.get_decision_num_replicas` looked up every registered deployment in the caller's target map without checking that it was there. Removing a deployment from a running app raised a `KeyError`, which stalled the application control loop permanently.
- **Fix:** Skip deployments that are missing from the caller's map, matching the membership checks used elsewhere in the class. Regression tests added.

Note: #66433 was an earlier copy of #66438 and was closed in favor of it.

### #66616 — Custom metrics with a `route` tag
- **Problem:** `ray.serve.metrics.Counter/Gauge/Histogram` raised `TypeError: argument of type 'NoneType' is not iterable` when `"route"` was in `tag_keys` and the metric was recorded without `tags`, because `tags=None` reached a `ROUTE_TAG not in tags` check.
- **Fix:** Handle `tags=None` in `inc()`, `set()` and `observe()`. Tests added. (Opened 2026-09-30.)

### #66910 — Report terminal actor failures (in review)
- **Problem:** When a replica actor failed its health check and every recreation attempt failed, the deployment stayed at `UNHEALTHY` with a "Retrying" message forever, so users had no sign that they needed to redeploy. There were two causes:
  - `DeploymentStatusInfo.handle_transition` handled `DEPLOYMENT_ACTOR_FAILED` only in the `UPDATING` and `DEPLOY_FAILED` states.
  - `DeploymentState.check_curr_status` raised the trigger only when no replica was running at the target version.
- **Fix:** Handle the trigger in every state and check for terminal failure even while some replicas are running. Also removes two unused, broken helpers from `ServeControllerClient`.

Note: #66904 was closed and replaced by #66910 because its branch contained unrelated changes.
