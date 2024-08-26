# TensorBoard

<a target="_blank" rel="noopener noreferrer" href="https://www.tensorflow.org/tensorboard?hl=zh-cn">TensorBoard</a> 是一款专为深度学习项目设计的可视化工具套件，主要用于监控和分析深度学习模型的训练过程。它能够实时展示模型训练中的各种指标，如损失函数、准确率、梯度分布等，帮助开发者直观地了解模型的性能和训练进展。TensorBoard 还提供了模型结构可视化功能，使用户能够清晰地查看复杂神经网络的架构。

在实际应用中，TensorBoard 的数据记录和可视化功能尤为有用。用户可以通过简单的代码将训练过程中的关键数据记录下来，然后在 TensorBoard 界面中以图表形式呈现。这些图表包括折线图、直方图、散点图等，能够帮助开发者快速发现训练中的问题，如过拟合、梯度消失等。此外，TensorBoard 的超参数优化工具允许用户比较不同配置下模型的表现，从而更容易找到最佳的模型参数设置。

<figure class="screenshot">
  <img alt="tensorboard-official" src="../assets/app/tensorboard/tensorboard-official.gif" />
</figure>

## 使用方法

待 App 就绪后，点击右侧的 <span class="twemoji"><svg class="MuiSvgIcon-root MuiSvgIcon-colorPrimary MuiSvgIcon-fontSizeMedium css-jxtyyz" focusable="false" aria-hidden="true" viewBox="0 0 24 24" data-testid="OpenInNewIcon"><path d="M19 19H5V5h7V3H5c-1.11 0-2 .9-2 2v14c0 1.1.89 2 2 2h14c1.1 0 2-.9 2-2v-7h-2zM14 3v2h3.59l-9.83 9.83 1.41 1.41L19 6.41V10h2V3z"></path></svg></span> 进入网页 UI，查看可视化数据。

<figure class="screenshot">
  <img alt="tensorboard" src="../assets/app/tensorboard/tensorboard.png" />
</figure>

对于 TensorFlow 框架，记录各种类型数据和使用网页 UI 的方法请参阅 <a target="_blank" rel="noopener noreferrer" href="https://www.tensorflow.org/tensorboard/get_started?hl=zh-cn">TensorBoard 指南</a>。

对于 PyTorch 框架，记录各种类型数据和使用网页 UI 的方法请参阅：

* <a target="_blank" rel="noopener noreferrer" href="https://pytorch.org/docs/stable/tensorboard.html">torch.utils.tensorboard</a> 文档
* <a target="_blank" rel="noopener noreferrer" href="https://pytorch.org/tutorials/intermediate/tensorboard_tutorial.html">Visualizing models, data, and training with TensorBoard</a> 教程
* <a target="_blank" rel="noopener noreferrer" href="https://pytorch.org/tutorials/intermediate/tensorboard_profiler_tutorial.html">PyTorch Profiler With TensorBoard</a> 教程

## 使用说明

你也可以[在 JupyterLab App 中创建 TensorBoard 示例](./jupyter-lab.md#tensorboard-插件)，展示的内容是相同的。

## 下一步

在下列任务中学习使用 TensorBoard App：

* [进行数据并行训练](../task/train-model/dp-training.md)
* [进行 LLM 大规模预训练](../task/train-model/llm-large-scale-pretraining.md)
* [分析性能](../task/train-model/profile.md)