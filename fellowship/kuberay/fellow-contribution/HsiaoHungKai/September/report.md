# KubeRay Report

### Contribution

- I reviewed kuberay#5305. The PR fixes kuberay#5301, where KubeRay endlessly recreates a Pod that YuniKorn failed with `ResourceReservationTimeout`. The PR changes `HasResourceReservationTimeoutPods` in `util.go`. It also changes `calculateStatus`, `reconcilePods` and `shouldDeletePod` in `raycluster_controller.go`. 

### Learn

- I understand the bug and the fix in kuberay#5305. A Pod in the `Failed` phase is terminal. If the operator recreates it against the same dead `applicationId`, the job stays stuck. The fix leaves those Pods alone and shows the failure in the `RayCluster` and `RayJob` status.
- I understand `BatchScheduler`. It adds gang scheduling, queues and priorities for RayCluster and RayJob Pods. Its interface has four functions: `Name`, `DoBatchSchedulingOnSubmission`, `AddMetadataToChildResource` and `CleanupOnCompletion`. YuniKorn, Kueue and Volcano each implement it.
- I understand that YuniKorn is a Kubernetes scheduler for batch workloads. KubeRay uses it as a `BatchScheduler`, so a RayCluster CR can ask for gang scheduling and a queue.
- I understand the idle timeout. It lives in the chain `RayClusterSpec`, `AutoscalerOptions`, `idleTimeoutSeconds`. The operator does not read it. The Ray Autoscaler reads it. Kueue drives suspension.
