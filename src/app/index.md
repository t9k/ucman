# Apps

## 概念

<b>Apps（应用）</b>是 TensorStack AI 平台的功能模块，用以提供各种能力。

这里的 Apps 既包括由 AI 模型驱动的应用程序，例如 vLLM、Open WebUI、Stable Diffusion WebUI，也包含在 AI 研究、开发过程中可能使用到的软件、工具，例如 JupyterLab、TensorBoard、Dify。利用这些 Apps，用户能够高效地完成模型开发、训练和部署，AI 应用构建和部署等任务，全面开展与 AI 有关的工作。

用户在完成某一项任务时可以根据任务的性质和自身的偏好，灵活地安装和使用多个 Apps，并且这些 Apps 可以通过共享存储、API 调用等方式进行协作，下面是一个示意图：

<figure class="architecture">
  <img alt="app" src="../assets/app/app.drawio.svg" />
  <figcaption>图 1：用户可以根据任务的性质和自身的偏好，灵活地安装和使用多个 Apps 完成工作任务。</figcaption>
</figure>

例如：

1）在任务[进行数据并行训练](../guide/train-model/dp-training.md)中：

- 用户在 [JupyterLab](./jupyterlab.md) App 中进行交互式开发。
- 用户在 [Job Manager](./job-manager.md) App 中查看运行的 PyTorchTrainingJob。
- JupyterLab、PyTorchTrainingJob 和 [TensorBoard](./tensorboard.md) App 挂载同一个 PVC，通过文件系统共享数据：
  - 用户在 JupyterLab 中拉取的训练代码可以被 PyTorchTrainingJob 执行。
  - PyTorchTrainingJob 中产生的 tfevents 日志文件可以被 TensorBoard 所读取并可视化展示。

2）在任务[部署 LLM 推理服务和聊天服务](../guide/deploy-model/deploy-llm.md)中：

- [JupyterLab](./jupyterlab.md) App 和 vLLM App 共享存储，在 JupyterLab 中下载的模型文件可以被 vLLM 所加载。
- NextChat 调用 vLLM 的 LLM 推理服务 API。
- 用户通过 NextChat 提供的简洁美观的 UI 中与 vLLM 部署的 LLM 聊天。

## Apps 列表

这里给出所有可用（需要管理员注册）的 App 的列表，供用户参考。部分 App 提供了链接，这些链接指向的文档会在相应 README 的基础上提供更多的信息。

