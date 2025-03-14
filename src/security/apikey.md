# API Key

在使用浏览器场景下（例如使用 User Console），用户能够直接、同步地参与到身份认证和授权流程。用户可以方便地通过交互式的方式提供安全凭证（用户名 + 密码）来完成认证和授权，并随后通过 Cookie + Access Token 建立安全的交互过程。

但是，还有其他环境或者场景不适合采用前述方式。例如，用户通过命令行或者在自动化脚本中向服务器发送请求，此时要求 “用户同步进行交互” 来获得访问令牌是非常不便捷的；或者，在此过程中需要通过客户程序输入密码，可能造成密码泄漏。同时，有些场景下，我们希望能够只授予客户端部分权限，但直接使用前述 access token 或者 Cookie 并不能更进一步缩小权限。
 
API Key 是 TensorStack AI 平台应对上述问题的解决方案。它是一种可撤销的、异步的、持久的安全授权方式，具有以下特点：

* 可以长期持有（适合在脚本中使用）
* 不会泄漏账户信息（无需使用用户名和密码）
* 可以选择性地包含账户的部分权限 
* 方便在脚本程序、命令行工具等“非交互场景”进行集成使用
* 可以随时方便地取消该授权

## 下一步

* 学习如何为账户 [管理 API Key](../guide/account/security-setting.md#管理-api-key)
