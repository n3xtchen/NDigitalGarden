---
title: "2023-03-28"
date: 2023-03-28T18:19:00+08:00
tags:
- AI
---

**fine-tuning** ，字面意义是**微调**，在不改变 **GPT** 预训练模型的基础上，增加新的用例，重新训练让这个模型能够更加贴合实际使用场景。一般新增的用例数量都不会多，所以确实可以称为**微调**。

### fine-tuning 的目的

> 官方提供目的：
> - 比 **prompt** 设计更高质量的结果
> - 可以训练的示例比 prompt 能容纳的多 
> - 由于 prompt 更短，因此可以节省令牌，较低的延迟请求

通常情况，使用 **GPT**，系统会认为是一种对话，**GPT** 一般都会通过更多的解释；在实际算法任务中，一般只需要知道结果即可；举个例子：

```text
提问：
    情感分析：我今天把手机给摔了。

ChatGPT 的回答：
    根据您提供的信息，我认为您可能感到有些烦躁或沮丧。毕竟，手机是现代人生活中的重要工具之一，且可能是您的重要财产之一。如果您感到情绪上不安，可以尝试冷静下来并采取一些放松的措施，例如深呼吸、冥想或散步。如果您感到身体上受伤，请寻求医疗支持以确保自己的健康和安全。

# token 量：305
```

这是个情感分析的例子，而我想要的是：

```text
提问：
    情感分析：我今天把手机给摔了。

ChatGPT 的回答：
	负面

# token 量：39
```

这样子，输出更有指向性，而且可以节省更多的 [[notes/2023-03-10-gpt3-token|Token]]（将近 1/10）。

在**答案更明确**和**更低成本**（没有过多的解释）面前，**fine-tuning** 就具备了独特的魅力。

### fine-tuning 的最佳实践

**fine-tuning** 可以解决各种各样的问题，最佳使用方式取决于你提供的具体用例。

下面是官方给出最常见的使用场景：

#### 分类问题（Classification）
在分类问题中，每一个 prompt 的输入都应该被分类成一个的预定义分类。对于这类问题，我们建议：

1. 在每个 **prompt** 的结尾使用同样的分隔符，来区分 **prompt** 和 **completion**；
2. 建议训练的时候，使用的每个**分类**对应一个单 **Token**； 并推理的时候，将参数 `max-tokens` 指定为 1，这样==回答精准，而且节省 **Token** 量==；
3. 每个分类的训练样本最好==不少于 100 个==；
4. 推理的时候，你可以指定 `logprobs=n` （n是个整数值）获取top n 的类以及它的估计概率；
5. 用于微调的数据集在结构上和任务类型最好比较相似；

下面是从官方提供的 case 中总结出来的

##### 不实陈述（untrue statement）判断: 

**案例**：判断你的广告语（`Ad`）是否与公司名（`Company`）和产品（`Product`）相匹配

**训练数据结构**：

```json
{"prompt":"Company: BHFF insurance\nProduct: allround insurance\nAd:One stop shop for all your insurance needs!\nSupported:", "completion":" yes"}
{"prompt":"Company: Loft conversion specialists\nProduct: -\nAd:Straight teeth in weeks!\nSupported:", "completion":" no"}
```

- 通用句式结构：`company: {公司描述}\nProduct: {产品}\nAd: {广告描述}\n"`（满足建议的第四点）;
- `Supported`：每个 prompt 的统一结尾（满足建议的第一点）
- 分类：`yes`（匹配） 和 `no`（不匹配），每个分类都是单 **Token**（满足建议的第3点）；

##### 情感分析（Sentiment analysis）：判断内容的正负面情绪

**案例**：判断每条 tweet 的正负面情绪

**训练数据的结构**：

```json
{"prompt":"Overjoyed with the new iPhone! ->", "completion":" positive"}
{"prompt":"@lakers disappoint for a third straight night https://t.co/38EFe43 ->", "completion":" negative"}
```
`
- `->`: 每个 prompt 的统一结尾（满足建议的第一点）
- 分类：`positive`（正面） 和 `negative`（负面），每个分类都是单 **Token**（满足建议的第3点）；

##### 邮件分类（Categorization for Email triage）

**句式结构**：（满足建议的第四点）
```json
{
  "prompt":"
    Subject: <邮件标题>\n
    From:<发件人>\n
    Date:<日期>\n
    Content:<邮件内容>
    \n\n###\n\n
  ", 
  "completion":" <numerical_category>"}
```

- `\n\n###\n\n`: 每个 prompt 的统一结尾（满足建议的第一点）

**样本举例**：

```json
{
  "prompt":"
    Subject: Update my address\n
    From:Joe Doe\nTo:support@ourcompany.com\n
    Date:2021-06-03\n
    Content:Hi,\nI would like to update my billing address to match my delivery address.\n\nPlease let me know once done.\n\nThanks,\nJoe
    \n\n###\n\n
  ", 
  "completion":" 4"}
```

- 这个信息是一家公司客服系统收到邮件
- 分类是 4（从内容大意看，真正的分类名是 `用户信息更新`），因为分类中可能包含多个 **Token**，将它映射成数字。

#### 条件生成（Conditional Generation）

**条件生成问题**（我觉得场景更为准确）指的是根据指定的输入（即指定了场景/条件）生成指定的内容，包括释义、总结、**实体提取**、编写指定功能的产品描述、**聊天机器人**等等。对于这类问题，我们建议：

- 建议使用**停止词**，在训练样本的 **completion** 中增加一个**结束词**（例如， `end`）；并推理的时候使用**停止词**参数，值和你训练样本的**结束词**一致（例如， `stop=[ "end" ]`），这样==让问题回答更加精准，避免话题扩散==；
- 训练样本==最好要 500 个以上==；
- 用于微调的数据集在结构上和任务类型最好比较相似；
- 确保样本的质量，并且遵循同样预期的格式；
- 训练的时候，使用更低的学习率和很少 epochs 数（可能只需要 1-2）对这里场景将会有更好的效果；