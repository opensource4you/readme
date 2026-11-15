# Ray Report

## Summary

From 2026/10/16 to 2026/11/15, I have **2 open Ray PRs** and **reviewed 1 Ray PR**.

## Open PRs

| PR | Description |
| -- | ----------- |
| [ray#68064](https://github.com/ray-project/ray/pull/68064) [Doc][KubeRay] Fix RayService upgrade step that leaves the new RayCluster unready | The RayService user guide changes `rayVersion` to trigger a zero downtime upgrade, but the new Pods then fail their health probes and the upgrade never finishes. The PR changes the step to update the head memory limit instead. |
| [ray#68079](https://github.com/ray-project/ray/pull/68079) [Doc][KubeRay] Fix serve Service name in RayService no-Serve-replica guide | The guide names a serve Service that does not exist. The PR adds the missing `-replica` to the name. |

## Reviews

| PR | Description |
| -- | ----------- |
| [ray#66929](https://github.com/ray-project/ray/pull/66929) [Data] Allow actor pool downscaling with pending actors (merged) | I pointed out that the pool might shrink too early after a big upscale. Cancelling pending actors also drops their resource requests, so the cluster autoscaler may not add the nodes the backlog needs. |
