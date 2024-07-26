# Summary

[概述](./overview.md)

---

* [首页](homepage.md)

* [存储](storage/index.md)
    * [创建 PVC](storage/volume.md)
    * [创建 StorageShim](storage/adapter.md)

* [应用](app/index.md)
    * [部署 JupyterLab](app/jupyter-lab.md)
    * [部署 Terminal](app/terminal.md)
    * [部署 FileBrowser](app/filebrowser.md)
    * [部署 CodeServer](app/codeserver.md)
    * [部署 TensorBoard](app/tensorboard.md)
    * [部署 Job Manager](app/job-manager.md)
    * [部署 PostgreSQL](app/postgresql.md)
    
* [网络服务](network/index.md)
    * [查看 Service](network/service.md)
    * [查看 Ingress](network/ingress.md)

* [辅助资源](auxiliary/index.md)
    * [创建 Secret](auxiliary/secret.md)
    * [创建 ConfigMap](auxiliary/configmap.md)

* [账户设置](account/index.md)
    * [查看账户信息](account/view-profile.md)
    * [项目管理](account/project-management.md)
    * [安全设置](account/security-setting.md)
    * [查看账单](account/view-bill.md)
    * [告警通知](account/alert-notification.md)

* [任务](task/index.md)
    * [上传和下载文件](task/upload-and-download-file.md)
    * [训练模型](task/train-model/index.md)
        * [进行数据并行训练](task/train-model/dp-training.md)
        * [进行 LLM 大规模预训练](task/train-model/llm-large-scale-pretraining.md)
        * [分析性能](task/train-model/profile.md)
        <!-- * [微调 LLM](task/train-model/finetune-llm.md) -->
        <!-- * [使用 Job Manager](task/train-model/use-job-manager.md) -->
    * [部署模型推理服务](task/deploy-model/index.md)
        * [部署 LLM 推理服务](task/deploy-model/deploy-llm.md)
