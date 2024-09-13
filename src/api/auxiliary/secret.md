# Secret

Secret 是一种 Kubernetes 原生资源，用于存储少量敏感信息，比如密码、OAuth 令牌、SSH 密钥等。使用 Secret 可以避免将敏感数据直接写入到 Pod 的定义中或者应用程序代码里，从而提高了安全性。

## 创建 Secret

下面是一个基本的 Secret 示例：

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-demo
type: Opaque
data:
  key1: dmFsdWUxCg==
  key2: dmFsdWUyCg==
```

在该例中，`secret-demo` 存储了两对键值对，其中值经过了 base64 编码。

## 使用 Secret

与 [PVC](../storage/pvc.md) 类似，Secret 可以以同样的方式作为数据卷被 Pod、Notebook、Job 等资源挂载。以 PyTorchTrainingJob 为例：

```yaml
apiVersion: batch.tensorstack.dev/v1beta1
kind: PyTorchTrainingJob
metadata:
  name: pytorchtrainingjob-demo
spec:
  replicaSpecs:
    - type: worker
      replicas: 1
      restartPolicy: OnFailure
      template:
        spec:
          containers:
            - image: pytorch/pytorch:2.0.0-cuda11.7-cudnn8-devel
              name: pytorch
              command:
                - python
                - dist_mnist.py
              resources:
                limits:
                  cpu: '1'
                  memory: 1Gi
                requests:
                  cpu: 500m
                  memory: 500Mi
              volumeMounts:
                - name: data
                  mountPath: /data
                - name: secret
                  mountPath: /secret
          volumes:
            - name: data
              persistentVolumeClaim:
                claimName: pvc-demo
            - name: secret
              secret:
                name: secret-demo
```

在该例中，`pytorchtrainingjob-demo` 将 `pvc-demo` 和 `secret-demo` 均作为数据卷（volume）来使用，其中容器 `pytorch` 将两个数据卷分别挂载到 `/data` 和 `/secret` 目录下。因此，容器 `pytorch` 的 `/secret` 目录下将存在两个文件 `/secret/key1` 和 `/secret/key2`，文件内容是经过 base64 解码后的对应的值。

## Secret 类型

Secret 有多种类型，分别适用于存储不同类型的敏感数据，并通过 label `tensorstack.dev/resource` 进行标识，具体类型如下表所示：

| 类型           | label                                   | 用途                                                                        | 数据来源                                |
| -------------- | --------------------------------------- | --------------------------------------------------------------------------- | --------------------------------------- |
| API Key        | `tensorstack.dev/resource: apikey`      | 存放 API Key                                                                | 在 User Console 生成                    |
| S3-cfg         | `tensorstack.dev/resource: s3-cfg`      | 存放 [.s3cfg 文件](https://s3tools.org/kb/item14.htm)的内容                 | 从管理员处获取                          |
| S3-env         | `tensorstack.dev/resource: s3-env`      | 存放 S3 配置                                                                | 从管理员处获取                          |
| Docker         | `tensorstack.dev/resource: docker`      | 存放 [Docker 配置文件](https://docs.docker.com/engine/swarm/configs/)的内容 | 本地的 `$HOME/.docker/config.json` 文件 |
| SSH Public Key | `tensorstack.dev/resource: ssh`         | 存放 SSH 公钥                                                               | 本地的 `$HOME/.ssh/id_rsa.pub` 文件     |
| Ceph Client    | `tensorstack.dev/resource: ceph-client` | 存放 Ceph 客户端配置                                                        | 从管理员处获取                          |
| Custom         | `tensorstack.dev/resource: other`       | 自定义用途                                                                  | -                                       |

例如：

* SSH Public Key 类型的 Secret 可用于部分 App 的 [SSH 访问]()
* Docker 类型的 Secret 可用于 [ImageBuilder](../imagebuilder.md) 构建并上传镜像
* S3-cfg 类型的 Secret 可用于创建 [S3 类型的 StorageShim](../storage/storageshim.md#s3-类型)
* Ceph Client 类型的 Secret 可用于创建 [CephFS 类型的 StorageShim](../storage/storageshim.md#cephfs-类型)

## 下一步

* 了解 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/concepts/configuration/configmap/">Secret 的概念</a>
* 了解 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/reference/kubernetes-api/config-and-storage-resources/secret-v1/">Secret 的定义</a>
* 学习如何[管理 Secret](../../guide/manage-storage-network-and-auxiliary/secret.md)
