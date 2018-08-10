# How to return arrays using for json path?

We will use the test data below for this post:

````
create table dbo.country_city
(
      country sysname
    , city    sysname
)

insert dbo.country_city values ('Australia', 'Melbourne'), ('Australia', 'Sydney'), ('Australia', 'Brisbane')
````

### 1. {"Name":"Value"},{"Name":"Value"},{"Name":"Value"}

This json format is the easiest to produce yet it consumes a lot more bytes than necessary due to the repeating column names.

````
select city
from dbo.country_city
for json path, without_array_wrapper
````

Output: `{"city":"Melbourne"},{"city":"Sydney"},{"city":"Brisbane"}`

### 2. ["Value","Value","Value"]

I haven't been able to find a "for json" syntax that could produce the simple string array. In the end I had to rely on string_agg to manually craft the json string. 

````
select concat('[', string_agg(concat('"', city, '"'), ','), ']')
from dbo.country_city
````

Output: `["Melbourne","Sydney","Brisbane"]`

### 3. {"Name":["Value","Value","Value"]}

This is a slight variation of the one above.

````
select json_query(concat('[', string_agg(concat('"', city, '"'), ','), ']'), '$') as 'cities'
from dbo.country_city
for json path, without_array_wrapper
````

Output: `{"cities":["Melbourne","Sydney","Brisbane"]}`

### 4. {"Name":"Value", "Name":"["Value","Value","Value"]}

Again, building on top of the one above to produce a nested array.

````
select country, json_query(concat('[', string_agg(concat('"', city, '"'), ','), ']'), '$') as 'cities'
from dbo.country_city
group by country
for json path, without_array_wrapper
````

Output: `{"country":"Australia","cities":["Melbourne","Sydney","Brisbane"]}`
