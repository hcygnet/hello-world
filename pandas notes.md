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

## 数据特性

DataFrame的存储形式类似于list和tuple，变量保存的实际上是指针，因此在备份数据时，应该使用copy()方法而非直接赋值
```python
import pandas as pd
df = pd.DataFrame(np.arange(5).reshape(5, 1), columns = ['a'])
df1 = df.copy()
df1['b'] = 1
print df1, df
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

如果希望GroupBy对象中，被groupby的列不成为index，要加一项设置
```python
gb2 = df.groupby('a', as_index = False)
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


### [Join](http://pandas.pydata.org/pandas-docs/version/0.19/merging.html#database-style-dataframe-joining-merging)

pandas的DataFrame结构可以像数据库一样实现join操作，并有着很高效率。

一个简单的join示例

```python
import pandas as pd, numpy as np
n = 5
df1 = pd.DataFrame(np.arange(n).reshape(n, 1), columns = ['a'])
df1['b'] = np.random.randint(1, 5, size = n)
print df1

df2 = pd.DataFrame(np.arange(n).reshape(n, 1) * 2, columns = ['a'])
df2['c'] = np.random.randint(10, 20, size = n)
print df2

res = pd.merge(df1, df2, how = 'left', on = ['a'])
print res
```

## 实操问题


### GroupBy.apply(func)中的自定义函数

对于自定义的group by函数，会出现奇怪的索引列，level_x。
```python
import pandas as pd, numpy as np

def gb_test(df):
    value = np.random.randint(5)
    return pd.DataFrame({'value':[value]})

df = pd.DataFrame(np.random.randint(0, 5, size = (10, 2)), columns = ['a', 'b'])
print df
gb = df.groupby(['a'])
res = gb.apply(gb_test)
print res
print res.reset_index()
```

输出：
```
   a  b
0  2  2
1  1  3
2  2  3
3  1  3
4  2  4
5  4  1
6  2  3
7  1  2
8  2  3
9  4  1
     value
a         
1 0      2
2 0      3
4 0      2
   a  level_1  value
0  1        0      2
1  2        0      3
2  4        0      2
```

这一索引导致后续处理要花费多余的操作来排出其影响，比如对df和df2就不能坐关联操作。下列两个操作都会报错
```python
df_merge = pd.merge(df, df2, how = 'left', left_on = ['a'], right_index = True)
df_join = df.join(df2, on = ['a'], how = 'left')
```