# 概述

## 目标读者

本书《TensorStack AI 计算平台 - User Console 用户手册》 的目标读者是“TensorStack AI 计算平台”的使用者，包括：

- AI 研究人员
- 建模工程师
- 数据处理工程师
- 机器学习工程师
- AI 应用开发工程师等

系统管理员、运维人员需要参考另外的《TensorStack AI 计算平台 - 管理员手册》系列。

## 产品概述

TensorStack AI 计算平台是面向 AI 集群的系统软件，针对 AI 集群的硬件架构和 AI 领域的计算任务的特性和需求，提供稳定、可扩展的 AI 技术平台能力，服务 AI 技术研发、应用和规模化落地。

AI 集群的拥有者可以使用这套软件，构建自己的 AI 私有云或 AI 混合云基础设施服务。

<aside class="note info">
<div class="title">TensorStack AI 计算平台的功能</div>

产品提供两方面的能力：

1. **计算服务**：针对 AI 集群使用者，向上支持各种 AI 计算场景，例如云端开发环境、模型训练、部署推理服务、应用开发等；
1. **集群管理**：针对 AI 集群的管理、运维人员，提供方便的机制，实施各种资源、安全、数据等管理策略。

</aside>

通过先进的架构和丰富的 API + Apps，TensorStack AI 计算平台合理地隐藏了分布式并行、异构计算、加速计算等技术的复杂细节，提高了抽象层次，并为 AI 领域的各种计算提供了针对性的支持，极大地提升了 AI 技术研究、开发、应用的工作效率。

<figure class="architecture">
  <img alt="t9k-arch" src="./assets/overview/t9k-arch.png" />
  <figcaption>图 1：TensorStack AI 计算平台为 AI 集群提供先进的 AI 基础设施能力。API 层提供了可扩展、可编程、云原生的系统服务；Apps 层为多样化的应用场景提供全面、完善的支撑。用户可根据需求，安装各种 Apps（IDE、LLM 开发框架、并行训练管理、推理服务管理、资源管理工具、完整的 AI 应用等），满足 AI 研究、开发和应用等业务需求。</figcaption>
</figure>

## User Console

“User Console（用户控制台）”是 TensorStack AI 计算平台的 **Apps 层**入口，为 **AI 集群使用者**提供了一个集中使用集群功能的图形化使用界面。

<b>Apps（应用）</b>是 User Console 的核心，作为用户使用平台提供的所有功能的主要入口。无论是进行 AI 研究、开发还是部署，用户都是通过安装和使用相应的 Apps 来完成。例如：

- AI 研究员可能通过 [JupyterLab App](./app/jupyterlab.md) 作为其日常开发环境，使用 [Job Manager App](./app/job-manager.md) 运行和管理并行训练任务。
- AI 应用工程师可能使用 vLLM App 或 Stable Diffusion WebUI App 来部署和测试 AI 模型。

<aside class="note info">
<div class="title">Apps 为中心</div>

这种以 Apps 为中心的设计使得用户：

1. 能够直观地访问和管理他们所需的所有 AI 相关工具和服务；
2. 根据自己的偏好和习惯，及工作的性质，灵活的选择合适的 Apps；
3. 可方便的通过安装新的 Apps 来获得能力扩展，随时使用 AI 领域的各种新技术个工具。

</aside>

围绕 Apps，User Console 的其他功能都为其提供方便的支持。

- **持久卷（PV + PVC）** 和<b>适配器（StorageShim）</b>支持 Apps 能够持久化保存数据，存储模型、数据集和训练数据等；
- 网络服务如 **Service** 和 **Ingress**，为 Apps 提供内部通信和外部访问的能力，使得 Apps 能够相互协作并对外提供服务；
- 辅助资源如 **Secret** 和 **ConfigMap** 则为 Apps 提供配置管理和敏感信息保护，确保 Apps 能够安全且灵活地运行。

除此之外，User Console 还提供了账户设置功能，帮助用户管理账户的项目和 API Key，接受账单和来自平台的告警信息。

### 使用流程

用户使用 User Console 的基本交互流程如下：

1. 登陆系统；
2. 在项目中安装并使用 Apps，必要时创建存储、网络和辅助资源等；
3. 在 Apps 中完成工作（创建计算任务，部署推理服务，使用推理服务，等等）。

<figure class="architecture">
  <img alt="use-user-console" src="./assets/overview/use-user-console.drawio.svg" />
  <figcaption>图 2：用户登陆系统后在项目中安装并使用 Apps 完成工作。</figcaption>
</figure>

### 准备工作

在开始使用 User Console 之前，你需要：

1. 了解[账户](./security/account.md)和[项目](./security/project.md)的概念。
1. 拥有一个账户，且该账户是至少一个项目的成员。

<aside class="note tip">
<div class="title">提示</div>

如果你没有账户或不是任何项目的成员，请联系平台的管理员。

</aside>

## 下一步

* [了解和使用 Apps](./app/index.md)
* [了解和使用 APIs](./api/index.md)
* 进入 [User Console 首页](./guide/homepage.md)
