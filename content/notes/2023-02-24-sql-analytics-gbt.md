---
title: "使用 GPT 进行数据分析"
date: 2023-02-24T15:54:00
tags:
- AI
- SQL
---

GPT 需要从我们这边获取的内容：
- 可以使用的数据==表结构==和样本数据（数据非必需）
- 我们需要解答的==分析问题==
- 明确的指示：非必需，但是详细明确的提示，有助于生成高质量的答案

请求方式见：[[notes/2023-03-01-openai-api |openAI api - Text Completions]]

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

```sql
SELECT products.name, SUM(order_products.units) as total_units, 
       SUM(order_products.units * order_products.real_price) as total_sales,
       SUM(order_products.units * order_products.real_price) / 
       (SELECT SUM(units * real_price) FROM order_products) as sales_percentage
FROM order_products
JOIN products ON order_products.product_id = products.uuid
GROUP BY products.name
ORDER BY total_sales DESC
```

该查询首先使用 `JOIN` 连接 `order_products` 表和 `products` 表，通过 `product_id` 和 `uuid` 字段建立关联，得到每个订单商品对应的商品名称。然后使用 `GROUP BY` 对商品名称进行分组，统计每个商品的销售数量和销售总额。接着使用一个子查询计算总销售额，并将每个商品的销售总额除以总销售额得到销售占比，别名为 `sales_percentage`。最后使用 `ORDER BY` 将结果按照销售总额降序排列，得到商品销售量占比。

#### 1. 过滤

```
现在你是一个资深的分析师，有着丰富分析经验和SQL能力，请你帮个忙

Customer 的表结构如下：
  id INTEGER COMMENT "主键"
  name VARCHAR(50)
  phone VARCHAR(15)
  address VARCHAR(100)
Customer 的样本数据如下：
 id name       phone address
  1   张三 13888888888 上海市浦东新区
  2   李四 13999999999  北京市海淀区
  3   王五 13666666666  广州市天河区
  
根据以上提供的数据结构和数据，编写一个具体和准确的 mysql 的查询语句来回答如下分析问题：

"获取客户 A 的电话号码和住址"

并使用你的逻辑来注释它。
```

下面是 GPT 给出来的答案：

```sql
SELECT phone, address 
FROM Customer 
WHERE name = 'A'
```
该查询语句会从 Customer 表中筛选出 name 等于 'A' 的记录，然后只返回这些记录中的 phone 和 address 字段，即客户 A 的电话号码和住址。

#### 2. 聚合

```
现在你是一个资深的分析师，有着丰富分析经验和SQL能力，请你帮个忙

Order 的表结构如下：
  id INTEGER
  order_no VARCHAR(50)
  customer_id INTEGER
  payment_id INTEGER
  shipping_address_id INTEGER
  order_status_id INTEGER
  invoice_id INTEGER
  created_at DATETIME
  updated_at DATETIME    
Order 的样本数据如下：
 id    order_no  customer_id  payment_id  shipping_address_id  order_status_id  invoice_id          created_at          updated_at
  1 20220601001            1           1                    1                2         2.0 2022-06-01 09:00:00 2022-06-02 10:00:00
  2 20220601002            2           3                    2                1         NaN 2022-06-01 14:00:00 2022-06-02 16:00:00
  3 20220602001            3           2                    3                3         1.0 2022-06-02 08:00:00                 NaT

根据以上提供的数据结构和数据，编写一个具体和准确的 mysql 的查询语句来回答如下分析问题：

"统计每个月的订单数"

并使用你的逻辑来注释它。
```

下面是 GPT 给出来的答案：

```sql
SELECT 
    DATE_FORMAT(created_at, '%Y-%m') AS month, 
    COUNT(*) AS order_count
FROM 
    Order
GROUP BY 
    month;

-- 将创建时间按年-月格式化，统计每个月的订单数，并按月份分组。
```

#### 3.  连表

