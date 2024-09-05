# SimpleMLService

SimpleMLService 用于在 TensorStack AI 平台上简单、快捷地部署 AI 模型推理服务，可用于小规模模型部署，快速测试等场景。

SimpleMLService 具有以下特性：

* 直接支持 TensorFlow、PyTorch 框架，并允许用户自定义框架，具有良好的可扩展性。
* 支持 PVC、S3 模型存储方式。
* 直接支持从集群内部访问推理服务；集群外访问需要用户进行额外配置。
* 服务容量固定，不支持自动伸缩。

## 创建 SimpleMLService

下面是一个基本的 SimpleMLService 示例：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: SimpleMLService
metadata:
  name: mnist
spec:
  replicas: 1
  storage:
    s3:
      secretRef:
        name: s3-secret
      uri: s3://models/mnist/
      mountPath: /var/lib/t9k/model
  tensorflow:
    image: t9kpublic/tensorflow-serving:2.6.0
    resources: 
      requests:
        cpu: 1
        memory: 1Gi
```

本示例的 spec 字段的子字段释义如下：
* `replicas`: 定义运行推理服务的副本数量是 1。
* `storage.s3`: 设定使用 S3 存储模型，子字段的释义如下：
    * `secretRef.name`: Secret `s3-secret` 中存储 S3 配置信息，其详情参见：[创建 S3 Secret](./storage.md#创建-s3-secret)。
    * `uri`: 模型在 S3 中的存储标识是 `s3://models/mnist/`。
    * `mountPath`: 模型被加载后，在容器中存储模型的文件系统路径是 `/var/lib/t9k/model`。
* `tensorflow`: 设定使用 `tensorflow` 推理框架，子字段释义如下：
    * `image`: 指定推理服务容器镜像 `t9kpublic/tensorflow-serving:2.6.0`。
    * `resources`: 这顶一个副本 Pod 使用的资源量。

## 直接支持的 AI 框架

SimpleMLService 目前直接支持 TensorFlow、PyTorch 两种框架。

### TensorFlow

可以通过设置 `spec.tensorflow` 字段来部署 TensorFlow 框架，参考示例：[创建 SimpleMLService](#创建-simplemlservice)。

当使用 TensorFlow 时，控制器会在容器中设置下列启动命令：

```bash
/usr/bin/tensorflow_model_server \
  --port=9090 \
  --rest_api_port=8080 \
  --model_name=<SimpleMLService name> \
  --model_base_path=<model-dir-in-container>
```

### PyTorch

可以通过设置 `spec.pytorch` 字段来部署 PyTorch 框架，示例如下：
```yaml
spec:
  pytroch:
    image: <pytorch-image>
    modelsFlag: "resnet-18=resnet-18.mar"
    resources: 
      requests:
        cpu: 1
        memory: 1Gi
```

当使用 PyTorch 时，控制器会在容器中设置下列启动命令：

```bash
torchserve \
  --start \
  --model-store=<mode-dir> \
  --models <spec.pytorch.modelsFlag>
```

## 自定义框架

如果需要使用 PyTorch, TensorFlow 之外的框架，可以通过设置 `spec.custom` 字段来自定义框架。

用户可在 `spec.custom.spec` 字段中定义一个完整的 [PodSpec](../../reference/api-reference/simplemlservice.md#customspec)，并需要满足下列要求：

1. 至少设置一个 `containers` 成员。
1. 启动推理服务运行命令时，指定正确的模型路径。
1. 未设置 [spec.service](#暴露服务) 时，推理服务的服务端口应该使用 8080。

示例如下：
```yaml
apiVersion: tensorstack.dev/v1beta1
kind: SimpleMLService
metadata:
  name: pvc-custom
spec:
  replicas: 1
  storage:
    s3:
      secretRef:
        name: s3-secret
      uri: s3://models/mnist/
      mountPath: /custom/path
  custom:
    spec:
      containers:
      - name: user-container
        args:
        - --port=9000
        - --rest_api_port=8080
        - --model_name=mnist
        - --model_base_path=/custom/path
        command:
        - /usr/bin/tensorflow_model_server
        image: "t9kpublic/tensorflow-serving:2.6.0"
```

## 副本数量

副本数量通过字段 `spec.replicas` 设置，用于定义 SimpleMLService 的 Pod 数量，默认值是 1。

## 暴露服务

通过设置 `spec.service` 字段来选择将服务的哪个端口暴露出来。未设置时，默认将 Pod 的 8080 端口映射到 Service 的 80 端口。

下面是一个示例：

```yaml
spec:
  service:
    ports:
    - name: http
      port: 80
      targetPort: 8080
      protocol: TCP
    type: ClusterIP
```

在该例中：
* 将 Pod 的 8080 端口映射到 Service 的 80 端口，协议是 TCP。
* Service 的 Type 是 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/concepts/services-networking/service/#type-clusterip">ClusterIP</a>。

## 调度器

SimpleMLService 支持使用两种调度器：

* <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/#kube-scheduler">Kubernetes 默认调度器</a>
* [T9k Scheduler 调度器]()

通过 `spec.scheduler` 字段可以设置欲使用的调度器：
* 不设置 `spec.scheduler` 字段，默认使用 Kubernetes 调度器。
* 设置 `spec.scheduler.t9kScheduler` 字段，使用 T9k Scheduler 调度器。

在下面的示例中，SimpleMLService 使用 T9k Scheduler 调度器，并申请使用 [队列]() `default` 中的资源。

```yaml
spec:
  scheduler:
    t9kScheduler:
      queue: default
```

## 模型存储

通过 `spec.storage` 字段可以设置 SimpleMLService 的模型存储信息，详情请见[模型存储](./storage.md)。

## 服务状态

SimpleMLService 的状态记录在 `status` 字段中。

`status.address` 字段记录了推理服务在集群内的访问地址，子字段如下：
* `url`: 推理服务在集群内的访问地址
* `ports`: 推理服务可供访问的服务端口

`status.conditions` 字段表明了当前 SimpleMLService 的状态，包括下列 2 种类型：
* `ModelDownloaded`: 模型是否成功地被下载到本地。
* `Ready`: 推理服务是否就绪。 

在下面的示例中：
* 访问推理服务的地址是 `sample.demo.svc.cluster.local`
* 模型已经下载到容器本地
* 推理服务处于就绪状态

```yaml
status:
  address:
    url: sample.demo.svc.cluster.local
    ports:
    - port: 80
      protocol: TCP
  conditions:
  - lastTransitionTime: "2023-12-27T06:52:39Z"
    status: "True"
    type: ModelDownloaded
  - lastTransitionTime: "2023-12-27T06:52:41Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Ready
```

## 参考

* API 参考：[SimpleMLService](../../reference/api-reference/simplemlservice.md)
