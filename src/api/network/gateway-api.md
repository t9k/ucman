# Gateway API

[Gateway API](https://gateway-api.sigs.k8s.io/) 是一组 Kubernetes 原生资源，包括 GatewayClass、Gateway、HTTPRoute 等，提供从集群外部到集群内服务的动态路由功能。Gateway API 是 [Ingress](./ingress.md) 的继任者，将替代 Ingress 成为下一代 Kubernetes 路由解决方案。

Gateway API 的设计模型如下：

<figure class="architecture">
  <img alt="ui" src="https://gateway-api.sigs.k8s.io/images/resource-model.png" width=70% />
</figure>

其中：

* 基础设施提供商（TensorStack AI 平台）负责创建 GatewayClass 资源；
* 集群管理员（平台管理员）负责创建 Gateway 资源；
* 应用开发者（平台 App 开发者、普通用户）负责创建 HTTPRoute 资源。

上述资源的功能分别是：

* [GatewayClass](https://gateway-api.sigs.k8s.io/api-types/gatewayclass/)：与 [IngressClass](https://kubernetes.io/docs/concepts/services-networking/ingress/#ingress-class)、[StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/) 类似，GatewayClass 定义了一组共享共同配置和行为的 Gateway，由实现该 GatewayClass 的控制器管理。
* [Gateway](https://gateway-api.sigs.k8s.io/api-types/gateway/)：描述了如何将流量翻译到集群内的服务。
* [HTTPRoute](https://gateway-api.sigs.k8s.io/api-types/httproute/)：定义了特定协议的规则，用于将流量从 Gateway 映射到 Kubernetes 服务。

## 查看 GatewayClass

TensorStack AI 平台默认使用 [Cilium](https://docs.cilium.io/en/stable/network/servicemesh/gateway-api/gateway-api/) 作为控制器实现 Gateway API 功能，所创建的 GatewayClass 如下：

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: cilium
spec:
  controllerName: io.cilium/gateway-controller
```

## HTTP 示例

针对 [Service](./service.md) 文档中创建的 nginx 服务，如果你想通过 HTTP 协议访问，可创建如下 Gateway 和 HTTPRoute：

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: my-gateway
spec:
  gatewayClassName: cilium
  listeners:
  - protocol: HTTP
    port: 80
    name: web-gw
---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: http-route-nginx
spec:
  parentRefs:
  - name: my-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /test
    backendRefs:
    - name: nginx-service
      port: 80
```

其中 `my-gateway` 的 `/test` 路径将被转发到 `nginx-service` 这个 Service。

通过以下命令查看该 Gateway 的 IP 地址为 `100.1.2.3`：

```bash
$ kubectl get gateway my-gateway
NAME         CLASS    ADDRESS      PROGRAMMED   AGE
my-gateway   cilium   100.1.2.3    True         13d
```

因此，可以通过以下命令访问 `nginx-service` 服务：

```bash
curl -G http://100.1.2.3/test
```

<aside class="note">
<div class="title">注意</div>

Gateway 的底层实现通过创建一个 LoadBalancer 类型的 Service 来拥有一个 IP 地址，请确保当前集群支持 LoadBalancer 类型的 Service。

</aside>

## HTTPS 示例

针对 [Service](./service.md) 文档中创建的 nginx 服务，如果你想通过 HTTPS 协议访问，可创建如下 Gateway 和 HTTPRoute：

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: tls-gateway
spec:
  gatewayClassName: cilium
  listeners:
  - name: https-1
    protocol: HTTPS
    port: 443
    hostname: "example.com"
    tls:
      certificateRefs:
      - kind: Secret
        name: demo-cert
---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: https-route-nginx
spec:
  parentRefs:
  - name: tls-gateway
  hostnames:
  - "example.com"
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /test
    backendRefs:
    - name: nginx-service
      port: 80
```

其中：

* `tls-gateway` 设置了域名为 `example.com`，存储证书的 Secret 为 `demo-cert`，你需要在该 Secret 中存储 `example.com` 的 TLS 证书；
* `tls-gateway` 的 `/test` 路径将被转发到 `nginx-service` 这个 Service。

通过以下命令查看该 Gateway 的 IP 地址为 `100.1.2.3`：

```bash
$ kubectl get gateway tls-gateway
NAME          CLASS    ADDRESS      PROGRAMMED   AGE
tls-gateway   cilium   100.1.2.3    True         13d
```

你需要配置 DNS 解析，将域名 `example.com` 解析到 IP 地址 `100.1.2.3`。然后可以通过以下命令访问 `nginx-service` 服务：

```bash
curl -G https://example.com/test
```

## 下一步

* 查看 <a target="_blank" rel="noopener noreferrer" href="https://gateway-api.sigs.k8s.io/">Gateway API 官方文档</a>
* 了解 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/reference/kubernetes-api/service-resources/ingress-v1/">Gateway API 相关资源定义</a>
* 了解 <a target="_blank" rel="noopener noreferrer" href="https://gateway-api.sigs.k8s.io/guides/migrating-from-ingress/">如何从 Ingress 迁移至 Gateway API</a>
