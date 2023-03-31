---
title: "OpenAI API：微调（fine-tuning）及其使用场景"
date: 2023-03-28T18:19:00+08:00
tags:
- AI
---

**fine-tuning** ，字面意义是**微调**，在不改变 **GPT** 预训练模型的基础上，增加新的用例，重新训练让这个模型能够更加贴合实际使用场景。一般新增的用例数量都不会多，所以确实可以称为**微调**。

### fine-tuning 的目的

> 官方提供目的：
> - 比 **prompt** 设计更高质量的结果
> - 可以训练的示例比 **prompt** 能容纳的多 
> - 由于 **prompt** 更短，因此可以节省 **Token**，较低的延迟请求

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
- `Supported`：每个 **prompt** 的统一结尾（满足建议的第一点）
- 分类：`yes`（匹配） 和 `no`（不匹配），每个分类都是单 **Token**（满足建议的第3点）；

##### 情感分析（Sentiment analysis）：判断内容的正负面情绪

**案例**：判断每条 tweet 的正负面情绪

**训练数据的结构**：

```json
{"prompt":"Overjoyed with the new iPhone! ->", "completion":" positive"}
{"prompt":"@lakers disappoint for a third straight night https://t.co/38EFe43 ->", "completion":" negative"}
```
`
- `->`: 每个 **prompt** 的统一结尾（满足建议的第一点）
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

- `\n\n###\n\n`: 每个 **prompt** 的统一结尾（满足建议的第一点）

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

1. 在每个 **prompt** 的结尾使用同样的分隔符，来区分 **prompt** 和 **completion**；
2. 建议使用**停止词**，在训练样本的 **completion** 中增加一个**结束词**（例如， `end`）；并推理的时候使用**停止词**参数，值和你训练样本的**结束词**一致（例如， `stop=[ "end" ]`），这样==让问题回答更加精准，避免话题扩散==；
3. 训练样本==最好要 500 个以上==；
4. 用于微调的数据集在结构上和任务类型最好比较相似；
5. 确保样本的质量，并且遵循同样预期的格式；
6. 训练的时候，使用更低的学习率和很少 `epochs` 数（可能只需要 1-2）对这里场景将会有更好的效果；

##### 基于 Wikipedia 生成精美广告

首先，你必须确保你提供的样本是你可以提供的最好样本，微调模型将尝试模仿他的风格（以及错误），500个样本将是一个不错的起点；下面是一个训练数据的格式：

```json
{
  "prompt":"
    <Product Name>\n
    <Wikipedia description>
    \n\n###\n\n
  ", 
  "completion":" <engaging ad> END"}
```

这个训练样本的案例：

```json
{
  "prompt":"
    Samsung Galaxy Feel\n
    The Samsung Galaxy Feel is an Android smartphone developed by Samsung Electronics exclusively for the Japanese market. The phone was released in June 2017 and was sold by NTT Docomo. It runs on Android 7.0 (Nougat), has a 4.7 inch display, and a 3000 mAh battery.\nSoftware\nSamsung Galaxy Feel runs on Android 7.0 (Nougat), but can be later updated to Android 8.0 (Oreo).\nHardware\nSamsung Galaxy Feel has a 4.7 inch Super AMOLED HD display, 16 MP back facing and 5 MP front facing cameras. It has a 3000 mAh battery, a 1.6 GHz Octa-Core ARM Cortex-A53 CPU, and an ARM Mali-T830 MP1 700 MHz GPU. It comes with 32GB of internal storage, expandable to 256GB via microSD. Aside from its software and hardware specifications, Samsung also introduced a unique a hole in the phone's shell to accommodate the Japanese perceived penchant for personalizing their mobile phones. The Galaxy Feel's battery was also touted as a major selling point since the market favors handsets with longer battery life. The device is also waterproof and supports 1seg digital broadcasts using an antenna that is sold separately.
    \n\n###\n\n
  ", 
  "completion":"Looking for a smartphone that can do it all? Look no further than Samsung Galaxy Feel! With a slim and sleek design, our latest smartphone features high-quality picture and video capabilities, as well as an award winning battery life. END"}
```

##### 实体抽取（Entity extraction）

这是一个常规的 NLP 任务，从指定文本中提出主体内容；下面是一个训练数据的格式：

```json
{
  "prompt":"<任何的文本>\n\n###\n\n", 
  "completion":" <实体列表, 使用换行符分割> END"
}
```

这个训练样本的案例：

```json
{
  "prompt":"Portugal will be removed from the UK's green travel list from Tuesday, amid rising coronavirus cases and concern over a \"Nepal mutation of the so-called Indian variant\". It will join the amber list, meaning holidaymakers should not visit and returnees must isolate for 10 days...\n\n###\n\n",
  "completion":" Portugal\nUK\nNepal mutation\nIndian variant END"}
```

一些建议：
- 训练样本中，实体列表的顺序：按==字母顺序排序==或按它们==在原始文本中出现的顺序排序==（需要有一定的规律），一定程度上会提升提取性能；
- 输入 **prompt** 的类型应具有高度的多样性（例如新闻文章、维基百科页面、推文、法律文件），以==反映在提取实体时可能遇到的文本类型==。

##### 客服支持类聊天机器人（chatbot）

一个聊天机器人通常包含对话的北京（如订单详情），历史对话的摘要以及最近的消息。对于这种用例，相同的过去对话可以生成数据集中的多个行，每次都带有稍微不同的上下文，以便每次代理生成完成时进行。

```json
{
  "prompt":"
    Summary: <对话摘要（总结历史至今）>\n\n
    Specific information:<这个对话的相关背景>
    \n\n###\n\n
    Customer: <用户提问>\nAgent: <客户回答>\n
    Customer: <用户提问>\nAgent:
  ", 
  "completion":" <机器人回答>\n"
}
{
  "prompt":"
    Summary: <对话摘要（总结历史至今）>\n\n
    Specific information:<这个对话的相关背景>
    \n\n###\n\n
    Customer: <用户提问>\nAgent: <客户回答>\n
    Customer: <用户提问>\nAgent: <客户回答>\n
    Customer: <用户提问>\nAgent:
  ", 
  "completion":" <机器人回答>\n"}
```

**一些建议**：
- 这种用例需要几千个示例，因为它可能涉及不同类型的请求和客户问题。
- 为了确保性能高质量，我们建议审核对话样本以==确保代理消息的质量==。
- 摘要可以使用单独的文本转换 fine-tuned 模型生成

##### 基于技术/功能参数列表生成产品描述

将指定的输入生成自然语言，训练时可以使用如下模版：

```json
{
  "prompt": "<属性列表，属性=值，多个属性使用 ，隔开>->",
  "completion": "<基于提示生成句子>"
}
```

这个训练样本的案例：

```json
{"prompt":"Item=handbag, Color=army_green, price=$99, size=S->", "completion":" This stylish small green handbag will add a unique touch to your look, without costing you a fortune."}
```


如果写成下面的 `prompt`，效果就不太好了：

```json
{"prompt":"Item is a handbag. Colour is army green. Price is midrange. Size is small.->", "completion":" This stylish small green handbag will add a unique touch to your look, without costing you a fortune."}
```

**一些建议**：
- 确保 `completions` 的内容一定要基于 `prompt` 的描述；
- 如果描述基于图片，请先用算法从图片中提取文本描述；
- 因为 `completions` 只有一句话，所以可以使用 `。` 作为停止词（英文使用 `.`）;