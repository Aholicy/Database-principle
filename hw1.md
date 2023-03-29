《数据库系统实验》

实验报告

| **题目** | （实验3） |
| -------- | --------- |
| **姓名** | 俞泽斌    |
| **学号** | 20337263  |
| **班级** | 计科二班  |

 

一．实验环境：

Windows10

mysql workbench 8.0

二．实验内容与完成情况：

（逐题给出相应的SQL语句及执行结果，SQL语句必须能够拷贝，实验结果可截图；题目内容不需抄写）

 ```sql
 CREATE VIEW `c_p_n` AS
 select customer.cno,customer.cna,paper.pno,paper.pna,cp.num
 from customer,paper,cp
 where customer.cno=cp.cno and cp.pno=paper.pno
 ```

```sql
CREATE 
    ALGORITHM = UNDEFINED 
    DEFINER = `root`@`localhost` 
    SQL SECURITY DEFINER
VIEW `mydb`.`c_p_n` AS
    SELECT 
        `mydb`.`customer`.`cno` AS `cno`,
        `mydb`.`customer`.`cna` AS `cna`,
        `mydb`.`paper`.`pno` AS `pno`,
        `mydb`.`paper`.`pna` AS `pna`,
        `mydb`.`cp`.`num` AS `num`
    FROM
        ((`mydb`.`customer`
        JOIN `mydb`.`paper`)
        JOIN `mydb`.`cp`)
    WHERE
        ((`mydb`.`customer`.`cno` = `mydb`.`cp`.`cno`)
            AND (`mydb`.`cp`.`pno` = `mydb`.`paper`.`pno`))
```

```sql
CREATE 
    ALGORITHM = UNDEFINED 
    DEFINER = `root`@`localhost` 
    SQL SECURITY DEFINER
VIEW `mydb`.`c_p_n` AS
    SELECT 
        `mydb`.`customer`.`cno` AS `cno`,
        `mydb`.`customer`.`cna` AS `cna`,
        `mydb`.`paper`.`pno` AS `pno`,
        `mydb`.`paper`.`pna` AS `pna`,
        `mydb`.`cp`.`num` AS `num`,
        `mydb`.`paper`.`ppr` AS `ppr`
    FROM
        ((`mydb`.`customer`
        JOIN `mydb`.`paper`)
        JOIN `mydb`.`cp`)
    WHERE
        ((`mydb`.`customer`.`cno` = `mydb`.`cp`.`cno`)
            AND (`mydb`.`cp`.`pno` = `mydb`.`paper`.`pno`))
```



作业说明：

作业内容：实验课教材第66页“实验内容与要求”1-3题


 作业要求：
 1.只需提交电子版的实验报告；
 2.实验报告格式可参考“实验报告参考例子”附件。