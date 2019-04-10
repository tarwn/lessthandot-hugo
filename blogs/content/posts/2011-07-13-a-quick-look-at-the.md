---
title: A Quick look at the new IIF function in Denali CTP3
author: SQLDenis
type: post
date: 2011-07-13T13:19:00+00:00
ID: 1249
excerpt: |
  Denali CTP3 comes with the IIF function, if you have used VB or Excel then you already know how this function works. In essence this function is a shorter version of a CASE statement. 
  
  The syntax is as follows
  
  IIF ( boolean_expression, true_value,&hellip;
url: /index.php/datamgmt/datadesign/a-quick-look-at-the/
views:
  - 6867
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - denali
  - functions
  - iif

---
Denali CTP3 comes with the IIF function, if you have used VB or Excel then you already know how this function works. In essence this function is a shorter version of a CASE statement. 

The syntax is as follows

<pre>IIF ( boolean_expression, true_value, false_value )</pre>

So instead of this

sql
SELECT CASE WHEN 1 = 2 THEN 'equal' ELSE 'not equal' END AS Comp
```

We can do this

sql
SELECT IIF(1=2,'equal','not equal') as Comp
```

Both of those will return not equal

Be aware that you can't use NULL like in the example below

sql
SELECT IIF(1=2,NULL ,NULL ) as calc
```

It throws an error (with a typo)

Msg 8133, Level 16, State 1, Line 1
  
At **lease** one of the result expressions in a CASE specification must be an expression other than the NULL constant.

If you use a variable then you can use NULL

sql
declare @i int = NULL 


SELECT IIF(1=2,@i,@i) as calc
```

Here is some more info from Books On Line

_IIF is a shorthand way for writing a CASE statement. It evaluates the Boolean expression passed as the first argument, and then returns either of the other two arguments based on the result of the evaluation. That is, the true\_value is returned if the Boolean expression is true, and the false\_value is returned if the Boolean expression is false or unknown. true\_value and false\_value can be of any type. The same rules that apply to the CASE statement for Boolean expressions, null handling, and return types also apply to IIF.</p> 

The fact that IIF is translated into CASE also has an impact on other aspects of the behavior of this function. Since CASE statements can nested only up to the level of 10, IIF statements can also be nested only up to the maximum level of 10. Also, IIF is remoted to other servers as a semantically equivalent CASE statement, with all the behaviors of a remoted CASE statement.</em>

Here is a nested (silly) example

sql
SELECT IIF(1=2,'equal',IIF(4=2,'equal','not equal')) as Comp
```

Here is another example that combines IIF with TRY_CONVERT to return if a value can be converted to a specific data type

sql
SELECT IIF(TRY_CONVERT(float,'bla')IS NULL,'Cast failed','Cast succeeded')
UNION
SELECT IIF(TRY_CONVERT(float,'1')IS NULL,'Cast failed','Cast succeeded')
```

——–
  
Cast failed
  
Cast succeeded

I welcome this function, anything that makes the code shorter is welcomed with open arms by me.