---
title: How to implement a typeof operator in SQL by using sql_variant_property
author: SQLDenis
type: post
date: 2009-01-16T17:25:06+00:00
ID: 286
url: /index.php/datamgmt/datadesign/how-to-implement-a-typeof-operator-in-sq/
views:
  - 10747
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
I posted a puzzle here [Prove that this is an integer][1] asking people to prove that the following code is indeed an int

```sql
DECLARE @d INT
SELECT @d = 500
```

or this the number 5 itself, how do you know that this is an integer?
  
Well this is pretty easy, you can sql\_variant\_property with the BaseType property. Run this code

```sql
IF cast(sql_variant_property(@d,'BaseType') as varchar(20)) = 'int'
PRINT 'yes'
ELSE
PRINT 'no'
```

As you can see yes is printed, the code below will return int for the number 5

```sql
select cast(sql_variant_property(5,'BaseType') as varchar(20))
```

what about scale and precision?
  
Here run this

```sql
select    
            cast(sql_variant_property(14400195639.123,'BaseType') as varchar(20)) + '(' + 
            cast(sql_variant_property(14400195639.123,'Precision') as varchar(10)) + ',' + 
            cast(sql_variant_property(14400195639.123,'Scale') as varchar(10)) + ')' 
```

Running that code will return numeric(14,3)

Now, why would you ever need this? Sometimes it is handy whene you have a table like this

```sql
declare table Foo(bar smallint ,col1 varchar(40), col2..........)
```

when you run a query like this

```sql
select * from Foo where bar = 3
```

you might get an index scan because 3 is not a smallint and you get a conversion. Here is a way to get around that

```sql
select * from Foo where bar = convert(smallint,3)
```



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewtopic.php?f=102&t=4235
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22