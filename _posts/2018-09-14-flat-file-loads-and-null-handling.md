# Flat file loads and null handling

There are two null settings in the a typical file load Data Flow task in a ssis package and I am always confused about the effects they have on the load.

**Flat File Source Editor - Retain null values from the source as null values in the data flow** Specify whether to keep null values when data is extracted. The default value of this property is false. When this value is false, the Flat File source replaces null values from the source data with appropriate default values for each column, such as empty strings for string columns and zero for numeric columns.

**Bulk Insert - Keep nulls** Specify whether the task should keep null values. The default value of this property is False.

I have setup a quick test to show how the different settings can affect the load. 

![Flat File Load]({{ site.url }}/img/flat_file_load.png)

The csv file looks like the one below, note the value for Tasmania is zero and I have intentionally left the Brisbane value as null/missing 

```
Melbourne,5
Sydney,10
Brisbane,
Tasmania,0
```

### Scenario 1

```sql
create table dbo.average_winter_temperature
(
      city        varchar(128)  not null
    , temperature int               null
)
```

|Combo|Flat File|Bulk Insert|Brisbane|
|:-:|:--|:--|:-:|
|1|Retain null values from the source as null values in the data flow - Yes|Keep nulls - Yes|null|
|2|Retain null values from the source as null values in the data flow - Yes|Keep nulls - No|null|
|3|Retain null values from the source as null values in the data flow - No|Keep nulls - Yes|0|
|4|Retain null values from the source as null values in the data flow - No|Keep nulls - No|0|

### Scenario 2

```sql
create table dbo.average_winter_temperature
(
      city        varchar(128)  not null
    , temperature int           not null
)
```

|Combo|Flat File|Bulk Insert|Brisbane|
|:-:|:--|:--|:-:|
|1|Retain null values from the source as null values in the data flow - Yes|Keep nulls - Yes|Error|
|2|Retain null values from the source as null values in the data flow - Yes|Keep nulls - No|Error|
|3|Retain null values from the source as null values in the data flow - No|Keep nulls - Yes|0|
|4|Retain null values from the source as null values in the data flow - No|Keep nulls - No|0|

### Scenario 3

```sql
create table dbo.average_winter_temperature
(
      city        varchar(128)  not null
    , temperature int           not null default(-1)
)
```

|Combo|Flat File|Bulk Insert|Brisbane|
|:-:|:--|:--|:-:|
|1|Retain null values from the source as null values in the data flow - Yes|Keep nulls - Yes|Error|
|2|Retain null values from the source as null values in the data flow - Yes|Keep nulls - No|-1|
|3|Retain null values from the source as null values in the data flow - No|Keep nulls - Yes|0|
|4|Retain null values from the source as null values in the data flow - No|Keep nulls - No|0|

### Observation

There is no such thing as a right or wrong setting as it all depends on your business logic in the data set. In this very example, it would be best to retain the null value for Brisbane all the way into the table. Remember that null does not automatically equal to zero as they could have different meanings in different contexts.

