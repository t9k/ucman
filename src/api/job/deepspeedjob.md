# DeepSpeedJob

DeepSpeedJob 是服务于 <a target="_blank" rel="noopener noreferrer" href="https://www.deepspeed.ai/">DeepSpeed</a> 分布式训练框架的 T9k Job。

你可以较为方便地使用 DeepSpeedJob 为 DeepSpeed 训练脚本提供训练环境，并监控训练进程。

## 创建 DeepSpeedJob

下面是一个基本的 DeepSpeedJob 示例：

```yaml
apiVersion: batch.tensorstack.dev/v1beta1
kind: DeepspeedJob
metadata:
  name: deepspeed-example
spec:
  config:
    slotPerWorker: 1
    run:
      python: /t9k/mnt/train.py
  worker:
    replicas: 4
    template:
      spec:
        restartPolicy: OnFailure
        containers:
        - image: deepspeed/deepspeed:v072_torch112_cu117
          imagePullPolicy: IfNotPresent
          name: worker
          resources:
            limits:
              cpu: 4
              memory: 4Gi
            requests:
              cpu: 2
              memory: 2Gi
          volumeMounts:
          - mountPath: /t9k/mnt
            name: code
        volumes:
        - name: code
          persistentVolumeClaim:
            claimName: deepspeed
```

在该例中：

* 创建 4 个训练副本，这些副本会启动 `sshd` 服务。
* 第一个副本会启动 `deepspeed` 程序，该程序会通过 `pdsh`（或其他方式）访问 4 个副本，并在每个副本上运行 `/t9k/mnt/train.py` 脚本。

<aside class="note">
<div class="title">注意</div>

一个副本中可以创建多个容器，DeepSpeedJob 需要确定哪一个容器才是训练容器。如果 `spec.worker.template` 中包含 `name` 为 `worker` 的 container，则该容器为训练容器；如果没有，会选取第一个 container 作为训练容器。

DeepSpeedJob 中的执行程序应是使用 DeepSpeed 框架的程序，否则可能达不到训练效果。

用户挂载文件时，需要避开下列路径，否则会导致 DeepSpeedJob 不能正常运行：`/root/.ssh`、`/t9k/hostfile`、`/root/.deepspeed_env`。

</aside>

## 副本设置

DeepSpeedJob 副本运行环境和命令可以通过 `spec.worker.template` 进行配置，可配置内容包括镜像、运行命令、资源配置、环境变量等。

### 资源配置

副本资源配置通过 `spec.worker.template.spec.containers[*].resources` 字段指定。

DeepSpeedJob 的资源配置包括两部分：

* 资源请求量（`requests`）：创建该副本时，节点上至少应具有这些数量的资源。如果集群中所有节点都不满足副本的资源请求量，则副本的创建可能会被阻塞；或者如果副本的优先级较高，则有可能驱逐节点上其他工作负载来为副本空出可用的资源。
* 资源上限（`limits`）：该副本在运行期间，最多可以使用的资源数量。比如：如果副本在运行时申请分配超过上限的内存，则有可能出现 `OOMKILLED` 错误。（注：资源上限不能小于资源请求量）

在下面的示例中，DeepSpeedJob 中每个副本设置了以下资源配置：

* 资源请求量：2 个 cpu 核心、2Gi 内存；
* 资源上限：4 个 cpu 核心、4Gi 内存。

```yaml
apiVersion: batch.tensorstack.dev/v1beta1
kind: DeepSpeedJob
metadata:
  name: deepspeed-example
spec:
  worker:
    replicas: 4
    template:
      spec:
        containers:
        - resources:
            limits:
              cpu: 4
              memory: 4Gi
            requests:
              cpu: 2
              memory: 2Gi
```

#### 共享内存

在进行多节点任务时，可以按照如下方式修改 DeepSpeedJob 来使用共享内存：

```yaml
apiVersion: batch.tensorstack.dev/v1beta1
kind: DeepSpeedJob
metadata:
  name: deepspeed-example
spec:
  worker:
    replicas: 4
    template:
      spec:
        containers:
        - ...
          volumeMounts:
            - mountPath: /dev/shm
              name: dshm
        volumes:
        - name: dshm
          emptyDir:
            medium: Memory
            sizeLimit: "1Gi"
```

在该例中：

* 在 `spec.worker.template.spec.volumes` 中增加一项，名称为 `dshm`，其中限制共享内存最大为 `1Gi`；
* 在 `spec.worker.template.spec.containers[*].volumeMounts` 中增加一项，将上述 `dshm` 绑定到 `/dev/shm` 路径。

<aside class="note tip">
<div class="title">提示</div>

如果当前副本中设置了内存资源上限，则共享内存的大小不能超过副本的内存上限；如果副本没有设置内存资源上限，则共享内存的大小最大可以设置为当前所在节点内存的最大容量。

