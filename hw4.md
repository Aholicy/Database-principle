20337263 俞泽斌

#### 4.18

使用外连接

```sql
select ID
from employee natural left outer join manages
where manager_id is null;
```

不使用外连接

```sql
select ID
from employee
where employee.ID not in
	(select ID from manages
	where manager_id is not null
	);
```

#### 4.20

```sql
create view tot_credits (year, num_credits) as
select year, sum(credits)
from takes natural join course
group by year;
```

#### 4.24

会，因为这时候用户A将选择的权限授权给了public，而B作为public 的一员就得到了A授予的选择的权限，但是B 又将选择权限授权给 A  ，这样的话在判断有无权限的时候，是需要去溯源的，但此时会出现A->public->B->A   的路径，为一个环路