# Terminal

[ttyd](https://github.com/tsl0922/ttyd) 是一个简单而强大的工具，它允许用户通过 HTTP 或 HTTPS 协议在 web 浏览器中访问远程服务器的终端，无需安装额外的客户端软件。ttyd 支持多种终端程序，如 sh、bash、zsh 等，使用户可以在熟悉的 shell 环境中工作。

Terminal 是一个基于 ttyd 的 web 终端 App，允许用户在浏览器中访问并执行命令。Terminal 预装了多种常用的命令行工具（如 kubectl、tmux、helm 和 vim），支持多种 shell 环境（如 sh、bash 和 zsh），可设置定期 ping 以保持长连接，并且可以挂载 PVC。

## 使用方法

待 App 就绪后，点击右侧的 <span class="twemoji"><svg class="MuiSvgIcon-root MuiSvgIcon-colorPrimary MuiSvgIcon-fontSizeMedium css-jxtyyz" focusable="false" aria-hidden="true" viewBox="0 0 24 24" data-testid="OpenInNewIcon"><path d="M19 19H5V5h7V3H5c-1.11 0-2 .9-2 2v14c0 1.1.89 2 2 2h14c1.1 0 2-.9 2-2v-7h-2zM14 3v2h3.59l-9.83 9.83 1.41 1.41L19 6.41V10h2V3z"></path></svg></span> 进入网页 UI，即可输入并执行命令：

<figure class="screenshot">
  <img alt="ui-terminal" src="../assets/app/terminal/terminal.png" />
</figure>

web 终端的使用方法与本地终端类似：在提示符后面输入命令并按下回车键，远程服务器会接收这些命令，执行后将结果返回并显示。

## 使用说明

* App（的镜像）预装了以下命令行工具：

| 名称    | 介绍                                                      |
| ------- | --------------------------------------------------------- |
| curl    | 用于从或向服务器传输数据，支持多种协议。                  |
| git     | 分布式版本控制系统，用于跟踪和协作开发软件项目的源代码。  |
| helm    | 用于管理 Kubernetes 应用的包管理工具。                    |
| rsync   | 用于高效同步和传输文件，支持本地和远程文件。              |
| ssh     | 用于安全地远程访问和管理服务器。                          |
| tmux    | 一个终端复用器，允许在一个终端窗口中同时运行多个会话。    |
| vim     | 一款高效、可定制的文本编辑器，常用于编程和文本编辑。      |
| wget    | 用于从网络上下载文件，支持 HTTP、HTTPS 和 FTP 协议。      |
| zip     | 用于创建和管理 ZIP 压缩文件。                             |
| kubectl | Kubernetes 的命令行工具，用于管理和操作 Kubernetes 集群。 |

* App 不可使用 GPU 等加速设备。
* sh、bash 和 zsh 是类 Unix 系统中常见的几种 shell：

    * sh：这是最早的 Unix shell，主要用于基本的脚本和系统管理任务。功能简单，兼容性高，但功能相对较少。
    * bash：bash 是 sh 的增强版，提供了更多的功能，比如命令行编辑、命令补全和脚本编程功能。它是许多 Linux 发行版的默认 shell。
    * zsh：zsh 是功能最强大的 shell 之一，提供了强大的命令补全、自动化脚本处理、插件系统等功能。它在兼容 bash 的同时，在用户界面和自定义方面比 bash 更加灵活。

    进一步参阅：

    * [Bash 快捷键大全](https://www.runoob.com/w3cnote/bash-shortcut.html)
    * [Bash 脚本教程](https://wangdoc.com/bash/)
    * [Zsh 开发指南](https://zshguide.readthedocs.io)

* 在使用上，Terminal App、[JupyterLab](./jupyterlab.md) App 的终端和 [Code Server](./codeserver.md) App 的终端几乎没有区别，除了预装的软件不同，Terminal 可以选用多种 shell 环境，以及 JupyterLab 和 Code Server 可以同时打开多个终端。
