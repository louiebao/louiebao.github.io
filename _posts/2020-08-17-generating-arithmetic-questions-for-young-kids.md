### Generating arithmetic questions for young kids

My kids are in primary school and in order to keep them occupied and stay away from the computer screens, each week I will generate a few pages of random maths questions for them to practise their arithmetic skills. I wanted to make this process as automated as possible so I don't need to spend time each week coming up with random questions. Below is an example how I achieved it using a bit of SQL and PowerShell.

**Step 1 - Setup**
First I generated a pool of questions that contains all possible arithmetic (+-*/) combinations between 1 and 2048.

```sql
use louie

drop table if exists dbo.number 
create table dbo.number 
(
    n int not null primary key
); 
 
with
  l0   as (select 1 as c union all select 1 as o)         -- 2 rows
, l1   as (select 1 as c from l0 as a cross join l0 as b) -- 4 rows
, l2   as (select 1 as c from l1 as a cross join l1 as b) -- 16 rows
, l3   as (select 1 as c from l2 as a cross join l2 as b) -- 256 rows
, l4   as (select 1 as c from l3 as a cross join l3 as b) -- 65,536 rows
, l5   as (select 1 as c from l4 as a cross join l4 as b) -- 4,294,967,296 rows
, nums as (select row_number() over(order by (select null)) as n from l5)
 
insert into dbo.number select top 2048 n from nums order by n;

drop table if exists dbo.maths_question
create table dbo.maths_question
(
      operator  nchar(1)       not null
    , num1      decimal(9, 0)  not null
    , num2      decimal(9, 0)  not null
    , question  as concat(num1
                        , case len(num1) 
                            when 1 then space(6)
                            when 2 then space(5)
                            when 3 then space(4)
                            when 4 then space(3)
                        end
                        , operator
                        , space(4)
                        , num2
                        , case len(num2) 
                            when 1 then space(6)
                            when 2 then space(5)
                            when 3 then space(4)
                            when 4 then space(3)
                        end
                        , '=')
)

truncate table dbo.maths_question

insert dbo.maths_question(operator, num1, num2)
          select '+', n1.n, n2.n	from number n1	cross join number n2
union all select '-', n1.n, n2.n	from number n1	cross join number n2
union all select '*', n1.n, n2.n	from number n1	cross join number n2
union all select '/', n1.n, n2.n	from number n1	cross join number n2 where n1.n % n2.n = 0 -- Don't want any fractions.
```

**Step 2 - Generate questions for the week**
Each week I will generate a list of random questions for each child tailored to their skill levels.

```sql
use louie

declare @output table
(
    question nvarchar(max)
)

insert @output
select top 5 question
from dbo.maths_question
where operator = '+'
and num1 > 10
and num2 > 10
order by checksum(newid())

insert @output
select top 5 question
from dbo.maths_question
where operator = '+'
and num1 = num2
and num2 in (16, 32, 64, 128, 256, 512, 1024, 2048)
order by checksum(newid())

insert @output
select top 10 question
from dbo.maths_question
where operator = '-'
and num1 != num2
and num1 > 10
and num2 > 10
order by checksum(newid())

insert @output
select top 16 question
from dbo.maths_question
where operator = '*'
and num1 between 2 and 9
and num2 between 2 and 9
order by checksum(newid())

insert @output
select top 5 question
from dbo.maths_question
where operator = '*'
and num1 > 10
and num2 = 11
order by checksum(newid())

insert @output
select top 19 question
from dbo.maths_question
where operator = '/'
and num1 / num2 < 10
and num2 between 2 and 9
and num1 != num2
order by checksum(newid())

select * from @output order by checksum(newid())
```

**Step 3 - Print out the questions**
I didn't want to manually copy the questions and print the output, so I automated this step as well.

```powershell
Param
(
      [string]$child
    , [int]$sheet = 3
)

$count = 1

while ($count -le $sheet)
{
    Push-Location
    $rs = Invoke-SqlCmd -ServerInstance louie_server -InputFile "c:\dev\maths\$($child).sql"
    Pop-Location

    $word = New-Object -ComObject word.application
    $word.Visible = $false
    $doc = $word.documents.add()

    $selection = $word.Selection
    $Selection.Font.Name = "Courier"
    $Selection.Font.Size = 11

    $selection.TypeText($child)
    $selection.TypeParagraph()

    foreach ($r in $rs)
    {
        $selection.TypeText($r.question)
        $selection.TypeParagraph()
    }

    Write-Host "Printing maths questions for $child ..." -ForegroundColor Yellow

    $doc.PrintOut()
    $doc.Close($false)
    $word.Quit()

    $count++
}
```

This is a generator for very simple arithmetic questions and can be extended to cover a lot more questions in the future.
