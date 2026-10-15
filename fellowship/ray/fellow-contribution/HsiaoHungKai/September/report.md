# Ray Report

## September

### Contribution

- I track the Ray issue "Rewrite ThreadedRayInstaller to Use Asyncio". It comes from a TODO at `autoscaler.py:167`. I traced the install flow, from the autoscaler tick to the subscriber `notify` call, followed by an "install Ray on nodes" step.
- I reviewed ray-project/ray#65171. The PR removes `_compute_min_resource_demand`, which `_try_schedule` uses.
- I reviewed ray-project/ray#65763. The PR adds `idleTerminationOptions` support to RayCluster. It merged.

### Learn

- I understand the autoscaler tick. `Autoscaler.update_autoscaling_state` reads the cluster state and calls `Reconciler.reconcile`. This method has two phases. First it syncs state from the cloud provider, the Ray cluster and the installers, with no side effects. Then it steps each instance forward.
- I understand the install path. `Reconciler._install_ray` sends RAY_INSTALLING events for allocated, running, non-head instances to `InstanceManager.update_instance_manager_state`. That method checks the expected version, writes to instance storage, and notifies every subscriber. This is how `ThreadedRayInstaller` starts.
- I understand the versions. The storage keeps one version counter for the whole instance table. Each instance keeps the storage version of its last write. A mismatch returns VERSION_MISMATCH and writes nothing.
- I understand `InMemoryStorage`. The `_tables` values are `VersionedValue` objects. Each one holds `instance.SerializeToString()` bytes, not a raw `Instance` protobuf, and the version of its last write. The `_lock` is a `threading` lock. It makes each read or write one uninterrupted step, so two threads cannot both pass the version check and overwrite each other.
- I understand the storage methods. `batch_update` checks `expected_version`, adds 1 to the version and writes the new `VersionedValue` objects. `get` returns the bytes and the version of each entry. `InstanceStorage.get_instances` rebuilds the instances with `ParseFromString()` and applies `status_filter`.
- I understand `ThreadedRayInstaller`. Its `notify` method finds the RAY_INSTALLING events and submits each worker to a `ThreadPoolExecutor`. Each install tries `install_ray` up to `_max_install_attempts` times (default 3).
- I understand where the config comes from. `AutoscalingConfig` serves the docker, setup and `ray start` config. `monitor.py` picks a `ReadOnlyProviderConfigReader` without `--autoscaling-config` and a `FileConfigReader` with it.
- I understand `NodeUpdater`. It syncs files and runs the init, setup and `ray start` commands on a node. `do_update` skips the setup only when the stored runtime hash and file-mounts contents hash both match. Otherwise it sets the node tags and syncs the file mounts with rsync.
- I understand the Asyncio model that I want to use in place of the install threads. A coroutine does not run until the code awaits it. `await` pauses it so other work can run. The event loop then picks the next ready task.

## October

### Contribution

- I keep working on the Ray issue "Rewrite ThreadedRayInstaller to Use Asyncio". The flow now ends with the subscriber `notify` call, followed by an "install Ray on nodes" step. 

### Learn

- I understand `InMemoryStorage`. The `_tables` values are `VersionedValue` objects. Each one holds `instance.SerializeToString()` bytes, not a raw `Instance` protobuf, and the version of its last write. The `_lock` is a `threading` lock. It makes each read or write one uninterrupted step, so two threads cannot both pass the version check and overwrite each other.
- I understand the storage methods. `batch_update` checks `expected_version`, adds 1 to the version and writes the new `VersionedValue` objects. `get` returns the bytes and the version of each entry. `InstanceStorage.get_instances` rebuilds the instances with `ParseFromString()` and applies `status_filter`.
- I understand `ThreadedRayInstaller`. Its `notify` method finds the RAY_INSTALLING events and submits each worker to a `ThreadPoolExecutor`. Each install tries `install_ray` up to `_max_install_attempts` times (default 3).
- I understand where the config comes from. `AutoscalingConfig` serves the docker, setup and `ray start` config. `monitor.py` picks a `ReadOnlyProviderConfigReader` without `--autoscaling-config` and a `FileConfigReader` with it.
- I understand `NodeUpdater`. It syncs files and runs the init, setup and `ray start` commands on a node. `do_update` skips the setup only when the stored runtime hash and file-mounts contents hash both match. Otherwise it sets the node tags and syncs the file mounts with rsync.
- I understand the Asyncio model that I want to use in place of the install threads. A coroutine does not run until the code awaits it. `await` pauses it so other work can run. The event loop then picks the next ready task.
