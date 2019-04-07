---
title: SQL Server Types – Numeric vs Int
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-09-02T12:20:43+00:00
excerpt: As I was working on a database yesterday I came across a curious sight, multiple columns defined as numeric(7,0), numeric(9,0), and so on. It seemed like someone was trying to provide the database with the most specific definition possible for a number of different pieces of data. Having never run into this particular practice, I immediately started searching for a reason. Was it smaller? faster? better?
url: /index.php/datamgmt/datadesign/sql-server-types-numeric-vs-int/
views:
  - 111410
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - performance
  - schema design
  - sqlcop
  - tip

---
Generally when we are defining tables, the more specific the column definition the better. Yesterday, however, I ran into a case where better definition actually has increased storage use for no appreciable benefit.

## Integers &#8211; Using Numeric vs Int

As I was working on the database I came across a curious sight, multiple columns defined as numeric(7,0), numeric(9,0), and so on. It seemed like someone was trying to provide the database with the most specific definition possible for a number of different pieces of data. Having never run into this particular practice, I immediately started searching for a reason. Was it smaller? faster? better?

### Storage

Using a very specific, well-defined numeric has actually cost us storage space, not reduced it. A numeric (or decimal) with a precision value of 1 to 9 requires 5 bytes and with 10-19 requires 9 bytes. Compare this to the many varieties of int:

<div class="tables">
  <table>
    <tr>
      <th>
        Digits
      </th>
      
      <th>
        Int Variety
      </th>
      
      <th>
        Int Bytes
      </th>
      
      <th>
        Numeric(*,0) Bytes
      </th>
      
      <th>
        Difference
      </th>
    </tr>
    
    <tr>
      <td>
        1 &#8211; 2
      </td>
      
      <td>
        tinyint
      </td>
      
      <td>
        1
      </td>
      
      <td>
        5
      </td>
      
      <td>
        4 bytes
      </td>
    </tr>
    
    <tr>
      <td>
        3 &#8211; 4
      </td>
      
      <td>
        smallint
      </td>
      
      <td>
        2
      </td>
      
      <td>
        5
      </td>
      
      <td>
        3 bytes
      </td>
    </tr>
    
    <tr>
      <td>
        5 &#8211; 9
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        5
      </td>
      
      <td>
        1 byte
      </td>
    </tr>
    
    <tr>
      <td>
        10 &#8211; 18
      </td>
      
      <td>
        bigint
      </td>
      
      <td>
        8
      </td>
      
      <td>
        9
      </td>
      
      <td>
        1 byte
      </td>
    </tr>
  </table>
</div>

So for every row and every index that includes this value, we lose storage space.

References: [Int reference on MSDN][1] and [Numeric/Decimal reference on MSDN][2]

### Performance #1

When SQL Server is asked to execute a math function (+,-,*,/), it uses a defined set of rules to determine the output type, then implicitly converts the arguments to that type (see [this article][3] for a subset that relates to decimals). This means that in many cases there could be implicit conversions to numeric from int, so it&#8217;s possible someone believed we could try and tweak our performance by defining the field as numeric instead of an int. 

Let&#8217;s test out implicit conversions:

<pre>/* ****** Creation of some number tables ****** */
Create Table NumberIntTest(Num Int Identity(1,1) Primary Key)
go

Set NOCOUNT ON
Begin Tran
	Insert Into NumberIntTest Default Values
	Insert Into NumberIntTest Default Values
	Insert Into NumberIntTest Default Values
	Insert Into NumberIntTest Default Values
	Insert Into NumberIntTest Default Values
	Insert Into NumberIntTest Default Values
	Insert Into NumberIntTest Default Values
	Insert Into NumberIntTest Default Values
	Insert Into NumberIntTest Default Values
	Insert Into NumberIntTest Default Values
Commit Tran
go 100000

Create Table NumberNumericTest(Num numeric(7,0) Primary Key)
Go

Set NOCOUNT ON
Begin Tran
	Insert Into NumberNumericTest(Num) Select Num From NumberIntTest WHERE Num &gt; 10001
Commit Tran
Go

/* ****** Execute a variety of test scripts ****** */
DECLARE @Start DateTime;
DECLARE @Garbage int, @Junk numeric(7,0);

DECLARE @Int int; SET @Int = 1;
DECLARE @Num numeric(7,0); SET @Num = 1;

-- Divide an int by an int
SELECT @start = GETDATE();
SELECT @Garbage = Num/@Int FROM NumberIntTest n;
SELECT DateDiff(Millisecond, @Start, GetDate());

-- Divide a numeric(7,0) by an int
SELECT @start = GETDATE();
SELECT @Junk = Num/@Int FROM NumberNumericTest n;
SELECT DateDiff(Millisecond, @Start, GetDate());

-- Divide a numeric(7,0) by a numeric(7,0)
SELECT @start = GETDATE();
SELECT @Junk = Num/@Num FROM NumberNumericTest n;
SELECT DateDiff(Millisecond, @Start, GetDate());

