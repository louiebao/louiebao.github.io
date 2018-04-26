# It took forever to delete one row

Have you ever tried to delete a record from a table and it took forever? 

Well, check the foreign key references on the primary key table. Most likely the foreign key constraints are causing the delete performance to grind to a halt.

I will demonstrate how you can work around ths issue.

### Setup

Execute the script below to setup some tables for testing:

```sql
use tempdb

create table dbo.primarykey
(
    id int not null primary key
)

create table dbo.foreignkey
(
    id int not null constraint fk references primarykey (id)
) 
```

### Check table

```sql
sp_help 'dbo.primarykey'
```

You will get several result sets and pay attention to the one titled "Table is referenced by foreign key".

### Disable foreign key constraints

- Copy the list of constraints to a new query window.
- Replace the beginning of the lines with "alter table ".
- Replace semi colons with " nocheck constraint ".

Your script should look like the one below

```sql
alter table tempdb.dbo.foreignkey nocheck constraint fk
```

Execute the script to disable the foreign keys.

### Delete the record(s)

Now you should be able to delete the desired records with a much faster response time (assuming you've got appropriate indexes and everything).

```sql
delete dbo.primarykey where id = 1
```

### Re-Enable the foreign key constraints

We can re-enable the constraints by using the same script we've prepared earlier for disabling the foreign key constraints. We just need to replace "nocheck" with "with check check".

```sql
alter table tempdb.dbo.foreignkey with check check constraint fk
```

