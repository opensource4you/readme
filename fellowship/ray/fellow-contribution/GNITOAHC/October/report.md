# Ray Monthly Contribution Report — October 2026

**Contributor:** [@GNITOAHC](https://github.com/GNITOAHC) · **Repository:** [ray-project/ray](https://github.com/ray-project/ray) · **Area:** Ray Serve

## Summary

This report covers October 2026 and the period after it up to 2026-11-17. In that time, three bug fixes merged in Ray Serve, two PRs are in review, one bug report was filed, and one community PR was reviewed. The table lists every PR and issue whose state changed in this period, with its status as of 2026-11-17.

| PR / Issue | Title | Status | Diff |
|----|-------|--------|------|
| [#66616](https://github.com/ray-project/ray/pull/66616) | [serve] Fix TypeError when recording a custom metric with a route tag | Merged 2026-10-03 | +97 / -14 |
| [#66910](https://github.com/ray-project/ray/pull/66910) | [serve] Surface terminal deployment actor failures outside UPDATING | Opened 2026-10-10, merged 2026-10-22 | +143 / -78 |
| [#67210](https://github.com/ray-project/ray/pull/67210) | [serve] Drain buffered gRPC stream messages before raising stream error | Opened 2026-10-20, open (awaiting maintainer re-review) | +178 / -8 |
| [#67494](https://github.com/ray-project/ray/issues/67494) (issue) | [Serve] Gang scheduling + autoscaling: bounds that aren't multiples of gang_size cause DEPLOY_FAILED or endless scale flapping | Opened 2026-10-28, open (fixes in progress in #67507 and #68056) | — |
| [#67923](https://github.com/ray-project/ray/pull/67923) | [serve] Observe the result of rank reassignment reconfigures | Opened 2026-11-07, merged 2026-11-17 | +244 / -11 |
| [#68085](https://github.com/ray-project/ray/pull/68085) | [serve] Drop completed requests from the multiplexed model ID index | Opened 2026-11-12, open (awaiting review) | +97 / -14 |

Note: #66616 was opened in September and is described in the September report.

### Reviews

| PR / Issue | Title | Author | Activity |
|----|-------|--------|----------|
| [#67507](https://github.com/ray-project/ray/pull/67507) | [Serve] Interpret num_replicas and autoscaling bounds as gang counts | [@hello-peter-tang](https://github.com/hello-peter-tang) | Reviewed 2026-10-30 |

- **#67507:** Follow-up to #67494, originally titled "[Serve] Reject gang-unaligned autoscaling bounds". Pointed out two mypy errors in `_private/config.py`, because an `Optional` autoscaling config was indexed without a `None` check. Suggested either an early return or removing `Optional` from the signature. Also suggested documenting the new bound check in `doc/source/serve/advanced-guides/gang-scheduling.md`.

## Details

### #66910 — Report terminal actor failures
- **Problem:** When a replica actor failed its health check and every recreation attempt failed, the deployment stayed at `UNHEALTHY` with a "Retrying" message forever, so users had no sign that they needed to redeploy. There were two causes:
  - `DeploymentStatusInfo.handle_transition` handled `DEPLOYMENT_ACTOR_FAILED` only in the `UPDATING` and `DEPLOY_FAILED` states.
  - `DeploymentState.check_curr_status` raised the trigger only when no replica was running at the target version.
- **Fix:** Handle the trigger in every state and check for terminal failure even while some replicas are running. Also removes two unused, broken helpers (`_wait_for_deployment_healthy`, `_wait_for_deployment_deleted`) from `ServeControllerClient`. Approved and merged on 2026-10-22.

### #67210 — Buffered gRPC stream chunks lost on failure (in review)
- **Problem:** `MessageQueue.get_one_message` raised a stored error before it returned messages that were already queued. With gRPC transport, `gRPCReplicaResult.consume_messages_from_gen` reads stream chunks ahead of the caller, so a failure partway through a stream (for example, the replica dying) dropped every chunk received but not yet read. This affects Serve LLM's streaming ingress by default.
- **Fix:** Return queued messages first and raise the error once the queue is empty, as the docstring already describes. `set_error` now also wakes waiting readers, so a blocked `get_one_message` no longer hangs. Opened 2026-10-20.
- **Review:** A reviewer found that after the change, cancelled requests still returned buffered chunks. This was fixed so cancellation raises `RequestCancelledError` right away, and the reviewer confirmed the fix. Two community reviewers have since approved, one after killing a replica mid-stream: master returned 0 chunks and this PR returned all of them before the error. The remaining maintainer feedback (a race between `cancel()` and reads, plus test changes) was addressed on 2026-11-10, and the PR is waiting for re-review.

### #67494 — Gang-size alignment with autoscaling (issue)
- **Problem:** `GangSchedulingAutoscalingPolicy` rounds the autoscaling target up to a multiple of `gang_size`, but `apply_bounds` then clips it to `[min_replicas, max_replicas]`. That brings back unaligned targets, which the scheduler can't reach. With `gang_size=2`:
  - `min=max=3` leads to `DEPLOY_FAILED`, with a misleading "constructor or initial health check" message.
  - `min=3, max=4` makes the deployment flap between 3 and 4 replicas forever.
  - `target_capacity` can also make aligned bounds unaligned.
- **Proposal:** Validate the bounds in `DeploymentConfig` against `gang_size`, and round capacity-adjusted values to whole gangs. Filed 2026-10-28 with reproduction scripts. Community follow-up PRs ([#67507](https://github.com/ray-project/ray/pull/67507), [#68056](https://github.com/ray-project/ray/pull/68056)) now implement the two parts.

### #67923 — Unchecked rank reassignment reconfigures
- **Problem:** When the controller renumbers replica ranks (for example, a 4 → 2 downscale turns ranks 2,3 into 0,1), `DeploymentState._reconfigure_replicas_with_new_ranks` sends a `reconfigure` RPC to a running replica and never checks the result. A failure left `serve.get_replica_context().rank` on the old value with no Serve log, a hang gave no signal at all, and the latency histogram was never recorded on success.
- **Fix:** Add a non-blocking `check_reconfigure_done()` and track renumbered replicas in `_pending_rank_reconfigures`. Each controller tick records the latency on success, logs a Serve error with the replica ID, new rank and traceback on failure, and warns once if the reconfigure is still running after `SLOW_STARTUP_WARNING_S`. Approved and merged on 2026-11-17.

### #68085 — Memory leak in the multiplexed model ID index (in review)
- **Problem:** Request routers never removed finished multiplexed requests from `_pending_requests_by_model_id`, so each one kept its `args`/`kwargs` alive. `FIFOMixin` routers (including the default `PowerOfTwoChoicesRequestRouter`) kept the last request of every model ID. Other routers (`ConsistentHashRouter`, `KVAwareRouter`, `PrefixCacheAffinityRouter`) kept every multiplexed request, so memory grew with traffic, for example on every LoRA request in Serve LLM. Cancelled requests were never cleaned up in any router.
- **Fix:** `_remove_pending_request_from_indices` now also pops finished entries from the front of the request's model ID deque, and drops the key once the deque is empty. The cost stays amortized O(1) and routing behavior doesn't change. Opened 2026-11-12.
