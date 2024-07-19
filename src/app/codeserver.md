# 部署 CodeServer

CodeServer 是一个可以通过浏览器访问的 VSCode 服务。本章演示如何部署 CodeServer 应用。

点击应用 **CodeServer**，进入详情页面：

<figure class="screenshot">
  <img alt="select-codeserver" src="../assets/app/select-codeserver.png" />
</figure>

在详情页面，你可以看到如何配置 CodeServer，这包含了示例 YAML 及参数说明。确认信息后，点击右上角的**立刻部署**进入创建页面：

<figure class="screenshot">
  <img alt="readme-codeserver" src="../assets/app/readme-codeserver.png" />
</figure>

通过**表单**配置 CodeServer。为 `spec.storageName` 字段赋值 `demo`，即之前创建的存储卷的名称。最后点击右上角的**部署**：

<figure class="screenshot">
  <img alt="readme-codeserver" src="../assets/app/readme-codeserver.png" />
</figure>

查看创建成功的 CodeServer 服务，并等待它就绪：

<figure class="screenshot">
  <img alt="wait-for-codeserver" src="../assets/app/wait-for-codeserver.png" />
</figure>

点击 CodeServer 右侧的**链接**，即可使用该应用：

<figure class="screenshot">
  <img alt="ui-codeserver" src="../assets/app/ui-codeserver.png" />
</figure>
