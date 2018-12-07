# How to replace an identity column with a sequence object using SSDT?

You have a table that has an identity column. You've learnt about the new sequence object and would like to generate sequence numbers using the sequence object instead of the identity column. Most importantly, you want to make sure the existing sequence numbers are retained and the next value will just continue from the last value. 

This post assumes that you are already managing database deployments using SSDT and publish profiles.

Let's use the table below for example:

```sql
create table dbo.application
(
    application_id int not null identity(1, 1)
)

insert dbo.application default values
go 100
```

**1. Remove the identity property from the table script.**

```sql
create table dbo.application
(
    application_id int not null
)
```

**2. Add the sequence object into the project.**

```sql
create sequence dbo.application_id_sequence
as int
start with 1
increment by 1;
```

**3. Find a way to set the next value**

When I was doing this for the first time, I naturally looked for a way to set the next value like how you would do it for the identity column using `dbcc checkident('dbo.application', reseed, 100)`, the only option I could find for the new sequence object was `alter sequence dbo.application_id_sequence with restart 100`.

I didn't realise at the time that the reset behaviour between identity and sequence is not the same. 

|Reset Command|Start Value|Current Value|Next Value|
|:--|:--|:--|:--|
|dbcc checkident('dbo.application', reseed, 100)|1|100|101|
|alter sequence dbo.application_id_sequence with restart 100|100|100|101|

What's not obvious is that by issuing `alter sequence with restart`, operations in the database will continue to work and sequence numbers will continue to go up UNTIL the next deployment. 

At the time of the deployment, SSDT will look at the model and the database and go, oh, the sequence object definition is different, let me generate a change script to match the model.

|Source|Definition|Change Script|
|:--|:--|:--|
|Model|start with 1||
|Database|start with 100|alter sequence dbo.application_id_sequence restart with 1|

See more discussions on the topic [here](https://feedback.azure.com/forums/908035-sql-server/suggestions/32897776-sequence-gets-reset-on-publish-from-ssdt-db-projec).

Ok, so who do we do? There are a few workarounds available for this problem, I've decided to go for the **fast forward** approach as it was easy to setup:

```sql
declare @max_application_id int = (select isnull(max(application_id), 0) from dbo.application)
declare @current_value int = (select cast(current_value as int) from sys.sequences where name = 'application_id_sequence')

while @current_value < @max_application_id
begin
    select next value for dbo.application_id_sequence
    set @current_value = (select cast(current_value as int) from sys.sequences where name = 'application_id_sequence')
end
```
After running the script above, `select start_value, current_value from sys.sequences where name = 'application_id_sequence'` should show:

|start_value|current_value|
|:--|:--|
|1|100|

This means SSDT will now leave the sequence object alone. 

**4. Add the fast forward script inside the PostDeployment script.**

Instead of manually executing the fast forward script, we can simply add it to the project and execute it in the PostDeployment script.

```
:r Scripts\fast_forward_sequence_number.sql
```

**5. Tick "Do not drop sequences" in the publish profile.**

This is a BONUS tip. Accidental removal of sequence objects is definitely not a pleasant surprise during a deployment, therefore I opted for manual removal if required.
