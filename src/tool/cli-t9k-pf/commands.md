# 命令


## config

生成、管理、查看配置文件。默认路径为 `${HOME}/.t9k/t9k-config.yaml`，可通过全局选项 `-c, --config` 指定该文件路径。

### auth

完成用户身份认证，生成配置文件。用户第一次使用 T9k 命令行工具时，需要使用此命令来进行身份认证。认证成功后，此命令会修改（如果是首次认证会新建）配置文件。之后用户便可以通过该认证信息去使用 T9k 其他命令。

#### 使用

```
t9k-pf config auth <server> [--apikey] [--user=<username>]
```

#### 选项

```
-k, --apikey            启用交互式输入 API Key。
-u, --user string       字符串，指定用户密码登录的用户名称。
```

#### 全局选项

```
-c, --config            字符串，指定要查看、修改的配置文件的路径。默认路径是 `$HOME/.t9k/t9k-config.yaml`。
-x, --context           字符串，指定新增 Context 的名称。
-h, --help              查看当前指令的帮助信息和示例。
-v, --verbose           指定输出 log 信息的详细程度。
```

#### 示例

##### 通过用户密码完成认证

用户指定要登录的域名，然后输入用户名和密码完成认证。认证完成后，当前的配置文件中便会新增一个 `<用户名>-<域名>` 的 Context，用户也可以自己指定 Context 的名称。

```
$ t9k-pf config auth <http://example.com>
Authenticating using username and password by default, add --apikey to use apikey.
Please enter your username: demo
Please enter your password:
Please enter Context name [default: demo-example.com]: demo
Login succeeded!
```

!!! note "注意" 
    Context 的名称需要满足 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names">DNS Subdomain Names</a> 的要求，如果 Context 名字重复，可以选择覆盖原内容或者重新输入。

##### 通过 API Key 完成认证

用户指定要登录的域名，然后输入 API Key。认证完成后，当前的配置文件中便会新增一个 `apikey-<hash>-<域名>` 的 Context，用户也可以自己指定 Context 的名称。

```
$ t9k-pf config auth <http://example.com> --apikey
Authenticating using API Key...
Please enter your API Key:
Please enter Context name [default: apikey-<hash>-example.com]: demo-apikey
Login succeeded!
```

### delete-context

删除指定的 Context。

!!! note "注意" 
    即使指定的 Context 是 current-context，此命令也会直接删除掉。所以务必确认之后再删除。

#### 使用

```
t9k-pf config delete-context <name>
```

#### 全局选项

```
-c, --config            字符串，指定要查看、修改的配置文件的路径。默认路径是 `$HOME/.t9k/t9k-config.yaml`。
-h, --help              查看当前指令的帮助信息和示例。
-v, --verbose           指定输出 log 信息的详细程度。
```

#### 示例

删除 Context demo：

```
t9k-pf config delete-context demo
```

### get-contexts

用于获取当前使用的配置文件中 Context 的相关信息。具体包括：

* `CURRENT`：是否是当前正在使用的 Context
* `NAME`：Context 的名称
* `SERVER`：T9k 平台的地址
* `AUTH_TYPE`：认证信息的类型（token 或者 apikey）

#### 使用

```
t9k-pf config get-contexts [name] 
```

#### 全局选项

```
-c, --config            字符串，指定要查看、修改的配置文件的路径。默认路径是 `$HOME/.t9k/t9k-config.yaml`。
-h, --help              查看当前指令的帮助信息和示例。
-v, --verbose           指定输出 log 信息的详细程度。
```

#### 示例

获取当前配置文件中所有 Context 的信息：

```
t9k-pf config get-contexts
```

获取指定 Context 的信息：

```
t9k-pf config get-contexts my-context
```

### use-context

切换当前使用的 Context，此命令会修改当前配置文件中 current-context 的值。

#### 使用

```
t9k-pf config use-context <name>
```

#### 全局选项

```
-c, --config            字符串，指定要查看、修改的配置文件的路径。默认路径是 `$HOME/.t9k/t9k-config.yaml`。
-h, --help              查看当前指令的帮助信息和示例。
-v, --verbose           指定输出 log 信息的详细程度。
```

#### 示例

切换到 Context foo：

```
t9k-pf config use-context foo
```

将文件 `$HOME/t9kConfig.yaml` 下的 Context 切换到 foo：

```
t9k-pf config use-context foo -c $HOME/t9kConfig.yaml
```


## notebook

针对 TensorStack AI 平台的 Notebook 资源，我们提供了直接通过其 URL 地址获取 SSH 连接方式的功能。使用该命令，你不需要指定名称、命名空间等信息。关于 SSH 连接的详细内容请参阅[通过 SSH 连接远程使用 Notebook]()。

### 使用

``` bash
t9k-pf notebook <url> <localPort>
```

其中 `<url>` 代表地址栏中的地址，`<localPort>` 代表你指定的本地端口号（小于 1024 的本地端口需要管理员权限才可以绑定），如果不指定，会随机使用一个本地端口。

`t9k-pf notebook` 也可以用 `t9k-pf nb` 代替。
    

<aside class="note">
<div class="title">注意</div>

