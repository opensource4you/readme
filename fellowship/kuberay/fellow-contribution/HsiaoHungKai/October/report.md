# KubeRay Report

## Summary

From 2026/10/16 to 2026/11/15, I have **2 KubeRay PRs merged**, have **6 open PRs** (4 in KubeRay and 2 in Ray), **opened 3 KubeRay issues**, and **reviewed 8 PRs** (7 in KubeRay and 1 in Ray).

## Merged PRs

| PR | Description |
| -- | ----------- |
| [kuberay#5519](https://github.com/kuberay-project/kuberay/pull/5519) [History Server] Fix jobs_test build after putSnapshot signature change | I found this problem when the CI of my own PR failed on GitHub. Two merged PRs touched the same call: [kuberay#5467](https://github.com/kuberay-project/kuberay/pull/5467) added two parameters to `SessionLoader.putSnapshot`, and [kuberay#5484](https://github.com/kuberay-project/kuberay/pull/5484) added `jobs_test.go` with a call that still used the old signature. Each PR passed CI on its own, and GitHub merged the second one without checking it against the new signature, so the test package did not compile on master. The fix passes `utils.ClusterInfo{}, false` in the call, the same values the other tests use. |
| [kuberay#5097](https://github.com/kuberay-project/kuberay/pull/5097) [Refactor] Share RayCluster across mTLS e2e checks | The auto-generated mTLS e2e test created a separate RayCluster for each check. The PR reuses one RayCluster for all checks and keeps the same coverage. |

## Open PRs

| PR | Description |
| -- | ----------- |
| [kuberay#5464](https://github.com/kuberay-project/kuberay/pull/5464) [ray-operator] Ignore NotFound when deleting unhealthy Pods | I came across this problem when I was reading the RayCluster controller. The Pod deletion paths in `reconcilePods` each handle a `NotFound` error in a different way. I had a short discussion with **Nary** and opened [kuberay#5446](https://github.com/kuberay-project/kuberay/issues/5446). After I created the PR, I talked about it at the KubeRay community sync. The PR moves all five deletion paths to one helper, `deletePod`, which treats `NotFound` as success. |
| [kuberay#5491](https://github.com/kuberay-project/kuberay/pull/5491) [ray-operator] Fail RayJob when the selected RayCluster is suspended | I found this problem when I was reading the RayJob controller. A RayJob with `clusterSelector` stays in `Initializing` forever when the selected RayCluster is suspended. Before I submitted a PR, I opened [kuberay#5476](https://github.com/kuberay-project/kuberay/issues/5476) and discussed it with **Justin** and **Nick**. I proposed three solutions: emit an event, fail the RayJob, or unsuspend the selected RayCluster. Before the community sync we leaned toward emitting an event, because it is the easiest solution and does not break any current logic. During the community sync, **KaiHsun** suggested failing the RayJob immediately. After I looked into it more and discussed it with the reviewers, I think this is the right choice. `clusterSelector` expects a running cluster, and failing the RayJob does little harm because it is still in `Initializing`. |
| [kuberay#5511](https://github.com/kuberay-project/kuberay/pull/5511) [Config][Test] Replace nightly Ray image with 2.60.0 for idle termination sample and e2e test | The idle termination sample and e2e test pinned a nightly Ray image. Ray 2.60.0 is now released, so the PR switches both to `rayproject/ray:2.60.0`. I tested it manually with the doc example. After that I opened the follow-up issue [kuberay#5512](https://github.com/kuberay-project/kuberay/issues/5512). |
| [kuberay#5518](https://github.com/kuberay-project/kuberay/pull/5518) [Test] Lower Locust warmup RPS threshold to deflake incremental upgrade e2e | The Locust warmup waits for RPS ≥ 400, but slower CI runners top out around 360–400, so the incremental upgrade tests were flaky. The PR lowers the threshold to 300. |
| [ray#68064](https://github.com/ray-project/ray/pull/68064) [Doc][KubeRay] Fix RayService upgrade step that leaves the new RayCluster unready | The RayService user guide changes `rayVersion` to trigger a zero downtime upgrade, but the new Pods then fail their health probes and the upgrade never finishes. The PR changes the step to update the head memory limit instead. |
| [ray#68079](https://github.com/ray-project/ray/pull/68079) [Doc][KubeRay] Fix serve Service name in RayService no-Serve-replica guide | The guide names a serve Service that does not exist. The PR adds the missing `-replica` to the name. |

## Issues

| Issue | Description |
| ----- | ----------- |
| [kuberay#5446](https://github.com/kuberay-project/kuberay/issues/5446) [Feature] Handle NotFound the same way at every RayCluster Pod deletion path | `reconcilePods` deletes Pods in five places, and each one handles `NotFound` differently. Fixed by kuberay#5464. |
| [kuberay#5476](https://github.com/kuberay-project/kuberay/issues/5476) [Bug] RayJob with clusterSelector stays in Initializing forever when the selected RayCluster is suspended | The RayJob waits for a suspended RayCluster that never becomes `Ready`, and nothing tells the user why. Fixed by kuberay#5491. |
| [kuberay#5512](https://github.com/kuberay-project/kuberay/issues/5512) [Test] Remove idleTerminationRayImage override after default e2e Ray version is upgraded to 2.60.0 | The idle termination e2e test sets its own Ray image because the shared e2e default is still 2.55.0. The override can be removed after the default moves to 2.60.0. |

## Reviews

| PR | Description |
| -- | ----------- |
| [ray#66929](https://github.com/ray-project/ray/pull/66929) [Data] Allow actor pool downscaling with pending actors (merged) | I pointed out that the pool might shrink too early after a big upscale. Cancelling pending actors also drops their resource requests, so the cluster autoscaler may not add the nodes the backlog needs. |
| [kuberay#5409](https://github.com/kuberay-project/kuberay/pull/5409) ray-operator: apply replica bounds to desired resource totals | Two callers divide by `NumOfHosts` again right after the changed function multiplies by it. I suggested exporting the helper and using it directly in both places. |
| [kuberay#5416](https://github.com/kuberay-project/kuberay/pull/5416) feat: add headFailurePolicy to RayJob for fast-fail on head pod | I asked for validation test cases for `OnFailure`, which should be rejected but was not covered. |
| [kuberay#5427](https://github.com/kuberay-project/kuberay/pull/5427) fix(raycluster): recognize autoscaling minimum readiness | I pointed out that the readiness checks iterate over the Pods twice. I also asked whether it is intended that the cluster becomes `Ready` when worker Pod creation always fails. |
| [kuberay#5465](https://github.com/kuberay-project/kuberay/pull/5465) Fix/remove rayjob legacy fields | I asked for the reason behind changing `Policy` from a pointer type to a value type. |
| [kuberay#5481](https://github.com/kuberay-project/kuberay/pull/5481) helm: preserve maxReplicas: 0 in the RayCluster chart (merged) | Approved. It fixes the Helm chart rendering `maxReplicas: 0` as `2147483647`. |
| [kuberay#5487](https://github.com/kuberay-project/kuberay/pull/5487) [Bug] Recognise the DRA implicit extended resource name in GPU detection | Approved with a nit: only one of the three callers lowercases the resource key, so I suggested normalizing it inside `IsGPUResourceKey`. |
| [kuberay#5530](https://github.com/kuberay-project/kuberay/pull/5530) kubectl-plugin: fix entrypoint resources flag | I reproduced the failure on master with a kind cluster, where the submit step fails with `No such option '--entrypoint-resource'`. With the PR the job is submitted successfully, so I approved it. |
