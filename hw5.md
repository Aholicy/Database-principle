20337263 俞泽斌

teaches表

```sql
create trigger t_trigger after update or insert on teaches
for each row
begin
	select building as b1, time_slot_id as t1, room_number as r1
	from section
	where
	( section.course_id = new.course_id
	and section.sec_id = new.sec_id
	and section.semeter = new.semeter
	and section.year = new.year;
	)
	select teaches.sec_id as id, section.building as b2,section.room_number as r2
	from teaches natural join section
	where
	(teaches.ID = new.ID
	and section.time_slot_id = t1
	and section.semeter = new.semeter
	and section.year = new.year
	);
	if (b1 <> b2)
	rollback;
	else if (r1<>r2)
	rollback;
	else if id = new.sec_id
	rollback;
end
```

section表

```sql
create trigger s_trigger after update or insert on section
for each row
begin
	select building as b1, time_slot_id as t1, room_number as r1
	from new;
	select teaches.sec_id as id, section.building as b2, section.room_number as r2
	from teaches natural join section
	where
		(teaches.ID =
     		(select ID from new natural join teaches)
        and section.time_slot_id = t1
		and section.semeter = new.semeter
		and section.year = new.year
	);
	if (b1 <> b2)
	rollback;
	else if (r1<>r2)
	rollback;
	else if id = new.sec_id
	rollback;
end
```

