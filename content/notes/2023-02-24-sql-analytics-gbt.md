---
title: "使用 GPT 进行数据分析"
date: 2023-02-24T15:54:00
tags:
- AI
- SQL
---


### 我们一起来看看整个构建过程

1. 提出问题，产生一条初始的候选查询语句；
2. 通过提问一些常见错误，使用 GPT 来检查下自己编写的 SQL，产生一条完善过的候选查询语句；
	- 我们日常经常犯的错误
	- 和 GPT 交互过程，它经常出现的问题
3. 在数据库中执行上一步生成的完善过的候选查询语句；
4. 如果出现一个错误或者没有输出结果，询问 GPT 修复这个查询语句，重复这个步骤知道产生正确的数据正常为止；
5. 最终返回结果。

#### 1. 从 GBT 中获取初始的候选答案

GPT 需要从我们这边获取的内容：
- 可以使用的数据==表结构==和样本数据（数据非必需）
- 我们需要解答的==分析问题==
- 明确的指示：非必需，但是详细明确的提示，有助于生成高质量的答案

下面这个是模版：
```
{数据表的内容（表结构和样本数据）}

作为一个资深的分析师，根据以上提供的表结构和数据，编写一条详细且准确的 MySQL 查询语句来解答如下分析问题：

"{问题}"

并根据你的逻辑对查询进行注解。
```

这里我来看一个完整的 **prompt**

```
订单表 orders 的表结构如下：
  uuid Text comment '订单ID'
    user_id text comment '用户ID'
    store_id text comment '门店ID'
    amt double comment '订单金额'
    created_at timestamp comment '创建时间'
    time_ship_end timestamp comment '配送完成时间'

订单商品表 order_products 的表结构如下：
  uuid Text comment '主键'
    order_id Text commnet '订单ID'
    product_id Text comment '商品ID'
    units int comment '份数'
    real_price double comment '实际单价'
    created_at timestamp comment '创建时间'

商品表 products 的表结构如下：
  uuid Text comment '商品ID'
  name Text comment '商品名'
  price double comment '建议零售价'
  created_at timestamp comment '创建时间'
  

作为一个资深的分析师，根据上面给出的表结构，写出一个详细和正确的 MySQL 查询来回答下面分析问题：

"给出商品销量占比"

根据你的逻辑注释查询。
```

下面是 GPT 给出来的答案：

```
\```sql
SELECT products.name, SUM(order_products.units) as total_units, 
       SUM(order_products.units * order_products.real_price) as total_sales,
       SUM(order_products.units * order_products.real_price) / 
       (SELECT SUM(units * real_price) FROM order_products) as sales_percentage
FROM order_products
JOIN products ON order_products.product_id = products.uuid
GROUP BY products.name
ORDER BY total_sales DESC
\```

该查询首先使用 `JOIN` 连接 `order_products` 表和 `products` 表，通过 `product_id` 和 `uuid` 字段建立关联，得到每个订单商品对应的商品名称。然后使用 `GROUP BY` 对商品名称进行分组，统计每个商品的销售数量和销售总额。接着使用一个子查询计算总销售额，并将每个商品的销售总额除以总销售额得到销售占比，别名为 `sales_percentage`。最后使用 `ORDER BY` 将结果按照销售总额降序排列，得到商品销售量占比。

```

#### 2. 让 GPT 再次检查下生成的语句

在使用 GPT 过程中，发现它和人一样不断犯相同的错误（和任何分析师一样），所以你需要就像提醒一个新手一样，给它一些明确的 prompt 来审查自己生成的每一个查询，并修改 bug：

```
{查询语句}

再次检查下上述 MySQL 查询是否存在常见错误，其中报错：
- 确定下 join 字段是否正确
- 值使用前，请转化成合适的类型
- 处理大小写敏感问题

如果存在任何问题，请在这里重写查询语句；如果没有问题，就直接输出原始查询。
```