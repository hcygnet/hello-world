# pandas使用手记

## 初始化和IO

pandas是一款基于numpy数据处理但有丰富高级功能的数据处理高级工具包

### 从数据库导入

先连上数据库，然后用pandas自带的sql工具读入。
以PostgreSQL为例
```python
import pandas.io.sql as psql, psycopg2
con = psycopg2.connect(dbname, user, password)
sql_s = """ SELECT * FROM test """

df = psql.read_sql(sql_s, con) # df as dataframe

con.close()
```

### 导出到数据库

直接调用dataframe的方法to_sql（这里需要安装sqlalchemy工具包）。
以PostgreSQL为例
```python
from sqlalchemy import create_engine
engine = create_engine('postgresql://username:password@host:port/database')
df.to_sql('test_copy', engine)
```