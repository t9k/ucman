# 部署 Jupyter Lab

<a target="_blank" rel="noopener noreferrer" href="https://jupyterlab.readthedocs.io/en/latest/">JupyterLab</a> 是一款非常流行的机器学习开发工具，它通过友好易用的 Web 界面提供交互式计算环境，支持多种编程语言和执行环境，在机器学习、AI、数据处理、数值模拟、统计建模、数据可视化等领域被广泛使用。

点击 **Jupyter Lab** 应用，可以选择 CPU 版本或者 GPU 版本，进入详情页面：

<figure class="screenshot">
  <img alt="select-jupyter-lab" src="../assets/app/select-jupyter-lab.png" />
</figure>

在详情页面，你可以看到如何配置 Jupyter Lab，这包含了可用镜像列表、示例 YAML 和参数说明。确认信息后，点击右上角的**立刻部署**进入创建页面：

<figure class="screenshot">
  <img alt="readme-jupyter-lab" src="../assets/app/readme-jupyter-lab.png" />
</figure>

点击上方的 **README** 再次查看说明信息，这些信息可以帮助你正确地设置 Jupyter Lab 的运行参数：

<figure class="screenshot">
  <img alt="parameter-jupyter-lab" src="../assets/app/parameter-jupyter-lab.png" />
</figure>

<aside class="note tip">
<div class="title">提示</div>

用户通常可以通过**表单**或者 **YAML 编辑器**进行应用的配置。前者提供了一个直观的界面，让你可以方便地填写和修改参数；而后者则需要你直接编辑 YAML 文件，适合熟悉 YAML 格式的资深用户使用。如果你不确定参数的含义，可以点击**README**标签来查看各个参数的说明。

</aside>

较简单的配置方式是直接在**表单**页面中填写给出的字段：

<figure class="screenshot">
  <img alt="form-jupyter-lab" src="../assets/app/form-jupyter-lab.png" />
</figure>

熟悉 YAML 的用户可以在 **YAML 编辑器**中编辑相应的字段：

<figure class="screenshot">
  <img alt="yaml-jupyter-lab" src="../assets/app/yaml-jupyter-lab.png" />
</figure>

填写配置后，点击右上角的**部署**。然后等待新的 Jupyter Lab 服务就绪：

<figure class="screenshot">
  <img alt="wait-for-jupyter-lab" src="../assets/app/wait-for-jupyter-lab.png" />
</figure>

服务就绪后，点击右侧的**链接**，即可使用该应用：

<figure class="screenshot">
  <img alt="ui-jupyter-lab" src="../assets/app/ui-jupyter-lab.png" />
</figure>
