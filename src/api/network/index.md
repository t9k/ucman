# 网络服务

网络服务负责为 Apps 提供平台内外的访问方式。它包括 [Service](./service.md)、[Ingress](./ingress.md)、[Gateway API](./gateway-api.md) 等资源。Service 用于管理平台内部的网络通信，确保不同 Apps 之间能够相互访问和交互；Ingress 和 Gateway API 则负责管理从平台外部到内部 Apps 的访问路由，使得外部用户或系统能够安全地访问平台内的服务。

基于这些网络服务，用户能够灵活配置 Apps 的网络连接，无论是内部微服务之间的通信，还是对外暴露 API 接口，都能得到有效支持，从而满足 AI 开发和部署过程中复杂多样的网络需求。
