# App 使用中的常见问题

## App 处于 NotReady 状态

App 处于 NotReady 状态有非常多可能的原因，请[进入 App 的详情页面](../../guide/manage-app/view-app-detail.md)，点击**状态**右侧的**诊断**查看诊断结果以尝试定位原因。下表汇总了一些常见的原因和相应的诊断结果示例：

| 原因                           | 诊断结果示例（Pod 状态）                                                                                                                                                                                     |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 集群的所有节点均没有足够的资源 | 0/10 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: }, 2 node(s) were unschedulable, 3 Insufficient memory, 4 Insufficient cpu, 5 Insufficient nvidia.com/gpu. |
| 超出项目的资源限额             | exceeded quota: demo, requested: cpu=4, used: cpu=6, limited: cpu=8                                                                                                                                          |

<aside class="note tip">
<div class="title">提示</div>

分析诊断结果可能需要具备一定的 Kubernetes 基础并且了解 App 的资源清单，如果你有任何疑问，请询问平台的管理员。

</aside>
