﻿# postgreSQL常用命令和技巧

## 常用语句

创建表格

```sql
CREATE TABLE trip_log (
	trip_id INTEGER PRIMARY KEY,
	vehicle_id TEXT,
	start_pt_x DOUBLE PRECISION,
	start_pt_y DOUBLE PRECISION,
	end_pt_x DOUBLE PRECISION,
	end_pt_y DOUBLE PRECISION
	start_time TIMESTAMP(0) WITHOUT TIME ZONE,
	end_time TIMESTAMP(0) WITHOUT TIME ZONE
);
```

插入数据、调整数据
```sql
UPDATE trip_log SET trip_id = -1 WHERE end_pt_x IS NULL;
```


查询字段
```sql
SELECT vehicle_id, count(vehicle_id) AS cnt FROM trip_log
WHERE start_time > '2018-03-22 15:00:00'::TIMESTAMP(0) WITHOUT TIME ZONE
GROUP BY vehicle_id
ORDER BY vehicle_id
LIMIT 100;
```

关联表格
```sql
CREATE TABLE vehicle_info (
	vehicle_id TEXT,
	v_type TEXT,
	emmision INTEGER
);

SELECT tb1.*, tb2.*
FROM trip_log AS tb1
LEFT JOIN vehilce_info AS tb2
ON tb1.vehicle_id = tb2.vehicle_id;
```

嵌套查询
```sql
SELECT * FROM 
WHERE vehicle_id = (
	SELECT vehicle_id FROM vehicle_info
	WHERE emmision < 100
);
```

相似查询
```sql
SELECT * FROM trip_log
WHERE vehicle_id LIKE '京_3%';
```

相邻行计算
```sql
CREATE TABLE gps (
	vehicle_id TEXT,
	x DOUBLE PRECISION,
	y DOUBLE PRECISION,
	time0 TIMESTAMP(0) WITHOUT TIME ZONE
);

SELECT tb2.time0 - tb1.time0
FROM (
	SELECT *, ROW_NUMBER() OVER (PARTITION BY vehicle_id ORDER BY time0) AS rid
	FROM gps
) AS tb1
LEFT JOIN (
	SELECT *, ROW_NUMBER() OVER (PARTITION BY vehicle_id ORDER BY time0) AS rid
	FROM gps
) AS tb2
ON (
	tb1.rid = tb2.rid - 1 AND tb1.vehicle_id = tb2.vehicle_id
)
ORDER BY tb1.vehicle_id
```

## 删除重复数据测试

```sql
SELECT count(*) FROM gps2avl.gps1012_ln1102
```

> 146502

DISTINCT 方法

```sql
SELECT DISTINCT * FROM gps2avl.gps1012_ln1102
```

> Successfully run. Total query runtime: 1 secs.

> 68692 rows affected.

数据重复率为 1 - 68692 / 146502 = 53.1%

[高效率ctid方法](https://blog.csdn.net/arcticjian/article/details/50042647)

```sql
DELETE FROM gps2avl.gps1012_ln1102_copy a
WHERE a.ctid = ANY (
	ARRAY (
		SELECT ctid FROM(
			SELECT row_number() OVER (
					PARTITION BY bus_company, vehicle_code,
						line_name, time0, pt
				),
				ctid FROM gps2avl.gps1012_ln1102_copy
			) t
		WHERE t.row_number > 1
	)
)
```

> DELETE 77810

> Query returned successfully in 1 secs.

我们来测试一个更大的表格

```sql
SELECT count(*) FROM gps2avl.gps1012_yt
```

> 4639386

```sql
SELECT DISTINCT * FROM gps2avl.gps1012_yt
```

> Successfully run. Total query runtime: 8 min.

> 2315975 rows affected.

数据重复率为 1 - 2315975 / 4639386 = 50.1%

再看看高效率ctid方法

```sql
DELETE FROM gps2avl.gps1012_yt a
WHERE a.ctid = ANY (
	ARRAY (
		SELECT ctid FROM(
			SELECT row_number() OVER (
					PARTITION BY bus_company, vehicle_code,
						line_name, time0, pt
				),
				ctid FROM gps2avl.gps1012_yt
			) t
		WHERE t.row_number > 1
	)
)
```

> DELETE 2323411

> Query returned successfully in 56 secs.


## 快速选取Distinct的一种方法

对于选取distinct数据，最直接有效的办法就是利用distinct关键字，或者利用group by。
但即便在加了索引的情况下，这两者的效率都还是很低。
利用explain查看发现，pg甚至还不使用索引（笔者目前还不能理解其原因）。
在数据量超过一定程度后，低效率就成为了数据处理的瓶颈。

这里提供一种快速选取distinct的思路，主要依靠上文叙述的快速去重实现。

分两步
1. 备份原表格中的目标字段
2. 对备份表去重

这样，得到的新表即为选取distinct的结果

样例代码如下

```sql
CREATE TABLE gps2avl.gps1012_yt_rpos_copy AS (
	SELECT line_name, vehicle_code, name0 FROM gps2avl.gps1012_yt_rpos
)
```
> SELECT 4606607
> Query returned successfully in 26 min.

```sql
DELETE FROM gps2avl.gps1012_yt_rpos_copy a
WHERE a.ctid = ANY (
	ARRAY (
		SELECT ctid FROM(
			SELECT row_number() OVER (
					PARTITION BY line_name, vehicle_code, name0
				),
				ctid FROM gps2avl.gps1012_yt_rpos_copy
			) t
		WHERE t.row_number > 1
	)
)
```
> DELETE 4604705
> Query returned successfully in 1 min.

相比之下，用distinct选取数据需要超过10h（没有耐心等下去了...可能远远不止）