# How to format nested REPLACE statements

REPLACE() is one of my favourite T-SQL string functions. One of its main attractions is the ability to remove noise words from a large chunk of text. Most of the time though, you are likely to have a few word patterns that you want to remove, and this could make the code hard to read, see the example below:

````sql
select replace(replace(replace(message, 'Microsoft (R) SQL Server Execute Package Utility', ''), 'version 10.0.5500.0', ''), 'Code: 0xC0202009', '') from msdb.dbo.sysjobhistory where run_status = 0
````

It only takes three to four nested replace statements to make the whole select statement look like a wiggly snake, not to mention those hard to track commas and brackets.

Below is a much better way of writing nested REPLACE statements:

````sql
select replace(replace(replace(message
    , 'Microsoft (R) SQL Server Execute Package Utility', '')
    , 'version 10.0.5500.0', '')
    , 'Code: 0xC0202009', '')
from msdb.dbo.sysjobhistory
where run_status = 0
````

By utilising one line per REPLACE, it becomes clear what you are replacing.
