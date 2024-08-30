# Ingress

Ingress 是一种 Kubernetes 原生资源，用于提供从集群外部到集群内服务的 HTTP 和 HTTPS 路由。

## 创建 Ingress

下面是一个基本的 Ingress 示例：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-example
spec:
  rules:
  - host: foo.example.com
    http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
  tls:
  - hosts:
    - foo.example.com
    secretName: my-tls-secret
```

在该例中，当用户从**集群外部**访问地址 `https://foo.example.com/testpath` 时，Ingress 会将请求转发至名为 `nginx-service` 的 Service 的 80 端口。

为了支持 HTTPS 协议，该 Ingress 需要配置如下格式的 Secret：

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-tls-secret
data:
  tls.crt: <base64 encoded cert>
  tls.key: <base64 encoded key>
type: kubernetes.io/tls
```

## 使用 Ingress

创建 Ingress 后，还需要为 Ingress 定义中的域名配置 DNS 解析，才能支持用户正常访问。当 Ingress 控制器为 [Ingress NGINX Controller](https://kubernetes.github.io/ingress-nginx/) 时，Ingress 定义中的域名需要解析到集群 ingress 节点的 IP 地址。

例如，可以通过以下命令得到集群 ingress 节点的 IP 地址为 `100.1.2.3`：

```
$ kubectl get node -l node-role.kubernetes.io/ingress -o wide
NAME                 STATUS   ROLES     AGE    VERSION    INTERNAL-IP
ingress-node-name    Ready    ingress   536d   v1.28.6    100.1.2.3
```

那么，对于上节中的 Ingress 示例，应当配置域名 `foo.example.com` 解析到地址 `100.1.2.3`。

## 下一步

* 了解 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/concepts/services-networking/ingress/">Ingress 的概念</a>
* 了解 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/reference/kubernetes-api/service-resources/ingress-v1/">Ingress 的定义</a>
