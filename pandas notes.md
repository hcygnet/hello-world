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



