# Summary

[概述](./overview.md)

* [快速开始](get-started/index.md)
    * [训练你的第一个模型](get-started/training-first-model.md)<!--在 notebook 完成训练-->
    * [进行并行训练](get-started/parallel-training.md)<!--使用 job 进行并行训练-->
    * [部署模型](get-started/deploy-model.md)<!--保存数据集和模型文件-->

---

* [Apps](app/index.md)
    * [Code Server](app/codeserver.md)
    * [File Browser](app/filebrowser.md)
    * [JupyterLab](app/jupyterlab.md)
    * [TensorBoard](app/tensorboard.md)
    * [Terminal](app/terminal.md)
    * [Job Manager](app/job-manager.md)
    * [Service Manager](app/service-manager.md)
    * [Virtual Machine](app/virtual-machine.md)
    * [Workflow](app/workflow.md)
    <!-- * [RStudio](app/rstudio.md) -->
    <!-- * [Argo Workflow](app/argo-workflow.md) -->
    <!-- * [PostgreSQL](app/postgresql.md) -->

* [APIs](api/index.md)

    * [存储](api/storage/index.md)
        * [PVC](api/storage/pvc.md)
        * [StorageShim](api/storage/storageshim.md)

    * [网络服务](api/network/index.md)
        * [Service](api/network/service.md)
        * [Ingress](api/network/ingress.md)
        * [Gateway API](api/network/gateway-api.md)

    * [辅助](api/auxiliary/index.md)
        * [Secret](api/auxiliary/secret.md)
        * [ConfigMap](api/auxiliary/configmap.md)
        <!-- * [ServiceAccountToken](api/auxiliary/serviceaccounttoken.md) -->

    * [T9k Job](api/t9k-job/index.md)
        * [GenericJob](api/t9k-job/genericjob.md)
        * [PyTorchTrainingJob](api/t9k-job/pytorchtrainingjob.md)
        * [TensorFlowTrainingJob](api/t9k-job/tensorflowtrainingjob.md)
        * [DeepSpeedJob](api/t9k-job/deepspeedjob.md)
        * [ColossalAIJob](api/t9k-job/colossalaijob.md)
        * [XGBoostTrainingJob](api/t9k-job/xgboosttrainingjob.md)
        * [MPIJob](api/t9k-job/mpijob.md)
        * [BeamJob](api/t9k-job/beamjob.md)

    * [T9k Service](api/t9k-service/index.md)
        * [SimpleMLService](api/t9k-service/simplemlservice.md)
        * [MLService](api/t9k-service/mlservice.md)
            * [日志收集](api/t9k-service/mlservice-logger.md)
        * [模型存储](api/t9k-service/storage.md)

    * [工作流](api/workflow/index.md)
        * [WorkflowTemplate](api/workflow/workflowtemplate.md)
        * [WorkflowRun](api/workflow/workflowrun.md)
        * [CronWorkflowRun](api/workflow/cronworkflowrun.md)
        * [WorkflowTrigger](api/workflow/workflowtrigger.md)

    * [AutoTuneExperiment](api/autotuneexperiment.md)
    * [ImageBuilder](api/imagebuilder.md)
    * [DataCube](api/datacube.md)

* [账户和安全](security/index.md)
    * [账户](security/account.md)
    * [项目](security/project.md)
    * [告警通知](security/alert.md)
    * [账单](security/bills.md)

---

