# 用户指南

## 下载安装

### 下载

t9k-pf 可以从[发布页面](https://github.com/t9k/ucman/releases)下载。请根据操作系统和架构（可以在命令行运行 `uname -om` 获取）选择适当的版本。

### 安装

根据下载的版本，设置 t9k-pf 的版本和系统架构等变量：

``` bash
version=0.2.8
os=darwin
arch=amd64
```

然后解压下载好的 t9k-pf，并把得到的二进制文件移动到 `/usr/local/bin` 路径下：

``` bash
tar -zxvf "$HOME/Downloads/t9k-pf-$version-$os-$arch.tar.gz"
mv t9k-pf-$os-$arch /usr/local/bin/t9k-pf
rm -f "$HOME/Downloads/t9k-pf-$version-$os-$arch.tar.gz"
```

安装完成后，运行以下命令来验证安装是否成功：

``` bash
t9k-pf version
```

<aside class="note tip">
<div class="title">提示</div>

macOS 系统可能会显示 `未打开 “t9k-pf” Apple 无法验证 “t9k-pf” 是否包含恶意软件` 的提示，可以按照以下步骤进行操作：
1. 打开 `系统设置` -> `隐私与安全性`。
2. 在 `安全性` 部分，找到 `已阻止 “t9k-pf” 以保护 Mac` 提示，点击 `仍然允许`。
3. 确认后，t9k-pf 将正常运行。

</aside>

### 卸载

删除二进制文件即可卸载 t9k-pf。

``` bash
rm -f /usr/local/bin/t9k-pf
```

## 身份认证和授权

### 通过配置文件进行身份认证和授权

t9k-pf 默认使用配置文件来进行身份认证和授权。你可以通过命令行参数 `-c, --config` 来指定 t9k-pf 使用的配置文件的路径，未指定时，默认使用 `$HOME/.t9k/t9k-config.yaml`。第一次使用时，可以通过 `t9k-pf config auth` 命令来生成配置文件，详情请参阅 [t9k-pf config auth](./commands.md#auth)。

配置文件示例如下：

``` yaml
current-context: default-context
contexts:
- name: default-context
  server: https://<example.com>
  auth:
    apikey: <your-apikey>
    token: <your-token>
  extension:
    codepack:
      data-copy-image: <your-image>
```

配置文件包括以下两个部分：

* `current-context`：字符串，记录默认使用的 Context 名称。你可以通过设置命令行参数 `-x, --context` 访问其他的 Context。
* `contexts`：数组，包含集群相关信息。
    * `name`：字符串，Context 的名称。
    * `server`：字符串，记录访问这个集群服务的域名。
    * `auth`：记录认证信息，支持 `apikey` 和 `token` 两种认证方式，需要填写其中一种。
      * `apikey`：API Key 是一种 TensorStack AI 平台的安全认证方式，支持在命令行工具等场景下进行身份认证，可以通过 [管理 API Key](../../guide/account/security-setting.md#管理-api-key) 启用并生成一个 API Key。
      * `token`：token 是一个 JWT（<a target="_blank" rel="noopener noreferrer" href="https://datatracker.ietf.org/doc/html/rfc7519">JSON Web Token</a>），可以通过 `t9k-pf config auth` 命令并输入用户名和密码登录会生成一个 token。
    * `extension`：记录其他工具需要用到的拓展配置。

### 通过命令行参数进行临时身份认证和授权

t9k-pf 支持通过命令行参数 `-k,--apikey` 直接指定 API Key，完成单次端口转发的身份认证和授权。

例如输入以下指令（其中 `notebook <URL>` 会在[命令](./commands.md)中介绍，此处只用关注 `--apikey` 的使用）：

``` bash
t9k-pf notebook <url> --apikey
```

命令行会进入交互式输入界面，粘贴你的 API Key 再按下回车即可。

<aside class="note">
<div class="title">注意</div>

此方式指定的 API Key 不会被保存，认证信息只在这次端口转发中生效。

</aside>

## 全局选项

* **-c, --config**

    使用的 T9k Config 文件的路径。默认路径是 `$HOME/.t9k/t9k-config.yaml`。

* **-k, --apikey**

    开启交互式输入 API Key。

* **-n, --namespace** 

    使用的项目（命名空间）名称。

* **-h, --help** 

    查看当前指令的帮助信息和示例。

* **--address** 

    本地监听地址。默认为 `localhost`。

* **--retryMaxTimes** 

    整数，最大重试次数。默认为 `10`。

* **--retryPeriod** 

    整数，重试间隔时间（单位：秒）。默认为 `10`。
