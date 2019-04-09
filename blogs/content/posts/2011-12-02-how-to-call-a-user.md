---
title: How to call a user defined function with a default parameter
author: SQLDenis
type: post
date: 2011-12-02T13:15:00+00:00
ID: 1405
excerpt: |
  Someone had some trouble earlier today with calling a user defined function that has a default value for a parameter
  
  When you have a stored procedure with default values for parameters, you can omit those when calling the proc. With user defined func&hellip;
url: /index.php/datamgmt/datadesign/how-to-call-a-user/
views:
  - 28691
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - function
  - gotcha
  - sql server 2005
  - sql server 2008
  - tip

---
Someone had some trouble earlier today with [calling a user defined function that has a default value for a parameter][1]

When you have a stored procedure with default values for parameters, you can omit those when calling the proc. With user defined functions, it works a little different, let's take a look.

First create this simple function

sql
CREATE FUNCTION dbo.fnTest(@param1 INT, @param2 int = 1 )
RETURNS int
AS
BEGIN
    
        RETURN @param2
    
END
GO
```

As you can see @param2 has a default of 1.

Calling the function by supplying both parameters works as expected

sql
SELECT dbo.fnTest(  23,3 )
```

Output
  
&#8212;&#8212;&#8212;&#8212;-
  
3

Now try to do this

sql
SELECT dbo.fnTest(  23 )
```

Here is the error message that we get back

_Msg 313, Level 16, State 2, Line 1
  
An insufficient number of arguments were supplied for the procedure or function dbo.fnTest._

If you look in books on line: http://msdn.microsoft.com/en-us/library/ms186755.aspx
  
You will see the following text

> If a default value is defined, the function can be executed without specifying a value for that parameter.
> 
> When a parameter of the function has a default value, **the keyword DEFAULT must be specified** when the function is called in order to retrieve the default value. This behavior is different from using parameters with default values in stored procedures in which omitting the parameter also implies the default value. An exception to this behavior is when invoking a scalar function by using the EXECUTE statement. When using EXECUTE, the DEFAULT keyword is not required.

So, let's try that

sql
SELECT dbo.fnTest(  23, default )
```

Output
  
&#8212;&#8212;&#8212;&#8212;-
  
1

There you have it, works like a charm

 [1]: http://stackoverflow.com/questions/8358315/tsql-fuction-with-default-parameters