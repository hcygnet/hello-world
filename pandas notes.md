# pandasʹ���ּ�

## ����

pandas��һ�����numpy���ݴ����зḻ�߼����ܵ����ݴ���߼����߰�

### ��ʼ��

#### �����ݿ⵼��

���������ݿ⣬Ȼ����pandas�Դ���sql���߶��롣��PostgreSQLΪ��
```python
import pandas.io.sql as psql, psycopg2
con = psycopg2.connect(dbname, user, password)
sql_s = """ SELECT * FROM test """
df = psql.read_sql(sql_s, con) # df as dataframe
con.close()
```