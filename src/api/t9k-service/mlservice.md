# MLService

MLService 用于在 TensorStack AI 平台上部署 AI 推理服务，其功能全面，可用于生产环境。

## 概述

`MLService` 是推理服务的核心 API，由 `releases` 和 `transformer` 两部分构成：

- `releases`：定义一个或多个版本的模型推理服务。
- [可选]`transformer`：定义前处理（pre-processing）和后处理（post-processing）计算。

<figure class="architecture">
  <img alt="mlservice-architecture" src="../../assets/api/t9k-service/mlservice-flow.drawio.svg" class="architecture">
  <figcaption> 图 1: MLService 的组成。一个 MLService 由一个或多个模型服务版本（releases） 及前后处理模块（transformer，非必需）构成；不同的 release 和 transformer 可独立进行规模伸缩。</figcaption>
</figure>

`MLService` 的主要特性包括：

- 支持定义多个版本（`release`）的推理服务，每个 `release` 定义了下列内容：
    - release 名称：推理服务的版本名称
    - 模型存储（`storage`）
    - 模型规约（`model`），包括 `parameters`，`runtime`（引用 `MLServiceRuntime` 定义运行推理服务 `Pod` 的模板）
    - 计算资源（`containersResources`）
    - 其他部署参数（`minReplicas, maxRelicas, logger ...`）
- 每个 `release` 服务的容量可独立自动伸缩，可通过 `minReplicas`、`maxReplicas` 设置容量的上下限。
- 支持<a target="_blank" rel="noopener noreferrer" href="https://en.wikipedia.org/wiki/Feature_toggle#Canary_release">金丝雀（canary release）</a>发布模式。
- 用户可定制 `transformer` 组件，以在调用推理服务时进行前处理（pre-processing），以及获得推理结果后进行后处理（post-processing）。
- `transformer` 的容量也可独立自动伸缩，可通过 `minReplicas`、`maxReplicas` 设置容量的上下限。

## 示例

下面是一个基本的 MLService 示例：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: MLService
metadata:
  name: torch-mnist
spec:
  default: version1
  releases:
    - name: version1
      predictor:
        minReplicas: 1
        maxReplicas: 3
        model:
          parameters:
            "MODEL_PATH": "mnist=model.mar"
          runtime: torchserve
        storage:
          pvc:
            name: tutorial
            subPath: tutorial-examples/deployment/pvc/mlservice-torch/
        containersResources:
        - name: user-container
          resources:
            limits:
              cpu: "500m"
              memory: 1Gi
```

<aside class="note info">
<div class="title">信息</div>

该示例部署的推理服务 `torch-mnist`，只包含一个推理服务版本（`release`） `version1`：

- 推理服务定义使用了 MLServiceRuntime `torchserve`，其详细定义见下文。
- 模型存储在 PVC `tutorial` 中。
- 服务的部署规模（副本数量）会根据服务负载情况自动调节，最小为 1，最大为 3。
- 运行模型推理服务器的副本（容器，container）的资源为：`{"limits": { "cpu": "500m", "memory": "1Gi"}}`。

</aside>

## MLServiceRuntime

在[示例](#示例)中，我们使用了 MLServiceRuntime `torchserve`。MLServiceRuntime 定义了推理服务的模板，包含了推理服务的关键信息，例如镜像、启动命令、资源需求等，能够方便地帮助用户快速部署多种模型推理服务程序。

一个 MLServiceRuntime 可以被多个 MLService 使用。

<aside class="note info">
<div class="title">注意</div>

创建 MLService 时必须设置其使用的 MLServiceRuntime。
</aside>


### 定义

一个基本的 MLServiceRuntime 的示例：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: MLServiceRuntime
metadata:
  name: torchserve
spec:
  enabled: true
  template:
    spec:
      containers:
      - name: user-container
        image: torchserve:0.9.0-cpu
        args:
          - torchserve
          - --start
          - --model-store=/var/lib/t9k/model
          - --models {{if .MODEL_PATH}}{{.MODEL_PATH}}{{else}}all{{end}}
        resources:
          limits:
            cpu: "200m"
            memory: 200Mi
        ports:
        - containerPort: 8080
          protocol: TCP
```