为了方便用户快速连接 SSH，t9k-pf 命令行不支持指定 Notebook 资源的目标端口。如果你有相关需求，可以先获取 Notebook 对应的 Pod 资源，然后参照[访问 Pod](#pod) 来访问特定的目标端口。

</aside>

### 全局选项

```
-c, --config            字符串，指定使用的配置文件的路径。默认路径是 `$HOME/.t9k/t9k-config.yaml`。
-h, --help              查看当前指令的帮助信息和示例。
-n, --namespace         字符串，指定使用的项目（命名空间）名称。
-k, --apikey            开启交互式输入 API Key。
--address               本地监听地址。默认为 `localhost`。
--retryMaxTimes         整数，最大重试次数。默认为 `10`。
--retryPeriod           整数，重试间隔时间（单位：秒）。默认为 `10`。
```

### 示例

通过本地 127.0.0.1 的随机端口访问 Project demo 下 Notebook ml-labs 的 SSH 端口。

``` bash
t9k-pf notebook <tensorstack-host>/t9k/build-console/projects/demo/notebooks/ml-labs/lab
```

通过本地 127.0.0.1:8888 访问 Project demo 下 Notebook ml-labs 的 SSH 端口。

``` bash
t9k-pf nb <tensorstack-host>/t9k/build-console/projects/demo/notebooks/ml-labs/lab 8888
```

通过本地 0.0.0.0:8888 访问 Project demo 下 Notebook ml-labs 的 SSH 端口。

``` bash
t9k-pf nb <tensorstack-host>/t9k/build-console/projects/demo/notebooks/ml-labs/lab 8888 --address 0.0.0.0
```

<aside class="note">
<div class="title">注意</div>

在 port-forward 成功后，你仍然需要保持你的 t9k-pf 命令行窗口一直保持运行状态。

</aside>

## pod

访问指定 Pod 的指定端口。

### 使用

``` bash
t9k-pf pod <name> [<localPort>:]<targetPort>
```

其中 `<localPort>` 代表本地监听的端口（小于 1024 的本地端口需要管理员权限才可以绑定），`<targetPort>` 代表目标 Pod 的端口。

### 全局选项

```
-c, --config            字符串，指定使用的配置文件的路径。默认路径是 `$HOME/.t9k/t9k-config.yaml`。
-h, --help              查看当前指令的帮助信息和示例。
-n, --namespace         字符串，指定使用的项目（命名空间）名称。
-k, --apikey            开启交互式输入 API Key。
--address               本地监听地址。默认为 `localhost`。
--retryMaxTimes         整数，最大重试次数。默认为 `10`。
--retryPeriod           整数，重试间隔时间（单位：秒）。默认为 `10`。
```

### 示例

通过本地 127.0.0.1:3333 访问命名空间 dev 下的 Pod example 的 2222 端口。

``` bash
t9k-pf pod example 3333:2222 -n dev
```

通过本地 0.0.0.0:3333 访问命名空间 dev 下的 Pod example 的 2222 端口。

``` bash
t9k-pf pod example 3333:2222 -n dev --address 0.0.0.0
```

通过本地 127.0.0.1 的随机端口访问命名空间 dev 下的 Pod example 的 2222 端口。

``` bash
t9k-pf pod example 2222 -n dev
```

<aside class="note">
<div class="title">注意</div>

在 port-forward 成功后，你仍然需要保持你的 t9k-pf 命令行窗口一直保持运行状态。

</aside>

## service

访问指定 Service 的指定端口。

### 使用

``` bash
t9k-pf service <name> [<localPort>:]<targetPort>
```

其中 `<localPort>` 代表本地监听的端口（小于 1024 的本地端口需要管理员权限才可以绑定），`<targetPort>` 代表目标 Service 的端口。

`t9k-pf service` 也可以用 `t9k-pf svc` 代替。

### 全局选项

```
-c, --config            字符串，指定使用的配置文件的路径。默认路径是 `$HOME/.t9k/t9k-config.yaml`。
-h, --help              查看当前指令的帮助信息和示例。
-n, --namespace         字符串，指定使用的项目（命名空间）名称。默认是 `default`。
-k, --apikey            开启交互式输入 API Key。
--address               本地监听地址。默认为 `localhost`。
--retryMaxTimes         整数，最大重试次数。默认为 `10`。
--retryPeriod           整数，重试间隔时间（单位：秒）。默认为 `10`。
```

### 示例

通过本地 127.0.0.1:8888 访问默认命名空间 default 下的 Service myservice 的 80 端口。

``` bash
t9k-pf service myservice 8888:80
```

通过本地 0.0.0.0:8888 访问默认命名空间 default 下的 Service myservice 的 80 端口。

``` bash
t9k-pf service myservice 8888:80 --address 0.0.0.0
```

通过本地 127.0.0.1 的随机端口访问命名空间 dev 下的 Service myservice 的 80 端口。

``` bash
t9k-pf service myservice 80 -n dev
```

<aside class="note">
<div class="title">注意</div>

在 port-forward 成功后，你仍然需要保持你的 t9k-pf 命令行窗口一直保持运行状态。

</aside>