| App                                         | 分类     | 简介                                                                                                                       |
| ------------------------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------- |
| [Code Server](./codeserver.md)              | IDE      | Code Server 是一个基于浏览器的 VSCode 环境。                                                                               |
| ComfyUI                                     | AI       | ComfyUI 是一个功能强大且模块化的 Stable Diffusion 图形用户界面和后端，支持基于图、节点和流程图设计和执行高级流水线。       |
| Dify                                        | AI       | Dify 是一个开源 LLM 应用开发平台，提供直观的界面，支持 AI 工作流、RAG 管道、Agent 和模型管理，便于从原型到生产的快速开发。 |
| [File Browser](./filebrowser.md)            | Tool     | File Browser 为指定目录提供了一个文件管理界面，用于上传、删除、预览、重命名和编辑文件。                                    |
| Fish Speech                                 | AI       | Fish Speech 是一套全新的 TTS 解决方案，支持无微调的音色克隆。                                                              |
| GPT Researcher                              | AI       | GPT Researcher 是一个智能体代理，专门用于各种任务的综合在线研究。                                                          |
| [Job Manager](./job-manager.md)             | Tool     | Job Manager 是一个计算作业管理控制台，支持作业创建、状态查看和事件监控等功能。                                             |
| [JupyterLab (CPU)](./jupyterlab.md)         | IDE      | JupyterLab 是一个基于 Web 的交互式开发环境，用于代码开发和数据处理，支持数据科学、科学计算和机器学习等任务。               |
| [JupyterLab (NVIDIA GPU)](./jupyterlab.md)  | IDE      | JupyterLab (NVIDIA GPU) 是一个基于 Web 的交互式开发环境，适用于 NVIDIA GPU 的计算任务。                                    |
| [JupyterLab (Enflame GCU)](./jupyterlab.md) | IDE      | JupyterLab (Enflame GCU) 是一个基于 Web 的开发环境，适用于 Enflame GCU 的计算任务。                                        |
| [JupyterLab (Hygon DCU)](./jupyterlab.md)   | IDE      | JupyterLab (Hygon DCU) 是一个基于 Web 的开发环境，适用于 Hygon DCU 的计算任务。                                            |
| Label Studio                                | AI, Tool | Label Studio 是一个数据标注工具。                                                                                          |
| Llama Board                                 | AI       | Llama Board 是 LLaMA-Factory 项目的网页 UI，用于（增量）预训练、指令微调和评估开源 LLM。                                   |
| MongoDB                                     | Database | MongoDB 是一个开源的 NoSQL 数据库，使用 JSON 样式文档存储数据，支持自动扩展和高性能，适合开发云原生应用。                  |
| NextChat                                    | AI       | NextChat 是一个设计精良的 ChatGPT 网页 UI，支持多种聊天服务，包括 ChatGPT、Claude、Gemini 和本地推理服务。                 |
| Ollama                                      | AI       | Ollama 提供本地使用 LLM 的解决方案。                                                                                       |
| Open WebUI                                  | AI       | Open WebUI 是一个用户友好的聊天交互网页界面。                                                                              |
| PostgreSQL                                  | Database | PostgreSQL 是一个开源对象关系数据库，支持 ACID 事务，提供外键、联接、视图、触发器和存储过程等功能。                        |
| Qdrant                                      | Database | Qdrant 是一个面向 AI 应用的向量数据库。                                                                                    |
| Redis                                       | Database | Redis 是一个内存数据库，支持多种数据结构如字符串、列表、集合等，同时数据持久化到磁盘。                                     |
| RStudio                                     | IDE      | RStudio 是一个集成开发环境，帮助你提高 R 和 Python 的开发效率。                                                            |
| Search with Lepton                          | AI       | Search with Lepton 是一个开源的对话式搜索引擎（conversational search engine）。                                            |
| [Service Manager](./service-manager.md)     | Tool     | Service Manager 是一个推理服务管理控制台。                                                                                 |
| Stable Diffusion WebUI aki                  | AI       | Stable Diffusion WebUI aki 是基于开源项目 Stable Diffusion WebUI 的整合包，由 bilibili@秋葉 aaaki 制作。                   |
| [TensorBoard](./tensorboard.md)             | Tool     | TensorBoard 是 TensorFlow 的可视化工具，展示模型训练过程中的各种数据。                                                     |
| [Terminal](./terminal.md)                   | Tool     | Terminal 是一个在浏览器中直接打开和操作的集群终端，便于管理集群。                                                          |
| vLLM                                        | AI       | vLLM 是一个高吞吐量和内存高效的 LLM 推理和服务引擎。                                                                       |
| vLLM (Llama 3.1)                            | AI       | 使用 vLLM 部署 Llama 3.1 系列模型。                                                                                        |
| vLLM (Enflame GCU)                          | AI       | vLLM (Enflame GCU) 是一个高吞吐量和内存高效的 LLM 推理和服务引擎，适用于燧原 GCU。                                         |
| vLLM (Hygon DCU)                            | AI       | vLLM (Hygon DCU) 是一个高吞吐量和内存高效的 LLM 推理和服务引擎，适用于海光 DCU。                                           |
| [Workflow](./workflow.md)                   | Tool     | Workflow 是一个工作流管理控制台，支持工作流创建、状态查看和事件监控等功能。                                             |


## 下一步

* 进一步了解各个 App
* 学习如何[管理 App](../guide/manage-app/index.md)
