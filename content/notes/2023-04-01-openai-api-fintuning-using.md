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