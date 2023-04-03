---
title: "OpenAI API：微调（fine-tuning）的使用
date: 2023-04-01T23:30:00+08:00
tags:
- AI
---


### 1. 准备数据，如下是数据结构

```json
{"prompt": "<训练的提示><提示结束词，作为和补齐的区分>", "completion": "<要补齐的分类或者描述><补齐结束词，避免补齐扩散>"}
```


### 2. 训练集和测试集拆分，并提供优化建议，以及下一步的命令

```
openai -k <你的apikey> tools fine_tunes.prepare_data -f data.json -q
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

### 3. 开始进行微调模式

```bash
openai api fine_tunes.create \
	-t "data_prepared_train.jsonl" 
	-v "data_prepared_valid.jsonl" 
	--compute_classification_metrics --classification_n_classes {分类数} 
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

Stream interrupted (client disconnected).
To resume the stream, run:

  openai api fine_tunes.follow -i ft-HnegTA8pgKKWxVKopDss0Z7G
```

#### 如果输出中断，可以使用下面命令继续：

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
