---
title: "OpenAI API：微调（fine-tuning）的使用
date: 2023-04-01T23:30:00+08:00
tags:
- AI
---

通过 [[notes/2023-03-28-openai-api-finetuning|OpenAI API：微调（fine-tuning）及其使用场景]] 对 Fine-Tuning 的介绍，我们可以了解常见场景以及相关场景的使用 Trick。总的来说，微调的好处就是 ==更省钱，答案更明确==。接下来，我就来介绍下具体的工程实现细节，主要分成四个步骤来写：

1. 数据预测处理
2. 模型训练
3. 模型评估

**开始之前，需要如下准备**：

- 安装 **OpenAI** 的命令行工具：`pip install --upgrade openai`
- 设置你的**API KEY**：`export OPENAI_API_KEY="<OPENAI_API_KEY>"`
	- 可以从这个地方获取： https://platform.openai.com/account/api-keys
	- 注册 OpenAI 账号的时候，会免费赠送 18 刀的额度。==注意，它是有使用期限的！==

### 1. 数据预处理

#### 数据格式

**训练集的格式**：

```json
{
  "prompt": "<训练的提示><分隔符>",
  "completion": " <要补齐的分类或者描述><停止词>"
}
```

**模型调用的方式**：

```json
/* POST https://api.openai.com/v1/completions -d */
{
	"model": "<你训练好的模型名称>",
	"prompt": "<你测试的提示><分隔符: 和训练集的格式中的分隔符一致>",
	"stop": ["<停止词: 和训练集的格式中的停止词一致>"]
	... /* 其他可选参数 */
}
```


- 每一个**提示**都应该以相同的**分隔符**结尾，来告诉模型==提示结束，开始补齐==，注意这个**分隔符**不能出现在**提示**和**补齐**中；
- 每一个**补齐**都应该以空白字符开头(` `)，因为GPT的**标注器**标注大部分**分词**都以空白打头；
- 每一个**补齐**都应该以相同的**停止词**结尾，来告诉模型==补齐结束==；
- **推理**的时候，**提示**的格式应该要==和训练集的格式构建方式相同==：
	- **提示**的文本结构和训练集的**提示**一致；
	- **提示**要以相同的**分隔符**结束；
	- 将 `stop` 参数和训练集的**补齐**的**停止词** 设置成一样的；

#### 训练集和测试集拆分，并提供优化建议，以及下一步的命令

```bash
openai tools fine_tunes.prepare_data -f data.json -q
```

它会自动将分析你的数据集，拆封成训练集（`_prepared_train` 后缀）和验证集（`_prepared_valid` 后缀）

**输出**:

