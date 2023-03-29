<center>数据库系统实验<center>

<center>实验报告<center>

| 题目 | （实验 7） |
| ---- | ---------- |
| 姓名 | 俞泽斌     |
| 学号 | 20337263   |
| 班级 | 计科 2 班  |

## 一 、实验环境

1、 操作系统： Windows 10
2、 DBMS ： mysql 

## 二 、实验内容

#### 索引的使用效果测试。 参照实验示例上机操作， 增大test表的记录到8万条或更多， 重做实验。 多次记录耗时， 并作分析比较。  

#### 1、创建表

输入命令

```sql
Create table test (id int unique AUTO_INCREMENT, rq datetime null, srq varchar (20) null,
hh smallint null, mm smallint null, ss smallint null, num numeric (12,3),primary key (id))
AUTO_INCREMENT= 1 engine=MyISAM;
```

```sql
Query OK, 0 rows affected (0.01 sec)
```

创建存储过程生成表中数据

这里将数据改为85000条

```sql
DELIMITER //
CREATE PROCEDURE p1()
begin
set @i= 1;
WHILE @i <= 85000 do
	INSERT INTO TEST (RQ,SRQ,hh,mm,SS,NUM)
	VALUES (now(),now() , hour(now()),minute(now()), second(now()), RAND(@i) * 100);
	set @i= @i+ 1;
END WHILE;
End//
call p1//
DELIMITER ;
```

```sql
Query OK, 0 rows affected (0.01 sec)

mysql> call p1//
Query OK, 0 rows affected (37.29 sec)
```

#### 2、运行测试代码

##### 1.未建索引时按以下步骤操作  

###### (1)单记录插入

```sql
delimiter //
select @i:=max(id) from test;
insert into test(rq,srq,hh,mm,ss,num)
values(now(),now(),hour(now()),minute(now()),second(now()),rand(@i)*100);
```

输出

```sql
+-------------+
| @i:=max(id) |
+-------------+
|       85000 |
+-------------+
1 row in set, 1 warning (0.00 sec)

Query OK, 1 row affected, 1 warning (0.00 sec)
```

###### （2）查询所有记录，按id排序

```sql
select * from test order by id;
```

具体输出太多不展示了，输出结果以及时间为

```sql
85001 rows in set (0.38 sec)
```

###### （3）查询所有记录， 按mm排序。  

```sql
select * from test order by mm;
```

输出结果及时间

```sql
85001 rows in set (0.38 sec)
```

###### （4）单记录查询  

```sql
select id from test where id=51;
```

输出结果及时间

```sql
+----+
| id |
+----+
| 51 |
+----+
1 row in set (0.00 sec)
```

##### 3 对 test 表的 mm 字段建立非聚集索引  

###### (1) 建立索引耗时

```sql
create index indexname1 on test(mm);
```

输出结果及耗时

```sql
Query OK, 85001 rows affected (1.17 sec)
Records: 85001  Duplicates: 0  Warnings: 0
```

###### (2) 单记录插入  

```sql
delimiter //
select @i:=max(id) from test;
insert into test(rq,srq,hh,mm,ss,num)
values(now(),now(),hour(now()),minute(now()),second(now()),rand(@i)*100);
```

输出结果及耗时

```sql
+-------------+
| @i:=max(id) |
+-------------+
|       85001 |
+-------------+
1 row in set, 1 warning (0.00 sec)

Query OK, 1 row affected, 1 warning (0.00 sec)
```

###### (3)查询所有记录， 按 id 排序  

```sql
select * from test order by id;
```

输出结果及耗时

```sql
85002 rows in set (0.38 sec)
```

###### (4)查询所有记录， 按 mm 排序  

```sql
select * from test order by mm;
```

输出结果及耗时

```sql
85002 rows in set (0.38 sec)
```

似乎提升速度不大，可能是IO已经到瓶颈了

###### （5）单记录查询  

```sql
select id from test where id=51;
```

输出结果及耗时

```sql
+----+
| id |
+----+
| 51 |
+----+
1 row in set (0.00 sec)
```

###### (6)删除索引  

```sql
drop index indexname1 on test;
```

输出结果及耗时

```sql
Query OK, 85002 rows affected (1.15 sec)
Records: 85002  Duplicates: 0  Warnings: 0
```

