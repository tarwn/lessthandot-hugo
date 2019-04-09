---
title: SQL Server Stored Procedure with nvarchar parameter
author: George Mastros (gmmastros)
type: post
date: 2009-01-31T17:44:29+00:00
ID: 309
url: /index.php/datamgmt/datadesign/sql-server-stored-procedure-with-nvarcha/
views:
  - 30639
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Most seasoned sql server programmers know this already, but for you beginners…. When you create a stored procedure with a &#8216;string' type of parameter (char, nchar, varchar, nvarchar), it is critically important that you specify the maximum size for this string. When you have a string type of parameter and do NOT specify the size, sql server will default to 1 character.

I really wish the T-SQL parser would error when trying to use a string type without specifying the size. This would prevent a lot of problems, especially for the beginners.

Anyway… you can test this for yourself by create a stored procedure with an nvarchar parameter without specifying the size. Like this:

sql
Create Procedure TestParameterLength
	@Data nvarchar
As
Select @Data As Data
```
Clearly, the stored procedure is rather useless, but you might think that it will return whatever string is passed to it. Not so. Since the @Data parameter does not have a size, it defaults to 1 character and will truncate the data that is passed to it. You can test this by calling the stored procedure like this:

sql
exec TestParameterLength N'Hello World'
```
The output you get will be:

<pre>Data
----
H

(1 row(s) affected)
</pre>

The same problem occurs when you declare a variable within a stored procedure. It defaults to 1 character. However, this is inconsistent with the convert function. If you convert to nvarchar without specifying the size, it defaults to 30 characters. Take a look at the following stored procedure to see what I mean:

sql
Create Procedure TestVariableLength
	@Data nvarchar(100)
As

Declare @LocalVariable nvarchar
Declare @AnotherVariable nvarchar(100)

Set @LocalVariable = @Data
Set @AnotherVariable = Convert(nvarchar, @Data)

Select @Data As Data, 
       @LocalVariable as LocalVariable, 
       @AnotherVariable As AnotherVariable
```
Notice that I am now specifying the size of the parameter as 100 characters. I declare a local variable without specifying the size, and another variable that matches the size of the parameter (100 characters).

When I set @LocalVariable = @Data, it is truncating it to 1 character. 

Clearly @AnotherVariable is large enough to store anything in @Data, but since I convert it to nvarchar (eventhough it is already nvarchar), it will get truncated to 30 characters, because that is the default for convert.

You can test it like this:

sql
exec TestVariableLength N'ABCDEFGHIJKLMNOPQRSTUVWXYZ123456789'
```
The output:

<pre>Data                                 LocalVariable AnotherVariable
------ ----------------------------- ------------- ------------------------------
ABCDEFGHIJKLMNOPQRSTUVWXYZ123456789  A             ABCDEFGHIJKLMNOPQRSTUVWXYZ1234

(1 row(s) affected)
</pre>

I encourage every developer to get in the habit of ALWAYS specifying the length of your strings. This will prevent you from getting some hard to find bugs. You'll be glad you did.