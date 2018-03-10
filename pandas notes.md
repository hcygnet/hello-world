# pandas使用手记

pandas是一款基于numpy数据处理但有丰富高级功能的数据处理高级工具包

## 初始化和IO

DataFrame的初始化方法有很多种，可以[由dict转换](http://pandas.pydata.org/pandas-docs/version/0.19/dsintro.html#from-dict-of-series-or-dicts)、[由ndarray或者list转换](http://pandas.pydata.org/pandas-docs/version/0.19/dsintro.html#from-dict-of-ndarrays-lists)、以及[其他种种](http://pandas.pydata.org/pandas-docs/version/0.19/dsintro.html#dataframe)。
下面是几个例子：

```python
df1 = pd.DataFrame({'Score':[83, 76, 91], 'Name':['Ben', 'April', 'Mike']})
df2 = pd.DataFrame([1, 2, 3], index = ['a', 'b', 'c'], columns = ['num'])
```

此外，pandas支持丰富的IO方法

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
df.to_sql('tablename', engine)
```

## 数据处理

pandas有着类似于数据库的数据处理功能

### [Group By](https://pandas.pydata.org/pandas-docs/stable/api.html#groupby)

pandas通过调用DataFrame/Series对象下的方法groupby()，生成GroupBy对象。
```python
import pandas as pd, numpy as np
d = np.random.randint(0, 4, size = (10, 3))
df = pd.DataFrame(d)
df.columns = ['a', 'b', 'c']
gb = df.groupby('a')
```

有一系列可以应用与GroupBy对象的[方法](https://pandas.pydata.org/pandas-docs/stable/api.html#id41)。
```python
print gb.sum()
print gb.count()
print gb.max()
```

如果想把对GroupBy对象应用更为复杂的方法，pandas也提供了很简易的写法[.apply()](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.core.groupby.GroupBy.apply.html#pandas-core-groupby-groupby-apply)。
```python
print gb.apply(lambda x: x.max() - x.min())
print gb.apply(lambda x: x.c.max() - x.b.sum())
```

也可以自己写函数，函数的返回值应为DataFrame对象或者Series。如果返回值为多列DataFrame，则groupby结果也会相应的得到多列。