该 MLServiceRuntime 在 `spec.template` 中定义了推理服务的副本（Pod）的模板，以指定容器镜像 `torchserve:0.9.0-cpu`、启动命令 `torchserve` 及其他命令行参数等。

<aside class="note info">
<div class="title">信息</div>

MLServiceRuntime 中的 Pod 模板有以下规范必须遵守：

1. 必须要有一个名为 `user-container` 的容器，后续所介绍的[模型存储](#模型存储)、[日志收集](#日志收集)等功能都只对 `user-container` 生效。
2. `user-container` 的容器中最多只能定义一个 `containerPort`，且其他的容器定义中不能有 `containerPort`。
3. `user-container` 容器中定义的唯一 `containerPort` 就是推理服务对应的端口，如果没有定义，默认使用 `8080` 端口。

</aside>

### 使用

用户可以在 MLService 的 `predictor` 定义中指定要使用的 MLServiceRuntime 名称，例如：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: MLService
metadata:
  name: demo
spec:
  default: version1
  releases:
  - name: version1
    predictor:
      model:
        runtime: torchserve
      storage:
        pvc:
          name: <pvc-name>
          subPath: <model-path>
```

用户在 release `version1` 的 `.predictor.model.runtime` 中指定了 `torchserve`，系统在创建推理服务器副本（Pod）时，将会使用名称为 `torchserve` 的 MLServiceRuntime。

<aside class="note">
<div class="title">注意</div>

如果用户更新了一个 MLServiceRuntime，所有使用了该 MLServiceRuntime 的 MLService 所创建的副本（Pod）也会随之进行更新，但此更新采取 “懒惰” 策略：

- 单纯的 MLServiceRuntime 修改并不会触发系统修改 MLServiceRuntime 更新之前创建的副本（Pod）。
- 只会在有必要时，例如伸缩 MLService 规模，或者其他 MLService 的变更，导致需要重新创建副本的场景时，系统才会使用新的 MLServiceRuntime。

</aside>

### 进一步的配置

除了直接使用 MLServiceRuntime 定义好的 Pod 模板，MLService 还支持对其进行进一步的配置和修改。

#### Parameters

MLService 支持在 `predictor` 的 `.model.parameters` 设置参数，该字段是一个 map 类型，key 为参数名，value 为参数值。

在之前的 [MLServiceRuntime 示例](#定义) 中包含了 `--models {{if .MODEL_PATH}}{{.MODEL_PATH}}{{else}}all{{end}}` 的内容。这里使用了 <a target="_blank" rel="noopener noreferrer" href="https://pkg.go.dev/text/template">golang template</a> 的语法，含义为：

* 如果使用此 MLServiceRuntime 的 MLService 指定了 `MODEL_PATH`，这一行会被设置为 `--model <用户指定的 MODEL_PATH>`
* 如果没有指定 `MODEL_PATH`，这一行会被设置为 `--model all`

如下所示，在 MLService 中设置 `MODEL_PATH`：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: MLService
metadata:
  name: demo
spec:
  default: version1
  releases:
  - name: version1
    predictor:
      model:
        parameters:
          "MODEL_PATH": "mnist=model.mar"
        runtime: torchserve
      storage:
        pvc:
          name: <pvc-name>
          subPath: <model-path>
```

由上述 MLService 最终产生的副本（Pod）的 `args` 中会包含 `--model mnist=model.mar`，指定了使用模型的名称和文件。

#### StrategicMergePatch

MLServiceRuntime 定义了 Pod 模板，但不一定能适用于所有场景。MLService 支持用户在 MLServiceRuntime 的基础上，进行进一步的叠加修改，例如：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: MLService
metadata:
  name: demo
spec:
  default: version1
  releases:
  - name: version1
    predictor:
      model:
        parameters:
          "MODEL_PATH": "mnist=model.mar"
        runtime: torchserve
      storage:
        pvc:
          name: <pvc-name>
          subPath: <model-path>
      template:
        spec:
          containers:
            - name: user-container
              image: self-torchserve:latest
```

将上面 MLService 中 predictor `version1` 的 `template.spec` 和之前的 [Runtime 定义示例](#定义) 相比，
可以发现他们都定义了一个名为 `user-container` 的 container，但是 `image` 不同。
于是最终生成的 Pod 中，MLService 中定义的 `image` 会覆盖 MLServiceRuntime 中的 `image`，但是 MLServiceRuntime 中 `args` 等其余设置都会被保留。

<aside class="note warning">
<div class="title">警告</div>

使用 StrategicMergePatch 在 MLService 中定义容器时，不可以设置 `ports` 字段。否则会导致合并后的 Pod 中定义了多个 `port`。

</aside>

<aside class="note info">
<div class="title">信息</div>

这里的覆盖合并原则采用的是 StrategicMergePatch。
用户可以通过阅览以下参考资料，进一步了解  StrategicMergePatch：
* <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#use-a-strategic-merge-patch-to-update-a-deployment">Update API Objects in Place Using kubectl patch</a>
* <a target="_blank" rel="noopener noreferrer" href="https://pkg.go.dev/k8s.io/apimachinery/pkg/util/strategicpatch">strategicpatch - k8s.io/apimachinery/pkg/util/strategicpatch</a>

以下给出几个常用的示例：

1. 添加 container，containers 数组中不同名的都会被保留。

| MLServiceRuntime| MLService| Result|
|-|-|-|
|containers:<br>- name: user-container<br>&nbsp;&nbsp;...|containers:<br>- name: proxy<br>&nbsp;&nbsp;...|containers:<br>- name: user-container<br>&nbsp;&nbsp;...<br>- name: proxy<br>&nbsp;&nbsp;...|

2. 修改 image，相同名称 container 的 image 会被覆盖。

| MLServiceRuntime| MLService| Result|
|-|-|-|
|containers:<br>- name: user-container<br>&nbsp;&nbsp;image: torchserve:alpha|containers:<br>- name: user-container<br>&nbsp;&nbsp;image: torchserve:beta|containers:<br>- name: user-container<br>&nbsp;&nbsp;image: torchserve:beta|

3. 修改 args，相同名称 container 的 args 数组会整个被覆盖。

| MLServiceRuntime| MLService| Result|
|-|-|-|
|containers:<br>- name: user-container<br>&nbsp;&nbsp;args: ["--k1=v1", "--k2=v2"]|containers:<br>- name: user-container<br>&nbsp;&nbsp;args: ["--k2=v3"]|containers:<br>- name: user-container<br>&nbsp;&nbsp;args: ["--k2=v3"]|

</aside>

#### 计算资源

MLServiceRuntime 定义了 Pod 模板，但对于容器的资源要求，不同场景之间存在差异。因此， MLServiceRuntime 中定义的容器资源要求只是一个缺省时的默认值。

用户可以直接在 MLService `predictor` 中的 `containersResources` 定义容器的资源要求，例如：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: MLService
metadata:
  name: demo
spec:
  default: version1
  releases:
  - name: version1
    predictor:
      model:
        modelFormat:
          name: pytorch
      storage:
        pvc:
          name: <pvc-name>
          subPath: <model-path>
      containersResources:
      - name: user-container
        resources:
          limits:
            cpu: "500m"
            memory: 1Gi
```

<aside class="note info">
<div class="title">信息</div>

用户还可以使用上一节 [StrategicMergePatch](#strategicmergepatch) 定义容器资源要求，但 `containersResources` 的优先级更高，如果两者定义了同一个 container 的资源要求，`containersResources` 会完全覆盖 `template.spec` 中的值。

</aside>

## 模型存储

你可以为 release 和 transformer 定义模型存储：
1. 通过 `spec.releases[*].predictor.storage` 可以设置当前 release 的模型存储信息。
2. 通过 `spec.transformer.storage` 可以设置 transformer 的模型存储信息。

详情请见[模型存储](./storage.md)。

## Transformer

MLService 支持部署含有 `transformer` 模块的前处理（pre-processing）及后处理（post-processing）的推理服务：

* 预处理：用户发向推理服务的原始数据，先经过 transformer 预处理，然后再被发送到推理服务。
* 后处理：推理服务返回的预测结果，先经过 transformer 后处理，然后再返回给用户。

用户可以使用 [Tensorstack SDK](../../../tools/python-sdk-t9k/index.md) 编写 transformer 代码，制作镜像，并基于该镜像创建含有 transformer 的推理服务。详细示例请参阅[制作并部署含有 Transformer 的模型推理服务](../../tasks/deploy-mlservice-transformer.md)。

下文是一个设置了 transformer 的 MLService 示例：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: MLService
metadata:
  name: pic-mnist
spec:
  default: origin
  transformer:
    minReplicas: 1
    template:
      spec:
        containers:
        - name: user-container
          image: t9kpublic/transformer-example:0.1.0
          resources:
            limits:
              cpu: "500m"
              memory: 500Mi
  releases:
    - name: origin
      predictor:
        minReplicas: 1
        model:
          runtime: t9k-tensorflow-serving
        containersResources:
        - name: user-container
          resources:
            limits:
              cpu: "500m"
              memory: 500Mi
        storage:
          pvc:
            name: tutorial
            subPath: tutorial-examples/deployment/mlservice/transformer/model
```

## 全局路由配置

MLService 提供了一个全局的 URL，用户可以通过这个 URL 来访问 MLService 部署的推理服务(详情见[访问推理服务](#访问推理服务))。当用户向这个 URL 发送请求时，MLService 会根据全局路由配置将用户请求转发到对应版本的推理服务。

全局路由配置最多可以设置两个版本（release）的推理服务来处理用户请求，其中一个推理服务版本作为默认版本，另一个推理服务版本作为金丝雀版本：
1. 默认版本：必需。将 `spec.default` 字段设置为 release 名称来表明将哪个 release 设置为路由的默认版本。
2. [可选]金丝雀版本：将 `spec.canary` 字段设置为 release 名称来表明将哪个 release 设置为路由的金丝雀版本。设置金丝雀版本的同时，你必须设置 `spec.canaryTrafficPercent` 字段，来配置金丝雀版本的路由权重。

下面是一个 MLService 示例，在这个示例中：
1. 部署了 3 个版本的推理服务，版本名称分别是：nov-02，nov-05，nov-11。
2. 全局路由配置：nov-02 设置为路由的默认版本，路由权重是 80%；nov-11 设置为路由的金丝雀版本，路由权重是 20%。

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: MLService
metadata:
  name: multi-releases
spec:
  default: nov-02
  canary: nov-11
  canaryTrafficPercent: 20
  releases:
  - name: nov-02
    predictor:
      model:
        runtime: torchserve
      storage:
        pvc:
          name: tutorial
          subPath: model-11-02
  - name: nov-05
    predictor:
      model:
        runtime: torchserve
      storage:
        pvc:
          name: tutorial
          subPath: model-11-05
  - name: nov-11
    predictor:
      model:
        runtime: torchserve
      storage:
        pvc:
          name: tutorial
          subPath: model-11-11
```

## 更多配置

### 发布策略

#### 多版本支持

一个 MLService 可以同时部署多个版本（release）的推理服务，以使用不同的模型文件，或者其他配置等。

在下面的示例中，MLService 同时部署了 `nov-02`（设置为默认）、`nov-05` 和 `nov-11` 三个版本的服务，这三个版本都使用同一个 MLServiceRuntime，但是使用了不同的模型：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: MLService
metadata:
  name: multi-releases
spec:
  default: nov-02
  releases:
  - name: nov-02
    predictor:
      model:
        runtime: torchserve
      storage:
        pvc:
          name: tutorial
          subPath: model-11-02
  - name: nov-05
    predictor:
      model:
        runtime: torchserve
      storage:
        pvc:
          name: tutorial
          subPath: model-11-05
  - name: nov-11
    predictor:
      model:
        runtime: torchserve
      storage:
        pvc:
          name: tutorial
          subPath: model-11-11
```

#### 金丝雀发布

MLService 支持金丝雀（canary release）发布策略。用户可以通过 `spec.canary` 字段设置金丝雀发布对应的模型版本（`release`），`spec.canaryTrafficPercent` 字段设置金丝雀发布的路由权重。`spec.default` 是必需字段，用于设置默认发布。

例如上一节中我们部署了 3 个版本的模型，我们想主要（`80%` 流量）使用 `nov-02` 这个版本，并且将刚刚训练好的 `nov-11` 作为金丝雀版本：

* 默认发布：`nov-02`，路由权重为 80%。
* 金丝雀发布：`nov-11`，路由权重为 20%。

```yaml
...
spec:
  default: nov-02
  canary: nov-11
  canaryTrafficPercent: 20
...
```

### 日志收集

MLService 支持对预测请求进行日志收集，详情见[日志收集](./mlservice-logger.md)

### 容量伸缩

MLService 支持自动伸缩服务容量：即根据服务负载的变化，自动调节推理服务的部署规模（副本数量）。具体原理可以查看 <a target="_blank" rel="noopener noreferrer" href="https://knative.dev/docs/serving/autoscaling/">Knative Autoscaling</a>。

用户可以通过设置 `spec.releases[*].predictor.minReplicas` 字段和 `spec.releases[*].predictor.maxReplicas` 字段来指定 Predictor 工作负载数量的下限和上限。

同样的，如果用户启用了 Transformer，可以通过 `spec.transformer.minReplicas` 字段和 `spec.transformer.maxReplicas` 字段来指定 Transformer 工作负载数量的下限和上限。

以下是一些特殊情况：

* `minReplicas` 不填时，工作负载数量的默认最小值为 1。
* `minReplicas` 等于 0 时，当没有流量请求时，MLService 会缩容到 0，不再占用系统资源。
* `maxReplicas` 不填或设为 0 时，工作负载数量没有上限。

除了负载数量的限制，用户还可以在具体的 Runtime 或者组件（Predictor 或者 Transformer）的 Pod 定义中设置 Knative Autoscaling 相关的 Annotation，例如：

```yaml
...
spec:
  releases:
    - name: version1
      minReplicas: 1
      maxReplicas: 3
      predictor:
        template:
          metadata:
            annotations: 
              autoscaling.knative.dev/metric: "rps"
              autoscaling.knative.dev/target: "100"
...
```

在上面的示例中，我们设置了工作负载数量的范围为 `[1,3]`，自动伸缩指标(metric)为 `rps`，自动伸缩目标(target)为 `100`。这意味着当每个工作负载每秒处理的请求数量（requests-per-second）达到 100 时，负载会开始扩容，且扩容最大数量为 3，最小数量为 1。

### 调度器

MLService 支持使用两种调度器：Kubernetes 默认调度器（默认）和 <a target="_blank" rel="noopener noreferrer" href="https://t9k.github.io/user-manuals/latest/modules/computing-resources/scheduler/index.html">T9k Scheduler</a>。MLService 通过 `spec.scheduler` 字段设置调度器：

* 不设置 `spec.scheduler` 字段，则默认使用 Kubernetes 默认调度器。
* 设置 `spec.scheduler.t9kScheduler` 字段，则使用 T9k Scheduler 调度器。

在下面的示例中，使用了 T9k Scheduler 调度器，且工作负载处于队列 `default` 中。

```yaml
...
spec:
  scheduler:
    t9kScheduler:
      queue: default
...
```

## 服务状态

通过 MLService 的状态字段可以获取如下信息：

* 推理服务的全局状态及服务地址：`status.ready` 表明推理服务是否准备就绪，`status.address.url` 表明全局推理服务地址。
* 每个部署的模型的状态及服务地址：`status.releases[*].ready` 表明该版本推理服务是否准备就绪，`status.releases[*].url` 表明该版本的推理服务地址。
* Transformer 的状态及服务地址：`status.ready` 表明 Transformer 服务是否准备就绪，`status.transformer.url` 表明 Transformer 服务地址。
* 推理服务没有就绪的原因：`status.conditions` 以及 `status.releases[*].message` 等字段中会记录相关报错信息。

以[多版本支持](#多版本支持)的示例的状态为例，其 MLService 状态字段如下。从状态字段可以看出，该推理服务处于就绪状态，外部访问地址为 `http://multi-releases.<project>.<domain>`，某个 release 的访问地址为 `http://multi-releases-predict-<release>.<project>.<domain>`

```yaml
status:
  address:
    url: http://multi-releases.<project>.<domain>
  canaryRelease: nov-02
  conditions:
  - lastTransitionTime: "2023-11-27T10:44:27Z"
    status: "False"
    type: Paused
  - lastTransitionTime: "2023-11-27T10:50:04Z"
    status: "True"
    type: Ready
  defaultRelease: nov-05
  releases:
  - name: nov-02
    ready: true
    readyReplicas: 1
    totalReplicas: 1
    trafficPercent: 80
    url: http://multi-releases-predict-nov-02.<project>.<domain>
  - name: nov-05
    ready: true
    readyReplicas: 1
    totalReplicas: 1
    trafficPercent: 20
    url: http://multi-releases-predict-nov-05.<project>.<domain>
  - name: nov-11
    ready: true
    readyReplicas: 1
    totalReplicas: 1
    trafficPercent: 0
    url: http://multi-releases-predict-nov-11.<project>.<domain>
```

如果推理服务没有就绪，你可以通过查看 `status.conditions` 中 type 为 `Ready` 的 reason 以及 message 来查看具体信息，同时 Event 中也会有相关的错误信息。

## 访问推理服务

有两种访问推理服务的方式：
1. 通过全局 URL 访问[全局路由配置](#全局路由配置)中设置的推理服务。
2. 通过某个版本的推理服务对应的 URL 来访问这个版本的推理服务。

### 全局 URL

MLService 部署成功后，通过状态字段 `status.address.url` 可以查询到全局推理服务的 Base URL，再加上部署模型对应的路径即可得到访问推理服务的地址。

以[示例](#示例)中的服务为例，推理服务地址的状态字段如下：

```yaml
...
status:
  address:
    url: http://torch-mnist.<project>.<domain>
...
```

由于服务使用的是 TorchServe 框架，按照其 <a target="_blank" rel="noopener noreferrer" href="https://pytorch.org/serve/inference_api.html">API 规范</a>，用户可以通过下述命令查看服务状态：

```bash
$ curl http://torch-mnist.<project>.<domain>/ping
{
  "status": "Healthy"
}
```

并调用推理服务：

```bash
# 数据在 https://github.com/t9k/tutorial-examples/blob/master/deployment/mlservice/torch-pvc/test_data/0.png
$ curl -T test_data/0.png http://torch-mnist.<project>.<domain>/v1/models/mnist:predict
{
    "predictions": <predict-result>
}
```

### 单版本 URL

MLService 部署成功后，通过状态字段 `status.releases` 可以查看每个版本的推理服务对应的 Base URL。

下面的是 `status.releases` 示例，这个 MLService 中部署了 3 个版本的推理服务，版本名称分别是：v1、v2、v3。

```bash
status:
  releases:
  - name: v1
    ready: true
    readyReplicas: 1
    totalReplicas: 1
    trafficPercent: 50
    url: http://torch-mnist-s3-predict-v1.<project>.<domain>
  - name: v2
    ready: true
    readyReplicas: 1
    totalReplicas: 1
    trafficPercent: 50
    url: http://torch-mnist-s3-predict-v2.<project>.<domain>
  - name: v3
    ready: true
    readyReplicas: 1
    totalReplicas: 1
    trafficPercent: 0
    url: http://torch-mnist-s3-predict-v3.<project>.<domain>
```

运行下列命令可以查看 v1 版本的推理服务的运行状态：
```bash
$ curl http://torch-mnist-s3-predict-v1.<project>.<domain>/ping
{
  "status": "Healthy"
}
```

<aside class="note tip">
<div class="title">注意</div>

当 MLService 设置了 Transformer 时，通过单版本 URL 访问推理服务不会经过 Transformer 的前处理和后处理。

</aside>

## 参考

- API 参考：[MLService](../../reference/api-reference/mlservice.md)
- API 参考：[MLServiceRuntime](../../reference/api-reference/mlservice.md#mlserviceruntime)
- <a target="_blank" rel="noopener noreferrer" href="https://knative.dev/docs/serving/autoscaling/">Knative Autoscaling</a>
