---
title: "openAI API如何编写 message?"
date: 2023-03-21T09:58:00+08:
tags:
- AI
---

```python
# Note: you need to be using OpenAI Python v0.27.0 for the code below to work
import openai

openai.ChatCompletion.create(
  model="gpt-3.5-turbo",
  messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Who won the world series in 2020?"},
        {"role": "assistant", "content": "The Los Angeles Dodgers won the World Series in 2020."},
        {"role": "user", "content": "Where was it played?"}
    ]
)
```

上面的这个例子，进行第二轮的问答，通过传如上一轮的问题，让助手能够==感知上下文==；

通常情况，一个对话遵循的模式是一个 `system` (系统) 信息开头，接着是 `user` （用户）和 `assistant` （助手）交替出现：
- `system`：系统信息，用来指示助手应该如何表示或者具有什么性格，例如让他扮演某个职业专家，以某种情绪的口吻来回答；
- `user`:  用户输入的部分，用来指导 `assistant`；
- `assistant`: **GPT** 基于用户输入，做出的回答或者补齐，==用来传递上一轮的回答的上下文==；

> gpt-3.5-turbo-0301 并不会永远强关注 `system` 信息。后续的模型将会改进。

### 对比 Text Completions

`messages` 的虽然比 `prompts` 显得==冗长==了很多，但是书写结构化好了很多。使用  `prompts` 估计模型==无法区分内容的产生于谁，注意力机制的关注点会有所发散==，助手应该更加关注用户输入的内容，这点上 `messages` 就能够表现的更好了，至少可以对录入的内容进行加权（==这个是我猜测的==）。

下面是官方将 `prompt` 转化成 `messages` 的例子:

**prompt**:

```text
Translate the following English text to French: "{text}"
```

**messages**:

```text
[
  {"role": "system", "content": "You are a helpful assistant that translates English to French."},
  {"role": "user", "content": 'Translate the following English text to French: "{text}"'}
]
```

#### 对比下 API 参数的差异

| 参数              | Text Completions | Chat Completions |
| ----------------- | ---------------- | ---------------- |
| model             | Y                | Y                |
| prompt            | Y                | N                |
| messages          | N                | Y                |
| max_tokens        | Y                | Y                |
| temperature       | Y                | Y                |
| top_n             | Y                | Y                |
| frequency_penalty | Y                | Y                |
| presence_penalty  | Y                | Y                |
| stop              | Y                | Y                |
| logit_bias        | Y                | Y                |
| best_of           | Y                | N                |
| suffix            | Y                | N                |
| stream            | Y                | Y                |
| echo              | Y                | N                |
| n                 | Y                | Y                |
| logprobs          | Y                | N                |

> 具体参数说明见：[[notes/2023-03-01-openai-api |openAI api - Text Completions]]

