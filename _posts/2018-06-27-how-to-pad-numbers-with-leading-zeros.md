# How to pad numbers with leading zeros

It is common to see single digit days and months padded with leading zeros. There is a dumb way and a "thinking outside the box" way to solve this.

## Dumb Way

```sql
select iif(day('2009-07-01') < 10, '0', '') + cast(day('2009-07-01') as varchar)
```

## "Thinking Outside The Box" Way

We pad enough zeros to the number (to go outside the box) and then take as many digits as we'd like (to get back inside the box).

```sql
select right('0' + cast(day('2009-07-01') as varchar), 2) -- e.g. select right('01', 2)
select right('0' + cast(day('2009-10-19') as varchar), 2) -- e.g. select right('019', 2)
```
