---
title: "MySQL 的小技巧"
date: 2023-02-20T15:55:00
tags:
- SQL
---

### 文件导出

```sql
<select 语句> 
INTO OUTFILE '/Users/nextchen/z.csv' FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\n'
```
#### 检查是否支持导出权限

 > 报错信息如下：
 > The MySQL server is running with the --secure-file-priv option so it cannot execute this statement

##### 检查下系统配置
```
SHOW VARIABLES LIKE "secure_file_priv"; 
```

| Variable_name | Value |
| -- | -- |
| secure_file_pri | NULL |

如果 `Values` 不为 `NULL`，*.my.conf* 尾部加入如下文本
```
[mysqld_safe]
[mysqld]
secure_file_priv=""
```

### 杀进程
```
SHOW FULL PROCESSLIST;
-- 找出你执行已经卡住很久的sql
-- id：进程ID，
KILL ID
```