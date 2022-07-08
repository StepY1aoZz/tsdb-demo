# TDEngine Demo: Smart Meters

1. Schema Design

       ![image](https://github.com/StepY1aoZz/tsdb-demo/blob/main/Untitled%20Diagram.drawio.png)

对于智能电表这类设备，建立一张超级表，规定其数据采集量为电压，电流以及对应的相位。使用其所在的位置作为静态量来对其进行标识

2. Create databases and super tables

```sql
CREATE DATABASE meter UPDATE 1;
USE meter;
```

建立一个名为meter的数据库，并将其设为可更新的(`UPDATE 1`意味着支持更新数据，并且每次更新需更新一行的所有数据)

```sql
CREATE STABLE meters (ts timestamp, current float, voltage int, phase float) TAGS (location binary(64));
```

对应上文的模式设计图创作一张超级表

3. Create tables

```sql
CREATE TABLE d1001 USING meters TAGS ("Sichuan.Chengdu");
```

按照meters超级表所规定的模式，建立一张表，表名为d1001，代表该设备名，TAG字段代表其所在位置

4. Insert sample data

```sql
INSERT INTO d1001 VALUES (1538548684000, 10.2, 220, 0.23) (1538548696650, 10.3, 218, 0.25);
INSERT INTO d1001 VALUES ('2022-07-01 14:38:16.600', 10.3, 219, 0.31);
```

    

5. Query

```sql
select * from d1001 where ts>now - 4w order by ts desc;
```

查询d1001电表前四周的记录

输出结果：

```
           ts            |       current        |   voltage   |        phase         |
======================================================================================
 2022-07-01 14:38:16.600 |             10.30000 |         219 |              0.31000 |
Query OK, 1 row(s) in set (0.001803s)

```


