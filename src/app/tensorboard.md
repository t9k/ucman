# TensorBoard

<a target="_blank" rel="noopener noreferrer" href="https://www.tensorflow.org/tensorboard?hl=zh-cn">TensorBoard</a> 是一款专为深度学习项目设计的可视化工具套件，主要用于监控和分析深度学习模型的训练过程。它能够实时展示模型训练中的各种指标，如损失函数、准确率、梯度分布等，帮助开发者直观地了解模型的性能和训练进展。TensorBoard 还提供了模型结构可视化功能，使用户能够清晰地查看复杂神经网络的架构。

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

* <a target="_blank" rel="noopener noreferrer" href="https://pytorch.org/docs/stable/tensorboard.html">torch.utils.tensorboard</a>
* <a target="_blank" rel="noopener noreferrer" href="https://pytorch.org/tutorials/intermediate/tensorboard_tutorial.html">Visualizing models, data, and training with TensorBoard</a>
* <a target="_blank" rel="noopener noreferrer" href="https://pytorch.org/tutorials/intermediate/tensorboard_profiler_tutorial.html">PyTorch Profiler With TensorBoard</a>

## 配置和使用说明

### 数据源

App 支持 PVC 和 S3 两种数据源，配置时必须且只能选择其中一种。

如使用 PVC 作为数据源，将 `logDir.pvc[0].name` 和 `logDir.pvc[0].subPath` 字段的值分别设为 PVC 的名称和目录，位于该目录及其子目录下的所有 tfevents 文件都将被可视化展示。

下面的配置示例可视化展示 PVC `tutorial` 的 `train/logs` 目录下的所有 tfevents 文件：

```yaml
logDir:
  pvc:
    - name: tutorial
      subPath:
        - "train/logs"

...
```

<figure class="screenshot">
  <img alt="pvc" src="../assets/app/tensorboard/pvc.png" />
</figure>

如使用 S3 作为数据源，将 `logDir.s3️.secretRef.name` 字段的值设为 [S3-env 类型的 Secret](../guide/manage-storage-network-and-auxiliary/secret-s3.md) 的名称，将 `logDir.s3️.uri` 字段的值设为以 `/` 结尾的 S3 URL，所有以该 URL 作为前缀的 tfevents 文件都将被可视化展示。

下面的配置示例可视化展示 URL 匹配 `s3://folder/**` 的所有 tfevents 文件，由 Secret my-s3-env 提供访问凭证：

```yaml
logDir:
  s3:
    secretRef:
      name: "my-s3-env"
    uri:
      - "s3://folder/"

...
```

<figure class="screenshot">
  <img alt="pvc" src="../assets/app/tensorboard/s3.png" />
</figure>

## 下一步

在下列任务中学习使用 TensorBoard App：

* [进行数据并行训练](../guide/train-model/dp-training.md)
* [进行 LLM 大规模预训练](../guide/train-model/llm-large-scale-pretraining.md)
* [分析性能](../guide/train-model/profile.md)
