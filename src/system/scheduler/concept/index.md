# 概念

本章节将为您介绍 T9k Scheduler 的基本概念，包含两方面内容：

* 介绍 T9k Scheduler 提供的系统 API。
* 介绍 T9k Scheduler 支持的 GPU 使用模式。

## 系统 API

T9k Scheduler 通过 CRD 扩展 Kubernetes API，增加资源 PodGroup 和 Queue 用于实现任务组的调度管理。

**PodGroup 和 Queue**

资源 [PodGroup](./podgroup.md) 和 [Queue](./queue.md) 用于实现任务组的管理调度，定义如下：

* PodGroup 是一组 [Pod:octicons-link-external-16:](https://kubernetes.io/docs/concepts/workloads/pods/){target=_blank} 的集合，代表一个任务组。
* Queue 是存放 PodGroup 的队列，T9k Scheduler 会不断尝试为 Queue 中的 PodGroup 分配资源。

## GPU 模式

GPU 是非常重要的计算资源。对于 NVIDIA GPU，针对不同使用场景，T9k Scheduler 提供了不同的使用[模式](./gpu-mode.md)。
