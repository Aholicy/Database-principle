<center>数据库系统实验<center>
<center>实验报告<center>

| 题目 | （实验 11） |
| ---- | ----------- |
| 姓名 | 俞泽斌      |
| 学号 | 20337263    |
| 班级 | 计科 2 班   |

## 一 、实验环境

1、 操作系统： Windows 10
2、 DBMS ： mysql 

## 二 、实验内容

#### 创建一个教工表teacher(tno,tname,tadd,telphone,tsex,id)，将教工号tno设为主键，性别默认值为“男“

输入命令

```sql
create table teacher(
	tno varchar(7) primary key,
	tname varchar(6),
	tadd int,
	telphone varchar(15),
	tsex char(2) default '男',
	id varchar(20)
)engine=InnoDB;
```

输出

```sql
Query OK, 0 rows affected (0.03 sec)
```

#### 根据教工表 teacher 完成以下任务：  

##### 1.设置 telphone 默认值为 00000000。  

```sql
alter table teacher modify telphone char(12)default '00000000';
```

输出

```sql
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

检验

```sql
insert into teacher (tno,tname,tadd,id)values('0000001','Mike','12','0000000001')
```

输出

```sql
Query OK, 1 row affected (0.04 sec)
```

查看

```sql
select * from teacher;
```

输出

```sql
+---------+-------+------+----------+------+------------+
| tno     | tname | tadd | telphone | tsex | id         |
+---------+-------+------+----------+------+------------+
| 0000001 | Mike  |   12 | 00000000 | 男   | 0000000001 |
+---------+-------+------+----------+------+------------+
1 row in set (0.04 sec)
```

可以看到此时telphone有默认值

##### 2.设置 tsex 的 check 检查约束为： 输入值只能为“ 男” 或“ 女” 。  

首先使用check后发现出错，查找资料后发现只能用alter操作的modify，enum为枚举函数

输入代码

```sql
alter table teacher modify tsex enum('男','女') default '男';
```

输出

```sql
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

检验

输入代码

```sql
insert into teacher (tno,tname,tadd,tsex,id)values('0000001','Mike','12','女','0000000001');
insert into teacher (tno,tname,tadd,tsex,id)values('0000002','Try','12','中','0000000002');
```

第一行插入成功

```sql
Query OK, 1 row affected (0.04 sec)
```

第二行不符合约束，插入失败

```sql
ERROR 1265 (01000): Data truncated for column 'tsex' at row 1
```

查看此时的表格

```sql
 select * from teacher;
+---------+-------+------+----------+------+------------+
| tno     | tname | tadd | telphone | tsex | id         |
+---------+-------+------+----------+------+------------+
| 0000001 | Mike  |   12 | 00000000 | 女   | 0000000001 |
+---------+-------+------+----------+------+------------+
1 row in set (0.00 sec)
```

符合上述要求

##### 3.设置 id 的位数为 15 位或 18 位， 每位都是数字。  

使用触发器来实现

```sql
delimiter //
create trigger id_check after insert on teacher
	for each row
		begin
		declare tempcheck int default 0;
		if (length(new.id)!=15 and length(new.id)!=18) then
			signal sqlstate'45000' set message_text = 'length error';
		end if;
		select new.id regexp '^[0-9]*$' into tempcheck; 
		if(tempcheck=0) then
			signal sqlstate'45000' set message_text='no such number';
		end if;
end //
```

输出结果

```sql
Query OK, 0 rows affected (0.04 sec)
```

检验

```sql
insert into teacher (tno,tname,tadd,tsex,id)values('0000001','Mike','12','女','1');
insert into teacher (tno,tname,tadd,tsex,id)values('0000002','Tom','12','男','abc000000000000');
insert into teacher (tno,tname,tadd,tsex,id)values('0000003','Alice','12','女','000000000000001');

```

结果

```sql
mysql> insert into teacher (tno,tname,tadd,tsex,id)values('0000001','Mike','12','女','1');
ERROR 1644 (45000): length error
mysql> insert into teacher (tno,tname,tadd,tsex,id)values('0000002','Tom','12','男','abc000000000000');
ERROR 1644 (45000): no such number
mysql> insert into teacher (tno,tname,tadd,tsex,id)values('0000003','Alice','12','女','000000000000001');
Query OK, 1 row affected (0.04 sec)
```

可以看到成功引发了定义的两个错误，检验成功

#### 设有订报管理子系统数据库DingBao中的表PAPER,表内容如下表：请在掌握数据库完整性知识的基础上，根据表内容设定尽可能多的完整性规则于该表，用于保障该表的正确性与完整性。

输入代码,首先创建表格，此时设置约束

1、编号为主键且长度固定为7

2、报纸名称不为空且不重复

3、报纸价格不为空

```sql
create table paper
(
	pno char(7) primary key,
	pna varchar(20) unique not null,
	ppr float not null
)engine=InnoDB;

```

输出

```sql
Query OK, 0 rows affected (0.05 sec)
```

还可以做一个与上面实验差不多的约束，即让pno的每一项都规定为数字，同样使用触发器来进行

```sql
delimiter //
create trigger pno_check after insert on paper
	for each row
		begin
		declare tempcheck int default 0;
		if (length(new.pno)!=7 ) then
			signal sqlstate'45000' set message_text = 'length error';
		end if;
		select new.pno regexp '^[0-9]*$' into tempcheck; 
		if(tempcheck=0) then
			signal sqlstate'45000' set message_text='no such number';
		end if;
end //
```

```sql
Query OK, 0 rows affected (0.01 sec)
```

检验，输入一个错误记录

```sql
insert into paper(pno,pna,ppr)values('abc1234','实验报','8.8');
```

报错为

```sql
ERROR 1644 (45000): no such number
```

输入一个正确记录

```sql
insert into paper(pno,pna,ppr)values('1111234','实验报','8.8');
```

```sql
Query OK, 1 row affected (0.04 sec)
```

```sql
select * from paper//
+---------+-----------+-----+
| pno     | pna       | ppr |
+---------+-----------+-----+
| 1111234 | 实验报    | 8.8 |
+---------+-----------+-----+
1 row in set (0.00 sec)
```

实验成功，约束制定完成。