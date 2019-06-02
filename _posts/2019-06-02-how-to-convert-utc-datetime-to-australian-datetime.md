# How to convert Azure datetime into Australian datetime

First find the time zone name for the local region:

```sql
select * 
from sys.time_zone_info
where name like '%Aus%'
```

|name|current_utc_offset|is_currently_dst|
|---|---|---|
|W. Australia Standard Time		|+08:00|0|
|Aus Central W. Standard Time	|+08:45|0|
|Cen. Australia Standard Time	|+09:30|0|
|AUS Central Standard Time		|+09:30|0|
|E. Australia Standard Time		|+10:00|0|
|AUS Eastern Standard Time		|+10:00|0|

Then use the time zone name to convert Azure times into Australian times: 

```sql
select
  cast(azure_server_time at time zone 'UTC' 
                         at time zone 'AUS Eastern Standard Time' as datetime) as 'local_au_time'
from dbo.some_table                         
```
