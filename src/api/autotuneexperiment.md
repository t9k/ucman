# AutoTuneExperiment

你可以通过创建 AutoTuneExperiment 来实现自动优化模型超参数。

## 创建 AutoTuneExperiment

下面是一个基本的 AutoTuneExperiment 示例：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
metadata:
  name: experiment-sample
spec:
  maxExecSeconds: 7200
  maxTrialNum: 50
  trialConcurrency: 3
  searchSpace: |-
    {
      "batch_size": {"_type": "choice", "_value": [512, 1024, 2048, 4096, 8092]},
      "learning_rate": {"_type": "choice", "_value": [0.0001, 0.001, 0.01, 0.1]}
    }
  trainingConfig:
    type: tensorflow
    tasks:
      - type: worker
        replicas: 1
        template:
          spec:
            securityContext:
              runAsUser: 1000
            containers:
              - command:
                  - python3
                  - dist_mnist.py
                workingDir: /mnt/mnist-distributed
                imagePullPolicy: IfNotPresent
                image: t9kpublic/tensorflow-1.15.2:tuner-2.23
                name: tensorflow
  tuner:
    builtinTunerName: TPE
    classArgs: |-
      {
        "optimize_mode": "minimize", 
        "constant_liar_type": "min"
      }
```

在该例中：

* 使用 TensorFlowTrainingJob 进行训练（由 `trainingConfig` 字段指定，详细配置方法请参阅[训练框架支持](#训练框架支持)）。
* 使用 TPE 算法进行超参数调优，算法的参数为 `{"optimize_mode": "minimize", "constant_liar_type": "min"}`（由 `tuner` 字段指定，更多参数请参阅[超参数调优算法](./hpo-algorithm.md)）。
* 超参数的搜索空间为 `{"batch_size": {"_type": "choice", "_value": [512, 1024, 2048, 4096, 8092]},"learning_rate": {"_type": "choice", "_value": [0.0001, 0.001, 0.01, 0.1]}}`（由 `searchSpace` 字段指定，搜索空间的格式及含义请参阅[搜索空间](./autotune-search-space.md)）。
* 可以同时测试 3 组超参数，最大测试次数为 50（分别由 `trialConcurrency` 和 `maxTrialNum` 字段指定），实验的最大执行时间为 2 小时（7200 秒，由 `maxExecSeconds` 字段指定），如果时间超出，实验进入 `TIMEOUT` 状态。

## AIStore 的使用

AutoTuneExperiment 支持将实验数据存储在 AIStore 中，通过设置 `spec.aistore` 字段以使用 AIStore 数据库，其中：

* `spec.aistore.folder` 声明实验数据存储在哪个 AIStore 文件夹中，内容应填写文件夹的 ID。
* `spec.aistore.secret` 引用一个 K8s Secret，其中应记录 API Key，用于上传数据时进行身份验证。

下面是一个存储 APIKey 的 Secret 示例：

```yaml
apiVersion: v1
data:
  apikey: ZDQyMjJjZjUtMmI0Ni00Mjk2LWFiMzYtYWI4NmVhZGUwZjQx
kind: Secret
metadata:
  name: aistore-secret
type: Opaque
```

## 训练框架支持

AutoTuneExperiment 支持对多种训练框架进行超参数的调优，包括 TensorFlow、PyTorch 和 XGBoost。

AutoTuneExperiment 通过设置 `spec.trainingConfig` 字段实现对上述框架的支持。其中：

* `spec.trainingConfig.type` 可设置为 `tensorflow`、`pytorch` 和 `xgboost`，分别表示使用 TensorFlow、PyTorch 和 XGBoost 框架进行训练。
* `spec.trainingConfig.tasks` 字段的设置请根据所使用的框架种类分别参阅 [TensorFlowTrainingJob](./t9k-job/tensorflowtrainingjob.md)、[PyTorchTrainingJob](./t9k-job/pytorchtrainingjob.md) 和 [XGBoostTrainingJob](./t9k-job/xgboosttrainingjob.md)。

## 搜索空间

搜索空间是实验过程中超参数的设置范围，AutoTuneExperiment 会在此范围中选择超参数进行训练，最后找出最优的超参数组合。

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
metadata:
  name: experiment-sample
spec:
  ...
  searchSpace: |-
    {
      "batch_size": {"_type": "choice", "_value": [512, 1024, 2048, 4096, 8092]},
      "learning_rate": {"_type": "choice", "_value": [0.0001, 0.001, 0.01, 0.1]}
    }
  ...
```

在上述示例中，实验要选择 `batch_size` 和 `learning_rate` 两个超参数，二者的搜索范围分别是 `[512, 1024, 2048, 4096, 8092]` 和 `[0.0001, 0.001, 0.01, 0.1]`。


