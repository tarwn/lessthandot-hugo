---
title: Concat function in SQL Server Denali CTP3
author: SQLDenis
type: post
date: 2011-07-15T07:53:00+00:00
ID: 1254
excerpt: |
  SQL Server Denali CTP3 brings a couple of new functions, one of these is the CONCAT function. The CONCAT  function returns a string that is the result of concatenating two or more string values.
  
  The syntax of the CONCAT function looks like this
  
  CO&hellip;
url: /index.php/datamgmt/datadesign/concat-function-in-sql-server/
views:
  - 16685
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - concat
  - denali
  - functions
  - sql server

---
SQL Server Denali CTP3 brings a couple of new functions, one of these is the CONCAT function. The CONCAT function returns a string that is the result of concatenating two or more string values.

The syntax of the CONCAT function looks like this

<pre>CONCAT ( string_value1, string_value2 [, string_valueN ] )</pre>

You can concatenate between 2 and 254 values, if you use for example only one value, you will get an error

sql
select CONCAT (1)
```

Msg 189, Level 15, State 1, Line 1
  
The concat function requires 2 to 254 arguments.

Here is some additional information

_CONCAT takes a variable number of string arguments and concatenates them into a single string. It requires a minimum of two input values; otherwise, an error is raised. All arguments are implicitly converted to string types and then concatenated. Null values are implicitly converted to an empty string. If all the arguments are null, then an empty string of type varchar(1) is returned. The implicit conversion to strings follows the existing rules for data type conversions_

Let's run some code and do some comparison with a regular string concatenation by using the @val + @val2

sql
declare @i char(1)  ='1'
declare @i3 char(1)  ='3'


select CONCAT (@i,@i3)
select @i+  @i3
```

&#8212;&#8212;
  
13
  
13

As you can see both of these return the value 13

What happens if one of the data type is an integer?

sql
declare @i char(1)  ='1'
declare @i3 int  ='3'


select CONCAT(@i,@i3)
select @i+  @i3
```

&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
13
  
4

As you can see CONCAT concatenates the values while the other method does arithmetic and adds the values since one of them is an integer

Here is another example that does the same

sql
declare @i char(1)  ='1'
declare @i2 int  =2
declare @i3 char(1)  ='3'


select CONCAT(@i,@i2,@i3)

select @i+ @i2+ @i3
```

&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
123
  
6

What happens if one of the values is NULL?

sql
declare @i char(1)  ='1'
declare @i2 int  =null
declare @i3 char(1)  ='3'


select CONCAT(@i,@i2,@i3)

select @i+ @i2+ @i3
```

&#8212;&#8212;&#8212;&#8212;&#8211;
  
13
  
null

As you can see the CONCAT functions makes the NULL an empty string while the other method does not.

In order to get the same output, the old method is a lot more code

sql
declare @i char(1)  ='1'
declare @i2 int  =null
declare @i3 char(1)  ='3'


select CONCAT(@i,@i2,@i3)


select isnull(@i,'')+ isnull(convert(varchar(10),@i2),'')+ isnull(@i3,'')
```

&#8212;&#8211;
  
13
  
13

So what is your opinion, are you happy that the CONCAT function has been added to SQL Server?