```text
Analyzing...

- Your file contains 7739 prompt-completion pairs
- Based on your data it seems like you're trying to fine-tune a model for classification
- For classification, we recommend you try one of the faster and cheaper models, such as `ada`
- For classification, you can estimate the expected model performance by keeping a held out dataset, which is not used for training
- More than a third of your `prompt` column/key is uppercase. Uppercase prompts tends to perform worse than a mixture of case encountered in normal language. We recommend to lower case the data if that makes sense in your domain. See [https://platform.openai.com/docs/guides/fine-tuning/preparing-your-dataset](https://platform.openai.com/docs/guides/fine-tuning/preparing-your-dataset) for more details
- All prompts end with suffix `\n\n###\n\n`
- The completion should start with a whitespace character (` `). This tends to produce better results due to the tokenization we use. See [https://platform.openai.com/docs/guides/fine-tuning/preparing-your-dataset](https://platform.openai.com/docs/guides/fine-tuning/preparing-your-dataset) for more details

Based on the analysis we will perform the following actions:
- [Recommended] Lowercase all your data in column/key `prompt` [Y/n]: Y
- [Recommended] Add a whitespace character to the beginning of the completion [Y/n]: Y
- [Recommended] Would you like to split into training and validation set? [Y/n]: Y


Your data will be written to a new JSONL file. Proceed [Y/n]: Y

Wrote modified files to `data_prepared_train.jsonl` and `data_prepared_valid.jsonl`
Feel free to take a look!

Now use that file when fine-tuning:
> openai api fine_tunes.create -t "data_prepared_train.jsonl" -v "data_prepared_valid.jsonl" --compute_classification_metrics --classification_n_classes 445

After you’ve fine-tuned a model, remember that your prompt has to end with the indicator string `\n\n###\n\n` for the model to start generating completions, rather than continuing with the prompt.
Once your model starts training, it'll approximately take 3.13 hours to train a `curie` model, and less for `ada` and `babbage`. Queue will approximately take half an hour per job ahead of you.
```

### 2. 微调模型的训练

**参数说明**：
- `-t <训练集路径>`：必填
- `-v <验证集路径>`：必填
-  `-m/--model <模型名称>`：
	- 设置基模型（base_model）
	- 默认值是 `curie`，可选的有 `ada`、`babbage` 和 `davinci`
- `--suffix <版本前缀>`: 
	- 如果设置，训练完的模型名称为：`{base_model}:ft-{组织名称}:{版本前缀}-{timestamp}`
	- 如果没有配置，模型名称为：`{base_model}:ft-{组织名称}-{timestamp}

**模型的超参数**：
- `--classification_n_classes <分类数量>`: 必填
- `--n_epochs <训练轮数>`: 默认值是 4
- `--batch <每批的数量>`: 
	- 默认值是训练样本数量的 0.2%，上限为 256；
	- ==数据集越大，想要得到更好的效果就需要设置更大的每批数量；==
- `--learning_rate_multiplier <学习率倍数>`:
	- $微调模型的学习率 = 预训练模型的学习率 \times 学习率倍数$
	- 默认值是 0.05、0.1 或者 0.2，这个值取决于每批数量；
	- ==建议范围在 0.02 和 0.2 之间会产生最佳的效果，每批数量越大，该值应该设置的更大来获取更好的效果==；
- `--prompt_loss_weight <loss 权重>`:
- `--classification_betas <> [<>]`:
	## - 使用场景：二分类

**模型的评估参数**：
- `--compute_classification_metrics`: 
	- 如果设置，会在每个 `epoch` 训练结束后，计算验证集的分类性能指标，如**准确率**和**F1-分数
- `--calssifiction_positive_class <正样本数量>`:
	- 使用场景：二分类
	- 想要计算分类性能指标的时候，就需要设置

```bash
openai api fine_tunes.create \
	-t "data_prepared_train.jsonl" \
	-v "data_prepared_valid.jsonl" \
	--compute_classification_metrics \
	--classification_n_classes <分类数> 
```

```text
Upload progress: 100%|███████████████████████| 509k/509k [00:00<00:00, 403Mit/s]
Uploaded file from data_prepared_train.jsonl: file-ZORlJyH7tHZX7T4nSTcZsSkm
Upload progress: 100%|████████████████████| 75.4k/75.4k [00:00<00:00, 58.3Mit/s]
Uploaded file from data_prepared_valid.jsonl: file-bGVC1Rhuu9i059NOtctNB4R9
Created fine-tune: ft-HnegTA8pgKKWxVKopDss0Z7G
Streaming events until fine-tuning is complete...

(Ctrl-C will interrupt the stream, but not cancel the fine-tune)
[2023-03-31 23:06:07] Created fine-tune: ft-HnegTA8pgKKWxVKopDss0Z7G
[2023-04-03 19:32:44] Fine-tune costs $2.41
[2023-04-03 19:32:44] Fine-tune enqueued. Queue number: 0
[2023-04-03 19:32:45] Fine-tune started
[2023-04-03 19:40:52] Completed epoch 1/4
[2023-04-03 19:55:40] Completed epoch 3/4
[2023-04-03 20:03:47] Uploaded model: curie:ft-personal-2023-04-03-12-03-46
[2023-04-03 20:03:48] Uploaded result file: file-obQbEb3RxT4Xz3T9rg1V5IgY
[2023-04-03 20:03:48] Fine-tune succeeded

Job complete! Status: succeeded 🎉
Try out your fine-tuned model:

openai api completions.create -m curie:ft-personal-2023-04-03-12-03-46 -p <YOUR_PROMPT>
```

由于训练时间通常都很长（我的这个命令执行了1个半小时），所以没有必要一直开着终端；你可以关闭终端，想要查看进度的时候，使用如下命令：

```bash
openai api fine_tunes.follow -i ft-HnegTA8pgKKWxVKopDss0Z7G
```

```text
[2023-04-03 19:27:39] Created fine-tune: ft-pTUpDsM7AlHkALMgSpeQxdD2
[2023-04-03 19:32:44] Fine-tune costs $2.41
[2023-04-03 19:32:44] Fine-tune enqueued. Queue number: 0
[2023-04-03 19:32:45] Fine-tune started
[2023-04-03 19:40:52] Completed epoch 1/4
[2023-04-03 19:55:40] Completed epoch 3/4
[2023-04-03 20:03:47] Uploaded model: curie:ft-personal-2023-04-03-12-03-46
[2023-04-03 20:03:48] Uploaded result file: file-obQbEb3RxT4Xz3T9rg1V5IgY
[2023-04-03 20:03:48] Fine-tune succeeded

Job complete! Status: succeeded 🎉
Try out your fine-tuned model:

openai api completions.create -m curie:ft-personal-2023-04-03-12-03-46 -p <YOUR_PROMPT>
```

### 3.  微信模型的评估

```bash
openai api fine_tunes.results -i ft-pTUpDsM7AlHkALMgSpeQxdD2 > result.csv
```

#### 数据可视化

```python
import plotly.graph_objs as go
import plotly.io as pio
import pandas as pd

results = pd.read_csv('result.csv')
results[results['classification/accuracy'].notnull()].tail(1)
roc = results[results['classification/accuracy'].notnull()]['classification/accuracy'] #.plot()
# 创建柱形图
fig_roc = go.Figure(data=[go.Line(x=roc.index, y=roc)])

# 设置布局和样式
fig_roc.update_layout(title='Bar chart', xaxis_title='X-axis label', yaxis_title='Y-axis label')

# 显示图形
pio.show(fig_roc)
```