### 格式

```json
{
  "parameter1": {"_type": "choice", "_value":[1, 2, 3, 4]},
  "parameter2": {"_type": "randint", "_value":[1, 10]},
  ...
}
```

在上述结构中：

* 每一个条目都记录了一个超参数的名称和搜索方式：
    * 每一个条目的键值（例如 `parameter1` 和 `parameter2`）表示超参数的名称。
    * `_type` 是超参数搜索方法。
    * `_value` 表示搜索方法所使用的参数。
* `choice` 和 `randint` 表示超参数的搜索方法，其具体含义请参阅[搜索方法](#搜索方法)。

### 搜索方法

|     _type     |    _value 格式    |                            详细描述                          |
| ------------- | ---------------- | ----------------------------------------------------------- |
| `choice`      | 数组（数字或字符串） | 每次训练选取数组 `_value` 中一个值作为超参数。例：`{"learning_rate":{"_type": "choice", "_value": [0.01, 0.02, 0.1, 0.2]}}`，进行超参数调优时，每一组训练会从四个值中选取一个。 |
| `randint`     | `[lower, upper]` | 每次训练选取 `lower` 和 `upper` 之间中的一个随机整数（不包括 `upper`）作为超参数。例：`{"learning_rate":{"_type": "randint", "_value": [1, 10]}}`，进行超参数调优时，每一组训练可能取到的值有 `[1, 2, 3, 4, 5, 6, 7, 8, 9]`。 |
| `uniform`     | `[low, high]`    | 每次训练从 `lower` 到 `upper` 区间内均匀采样作为超参数。 |
| `quniform`    | `[low, high, q]` | 取值方法为 `clip(round(uniform(low, high) / q) * q, low, high)`，此公式含义为：从 `low` 到 `high` 区间内均匀采样，然后将取值除以 `q`，并四舍五入为整数，然后将超出 `[low, high]` 区间的值舍弃，加上 `low` 和 `upper` 两个值，构成选值区间。例：`_value` 为 `[0, 10, 2.5]`，表示超参数的选值范围时 `[0, 2.5, 5, 7.5, 10]`；`_value` 为 `[2, 10, 5]`，表示超参数的选值范围时 `[2, 5, 10]`。 |
| `loguniform`  | `[low, high]`    | 取值方法为 `exp(uniform(log(low), log(high)))`，此公式含义为：从 `log(low)` 到 `log(high)` 区间内均匀采样得到样本 `x`，然后计算 `exp(x)` 得到超参数。 |
| `qloguniform` | `[low, high, q]` | 取值方法为 `clip(round(loguniform(low, high) / q) * q, low, high)`（其中 `loguniform(low, high)` 表示 `exp(uniform(log(low), log(high)))`），此公式含义参考 `quniform` 和 `loguniform` 条目。 |
| `normal`      | `[mu, sigma]`    | 超参数的取值满足正态分布 `N(mu, sigma^2)`。 |
| `qnormal`     | `[mu, sigma, q]` | 取值方法为 `round(normal(mu, sigma) / q) * q`，此公式含义参考 `quniform` 条目。 |
| `lognormal`   | `[mu, sigma]`    | 取值方法为 `exp(normal(mu, sigma))`，此公式含义参考 `loguniform` 条目。 |
| `qlognoraml`  | `[mu, sigma, q]` | 取值方法为 `round(exp(normal(mu, sigma)) / q) * q`，此公式含义参考 `quniform` 和 `loguniform` 条目。 |

<aside class="note info">
<div class="title">信息</div>

不同算法可以支持不同种类的搜索方法：并不是在每一种调优算法中都可以使用以上所有搜索方法；也有些调优算法支持使用上述方法之外的搜索方法，例如 [PPO 算法](#ppotuner)。

</aside>

## 超参数调优算法

AutoTuneExperiment 在调参过程中需要使用超参数调优算法对超参数进行选择，对于不同的训练框架和不同的超参数种类，超参数调优算法的效率和效果也是不一样的。

算法根据功能和定义分为两类：

* Tuner 算法：超参数调优算法，用于选取合适的超参数组合。
* Assessor 算法：训练评估算法，用于判断当前训练是否符合预期，如果不符合则终止训练。

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  tuner:
    builtinTunerName: "Anneal"
    classArgs: |-
      {
        "optimize_mode": "minimize", 
        "constant_liar_type": "min"
      }
  assessor:
    builtinAssessorName: "Medianstop"
    classArgs: |-
      {
        "optimize_mode": "maximize",
        "start_step": 0
      }
```

在上述示例中，实验使用 `Anneal` 算法选择超参数，使用 `Medianstop` 算法来判断是否中止试验。

### Tuner 算法

#### HyperOpt 类算法

HyperOpt 是一个用于超参数调优的 Python 库，其中主要包含三种超参数调优算法：`Random`、`Anneal` 和 `TPE`。三种算法之间的区别以及算法的使用方法请参阅 <a target="_blank" rel="noopener noreferrer" href="https://hyperopt.github.io/hyperopt/">HyperOpt 文档</a>。

算法支持使用的搜索方法有：`choice`、`randint`、`loguniform` 和 `qloguniform`。

下面是 AutoTuneExperiment 使用此算法的一个示例（示例中的 `classArgs` 字段包含当前算法所有参数，有些参数在实际使用中可以选择不指定）：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  tuner:
    builtinTunerName: "Anneal"
    classArgs: |-
      {
        "optimize_mode": "minimize", 
        "constant_liar_type": "min"
      }
```

#### Evolution

进化算法（Evolution）是受生物进化启发得到的一种优化算法。进化算法的详细介绍请参阅 <a target="_blank" rel="noopener noreferrer" href="https://en.wikipedia.org/wiki/Evolutionary_algorithm">Evolutionary algorithm WIKI 文档</a>。

下面是 AutoTuneExperiment 使用此算法的一个示例（示例中的 `classArgs` 字段包含当前算法所有参数，有些参数在实际使用中可以选择不指定）：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  tuner:
    builtinTunerName: "Evolution"
    classArgs: |-
      {
        "optimize_mode": "minimize", 
        "population_size": 32
      }
```

#### Batch

Batch 是一种最简单的选参算法，此算法只支持一种超参数的优化（搜索空间中只能有一个条目）且仅支持 `choice` 搜索方法。

下面是 AutoTuneExperiment 使用此算法的一个示例（Batch 算法不需要填写参数）：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  searchSpace: |-
    {
      "learning_rate": {"_type": "choice", "_value": [0.0001, 0.001, 0.01, 0.1]}
    }
  tuner:
    builtinTunerName: "Batch"
    classArgs: |-
      {
        "optimize_mode": "minimize", 
        "population_size": 32
      }
```

上述示例中，搜索空间中只有一个超参数，且 `_type` 为 `choice`；Batch 算法不需要传入参数，所以没有填写 `tuner.classArgs` 字段。

#### GridSearch

一种基本的选参算法，根据搜索空间中的参数和范围，枚举所有可能的超参数组合，一一测试。

算法支持使用的搜索方法有：`choice`、`randint` 和 `quniform`。

下面是 AutoTuneExperiment 使用此算法的一个示例（GridSearch 算法不需要填写参数）：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  tuner:
    builtinTunerName: "GridSearch"
```

#### MetisTuner

Metis 算法的详细介绍请参阅论文 <a target="_blank" rel="noopener noreferrer" href="https://www.microsoft.com/en-us/research/publication/metis-robustly-tuning-tail-latencies-cloud-systems/">*Metis: Robustly Optimizing Tail Latencies of Cloud Systems*</a>。

算法支持使用的搜索方法有：`choice`、`randint`、`uniform` 和 `quniform`。

下面是 AutoTuneExperiment 使用此算法的一个示例（示例中的 `classArgs` 字段包含当前算法所有参数，有些参数在实际使用中可以选择不指定）：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  tuner:
    builtinTunerName: "MetisTuner"
    classArgs: |-
      {
        "optimize_mode": "maximize",
        "no_resampling": true,
        "no_candidates": false,
        "selection_num_starting_points": 600,
        "cold_start_num": 10,
        "exploration_probability": 0.9
      }
```

#### GPTuner

GPT 是一种能够极大减少超参数优化步骤的贝叶斯优化算法。算法的详细介绍请参阅 <a target="_blank" rel="noopener noreferrer" href="https://arxiv.org/abs/2311.03157">GPTuner 文档</a>。

算法支持使用的搜索方法有：`randint`、`uniform`、`quniform`、`loguniform`、`qloguniform` 和数字形式的 `choice`。

下面是 AutoTuneExperiment 使用此算法的一个示例（示例中的 `classArgs` 字段包含当前算法所有参数，有些参数在实际使用中可以选择不指定）：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  tuner:
    builtinTunerName: "GPTuner"
    classArgs: |-
      {
        "optimize_mode": "maximize",
        "utility": "ei",
        "kappa": 5,
        "xi": 0,
        "nu": 2.5,
        "alpha": 1e-6,
        "cold_start_num": 10,
        "selection_num_warm_up": 100000,
        "selection_num_starting_points": 250
      }
```

#### PPOTuner

PPO 算法继承了 <a target="_blank" rel="noopener noreferrer" href="https://github.com/openai/baselines/tree/master/baselines/ppo2">OpenAI 中 ppo2</a> 的主要逻辑，并适应 NAS 场景。

算法支持使用的搜索方法有：`layer_choice` 和 `input_choice`。下面是一个搜索空间设置的示例：

```json
{
  "first_conv": {"_type": "layer_choice", "_value": ["conv5x5", "conv3x3"]},
  "mid_conv": {"_type": "layer_choice", "_value": ["0", "1"]},
  "skip": {
    "_type": "input_choice",
    "_value": {"candidates": ["", ""], "n_chosen": 1}
  }
}
```

下面是 AutoTuneExperiment 使用此算法的一个示例（示例中的 `classArgs` 字段包含当前算法所有参数，有些参数在实际使用中可以选择不指定）：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  tuner:
    builtinTunerName: "PPOTuner"
    classArgs: |-
      {
        "optimize_mode": "maximize",
        "trials_per_update": 20,
        "epochs_per_update": 4,
        "minibatch_size": 4,
        "ent_coef": 0.0,
        "lr": 3e-4,
        "vf_coef": 0.5,
        "max_grad_norm": 0.5,
        "gamma": 0.99,
        "lam": 0.95,
        "cliprange": 0.2
      }
```

#### PBTTuner

PBT 算法得自 <a target="_blank" rel="noopener noreferrer" href="https://arxiv.org/abs/1711.09846">*Population Based Training of Neural Networks*</a>。

下面是 AutoTuneExperiment 使用此算法的一个示例（示例中的 `classArgs` 字段包含当前算法所有参数，有些参数在实际使用中可以选择不指定）：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  tuner:
    builtinTunerName: "PBTTuner"
    classArgs: |-
      {
        "optimize_mode": "maximize",
        "population_size": 10,
        "factor": 0.2,
        "resample_probability": 0.25,
        "fraction": 0.2
      }
```

### Assessor 算法

#### Medianstop

Medianstop 算法的逻辑是：如果在某一步 `S`，当前运行的实验的最佳观测值比所有已经完成的训练的第 S 步前的观测值的中位数差，则停止此次训练。此策略出自论文 <a target="_blank" rel="noopener noreferrer" href="https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/46180.pdf">*Google Vizier: A Service for Black-Box Optimization*</a> 。

下面是 AutoTuneExperiment 使用此算法的一个示例（示例中的 `classArgs` 字段包含当前算法所有参数，有些参数在实际使用中可以选择不指定）：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  assessor:
    builtinAssessorName: "Medianstop"
    classArgs: |-
      {
        "optimize_mode": "maximize",
        "start_step": 0
      }
```

在上述示例中，`start_step` 表示从第几步开始上传观测值（过早上传观测值，可能会错误的停止一些刚开始表现较差的训练）。

#### Curvefitting

Curvefitting 算法使用学习曲线拟合算法来预测未来的学习曲线性能。其逻辑是：如果在某一步，当前训练的预测结果是收敛的并且比历史上的最佳结果差，则停止此次训练。Curvefitting 算法的详细介绍请参阅 <a target="_blank" rel="noopener noreferrer" href="https://en.wikipedia.org/wiki/Curve_fitting">Curve fitting WIKI 文档</a>。

下面是 AutoTuneExperiment 使用此算法的一个示例（示例中的 `classArgs` 字段包含当前算法所有参数，有些参数在实际使用中可以选择不指定）：

```yaml
apiVersion: tensorstack.dev/v1beta1
kind: AutoTuneExperiment
spec:
  assessor:
    builtinAssessorName: "Curvefitting"
    classArgs: |-
      {
        "optimize_mode": "minimize",
        "epoch_num": 20,
        "start_step": 6,
        "threshold": 0.95,
        "gap": 1
      }
```

## 实验状态

AutoTuneExperiment 的生命周期包括以下五个阶段：

* `PENDING`：AutoTune 的相关组件（包括 AutoTune Server 和 AutoTune Web）已经创建成功，等待控制器启动 AutoTuneExperiment。
* `RUNNING`：AutoTuneExperiment 运行中。
* `TIMEOUT`：如果达到最大执行时间（由 `spec.maxExecSeconds` 字段指定）后，实验仍未完成（实际训练次数小于 `maxTrialNum` 字段指定的训练次数），则实验超时，不再创建新的训练。
* `DONE`：AutoTuneExperiment 在最大执行时间内完成了 `maxTrialNum` 字段指定的训练次数（无论成功或失败），则实验完成。
* `ERROR`：AutoTuneExperiment 初始化阶段或运行阶段出现错误。

<figure>
  <img alt="phase" src="../../../assets/api/autotune/phase.drawio.svg" width="350" />
</figure>
