# Ray Report

## Summary

From 2026/10/16 to 2026/11/15, I **reviewed 1 Ray PR**.

## Reviews

| PR | Description |
| -- | ----------- |
| [ray#66929](https://github.com/ray-project/ray/pull/66929) [Data] Allow actor pool downscaling with pending actors (merged) | I pointed out that the pool might shrink too early after a big upscale. Cancelling pending actors also drops their resource requests, so the cluster autoscaler may not add the nodes the backlog needs. |
