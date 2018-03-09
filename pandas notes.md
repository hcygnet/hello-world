# pandasʹ���ּ�

## ��ʼ����IO

pandas��һ�����numpy���ݴ����зḻ�߼����ܵ����ݴ���߼����߰�

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
df.to_sql('test_copy', engine)
```