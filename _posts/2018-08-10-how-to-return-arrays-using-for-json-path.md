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

### 1. {Name: [Value,Value,Value]}

### 2. [Value,Value,Value]

### 3. {Name: Value, Name: [Value,Value,Value]}
