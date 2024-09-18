# 告警通知

当项目中出现异常情况时（例如余额不足、任务失败、App 长时间未就绪等），项目成员可以通过告警通知来订阅、查看**告警信息**，以了解异常情况。

## 告警信息

告警通知通过**告警信息**向用户传递异常情况，下面是一条告警信息的示例：

```txt
开始时间
2024-09-14T01:06:48Z
状态
firing
标签
alertname = KubePodNotReady
namespace = demo
pod = torch-mnist-trainingjob
severity = warning
注释
2024-09-14T01:06:48Z
description = Pod demo/torch-mnist-trainingjob has been in a non-ready state for longer than 15 minutes.
runbook_url = https://runbooks.prometheus-operator.dev/runbooks/kubernetes/kubepodnotready
summary = Pod has been in a non-ready state for more than 15 minutes.
```

以上述告警信息为例，一条告警信息主要包含下列内容：

1. 开始时间：当异常情况持续存在时，系统并不会持续产生告警信息，而是会周期性地（例如每 6 个小时）产生一条对应的告警信息。开始时间是系统最近一次产生这条告警信息的时间，并非异常情况出现的时间。
1. 状态：表明这条告警信息的状态，可能的值有：
    * pending：表明系统已经达到该告警信息对应的条件，但尚未超过规定时长。处于 pending 状态的告警信息无法通过[订阅](#订阅告警信息)来查看。
    * firing：表明系统已经达到该告警信息对应的条件，并且已经超过规定时长。
1. 标签：记录了这条告警信息的所有内容，包括告警名称、相关对象等等，具有相同标签的告警信息会被认为是同一条告警信息。
    * 告警名称：标签键 alertname 对应的值是告警名称，上述告警信息的告警名称是 KubePodNotReady。告警名称代表告警信息的**类型**，同一类告警信息会有相同的告警名称，例如：当多个 Pod 处于 NotReady 状态时，系统会产生多个告警信息，这些告警信息的告警名称都是 KubePodNotReady。
1. 注释：注释中会存放总结信息，通常情况下，你只需要看注释就知道这条告警信息对应的异常情况是什么。

## 订阅告警信息

项目成员可以在 User Console 中订阅告警信息，不同项目的订阅设置是互相独立的。订阅告警信息时，项目成员需要进行下列操作：

1. 选择项目名称，下面的订阅设置只作用于这个项目
2. 填写接收通知的邮箱地址
3. 设置需要订阅的告警名称

默认情况下，项目成员只能订阅下列告警信息：

- KubePodCrashLooping：Pod 容器处于 CrashLooping 状态
- KubeImagePullError：Pod 容器无法拉取镜像（错误原因是 ImagePullError 或 ErrImagePull）
- KubeContainerOOM：容器的内存溢出，原因可能是超出容器的资源限制或节点的内存上限
- KubeQuotaAlmostFull：资源配额几乎耗尽了，需要减少资源使用或增加资源配额
- KubeQuotaFullyUsed：资源配额几乎耗尽了，需要减少资源使用或增加资源配额
- KubeQuotaExceeded：资源请求总量超过了资源配额
- KubePersistentVolumeFillingUp：存储卷容量已使用 97%
- T9kAccountBalanceLow：账户余额不足
- T9kServiceNotReady：未处于暂停状态的 T9kService 超过 15 分钟还未就绪
- T9kJobFailed：T9k Job 运行失败
- T9kAppNotReady：T9k App 超过 10 分钟还未就绪
- T9kAppNotInstalled：T9k App 超过 10 分钟还未被成功安装

## 查看告警信息

项目成员可以在 User Console 中直接查看项目的所有告警信息：

- 告警信息通过告警名称进行分类
- 告警信息状态：红色代表处于 firing 状态的告警信息，黄色代表处于 pending 状态的告警信息

## 下一步

- [查看告警通知](../guide/account/alert-notification.md)
