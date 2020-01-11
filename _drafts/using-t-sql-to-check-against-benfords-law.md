# Using T-SQL to check against Benford's Law

### Benford's Law
[Benford's law](https://en.wikipedia.org/wiki/Benford%27s_law), the law of the first-digit, is an observation about the frequency distribution of leading digits in many real-life sets of numerical data. Benford's Law could be used to detect accounting and expenses fraud. In the 2016 movie [The Accountant](https://en.wikipedia.org/wiki/The_Accountant_(2016_film)), Ben Affleck's character uses Benford's Law to expose the theft of funds from a robotics company.

You can use the code snippet below to check the distribution of your data set against Benford's Law. Just replace *dbo.num* and *amount* with your table and column names.

```sql
;with cte_benford(first_digit, expected_distribution)
as
(
              select 1, 30.1
    union all select 2, 17.6
    union all select 3, 12.5
    union all select 4, 9.7
    union all select 5, 7.9
    union all select 6, 6.7
    union all select 7, 5.8
    union all select 8, 5.1
    union all select 9, 4.6
)
, cte_data
as
(
    select 
          left(cast(abs(amount) as varchar(max)), 1)    as 'first_digit'
        , cast(count(*) as float)                       as 'digit_count'
        , cast((select count(*) from dbo.num) as float) as 'total_count'
    from dbo.num
    group by left(cast(abs(amount) as varchar(max)), 1)
)
select 
    a.*
    , cast(b.digit_count / b.total_count * 100 as decimal(9, 1)) as 'data_distribution'
from cte_benford a
left join cte_data b        on a.first_digit = b.first_digit
order by a.first_digit
```
