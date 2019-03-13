# How to find procs that modify a table?

### sys.sql_modules

```sql
select
from sys.sql_modules
where definition like '%delete%student%'
```

### DMV


### Full-Text Search

