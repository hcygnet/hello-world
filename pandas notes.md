# pandas使用手记

## 基础

pandas是一款基于numpy数据处理但有丰富高级功能的数据处理高级工具包

### 初始化

#### 从数据库导入

先连上数据库，然后用pandas自带的sql工具读入。以PostgreSQL为例
```python
import pandas.io.sql as psql, psycopg2
con = psycopg2.connect(dbname, user, password)
sql_s = """ SELECT * FROM test """
df = psql.read_sql(sql_s, con) # df as dataframe
con.close()
```