```
现在你是一个资深的分析师，有着丰富分析经验和SQL能力，请你帮个忙

Customer 的表结构如下：
  id INTEGER COMMENT "主键"
  name VARCHAR(50)
  phone VARCHAR(15)
  address VARCHAR(100)    
Customer 的样本数据如下：
 id name       phone address
  1   张三 13888888888 上海市浦东新区
  2   李四 13999999999  北京市海淀区
  3   王五 13666666666  广州市天河区

Order 的表结构如下：
  id INTEGER
  order_no VARCHAR(50)
  customer_id INTEGER
  payment_id INTEGER
  shipping_address_id INTEGER
  order_status_id INTEGER
  invoice_id INTEGER
  created_at DATETIME
  updated_at DATETIME    
Order 的样本数据如下：
 id    order_no  customer_id  payment_id  shipping_address_id  order_status_id  invoice_id          created_at          updated_at
  1 20220601001            1           1                    1                2         2.0 2022-06-01 09:00:00 2022-06-02 10:00:00
  2 20220601002            2           3                    2                1         NaN 2022-06-01 14:00:00 2022-06-02 16:00:00
  3 20220602001            3           2                    3                3         1.0 2022-06-02 08:00:00                 NaT

根据以上提供的数据结构和数据，编写一个具体和准确的 mysql 的查询语句来回答如下分析问题：

"获取本月的所有订单，需要显示客户名和单号"

并使用你的逻辑来注释它。
```

下面是 GPT 给出来的答案：

```sql
SELECT c.name, o.order_no
FROM Customer c
INNER JOIN Order o
ON c.id = o.customer_id
WHERE MONTH(o.created_at) = MONTH(CURRENT_DATE()) 
AND YEAR(o.created_at) = YEAR(CURRENT_DATE()); 

-- 使用内连接将 Customer 表和 Order 表连接起来
-- 在 WHERE 子句中筛选出本月的订单
-- 最后只选出客户名和单号这两个需要显示的字段
```

#### 4. 连表聚合

```
现在你是一个资深的分析师，有着丰富分析经验和SQL能力，请你帮个忙

Customer 的表结构如下：
  id INTEGER COMMENT "主键"
  name VARCHAR(50)
  phone VARCHAR(15)
  address VARCHAR(100)    
Customer 的样本数据如下：
 id name       phone address
  1   张三 13888888888 上海市浦东新区
  2   李四 13999999999  北京市海淀区
  3   王五 13666666666  广州市天河区

Order 的表结构如下：
  id INTEGER
  order_no VARCHAR(50)
  customer_id INTEGER
  payment_id INTEGER
  shipping_address_id INTEGER
  order_status_id INTEGER
  invoice_id INTEGER
  created_at DATETIME
  updated_at DATETIME    
Order 的样本数据如下：
 id    order_no  customer_id  payment_id  shipping_address_id  order_status_id  invoice_id          created_at          updated_at
  1 20220601001            1           1                    1                2         2.0 2022-06-01 09:00:00 2022-06-02 10:00:00
  2 20220601002            2           3                    2                1         NaN 2022-06-01 14:00:00 2022-06-02 16:00:00
  3 20220602001            3           2                    3                3         1.0 2022-06-02 08:00:00                 NaT

根据以上提供的数据结构和数据，编写一个具体和准确的 mysql 的查询语句来回答如下分析问题：

"统计每个月的手机号数量"

并使用你的逻辑来注释它。
```

下面是 GPT 给出来的答案：

```sql
SELECT 
    DATE_FORMAT(created_at, '%Y-%m') AS `month`, -- 提取订单创建时间的月份
    COUNT(DISTINCT phone) AS phone_count -- 统计每月不同手机号的数量
FROM 
    Order o 
    JOIN Customer c ON o.customer_id = c.id -- 关联订单和顾客表
WHERE 
    c.phone IS NOT NULL -- 排除手机号为空的顾客
GROUP BY 
    `month` -- 按月份分组
ORDER BY 
    `month` ASC; -- 按月份升序排列
```

逻辑解析：
1. 通过JOIN操作关联订单表和顾客表，以获取订单所属顾客的手机号码信息。
2. 使用DATE_FORMAT()函数提取订单创建时间的月份信息。
3. 使用COUNT()函数和DISTINCT关键字统计每个月份下不同手机号的数量。
4. 使用WHERE子句过滤出手机号不为空的顾客。
5. 使用GROUP BY子句按月份对数据进行分组。
6. 使用ORDER BY子句按月份升序排列结果。

