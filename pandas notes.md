# pandasʹ���ּ�

pandas��һ�����numpy���ݴ����зḻ�߼����ܵ����ݴ���߼����߰�

## ��ʼ����IO

DataFrame�ĳ�ʼ�������кܶ��֣�����[��dictת��](http://pandas.pydata.org/pandas-docs/version/0.19/dsintro.html#from-dict-of-series-or-dicts)��[��ndarray����listת��](http://pandas.pydata.org/pandas-docs/version/0.19/dsintro.html#from-dict-of-ndarrays-lists)���Լ�[��������](http://pandas.pydata.org/pandas-docs/version/0.19/dsintro.html#dataframe)��
�����Ǽ������ӣ�

```python
df1 = pd.DataFrame({'Score':[83, 76, 91], 'Name':['Ben', 'April', 'Mike']})
df2 = pd.DataFrame([1, 2, 3], index = ['a', 'b', 'c'], columns = ['num'])
```

���⣬pandas֧�ַḻ��IO����

### �����ݿ⵼��

���������ݿ⣬Ȼ����pandas�Դ���sql���߶��롣
��PostgreSQLΪ��

```python
import pandas.io.sql as psql, psycopg2
con = psycopg2.connect(dbname, user, password)
sql_s = """ SELECT * FROM test """

df = psql.read_sql(sql_s, con) # df as dataframe

con.close()
```

### ���������ݿ�

ֱ�ӵ���dataframe�ķ���to_sql��������Ҫ��װsqlalchemy���߰�����
��PostgreSQLΪ��

```python
from sqlalchemy import create_engine
engine = create_engine('postgresql://username:password@host:port/database')
df.to_sql('tablename', engine)
```

## ��������

DataFrame�Ĵ洢��ʽ������list��tuple�����������ʵ������ָ�룬����ڱ�������ʱ��Ӧ��ʹ��copy()��������ֱ�Ӹ�ֵ
```python
import pandas as pd
df = pd.DataFrame(np.arange(5).reshape(5, 1), columns = ['a'])
df1 = df.copy()
df1['b'] = 1
print df1, df
```

## ���ݴ���

pandas�������������ݿ�����ݴ�����

### [Group By](https://pandas.pydata.org/pandas-docs/stable/api.html#groupby)

pandasͨ������DataFrame/Series�����µķ���groupby()������GroupBy����
```python
import pandas as pd, numpy as np
d = np.random.randint(0, 4, size = (10, 3))
df = pd.DataFrame(d)
df.columns = ['a', 'b', 'c']
gb = df.groupby('a')
```

���ϣ��GroupBy�����У���groupby���в���Ϊindex��Ҫ��һ������
```python
gb2 = df.groupby('a', as_index = False)
```

��һϵ�п���Ӧ����GroupBy�����[����](https://pandas.pydata.org/pandas-docs/stable/api.html#id41)��
```python
print gb.sum()
print gb.count()
print gb.max()
```

�����Ѷ�GroupBy����Ӧ�ø�Ϊ���ӵķ�����pandasҲ�ṩ�˺ܼ��׵�д��[.apply()](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.core.groupby.GroupBy.apply.html#pandas-core-groupby-groupby-apply)��
```python
print gb.apply(lambda x: x.max() - x.min())
print gb.apply(lambda x: x.c.max() - x.b.sum())
```

Ҳ�����Լ�д�����������ķ���ֵӦΪDataFrame�������Series���������ֵΪ����DataFrame����groupby���Ҳ����Ӧ�ĵõ����С�


### [Join](http://pandas.pydata.org/pandas-docs/version/0.19/merging.html#database-style-dataframe-joining-merging)

pandas��DataFrame�ṹ���������ݿ�һ��ʵ��join�����������źܸ�Ч�ʡ�

һ���򵥵�joinʾ��

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

## ʵ������


### GroupBy.apply(func)�е��Զ��庯��

�����Զ����group by�������������ֵ������У�level_x��
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

�����
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

��һ�������º�������Ҫ���Ѷ���Ĳ������ų���Ӱ�죬�����df��df2�Ͳ������������������������������ᱨ��
```python
df_merge = pd.merge(df, df2, how = 'left', left_on = ['a'], right_index = True)
df_join = df.join(df2, on = ['a'], how = 'left')
```