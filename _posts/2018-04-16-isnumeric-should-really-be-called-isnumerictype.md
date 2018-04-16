# isnumeric() should really be called isnumerictype()

**EDIT:** This is an old post but still relevant today. 

isnumeric() is such a misleading name, the official documentation is filled with warning signs on its usage, for example:

> ISNUMERIC returns 1 for some characters that are not numbers, such as plus (+), minus (-), and valid currency symbols such as the dollar sign ($).

So what other characters are considered isnumeric? I have built two tables to find out:

Run the script below and inspect the output:

```sql
if object_id('is_numeric') is not null
    drop table is_numeric

create table is_numeric
(
      ascii         int not null primary key
    , character     as char(ascii)  
    , description   sysname not null
    , is_numeric    as isnumeric(char(ascii))   
)

insert dbo.is_numeric (ascii, description)
          select 9,   'tab'
union all select 10,  'new line'
union all select 13,  'carriage return'
union all select 32,  'space'
union all select 33,  'exclamation mark'
union all select 34,  'double quote'
union all select 35,  'hash'
union all select 36,  'dollar'
union all select 37,  'percent'
union all select 38,  'ampersand'
union all select 39,  'single quote'
union all select 40,  'open bracket'
union all select 41,  'close bracket'
union all select 42,  'multiply/star'
union all select 43,  'plus'
union all select 44,  'comma'
union all select 45,  'minus/dash'
union all select 46,  'dot'
union all select 47,  'forward slash'
union all select 48,  '0'
union all select 49,  '1'
union all select 50,  '2'
union all select 51,  '3'
union all select 52,  '4'
union all select 53,  '5'
union all select 54,  '6'
union all select 55,  '7'
union all select 56,  '8'
union all select 57,  '9'
union all select 58,  'colon'
union all select 59,  'semicolon'
union all select 60,  'less than'
union all select 61,  'equal'
union all select 62,  'greater than'
union all select 63,  'question mark'
union all select 64,  'at'
union all select 65,  'A'
union all select 66,  'B'
union all select 67,  'C'
union all select 68,  'D'
union all select 69,  'E'
union all select 70,  'F'
union all select 71,  'G'
union all select 72,  'H'
union all select 73,  'I'
union all select 74,  'J'
union all select 75,  'K'
union all select 76,  'L'
union all select 77,  'M'
union all select 78,  'N'
union all select 79,  'O'
union all select 80,  'P'
union all select 81,  'Q'
union all select 82,  'R'
union all select 83,  'S'
union all select 84,  'T'
union all select 85,  'U'
union all select 86,  'V'
union all select 87,  'W'
union all select 88,  'X'
union all select 89,  'Y'
union all select 90,  'Z'
union all select 91,  'open square bracket'
union all select 92,  'backslash'
union all select 93,  'close square bracket'
union all select 94,  'power'
union all select 95,  'underscore'
union all select 96,  'apostrophe'
union all select 97,  'a'
union all select 98,  'b'
union all select 99,  'c'
union all select 100, 'd'
union all select 101, 'e'
union all select 102, 'f'
union all select 103, 'g'
union all select 104, 'h'
union all select 105, 'i'
union all select 106, 'j'
union all select 107, 'k'
union all select 108, 'l'
union all select 109, 'm'
union all select 110, 'n'
union all select 111, 'o'
union all select 112, 'p'
union all select 113, 'q'
union all select 114, 'r'
union all select 115, 's'
union all select 116, 't'
union all select 117, 'u'
union all select 118, 'v'
union all select 119, 'w'
union all select 120, 'x'
union all select 121, 'y'
union all select 122, 'z'
union all select 123, 'open brace'
union all select 124, 'vertical bar/pipe'
union all select 125, 'close brace'
union all select 126, 'tilde'

select * from dbo.is_numeric
```