* [操作指南](guide/index.md)

    * [User Console 首页](guide/homepage.md)

    * [管理 App](guide/manage-app/index.md)
        * [安装 App](guide/manage-app/install-app.md)
        * [卸载 App](guide/manage-app/uninstall-app.md)
        * [查看 App 详情](guide/manage-app/view-app-detail.md)

    * [管理存储、网络服务和辅助资源](guide/manage-storage-network-and-auxiliary/index.md)
        * [管理 PVC](guide/manage-storage-network-and-auxiliary/pvc.md)
        * [创建 StorageShim](guide/manage-storage-network-and-auxiliary/storageshim.md)
        * [查看 Service](guide/manage-storage-network-and-auxiliary/service.md)
        * [查看 Ingress](guide/manage-storage-network-and-auxiliary/ingress.md)
        * [管理 Secret](guide/manage-storage-network-and-auxiliary/secret.md)
            * [SSH 场景](guide/manage-storage-network-and-auxiliary/secret-ssh.md)
            * [S3 场景](guide/manage-storage-network-and-auxiliary/secret-s3.md)
            <!-- * [Docker 场景](guide/manage-storage-network-and-auxiliary/secret-docker.md) -->
        * [管理 ConfigMap](guide/manage-storage-network-and-auxiliary/configmap.md)

    * [设置账户](guide/account/index.md)
        * [账户信息](guide/account/view-profile.md)
        * [项目管理](guide/account/project-management.md)
        * [安全设置](guide/account/security-setting.md)
        * [查看账单](guide/account/view-bill.md)
        * [告警通知](guide/account/alert-notification.md)

    * [训练模型](guide/train-model/index.md)
        * [进行数据并行训练](guide/train-model/dp-training.md)
        * [进行 LLM 大规模预训练](guide/train-model/llm-large-scale-pretraining.md)
        * [分析性能](guide/train-model/profile.md)
        * [指令微调 LLM](guide/train-model/llm-instruction-tuning.md)

    * [部署模型推理服务](guide/deploy-model/index.md)
        * [部署 PyTorch 模型](guide/deploy-model/deploy-pytorch.md)
        * [部署 LLM 推理服务和聊天服务](guide/deploy-model/deploy-llm.md)

    * [专题](guide/theme/index.md)
        * [上传和下载文件](guide/theme/upload-and-download-file.md)
        <!-- [mlflow] -->
        <!-- [argo] -->
        <!-- [使用特定类型的 GPU] 不同厂商、不同型号 nodeselector -->

---

* [命令行工具和 SDK](tool/index.md)

    * [命令行工具：t9k](tool/cli-t9k/index.md)
        * [用户指南](tool/cli-t9k/guide.md)
        * [命令](tool/cli-t9k/commands.md)

    * [命令行工具：t9k-pf](tool/cli-t9k-pf/index.md)
        * [用户指南](tool/cli-t9k-pf/guide.md)
        * [命令](tool/cli-t9k-pf/commands.md)

    <!-- * [Python SDK：t9k](tool/python-sdk-t9k/index.md)
        * [用户指南](tool/python-sdk-t9k/guide.md)
        * [API](tool/python-sdk-t9k/api/index.md)
            * [t9k.ah](tool/python-sdk-t9k/api/t9k-ah.md)
            * [t9k.ah.core](tool/python-sdk-t9k/api/t9k-ah-core.md)
            * [t9k.config](tool/python-sdk-t9k/api/t9k-config.md)
            * [t9k.em](tool/python-sdk-t9k/api/t9k-em.md)
            * [t9k.tuner](tool/python-sdk-t9k/api/t9k-tuner.md) -->

    <!-- * [Codepack](tool/codepack/index.md)
        * [概念](tool/codepack/concepts.md)
        * [Codepack 定义](tool/codepack/definition.md)
        * [命令行工具](tool/codepack/cli.md)
        * [示例](tool/codepack/example.md) -->

* [参考](reference/index.md)
    * [常见问题](reference/faq/index.md)
        * [App 使用中的常见问题](reference/faq/faq-in-app-usage.md)
        * [JupyterLab 使用中的常见问题](reference/faq/faq-in-jupyterlab-usage.md)
    * [API 参考](reference/api-reference/index.md)
        * [Project](reference/api-reference/project.md)
        * [GenericJob](reference/api-reference/genericjob.md)
        * [TensorFlowTrainingJob](reference/api-reference/tensorflowtrainingjob.md)
        * [PyTorchTrainingJob](reference/api-reference/pytorchtrainingjob.md)
        * [XGBoostTrainingJob](reference/api-reference/xgboosttrainingjob.md)
        * [ColossalAIJob](reference/api-reference/colossalaijob.md)
        * [DeepSpeedJob](reference/api-reference/deepspeedjob.md)
        * [MPIJob](reference/api-reference/mpijob.md)
        * [BeamJob](reference/api-reference/beamjob.md)
        * [TensorBoard](reference/api-reference/tensorboard.md)
        * [Notebook](reference/api-reference/notebook.md)
        * [AutoTuneExperiment](reference/api-reference/autotune.md)
        * [Explorer](reference/api-reference/explorer.md)
        * [StorageShim](reference/api-reference/storageshim.md)
        * [Scheduler](reference/api-reference/scheduler.md)
        * [Workflow](reference/api-reference/workflow.md)
        * [WorkflowTrigger](reference/api-reference/workflowtrigger.md)
        * [SimpleMLService](reference/api-reference/simplemlservice.md)
        * [MLService](reference/api-reference/mlservice.md)
        * [VirtualServer](reference/api-reference/virtualserver.md)
        * [DataCube](reference/api-reference/datacube.md)
        * [ServiceAccountToken](reference/api-reference/serviceaccounttoken.md)