</aside>

### 环境变量

副本环境变量通过 `spec.worker.template.spec.containers[*].env` 字段指定。DeepSpeedJob 支持直接设置环境变量内容和引用其他资源字段作为环境变量两种方式。

在下面的示例中，DeepSpeedJob 给副本设置了两个环境变量：`ENV_DIRECT` 和 `ENV_REFERENCED`。其中 `ENV_DIRECT` 环境变量被直接设置为 `env-value`，`ENV_REFERENCED` 环境变量引用了 `secret-name` Secret 的 `key-in-secret` 字段的内容。

```yaml
apiVersion: batch.tensorstack.dev/v1beta1
kind: DeepSpeedJob
metadata:
  name: deepspeed-example
spec:
  worker:
    replicas: 4
    template:
      spec:
        containers:
          - env:
            - name: ENV_DIRECT
              value: env-value
            - name: ENV_REFERENCED
              valueFrom:
                secretKeyRef:
                  name: secret-name
                  key: key-in-secret
```

<aside class="note tip">
<div class="title">提示</div>

环境变量常被用于：

1. 设置网络代理：`HTTP_PROXY` 和 `HTTPS_PROXY`；
2. 设置额外的 Python 包和模块路径：`PYTHONPATH`；
3. 设置 C 语言静态库和共享库路径：`LIBRARY_PATH` 和 `LD_LIBRARY_PATH`；
4. ...

</aside>

<aside class="note tip">
<div class="title">提示</div>

更多环境变量相关配置，请参考 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/tasks/inject-data-application/">Inject Data Into Applications
</a>。

</aside>

## 训练配置

DeepSpeedJob 在 `spec.config` 中配置如何执行训练。有以下参数可以设置：

* `run`：如何启动训练，以下三个参数只能填写一个，否则报错：
    * `python`：使用 Python 脚本进行训练。指定 Python 文件以及启动参数。
    * `module`：使用 Python module 进行训练。指定 Python module 以及启动参数。
    * `exec`：使用可执行文件/命令进行训练。指定可执行文件以及启动参数。
* `slotsPerWorker`：每一个副本上设置多少个“插槽”。“插槽”是继承自 MPI 中的概念，表示一个副本上可以运行多少个训练进程。一般来说该值被设为每个副本分配的 GPU 数量。例如当创建了一个 `replica` 为 4 的任务，并且给每个副本分配了 2 个 `nvidia.com/gpu`，则应该将 `slotsPerWorker` 设为 2，这样最后一共会运行 `4 * 2 = 8` 个训练进程。
* `localRank`：是否传递 `LOCAL_RANK` 环境变量，默认为 `true`。
* `autotune`：启用超参数调优，可以设置为 `none`、`run`、`tune`，默认为 `none`。`none` 为不启动超参数调优；`tune` 只查找最合适的超参数组合，但是不执行训练；`run` 查找最合适的超参数组合，并用该超参数执行训练。
* `otherArgs`：设置其他 DeepSpeed 参数，详见下文。

### otherArgs

DeepSpeedJob 希望提供用户足够的灵活性，所以支持用户通过 `otherArgs` 字段设置传入 DeepSpeed 的参数。config 中的配置实际上也是通过 DeepSpeed 参数实现的，以下列出除了在配置文件中指定的参数之外的其他可用参数：

* `--launcher`： 多节点训练使用的启动器后端，目前的选项包括 PDSH、OpenMPI、MVAPICH、SLURM、MPICH。（默认：`pdsh`）。目前 DeepSpeedJob 只支持 `pdsh`。
* `--no_ssh_check`：多节点训练时不执行 ssh 检查。
* `--save_pid`： 在 `/tmp/<main-pid>.ds` 处保存包含启动器进程 ID（pid），其中 `<main-pid>` 是第一个调用 DeepSpeed 的进程的 pid。PDSH 模式下不支持。
* `--enable_each_rank_log`： 将每个 Rank 的 stdout 和 stderr 重定向到不同的日志文件。PDSH 模式下不支持。
* `--bind_cores_to_rank`：将每个 Rank 绑定到主机的不同核心。PDSH 模式下不支持。
* `--bind_core_list`：要绑定的核心列表，以逗号分隔。例如 `1,3-5,7 => [1,3,4,5,7]`。 未指定时，系统上的所有核心都将被绑定。PDSH 模式下不支持。

<aside class="note info">
<div class="title">信息</div>

config 中的配置实际上是通过 DeepSpeed 参数实现的，而 `otherArgs` 可以指定任意值，所以可能会造成冲突。以下列出了会导致冲突的参数，请勿在 `otherArgs` 中设置：

* `--no_local_rank`：与 `spec.config.localRank` 字段冲突。
* `--autotuning`：与 `spec.config.autotune` 字段冲突。
* `--module` 和 `--no_python`：与 `spec.config.autotune` 字段冲突。

