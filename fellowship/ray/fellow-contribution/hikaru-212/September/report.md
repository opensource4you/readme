# September 2026 — Ray

**Reporting period:** 2026-09-01 to 2026-10-10

## Summary

- **PR:** 2 (2 under review)
- **Code Review:** 2
- **Technical Write-up:** 1

This period focused on Ray Core scheduling, recovery, and actor retry semantics,
with additional correctness reviews covering GCS restart reconciliation and
Ray Data error-contract behavior.

## Pull Requests

### Ray #65147 / PR #65970 — Placement Group recovery retry starvation

**Problem**

Repeated failed Placement Group recovery attempts could retain highest scheduling priority and delay unrelated pending placement groups.

**Work**

- Traced the GCS recovery and pending-queue scheduling path.
- Identified the fresh retry-state reset after failed `RESCHEDULING` attempts.
- Reused the existing exponential backoff for failed recovery retries while preserving top priority for fresh recovery.
- Added a RED → GREEN manager-level regression.
- Published a technical write-up explaining the failure mechanism and scheduling-policy tradeoff.

**Evidence**

- Issue: [ray-project/ray#65147](https://github.com/ray-project/ray/issues/65147)
- Pull request: [ray-project/ray#65970](https://github.com/ray-project/ray/pull/65970)
- Technical write-up: [Recovery Is Not Progress](https://medium.com/@tpa33.36.09.013/recovery-is-not-progress-5975d5aa4153)

**Status**

Under upstream review. The current discussion is whether strict recovery priority should remain across every failed retry or yield during backoff.

---

### Ray #44719 / PR #66818 — Actor restart × per-call retry policy mismatch

**Problem**

Restart-time task disposition and retry accounting can use different `max_task_retries` policies when the actor handle default and per-call override differ, causing inconsistent fast-fail / buffering behavior.

**Work**

- Traced the restart and retry-policy paths.
- Identified the handle-level vs task-level policy mismatch.
- Reproduced both mixed-policy cases.
- Implemented a queue-owned fix preserving ordering semantics.
- Added focused regression coverage.

**Evidence**

- Issue: [ray-project/ray#44719](https://github.com/ray-project/ray/issues/44719)
- Pull request: [ray-project/ray#66818](https://github.com/ray-project/ray/pull/66818)

**Status**

Open; ready for review.

---

## Code Reviews

### Ray #66326 — GCS restart reconciliation gate

**Problem**

The restart-reconciliation gate could be cleared after a non-OK `ReleaseUnusedActorWorkers` result, allowing new worker leases before cleanup had been established as successful.

**Work**

- Reviewed the GCS restart reconciliation path.
- Constructed a deterministic failure path where the node remains alive, cleanup returns `UNAVAILABLE`, but a later scheduling retry sends a new worker lease.
- Asked whether reconciliation should remain active until cleanup succeeds or node removal is confirmed.

**Evidence**

- Pull request: [ray-project/ray#66326](https://github.com/ray-project/ray/pull/66326)

**Status**

Review comment posted; upstream discussion pending.

---

### Ray #66878 — JSONL exception subtype preservation

**Problem**

The new JSONL error-context wrapper catches `ValueError`, but `UnicodeDecodeError` is a subclass of `ValueError`, potentially changing the public exception behavior for invalid UTF-8 input.

**Work**

- Reviewed the JSON/JSONL error-context handling.
- Reproduced the behavior in both chunk-size estimation and direct JSONL reading.
- Confirmed that input which previously raised `UnicodeDecodeError` becomes a plain `ValueError`.
- Asked whether this normalization is intentional or whether the original subtype should be preserved consistently with the Arrow fallback.

**Evidence**

- Pull request: [ray-project/ray#66878](https://github.com/ray-project/ray/pull/66878)

**Status**

Review finding accepted by the author; the exception subtype will be preserved and regression coverage added.