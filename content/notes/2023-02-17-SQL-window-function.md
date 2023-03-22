---
title: "SQL 窗口函数"
date: 2023-02-17T19:27:00
tags:
- SQL
---

```
<窗口函数>([col2]) OVER (
  [PARITION BY col1]
  [ORDER BY col2]
  [RANGE BETWEEN [START] AND [END]] -- 划窗
)
```

### 排名函数

#### 全局排序

`<排序函数>() OVER (ORDER BY col)`

排序函数：
- ROW_NUMBER:  排序，1， 1，2/1，2，3
- RANK：排序，1，1，2 / 1，1，3
- DENSE_RANK：排序  1，1，2 / 1，1，2
- PERCENT_RANK：百分比排序
```sql
CREATE TABLE order_list (val int);
INSERT INTO order_list VALUES (1),(2),(2),(3),(4),(6),(7);
SELECT
  val,
  ROW_NUMBER() OVER (ORDER BY val),
  RANK() OVER (ORDER BY val),
  DENSE_RANK() OVER (ORDER BY val),
  ROUND(PERCENT_RANK()  OVER (ORDER BY val), 2)
FROM order_list
```

#### 分组排序
这个是个很常见的场景，比如：
- 想要统计每个部门的业绩排名，激励员工上进
- 找到每个班级的成绩前 5 名进行表彰

```sql 
CREATE TABLE group_list (grp varchar(2), val int);
INSERT INTO group_list VALUES ('a', 1),('a',2),('a',3),('b',1),('b',2),('b',3),('b',4);
SELECT
  grp, val,
  ROW_NUMBER() OVER (PARTITION BY grp ORDER BY val),
  RANK() OVER (PARTITION BY grp ORDER BY val),
  DENSE_RANK() OVER (PARTITION BY grp ORDER BY val),
  ROUND(PERCENT_RANK() OVER (PARTITION BY grp ORDER BY val),2)
FROM group_list;
```