</aside>

## 训练的成功和失败判定

DeepSpeedJob 分布式训练框架中，第一个训练副本（下文记为 `worker-0`）是分布式任务的主节点。当 `worker-0` 成功结束，则 DeepSpeedJob 训练成功；反之，当 `worker-0` 执行失败，DeepSpeedJob 训练失败。

如果一次训练执行时间过长，用户可能需要考虑代码是否需要优化、是否需要分配更多资源等问题。DeepSpeedJob 可以设置最长执行时间（由 `spec.runPolicy.activeDeadlineSeconds` 字段指定），当超过这个执行时间后，训练失败。

## 清除策略

在训练完毕后，可能有些副本仍处于运行状态。这些运行的副本仍然会占用集群资源，DeepSpeedJob 提供清除策略，可以在训练结束后删除这些训练副本。

DeepSpeedJob 提供以下三种策略：

* `None`：不删除副本。
* `All`：删除所有副本。
* `Unfinished`：只删除未结束的副本。

<aside class="note tip">
<div class="title">提示</div>

已结束的副本不会继续消耗集群资源，因此在一定程度上，`Unfinished` 策略比 `All` 策略更优。但这并不总是适用，由于一个项目的资源配额的计算不考虑 Pod 是否已经结束，对于资源紧张的项目，如果确定不需要通过日志来调试 Job，则可以使用 `All` 策略。

</aside>
    
    `None` 策略主要用于训练脚本调试阶段。如果需要从副本中读取训练日志，则可以选用此策略。但由于这些副本可能占用资源并影响后续训练，建议你在调试完毕后手动删除这些副本或删除整个 DeepSpeedJob。

## 调度策略

目前 DeepSpeedJob 支持两种调度策略：

1. Kubernetes 的<a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/#kube-scheduler">默认调度器</a>
2. [T9k Scheduler](../../../../t9k-scheduler/)

调度策略通过 CRD 的 `spec.scheduler` 字段设置：

* 不设置 `spec.scheduler` 字段，则默认使用 Kubernetes 的默认调度策略。
* 设置 `spec.scheduler.t9kScheduler` 字段，则使用 T9k Scheduler 调度器。

在下面的示例中，MPIJob 启用 T9k Scheduler 调度器，将副本插入 `default` 队列中等待调度，其优先级为 50。

```yaml
...
spec:
  scheduler:
    t9kScheduler:
      queue: default
      priority: 50
```

<aside class="note info">
<div class="title">信息</div>

队列和优先级都是 T9k Scheduler 的概念，具体含义请参阅 [T9k Scheduler](../scheduling/index.md)。

</aside>

## 调试模式

DeepSpeedJob 支持调试模式。在该模式下，训练环境会被部署好，但不会启动训练，用户可以连入副本测试环境或脚本。

该模式可以通过 `spec.runMode.debug` 字段来设置：

* `spec.runMode.debug.enabled` 表示是否启用调试模式。
* `spec.runMode.debug.replicaSpecs` 表示如何配置各个副本的调试模式：
    * `spec.runMode.debug.replicaSpecs.type` 表示作用于的副本类型。
    * `spec.runMode.debug.replicaSpecs.skipInitContainer` 表示让副本的 InitContainer 失效，默认为 `false`。
    * `spec.runMode.debug.replicaSpecs.command` 表示副本在等待调试的时候执行的命令，默认为 `/usr/sbin/sshd -D`。
    * 如果不填写 `spec.runMode.debug.replicaSpecs` 字段，则表示所有副本都使用默认设置。

在下面的示例中：

* 示例一：开启了调试模式，并配置 worker 跳过 InitContainer，并执行 `sleep inf`。
* 示例二：开启了调试模式，副本使用默认调试设置，即不跳过 InitContainer，并执行 `/usr/sbin/sshd -D`。

```yaml
# 示例一
...
spec:
  runMode:
    debug:
      enabled: true
      replicaSpecs:
        - type: worker
          skipInitContainer: true
          command: ["sleep", "inf"]

---
# 示例二
...
spec:
  runMode:
    debug:
      enabled: true
```

## 暂停模式

DeepSpeedJob 支持暂停模式。在该模式下，删除（或不创建）副本，停止训练。

该模式可以通过 `spec.runMode.pause` 字段来设置：

* `spec.runMode.pause.enabled` 表示是否启用暂停模式。
* `spec.runMode.pause.resumeSpecs` 表示结束暂停后，如何恢复各个副本：
    * `spec.runMode.pause.resumeSpecs.type` 表示作用于的副本类型。
    * `spec.runMode.pause.resumeSpecs.skipInitContainer` 表示让副本的 InitContainer 失效，默认为 `false`。
    * `spec.runMode.pause.resumeSpecs.command` 和 `spec.runMode.pause.resumeSpecs.args` 表示副本在恢复运行时候执行的命令，默认使用 `spec.replicaSpecs[0].template` 中的命令。
    * 如果不填写 `spec.runMode.pause.resumeSpecs` 字段，则表示所有副本都使用默认设置。