| ascii | character | description | isnumeric |
| :---: | :-------: | :---------- | :--------: |
|9|	|tab|**1**|
|10||new line|**1**|
|13||carriage return|**1**|
|32| |space|0|
|33|!|exclamation mark|0|
|34|"|double quote|0|
|35|#|hash|0|
|36|$|dollar|**1**|
|37|%|percent|0|
|38|&|ampersand|0|
|39|'|single quote|0|
|40|(|open bracket|0|
|41|)|close bracket|0|
|42|*|multiply/star|0|
|43|+|plus|**1**|
|44|,|comma|**1**|
|45|-|minus/dash|**1**|
|46|.|dot|**1**|
|47|/|forward slash|0|
|48|0|0|**1**|
|49|1|1|**1**|
|50|2|2|**1**|
|51|3|3|**1**|
|52|4|4|**1**|
|53|5|5|**1**|
|54|6|6|**1**|
|55|7|7|**1**|
|56|8|8|**1**|
|57|9|9|**1**|
|58|:|colon|0|
|59|;|semicolon|0|
|60|<|less than|0|
|61|=|equal|0|
|62|>|greater than|0|
|63|?|question mark|0|
|64|@|at|0|
|65|A|A|0|
|66|B|B|0|
|67|C|C|0|
|68|D|D|0|
|69|E|E|0|
|70|F|F|0|
|71|G|G|0|
|72|H|H|0|
|73|I|I|0|
|74|J|J|0|
|75|K|K|0|
|76|L|L|0|
|77|M|M|0|
|78|N|N|0|
|79|O|O|0|
|80|P|P|0|
|81|Q|Q|0|
|82|R|R|0|
|83|S|S|0|
|84|T|T|0|
|85|U|U|0|
|86|V|V|0|
|87|W|W|0|
|88|X|X|0|
|89|Y|Y|0|
|90|Z|Z|0|
|91|[|open square bracket|0|
|92|\ |backslash|**1**|
|93|]|close square bracket|0|
|94|^|power|0|
|95|_|underscore|0|
|96|`|apostrophe|0|
|97|a|a|0|
|98|b|b|0|
|99|c|c|0|
|100|d|d|0|
|101|e|e|0|
|102|f|f|0|
|103|g|g|0|
|104|h|h|0|
|105|i|i|0|
|106|j|j|0|
|107|k|k|0|
|108|l|l|0|
|109|m|m|0|
|110|n|n|0|
|111|o|o|0|
|112|p|p|0|
|113|q|q|0|
|114|r|r|0|
|115|s|s|0|
|116|t|t|0|
|117|u|u|0|
|118|v|v|0|
|119|w|w|0|
|120|x|x|0|
|121|y|y|0|
|122|z|z|0|
|123|{|open brace|0|
|124|\||vertical bar/pipe|0|
|125|}|close brace|0|
|126|~|tilde|0|

```sql
if object_id('is_numeric_2') is not null
    drop table is_numeric_2

create table is_numeric_2
(
      a                nchar(1) default(1)
    , b                nchar(1)
    , c                nchar(1) default(2)

    , ab               as concat(a, b)
    , ab_is_numeric    as isnumeric(concat(a, b))

    , abc              as concat(a, b, c)
    , abc_is_numeric   as isnumeric(concat(a, b, c))    

    , bc               as concat(b, c)
    , bc_is_numeric    as isnumeric(concat(b, c))
)

insert dbo.is_numeric_2 (b)
select character
from dbo.is_numeric

