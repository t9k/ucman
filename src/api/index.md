# APIs

## 概念

应用程序接口（API）是一组规则和协议，允许不同的软件组件之间相互通信。在 TensorStack AI 平台中，API 采用 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/">Kubernetes 的 API 扩展机制</a> 实现。

API 是平台的核心组成部分，扮演着几个关键角色：

1. 请求系统服务：集群的系统功能，如创建工作负载，申请存储空间，设置网络配置等，都是通过 API 实现。
1. API 对象管理： API 允许用户管理各种 Kubernetes API 对象，如 Pod、Service、Deployment，及其它 CRD 类型。这种管理包括根据需要创建、更新和删除资源。
1. 通信： API 支持不同组件之间促进通信，确保它们能够有效地协同工作。
1. 可扩展性： 通过 CRD 和其他机制，API 使用户能够扩展平台的功能，允许创建满足特定要求的自定义解决方案。
1. 自动化： API 使自动化工具和脚本能够与平台交互,从而实现应用程序的简化部署和管理。

## 系统中的 API

系统提供的 API 由管理员安装，普通用户可以查看系统中的 API 安装情况。

例如，查看系统中的 TensorStack 提供的 APIs:

```bash
$ kubectl api-resources |grep tensorstack
instances                                                                                      app.tensorstack.dev/v1beta1                 true         Instance
beamjobs                             bj                                                        batch.tensorstack.dev/v1beta1               true         BeamJob
colossalaijobs                       caij                                                      batch.tensorstack.dev/v1beta1               true         ColossalAIJob
cronworkflowruns                     cwr                                                       batch.tensorstack.dev/v1beta1               true         CronWorkflowRun
deepspeedjobs                        dj                                                        batch.tensorstack.dev/v1beta1               true         DeepSpeedJob
genericjobs                          gj                                                        batch.tensorstack.dev/v1beta1               true         GenericJob
mpijobs                              mj                                                        batch.tensorstack.dev/v1beta1               true         MPIJob
pytorchtrainingjobs                  pj                                                        batch.tensorstack.dev/v1beta1               true         PyTorchTrainingJob
tensorflowtrainingjobs               tj                                                        batch.tensorstack.dev/v1beta1               true         TensorFlowTrainingJob
...
```
## API 详情

如希望了解一个具体的 API 详情，可方便通过命令行获得。

以 `DeepSpeedJob` API 为例，查看 API 基本情况：

```bash
$ kubectl explain deepspeedjobs
KIND:     DeepSpeedJob
VERSION:  batch.tensorstack.dev/v1beta1

DESCRIPTION:
     DeepSpeedJob defines the schema for the DeepSpeedJob API.

FIELDS:
   apiVersion	<string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources
...
```

可进一步指定子字段，查看其详情：

```bash
$ kubectl explain deepspeedjobs.spec
KIND:     DeepSpeedJob
VERSION:  batch.tensorstack.dev/v1beta1

RESOURCE: spec <Object>

DESCRIPTION:
     DeepSpeedJobSpec outlines the intended configuration and execution
     parameters for a DeepSpeedJob.

FIELDS:
   config	<Object>
     Key configurations for executing DeepSpeed training jobs.
...
```

## 下一步

- 阅读本 APIs 章节内容，全面了解平台提供的各种 APIs
- 了解 TensorStack AI 平台 [计算任务（T9k Job）APIs](./t9k-job/index.md)
- 了解 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/">Kubernetes 的 API 扩展机制</a>
