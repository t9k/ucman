# Service

Service 是一种 Kubernetes 原生资源，用于访问一个或多个 Pod。它为一组 Pod 提供了一个稳定的 IP 地址和 DNS 名称，以便其他应用程序或用户可以访问它们。Service 允许 Pod 动态添加或删除，而不会影响服务的可用性。Service 还支持负载均衡，可以将请求分配给多个 Pod 以提高可扩展性和可靠性。


## 创建 Service

下面是一个基本的 Service 示例：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

其中：

* `spec.selector` 字段表示该 Service 指向一组拥有标签 `app: nignx` 的 Pod
* `spec.ports` 字段表示该 Service 本身对外提供服务的端口（`port`）为 80，指向的 Pod 的端口（`targetPort`）也为 80

下面是该 Service 指向的一组 Pod 的示例，以 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/concepts/workloads/controllers/deployment/">Deployment</a> 的形式运行：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 3
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

在该例中，Deployment 一共会创建 3 个 Pod，每个 Pod 都拥有标签 `app: nginx`，且在 80 端口提供服务。因此，上述 Service 会将流量均匀分布到这 3 个 Pod 中。


## 使用 Service

创建 Service 后，其他应用程序可以从**集群内部**根据 Service 的名称和命名空间访问该 Service，访问地址为 `http://<service-name>.<service-namespace>:<service-port>`。

例如，上节中的 Service 示例可以在集群内部通过如下命令访问：

```bash
curl -G http://nginx-service.default:80
```

注：如果应用程序与 Service 处于同一命名空间，可以省略地址中的命名空间部分，形如 `http://<service-name>:<service-port>`。


## Service 类型

Service 支持以下几种类型：

* `ClusterIP`：`ClusterIP` 是最常见的 Service 类型，也是默认类型。`ClusterIP` 类型的 Service 只能从集群内部访问，不允许从集群外部访问；
* `NodePort`：`NodePort` 允许在每个节点上公开一个端口，以便从集群外部根据节点的 IP 和端口访问 Service；
* `LoadBalancer`：`LoadBalancer` 使用外部负载均衡器将流量分配到 Service 上。它需要在云提供商上创建负载均衡器，然后将流量转发到Service。
* `ExternalName`：`ExternalName` 将 Service 映射到另一个服务的地址（例如 api.example.com）。它通常用于连接到外部服务。


## 下一步

* 了解 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/concepts/services-networking/service/">Service 的概念</a>
* 了解 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/reference/kubernetes-api/service-resources/service-v1/">Service 的定义</a>
