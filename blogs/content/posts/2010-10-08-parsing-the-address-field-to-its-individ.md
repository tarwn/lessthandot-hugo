---
title: Parsing the Address field to its individual components
author: Naomi Nosonovsky
type: post
date: 2010-10-08T16:11:55+00:00
ID: 918
excerpt: |
  This blog post is a result of the currently active thread at MSDN T-SQL forum
  Parsing Address field to its components
  
  The question of parsing a row data with bad formatted addresses often comes in the SQL newsgroups. Unfortunately, there is no unive&hellip;
url: /index.php/datamgmt/datadesign/parsing-the-address-field-to-its-individ/
views:
  - 22894
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
This blog post is a result of the currently active thread at MSDN T-SQL forum
  
[Parsing Address field to its components][1]

The question of parsing a row data with bad formatted addresses often comes in the SQL newsgroups. Unfortunately, there is no universal 100% bullet-proof solution that covers all possible scenarios. However, for a common scenario of City, State Zip the parsing can be done using the following technique:

```sql
declare @t table (Address varchar(max))
insert into @t 
select 
'ROSSIVILLE, GA'
union all select
'LEESBURG, FL 34788'
union all select
'COLUMBUS, OH 43221'
union all select
'FORT BELVOIR, VA 22060'
union all select
'MADISON HGTS, MI'
union all select
'PALM BEACH, FL'
union all select
'MCDONOUGH .GA 30352'

select Address, F.City, F10.State, F4.Zip from @t 
cross apply (select charindex(', ', Address) AS CommaPos) F0
cross apply (select case when CommaPos = 0 then 'Bad Address' else SUBSTRING(Address,1,CommaPos - 1) end as City) F
cross apply (select case when CommaPos = 0 then NULL else ltrim(substring(Address,CommaPos+1, len(Address))) end as Rest) F2
cross apply (select PATINDEX('%[0-9]%',Rest) as DigitPos) F3
cross apply (select case when DigitPos > 0 then substring(Rest, DigitPos, len(Rest)) end as Zip) F4
cross apply (select case when Zip Is not NULL then replace(Rest,Zip,'') else Rest end as State) F10
```

Please, note, that I used CROSS APPLY technique to divide the parsing job into several related steps by first taking the City, then the rest of the string, then parsing the remainder.

I learned this CROSS APPLY technique from this excellent blog post by Brad Schulz
  
[Cool CROSS APPLY tricks – part 2][2] That's when this technique finally became very familiar for me. I want to tell here that I always enjoy reading Brad's blogs because of the humorous style of his writing.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/2b9c377c-e5a8-4de6-accf-9e5a12051ea9
 [2]: http://bradsruminations.blogspot.com/2009/07/cool-cross-apply-tricks-part-2.html
 [3]: http://forum.lessthandot.com/viewforum.php?f=17
 [4]: http://forum.lessthandot.com/viewforum.php?f=22