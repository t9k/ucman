# 应用

## 概念

**应用（Apps）** 是平台提供各种能力的功能模块，用户可根据自己的需求进行安装和使用。

这里的 Apps 既包括直接 AI 模型驱动的应用程序，例如 vLLM、Open WebUI、Stable Diffusion WebUI，也包含在 AI 研究、开发过程中可能使用到的软件、工具，例如 JupyterLab、PyCharm、TensorBoard、Dify 等。

并且，这些 Apps 可以通过共享存储、API 调用等方式进行协作。

用户在完成某一项任务时可以根据自己的偏好和任务的性质，灵活使用多个应用，进而高效地完成模型开发、训练、部署，AI 应用构建和部署等任务，全面开展与 AI 有关的工作，如下图示意：

<figure class="architecture">
  <img alt="app" src="../assets/app/app.drawio.svg" />
  <figcaption>图 1：用户可以根据自己的偏好和任务的性质，灵活地使用多个 Apps 完成工作任务。</figcaption>
</figure>

例如：

1）在任务 [进行数据并行训练](../task/train-model/dp-training.md) 中：

- 用户在 JupyterLab 应用中进行交互式开发；
- 在 Job Manager 中查看和管理运行的 PyTorchTrainingJob；
- JupyterLab、TensorBoard 和 PyTorchTrainingJob 挂载同一个 PVC，通过文件系统共享数据；
- 用户在 JupyterLab 中拉取的训练代码可以被 PyTorchTrainingJob 执行；
- PyTorchTrainingJob 中产生的 `tfevents` 日志文件可以被 TensorBoard 应用所读取并可视化展示。

2） 在任务 [部署 LLM 推理服务和聊天服务](../task/deploy-model/deploy-llm.md) 中：

- JupyterLab 和 vLLM 共享存储，在 JupyterLab 中下载的模型文件可以被 vLLM 应用所加载；
- NextChat 调用 vLLM 的 LLM 推理服务 API；
- 用户通过 NextChat 提供的简洁美观的 UI 中与 vLLM 部署的 LLM 聊天。

## 运行中的 Apps

点击左侧导航菜单的**应用**，查看所有运行中（已经部署）的应用：

<figure class="screenshot">
  <img alt="list-app" src="../assets/app/list-app.png" />
  <figcaption>图 2：查看所有运行中（已经部署）的应用。</figcaption>
</figure>

## 安装 Apps

点击上图右上角的**部署应用**，查看所有可部署的应用：

<figure class="screenshot">
  <img alt="app-catalog" src="../assets/app/app-catalog.png" />
  <figcaption>图 3：查看所有可部署的应用。</figcaption>
</figure>

## 下一步

- [部署 JupyterLab](jupyter-lab.md)
- 了解 [网络服务](../network/index.md)

