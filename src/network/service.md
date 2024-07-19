# 查看 Service

<a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/zh-cn/docs/concepts/services-networking/service/">Service</a> 是将运行在一个或一组 Pod 上的网络应用程序公开为网络服务的方法。大部分应用在部署时会自动创建相应的 Service，以方便集群中的其他服务进行访问。

点击**网络服务 > Service**，查看已有的 Service：

<figure class="screenshot">
  <img alt="list-service" src="../assets/network/list-service.png" />
</figure>

例如，部署 PostgreSQL 应用时，会自动创建以下 Service：

<figure class="screenshot">
  <img alt="create-service" src="../assets/network/create-service.png" />
</figure>
