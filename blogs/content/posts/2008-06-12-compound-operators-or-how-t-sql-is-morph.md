---
title: 'Compound Operators Or How T-SQL Is Morphing Into VB Or C#'
author: SQLDenis
type: post
date: 2008-06-12T13:19:21+00:00
ID: 58
url: /index.php/datamgmt/datadesign/compound-operators-or-how-t-sql-is-morph/
views:
  - 11859
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
C# is becoming more dynamic (like Python and Ruby) but now SQL is becoming more like C# (but with LINQ C# is becoming more like SQL)
  
Confused? Good!

SQL Server has added compound operators. Instead of writing

```sql
SET @value = @value + 100
```

you can just do

```sql
SET @value += 100
```

See that? I can see a lot of people writing Dynamic SQL already smiling. Really who wants to write code like this whole day long?

```sql
SET @MyBigDynamicSQLString = MyBigDynamicSQLString + ' From ' + @Table
```

This is much shorter (but not better IMNSHO)

```sql
SET @MyBigDynamicSQLString +=  ' From ' + @Table
```

Anyway here are all the compound operators that you can use:

+= (Add EQUALS)
  
Adds some amount to the original value and sets the original value to the result.

**-= (Subtract EQUALS)**
  
Subtracts some amount from the original value and sets the original value to the result.

***= (Multiply EQUALS)** 
  
Multiplies by an amount and sets the original value to the result.

**/= (Divide EQUALS)** 
  
Divides by an amount and sets the original value to the result.

**%= (Modulo EQUALS)** 
  
Divides by an amount and sets the original value to the modulo.

**&= (Bitwise AND EQUALS)** 
  
Performs a bitwise AND and sets the original value to the result.

**^= (Bitwise Exclusive OR EQUALS)**
  
Performs a bitwise exclusive OR and sets the original value to the result.

**|= (Bitwise OR EQUALS)** 
  
Performs a bitwise OR and sets the original value to the result.

Have fun reconstructing your strings 🙂

Here is another example where SQL is morphing

```sql
DECLARE @find varchar(30); 
SET @find = 'Man%';
```

In SQL Server 2008 you can do

```sql
DECLARE @find varchar(30) = 'Man%'; 
```

Much nicer