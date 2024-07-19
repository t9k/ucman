# 部署 Job Manager

Job Manager 是一个用于管理计算任务的控制台，可执行创建任务、查看任务状态、监控任务事件等操作。

点击 **Job Manager** 应用，进入详情页面：

<figure class="screenshot">
  <img alt="select-job-manager" src="../assets/app/select-job-manager.png" />
</figure>

在详情页面，你可以看到如何配置 Jupyter Lab，这包含了示例 YAML 和参数说明。确认信息后，点击右上角的**立刻部署**进入创建页面：

<figure class="screenshot">
  <img alt="readme-job-manager" src="../assets/app/readme-job-manager.png" />
</figure>

Job Manager 的可配置参数较多，因此没有通过**表单**配置的选项。**YAML 编辑器**中已经预设了一组可用的值，你可以直接使用预设值部署：

<figure class="screenshot">
  <img alt="yaml-job-manager" src="../assets/app/yaml-job-manager.png" />
</figure>

确认配置后，点击右上角的**部署**，并等待应用就绪：

<figure class="screenshot">
  <img alt="wait-for-job-manager" src="../assets/app/wait-for-job-manager.png" />
</figure>

服务就绪后，点击右侧的**链接**，即可使用该应用：

<figure class="screenshot">
  <img alt="ui-job-manager" src="../assets/app/ui-job-manager.png" />
</figure>

<aside class="note tip">
<div class="title">提示</div>

Job 的定义请参考<a target="_blank" rel="noopener noreferrer" href="https://t9k.github.io/user-manuals/latest/modules/jobs/index.html">用户文档 Job</a>，使用示例请参考<a target="_blank" rel="noopener noreferrer" href="https://t9k.github.io/user-manuals/latest/tasks/model-training.html">运行模型训练</a>。

</aside>
