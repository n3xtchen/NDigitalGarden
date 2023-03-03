---
title: "OpenAI API 使用说明"
created: 2023-03-01 15:08
tags:
- AI
---

> [API Reference - OpenAI API](https://platform.openai.com/docs/api-reference/completions)

### 必需参数
- model
- prompt

## 模型调优参数

### max_tokens

可以简单理解为生成文本的单词数（中文的话，得看分词，英文相对比较一致）

用于控制模型生成的文本长度。max_tokens 参数指定模型生成的最大令牌数（token数），令牌可以理解为文本的基本单位，例如单词或标点符号。较大的 max_tokens 值会导致生成更长的文本，但也会增加生成文本的时间和计算成本。

默认：16
最大值：4096

### temperature 和 top_n

==定义==
- temperature：
	- 原理：temperature 参数控制了生成文本的“温度”，温度越高，则生成文本的随机性和创造力也越高，但也可能导致生成的文本不那么准确或连贯。
	- 更加强调随机性和多样性
	- temperature 参数通过在 softmax 操作中对概率分布进行加权，从而使生成的文本更加随机和多样化
	- default：1， 0.1-1.0
- top_n: 
	- 原理：在 softmax 操作中选择概率值最高的前 top_n 个 token，从而限制了 token 选择的范围，使生成的文本更加多样化。
	- 更加强调准确性和流畅性
	- default: 1，1-5
	- 不推荐和 temperature 混用，取其一

==原理==：

temperature 和 top_n 参数的作用机制不同。temperature 参数通过在 softmax 操作中对概率分布进行加权，从而使生成的文本更加随机和多样化；而 top_n 参数则是在 softmax 操作中选择概率值最高的前 top_n 个 token，从而限制了 token 选择的范围，使生成的文本更加多样化。因此，temperature 参数更加强调随机性和多样性，而 top_n 参数更加强调准确性和流畅性。

==如何调优==：

通常情况下，可以先尝试将 temperature 和 top_n 参数都设置为较小的值，以获得最基本的生成结果。如果生成的文本不够随机或多样化，可以逐步增大 temperature 参数的值或减小 top_n 参数的值；如果生成的文本不够准确或流畅，可以逐步减小 temperature 参数的值或增大 top_n 参数的值。

### frequency_penalty 和 presence_penalty

frequency_penalty 和 presence_penalty 是 OpenAI GPT 系列模型中的两个参数，可以用于控制生成文本中词汇的重复程度和多样性。

==定义==：
-   frequency_penalty：频率惩罚参数，用于惩罚在生成文本中出现过多次的词汇。设置较大的 frequency_penalty 值可以减少文本中重复的词汇，但也可能导致生成的文本不够流畅或不符合语言习惯。通常建议将 frequency_penalty 参数设置在 0.0 到 1.0 之间。
    
-   presence_penalty：存在惩罚参数，用于惩罚在生成文本中未出现过的词汇。设置较大的 presence_penalty 值可以增加文本中的词汇多样性，但也可能导致生成的文本不够准确或不符合语言习惯。通常建议将 presence_penalty 参数设置在 0.0 到 1.0 之间。

==如何调优==

这两个参数的具体设置需要根据具体应用场景和需求进行调整。通常情况下，可以先尝试将 frequency_penalty 和 presence_penalty 参数都设置为 0.0，以获得最基本的生成结果。如果生成的文本中存在过多的重复词汇，可以逐步增大 frequency_penalty 参数的值；如果生成的文本中存在较少的词汇多样性，可以逐步增大 presence_penalty 参数的值。同时还需要结合其他参数（如 temperature、top_p、n 等）进行调整和优化，以获得更高质量的生成结果。


### best_of

best_of 参数表示从这些生成序列中选择最优的 k 个结果返回，可以==用于增强生成文本的准确性和流畅度==。

返回 K个结果，并从 K个结果里选择 一个最有的序列返回

default：1


需要注意的是，较大的 best_of 参数值可能会导致 API 响应时间延长和 API 调用次数限制增加。因此，应该根据具体需求和应用场景进行合理的参数选择和调整，以获得最优的生成结果和最高的 API 使用效率。

#### logit_bias

logit_bias 参数是一个包含不同标记偏好的字典，可以将该参数用于修改特定标记的生成偏好。

defalut null

接受一个 json

格式：{"token": weight }

例如，将 logit_bias 参数设置为 {'happy': 2.0, 'sad': -2.0}，则 API 生成的文本序列中将更有可能出现 happy 相关的单词，同时更少出现 sad 相关的单词。

需要注意的是，logit_bias 参数==只适用于一些特定的生成任务==，例如情感分析、文本分类等任务，==对于一般的文本生成任务可能没有显著的效果==。在使用 logit_bias 参数时，需要仔细评估生成结果的质量和效果，以获得最优的生成结果。


## 功能参数

- n：返回多少个结果，默认：1
- suffix
- stop
- stream
- echo：要不要在输出中，重新打印输入的 prompt

## 评估参数

### logprobs（评估）


logprobs 参数用于控制模型输出的可靠性和准确性。当 logprobs 参数被设置为 true 时，API 将返回每个 token 的 log 概率值，可以用于计算和评估生成文本的质量。但使用 logprobs 参数可能会导致响应时间延长和 API 调用次数限制增加，因为每个 token 的概率值都需要进行计算和返回。



logprobs 参数则是返回每个 token 的 log 概率值，可以用于计算和评估生成文本的质量，使生成的文本更加准确和可靠。


如果需要评估生成文本的质量，可以将 logprobs 参数设置为 true，以获取每个 token 的 log 概率值。

default：null
免费的接口，最多设成 5.
















