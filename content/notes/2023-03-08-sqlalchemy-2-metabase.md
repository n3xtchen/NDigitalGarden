---
title: "SQLAlchemy 2: 使用反射获取元数据"
date: 2023-03-08T16:29:00
tags:
- python
- database
---

在 **SQLAlchemy** 的新版本中，已经移除了“隐式”和“无连接”的执行方式和“绑定元数据”的概念。连接对象（如 `Engine` 或 `Connection`）必须显式地与元数据对象进行连接，才能进行元数据操作；这确保了操作的安全性和可靠性。所以 **SQLAlchemy** 2 不提供 `MetaData` 的 `bind` 参数，先来看看旧版的用法：

```python
from sqlalchemy import create_engine, MetaData

engine = create_engine("postgresql://user:password@host:port/database")
meta_data = MetaData(bind=engine) # 2.0 不在支持 bind 这个参数了

meta_data.create_all()  # 2.0 要求传入 engine 或者 connection
meta_data.reflect()  # 2.0 要求传入 engine 或者 connection

# 2.0 要求使用 autoload_with=engine 替代 autoload=True
t = Table("t",zz meta_data, autoload=True)  
```

现在看看新版的用法：

```python
from sqlalchemy import create_engine, MetaData

engine = create_engine("postgresql://user:password@host:port/database")
meta_data = MetaData() # bind 不见了

meta_data.create_all(engine)  # 要传入 engine
meta_data.reflect(engine)  # 要传入 engine

# 要使用 autoload_with=engine 替代 autoload=True
t = Table("t", meta_data, autoload_with=engine)  
```