用户可以随时修改 `spec.runMode.pause.enabled` 来控制任务暂停，但是不可以更改 `spec.runMode.pause.resumeSpecs`，所以如果有暂停 DeepSpeedJob 的需求，请提前设置好恢复设置。

在下面的示例中：

* 示例一：开启了暂停模式，并配置 worker 跳过 InitContainer，并执行 `/usr/bin/sshd`。
* 示例二：开启了暂停模式，副本使用默认恢复设置，即不跳过 InitContainer，并执行 `spec.replicaSpecs[0].template` 中设置的命令。

```yaml
# 示例一
...
spec:
  runMode:
    pause:
      enabled: true
      resumeSpecs:
        - type: worker
          skipInitContainer: true
          command: ["/usr/bin/sshd"]

---
# 示例二
...
spec:
  runMode:
    pause:
      enabled: true
```

## DeepSpeedJob 状态

### DeepSpeedJob 的状态和阶段

`status.conditions` 字段用于描述当前 DeepSpeedJob 的状态，包括以下 5 种类型：

1. `Initialized`：DeepSpeedJob 已经成功创建各子资源，完成初始化。
2. `Running`：开始执行任务。
3. `ReplicaFailure`：有一个或多个副本出现错误。
4. `Completed`：DeepSpeedJob 成功。
5. `Failed`：DeepSpeedJob 失败。

`status.phase` 字段用于描述当前 DeepSpeedJob 所处的阶段，DeepSpeedJob 的整个生命周期主要有以下几个阶段：

1. `Pending`：DeepSpeedJob 刚刚创建，等待副本启动。
2. `Running`：副本创建成功，开始执行任务。
3. `Succeeded`：DeepSpeedJob 成功。
4. `Failed`：DeepSpeedJob 失败。
5. `Unknown`：控制器无法获得 DeepSpeedJob 的阶段。

在下面的示例中，DeepSpeedJob 所有子资源创建成功，所以类型为 `Initalized` 的 `condition` 被设为 `True`；DeepSpeedJob 运行结束，所以类型为 `Completed` 的 `condition` 被设置为 `True`；但是 DeepSpeedJob 的训练结果是失败的，所以类型为 `Failed` 的 `condition` 被设置为 `True`。当前 DeepSpeedJob 运行阶段为 `Failed`。

```yaml
...
status:
  conditions:
    - lastTransitionTime: "2021-01-18T02:36:09Z"
      status: "True"
      message: "The job has been initialized successfully."
      reason: "-"
      type: Initializing
    - lastTransitionTime: "2021-01-18T02:36:09Z"
      status: "True"
      message: "All pods are running normally."
      reason: "-"
      type: Running
    - lastTransitionTime: "2021-01-18T02:36:09Z"
      status: "False"
      message: "All pods are running normally."
      reason: "-"
      type: ReplicaFailure
    - lastTransitionTime: "2021-01-18T02:36:31Z"
      status: "False"
      message: "The job exited with an error code."
      reason: "Failed"
      type: Completed
    - lastTransitionTime: "2021-01-18T02:36:31Z"
      status: "True"
      message: "The job exited with an error code."
      reason: "Failed"
      type: Failed
  phase: Failed
```

### 副本的状态

`status.tasks` 字段用来记录副本的状态，记录的内容主要包括：

* 副本的重启次数（同一种角色的副本的重启次数之和）
* 副本当前的运行阶段
* 副本在集群中对应的 Pod 的索引信息

在下面的示例中，DeepSpeedJob 创建了 2 个训练副本，当前均处于 `Running` 阶段，分别运行在 `deepspeed-example-worker-0` 和 `deepspeed-example-worker-1` 这 2 个 Pod 上。

```yaml
...
status:
  tasks:
  - type: worker
    restartCount: 0
    status:
    - phase: Running
      name: deepspeed-example-worker-0
      uid: e3ec2ee3-6645-4e21-993f-1e472b94e0ae
      containers: []
    - phase: Running
      name: deepspeed-example-worker-1
      uid: 908a93f0-7b8b-491e-85d5-3da0abcb4ca4
      containers: []
```

### 副本状态统计

`status.aggregate` 字段统计了各个阶段的副本数量。

在下面的示例中，DeepSpeedJob 创建了 3 个副本，其中 1 个处于 `Pending` 阶段，另外两个处于 `Running` 阶段。

```yaml
...
status:
  aggregate:
    creating: 0
    deleted: 0
    failed: 0
    pending: 1
    running: 2
    succeeded: 0
    unknown: 0
...
```
