# 首页

首页是 “用户控制台（User Console）” 的起时界面，为用户提供功能快速入口，及账户的基本统计信息。

## 概述

在开始使用 “User Console” 前，你需要：

1. 了解 <a target="_blank" rel="noopener noreferrer" href="https://t9k.github.io/user-manuals/latest/modules/security/account.html">账户</a> 和 <a target="_blank" rel="noopener noreferrer" href="https://t9k.github.io/user-manuals/latest/modules/security/project.html">项目</a> 的概念。
1. 拥有一个账户，且该账户至少是一个项目的成员。

User Console 首页包含以下元素：

1. 顶部导航栏（包括白色和蓝色两个导航栏）
1. 左侧导航菜单
1. 中间的各项功能和账户统计

<figure class="screenshot">
  <img alt="homepage" src="./assets/homepage.png" />
</figure>

顶部白色导航栏的右侧有一些快捷按键，分别提供了以下功能：

1. <span class="twemoji"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M19 13h-6v6h-2v-6H5v-2h6V5h2v6h6v2Z"/></svg></span>：使用 YAML 创建 K8s 资源
1. <span class="twemoji"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="m12.87 15.07-2.54-2.51.03-.03A17.52 17.52 0 0 0 14.07 6H17V4h-7V2H8v2H1v2h11.17C11.5 7.92 10.44 9.75 9 11.35 8.07 10.32 7.3 9.19 6.69 8h-2c.73 1.63 1.73 3.17 2.98 4.56l-5.09 5.02L4 19l5-5 3.11 3.11.76-2.04M18.5 10h-2L12 22h2l1.12-3h4.75L21 22h2l-4.5-12m-2.62 7 1.62-4.33L19.12 17h-3.24Z"/></svg></span>：切换语言
1. <span class="twemoji"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M11 18h2v-2h-2v2m1-16A10 10 0 0 0 2 12a10 10 0 0 0 10 10 10 10 0 0 0 10-10A10 10 0 0 0 12 2m0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8m0-14a4 4 0 0 0-4 4h2a2 2 0 0 1 2-2 2 2 0 0 1 2 2c0 2-3 1.75-3 5h2c0-2.25 3-2.5 3-5a4 4 0 0 0-4-4Z"/></svg></span>：打开用户文档
1. <span class="twemoji"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M10 21h4c0 1.1-.9 2-2 2s-2-.9-2-2m11-2v1H3v-1l2-2v-6c0-3.1 2-5.8 5-6.7V4c0-1.1.9-2 2-2s2 .9 2 2v.3c3 .9 5 3.6 5 6.7v6l2 2m-4-8c0-2.8-2.2-5-5-5s-5 2.2-5 5v7h10v-7Z"/></svg></span>：显示当前账户订阅的告警信息
1. <span class="twemoji"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M12 19.2c-2.5 0-4.71-1.28-6-3.2.03-2 4-3.1 6-3.1s5.97 1.1 6 3.1a7.232 7.232 0 0 1-6 3.2M12 5a3 3 0 0 1 3 3 3 3 0 0 1-3 3 3 3 0 0 1-3-3 3 3 0 0 1 3-3m0-3A10 10 0 0 0 2 12a10 10 0 0 0 10 10 10 10 0 0 0 10-10c0-5.53-4.5-10-10-10Z"/></svg></span>：显示当前用户信息，可用于退出登录

蓝色面包屑导航栏的左侧可以显示/隐藏左侧导航菜单，右侧是手动刷新按键和自动刷新的开关。

左侧导航菜单和中间区域对应的各项功能在下文介绍。

## 下一步

- [创建集群存储](./storage/index.md)，或者
- [部署应用](./app/index.md)