-- Divide an int by an int w/ explicit casting to numeric
SELECT @start = GETDATE();
SELECT @Junk = Num/CAST(@Int as numeric(7,0)) FROM NumberIntTest n;
SELECT DateDiff(Millisecond, @Start, GetDate());

-- Divide an numeric(7,0) by an int w/ explicit casting
SELECT @start = GETDATE();
SELECT @Junk = Num/CAST(@Int as numeric(7,0)) FROM NumberNumericTest n;
SELECT DateDiff(Millisecond, @Start, GetDate());</pre>

Initially I compared the execution plans and didn&#8217;t see much difference, but after some modifications (thanks George!) and additions we can see the differences between a number of different situations.

Sample Results:

<div class="tables">
  <table>
    <tr>
      <th>
        Test
      </th>
      
      <th>
        time (ms)
      </th>
    </tr>
    
    <tr>
      <td>
        int/int &#8211; No Cast
      </td>
      
      <td>
        170
      </td>
    </tr>
    
    <tr>
      <td>
        numeric/int &#8211; Implicit Cast
      </td>
      
      <td>
        313
      </td>
    </tr>
    
    <tr>
      <td>
        numeric/numeric &#8211; Implicit Cast
      </td>
      
      <td>
        296
      </td>
    </tr>
    
    <tr>
      <td>
        int/CAST(int as numeric) &#8211; Implicit Cast
      </td>
      
      <td>
        320
      </td>
    </tr>
    
    <tr>
      <td>
        numeric/CAST(int as numeric)
      </td>
      
      <td>
        290
      </td>
    </tr>
  </table>
</div>

In the second test&#8217;s plan we can see an example of that implicit cast:

<pre>numeric/int w/ Implicit Cast:

  |--Compute Scalar(DEFINE:([Expr1002]=CONVERT_IMPLICIT(numeric(7,0),[utils].[dbo].[NumberNumericTest].[Num] as [n].[Num]/CONVERT_IMPLICIT(numeric(10,0),[@Int],0),0)))
       |--Clustered Index Scan(OBJECT:([utils].[dbo].[NumberNumericTest].[PK__NumberNu__C7D08B630AD2A005] AS [n]))</pre>

So if we did have an integer that we need to operate on with a float value (the last case), the addition of a simple cast on the integer argument will bring the execution performance in line with having two numerics, meaning there is no gain in storing the value as a numeric(7,0).

### Performance #2

The other potential performance impact is with auto-parameterization. Auto-parameterization occurs when you provide SQL Server with a non-parameterized SQL statement. The server determines a type for those parameters and parameterizes them (part of the magic that makes plans reusable for non-parameterized queries). I couldn&#8217;t find anything terribly recent, but as far back as SQL Server 6.5 and 7.0 the engine was documented as using the int type for any non-decimal value of 9 digits or less. This means that in the unlikely situation that you&#8217;re executing inline, non-parameterized SQL statements and have used numeric(\*,0) types in your table definitions, you will actually be taking a performance hit for the implicit conversion from auto-parameterized integer to the numeric(\*,0) field.

And if that wasn&#8217;t bad enough, the same SQL Server documentation says that SQL Server treats integers as more exact than numeric and decimal types. It doesn&#8217;t specify why the document goes out of its way to share this information with us, but generally when someone goes out of their way to point out something like this in a document, I get a little nervous and tend to focus more heavily on their &#8216;recommended&#8217; practice (use int).

More information on [Parameterization][4] and [SQL 7 Comparison Optimization][5]

## The Wrap-up

So at the end of the day, using a numeric(*,0) requires more space, provides no appreciable benefit over explicit casting, and can actually harm you if you are executing non-parameterized SQL statements against your server.

There are two options for finding these columns, using a SQL query like the one below or [downloading SQLCop][6] to check for this and many other common situations.

<pre>SELECT TABLE_CATALOG, TABLE_SCHEMA, TABLE_NAME, COLUMN_NAME, DATA_TYPE, NUMERIC_PRECISION, NUMERIC_SCALE
FROM INFORMATION_SCHEMA.COLUMNS C
WHERE C.DATA_TYPE IN ('numeric','decimal') AND NUMERIC_SCALE = 0 AND NUMERIC_PRECISION <= 18</pre>

 [1]: http://msdn.microsoft.com/en-us/library/ms187745.aspx "Int reference on MSDN"
 [2]: http://msdn.microsoft.com/en-us/library/ms187746.aspx "Numeric/Decimal reference on MSDN"
 [3]: http://msdn.microsoft.com/en-us/library/ms190476.aspx "Precision, Scale, and Length article on MSDN"
 [4]: http://msdn.microsoft.com/en-us/library/ms186219.aspx "MSDN article on Simple Parameterization"
 [5]: http://support.microsoft.com/kb/198625 "KB article on SQL 7 Comparison Optimization"
 [6]: http://sqlcop.ltd.local/ "Download the SQLCop application"