select * from is_numeric_2
```

Now let's take a look at the effect after mixing characters and numbers:

| a | b | c | a+b | <- isnumeric | a+b+c | <- isnumeric | b+c | <- isnumeric |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
|1|(\t)|2|1(\t)|0|1(\t)2|0|(\t)2|**1**|
|1|(\n)|2|1|0|1(\n)2|0|(\n)2|**1**|
|1|(\r)|2|1(\r)|0|1(\r)2|0|(\r)2|**1**|
|1|(space)|2|1(space)|**1**|1(space)2|0|(space)2|**1**|
|1|!|2|1!|0|1!2|0|!2|0|
|1|"|2|1"|0|1"2|0|"2|0|
|1|#|2|1#|0|1#2|0|#2|0|
|1|$|2|1$|0|1$2|0|$2|**1**|
|1|%|2|1%|0|1%2|0|%2|0|
|1|&|2|1&|0|1&2|0|&2|0|
|1|'|2|1'|0|1'2|0|'2|0|
|1|(|2|1(|0|1(2|0|(2|0|
|1|)|2|1)|0|1)2|0|)2|0|
|1|*|2|1*|0|1*2|0|*2|0|
|1|+|2|1+|0|1+2|0|+2|**1**|
|1|,|2|1,|**1**|1,2|**1**|,2|**1**|
|1|-|2|1-|0|1-2|0|-2|**1**|
|1|.|2|1.|**1**|1.2|**1**|.2|**1**|
|1|/|2|1/|0|1/2|0|/2|0|
|1|0|2|10|**1**|102|**1**|02|**1**|
|1|1|2|11|**1**|112|**1**|12|**1**|
|1|2|2|12|**1**|122|**1**|22|**1**|
|1|3|2|13|**1**|132|**1**|32|**1**|
|1|4|2|14|**1**|142|**1**|42|**1**|
|1|5|2|15|**1**|152|**1**|52|**1**|
|1|6|2|16|**1**|162|**1**|62|**1**|
|1|7|2|17|**1**|172|**1**|72|**1**|
|1|8|2|18|**1**|182|**1**|82|**1**|
|1|9|2|19|**1**|192|**1**|92|**1**|
|1|:|2|1:|0|1:2|0|:2|0|
|1|;|2|1;|0|1;2|0|;2|0|
|1|<|2|1<|0|1<2|0|<2|0|
|1|=|2|1=|0|1=2|0|=2|0|
|1|>|2|1>|0|1>2|0|>2|0|
|1|?|2|1?|0|1?2|0|?2|0|
|1|@|2|1@|0|1@2|0|@2|0|
|1|A|2|1A|0|1A2|0|A2|0|
|1|B|2|1B|0|1B2|0|B2|0|
|1|C|2|1C|0|1C2|0|C2|0|
|1|D|2|1D|0|1D2|**1**|D2|0|
|1|E|2|1E|0|1E2|**1**|E2|0|
|1|F|2|1F|0|1F2|0|F2|0|
|1|G|2|1G|0|1G2|0|G2|0|
|1|H|2|1H|0|1H2|0|H2|0|
|1|I|2|1I|0|1I2|0|I2|0|
|1|J|2|1J|0|1J2|0|J2|0|
|1|K|2|1K|0|1K2|0|K2|0|
|1|L|2|1L|0|1L2|0|L2|0|
|1|M|2|1M|0|1M2|0|M2|0|
|1|N|2|1N|0|1N2|0|N2|0|
|1|O|2|1O|0|1O2|0|O2|0|
|1|P|2|1P|0|1P2|0|P2|0|
|1|Q|2|1Q|0|1Q2|0|Q2|0|
|1|R|2|1R|0|1R2|0|R2|0|
|1|S|2|1S|0|1S2|0|S2|0|
|1|T|2|1T|0|1T2|0|T2|0|
|1|U|2|1U|0|1U2|0|U2|0|
|1|V|2|1V|0|1V2|0|V2|0|
|1|W|2|1W|0|1W2|0|W2|0|
|1|X|2|1X|0|1X2|0|X2|0|
|1|Y|2|1Y|0|1Y2|0|Y2|0|
|1|Z|2|1Z|0|1Z2|0|Z2|0|
|1|[|2|1[|0|1[2|0|[2|0|
|1|\ |2|1\ |0|1\2|0|\2|**1**|
|1|]|2|1]|0|1]2|0|]2|0|
|1|^|2|1^|0|1^2|0|^2|0|
|1|_|2|1_|0|1_2|0|_2|0|
|1|`|2|1`|0|1`2|0|`2|0|
|1|a|2|1a|0|1a2|0|a2|0|
|1|b|2|1b|0|1b2|0|b2|0|
|1|c|2|1c|0|1c2|0|c2|0|
|1|d|2|1d|0|1d2|**1**|d2|0|
|1|e|2|1e|0|1e2|**1**|e2|0|
|1|f|2|1f|0|1f2|0|f2|0|
|1|g|2|1g|0|1g2|0|g2|0|
|1|h|2|1h|0|1h2|0|h2|0|
|1|i|2|1i|0|1i2|0|i2|0|
|1|j|2|1j|0|1j2|0|j2|0|
|1|k|2|1k|0|1k2|0|k2|0|
|1|l|2|1l|0|1l2|0|l2|0|
|1|m|2|1m|0|1m2|0|m2|0|
|1|n|2|1n|0|1n2|0|n2|0|
|1|o|2|1o|0|1o2|0|o2|0|
|1|p|2|1p|0|1p2|0|p2|0|
|1|q|2|1q|0|1q2|0|q2|0|
|1|r|2|1r|0|1r2|0|r2|0|
|1|s|2|1s|0|1s2|0|s2|0|
|1|t|2|1t|0|1t2|0|t2|0|
|1|u|2|1u|0|1u2|0|u2|0|
|1|v|2|1v|0|1v2|0|v2|0|
|1|w|2|1w|0|1w2|0|w2|0|
|1|x|2|1x|0|1x2|0|x2|0|
|1|y|2|1y|0|1y2|0|y2|0|
|1|z|2|1z|0|1z2|0|z2|0|
|1|{|2|1{|0|1{2|0|{2|0|
|1|\||2|1\||0|1\|2|0|\|2|0|
|1|}|2|1}|0|1}2|0|}2|0|
|1|~|2|1~|0|1~2|0|~2|0|
