# Remember to include the SEQUENCE number in data seeding scripts

It is common knowledge that only one identity column can exist in a table. So what if you need to have another incrementing ID column in the same table? That's where the SEQUENCE object comes in handy.

For example

```sql
create table dbo.product
(
      id            int           not null identity(1, 1)
      product_id    int           not null
    , product_name  nvarchar(128) not null
)
go

create sequence dbo.product_id_seq
as int
start with 1
increment by 1;
go
```

Before the SEQUENCE object was introduced, you'd had to query the table to find the max product_id and increment by 1.

```sql
insert dbo.product(product_id, product_name)
values ((select max(product_id) + 1 from dbo.product), 'test')
```

But now, you can easily generate the next number without worrying about the last used ID:

```sql
insert dbo.product(product_id, product_name)
values (next value for dbo.product_id_seq, 'test')
```

There is one catch though. Despite what you name the sequence object, it knows nothing about where or how the IDs are being used, so it is entirely up to the dev to control the usage of the IDs. I have once made a mistake in a data seeding script:

```sql
;with cte_source(product_id, product_name)
as
(
            select 1, 'A'
  union all select 2, 'B'
  union all select 3, 'C'
)
merge into dbo.product t
using cte_source s        on t.product_id = s.product_id
when not matched then
  insert (product_id, product_name)
  values (product_id, product_name)
;  
```

I have prepopulated the dbo.product table with known IDs without thinking/realising that the sequence object was still defined to start at 1. Guess what would happen when product 'D' gets inserted into the table later on? 

```sql
insert dbo.product(product_id, product_name)
values (next value for dbo.product_id_seq, 'D')
```

|id|product_id|product_name|
|-|-|-|
|1|1|A|
|2|2|B|
|3|3|C|
|4|1|D|

Wow, luckily this was picked up in testing. The fix I put in was:

```sql
;with cte_source(product_id, product_name)
as
(
            select (next value for dbo.product_id_seq), 'A'
  union all select (next value for dbo.product_id_seq), 'B'
  union all select (next value for dbo.product_id_seq), 'C'
)
merge into dbo.product t
using cte_source s        on t.product_id = s.product_id
when not matched then
  insert (product_id, product_name)
  values (product_id, product_name)
;  
```

and that was no good either:

```
Msg 11721, Level 15, State 1, Line 4
NEXT VALUE FOR function cannot be used directly in a statement that uses a DISTINCT, UNION, UNION ALL, EXCEPT or INTERSECT operator.
```

OK, that made sense, the sequence needs to be generated one at a time. So I turned the merge statement into individual inserts:

```sql
insert dbo.product(product_id, product_name)
select (next value for dbo.product_id_seq), 'A'

insert dbo.product(product_id, product_name)
select (next value for dbo.product_id_seq), 'B'

insert dbo.product(product_id, product_name)
select (next value for dbo.product_id_seq), 'C'
```

and now if we were to insert product 'D' again:

```
insert dbo.product(product_id, product_name)
values (next value for dbo.product_id_seq, 'D')
```

|id|product_id|product_name|
|-|-|-|
|1|1|A|
|2|2|B|
|3|3|C|
|4|4|D|

All good.
