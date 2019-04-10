---
title: TRY_CONVERT in SQL Server Denali CTP3
author: SQLDenis
type: post
date: 2011-07-19T22:54:00+00:00
ID: 1257
excerpt: 'TRY_CONVERT  is a new function in SQL Server Denali CTP3, TRY_CONVERT   enables you to test if a value can be converted to a specific data type, TRY_CONVERT  returns a value cast to the specified data type if the cast succeeds; otherwise, TRY_CONVERT&hellip;'
url: /index.php/datamgmt/datadesign/try_convert-in-sql-server-denali/
views:
  - 7936
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
  - sql server
  - try_convert

---
TRY\_CONVERT is a new function in SQL Server Denali CTP3, TRY\_CONVERT enables you to test if a value can be converted to a specific data type, TRY\_CONVERT returns a value cast to the specified data type if the cast succeeds; otherwise, TRY\_CONVERT returns null.

Here is what Books On Line has to say about TRY_CONVERT

_TRY\_CONVERT takes the value passed to it and tries to convert it to the specified data\_type. If the cast succeeds, TRY\_CONVERT returns the value as the specified data\_type; if an error occurs, null is returned. However if you request a conversion that is explicitly not permitted, then TRY_CONVERT fails with an error.</p> 

**Arguments**

**data_type [ ( length ) ]**
  
The data type into which to cast expression.

**expression**
  
The value to be cast.

**style**
  
Optional integer expression that specifies how the TRY_CONVERT function is to translate expression.

style accepts the same values as the style parameter of the CONVERT function. 

The range of acceptable values is determined by the value of data\_type. If style is null, then TRY\_CONVERT returns null.</em>

Here is what the syntax looks like

<pre>TRY_CONVERT ( data_type [ ( length ) ], expression [, style ] )</pre>

Let's take a look how this all works, I will create a table and inserts some values

sql
create table #test(SomeCol varchar(100))
GO

insert #test values('1')
insert #test values('.')
insert #test values('$')
insert #test values('ddd')
insert #test values('---')
insert #test values('000')
insert #test values('123aaa1')
insert #test values('2de')
insert #test values('((((')
insert #test values('20110230')
insert #test values('20110228')
insert #test values('14:58')
insert #test values('16000228')
insert #test values('0.12345678901')
```

Now, I will try to convert the values in the table to various data types

sql
select SomeCol,
	   TRY_CONVERT(float,SomeCol) as float,
	   TRY_CONVERT(date,SomeCol) as date,
	   TRY_CONVERT(datetime2,SomeCol) as datetime2,
	   TRY_CONVERT(datetime,SomeCol) as datetime,
	   TRY_CONVERT(time,SomeCol) as time,
	   TRY_CONVERT(numeric(30,10),SomeCol) as numeric,
	   TRY_CONVERT(int,SomeCol) as int
FROm #test
```
Here is the result

<div class="tables">
  <table>
    <tr>
      <th>
        SomeCol
      </th>
      
      <th>
        float
      </th>
      
      <th>
        date
      </th>
      
      <th>
        datetime2
      </th>
      
      <th>
        datetime
      </th>
      
      <th>
        time
      </th>
      
      <th>
        numeric
      </th>
      
      <th>
        int
      </th>
      
      <tr>
        <tr>
          <td>
            1
          </td>
          
          <td>
            1
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            1
          </td>
          
          <td>
            1
          </td>
        </tr>
        
        <tr>
          <td>
            .
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
        </tr>
        
        <tr>
          <td>
            $
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
        </tr>
        
        <tr>
          <td>
            ddd
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
        </tr>
        
        <tr>
          <td>
            —
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
        </tr>
        
        <tr>
          <td>
            0
          </td>
          
          <td>
            0
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            0
          </td>
          
          <td>
            0
          </td>
        </tr>
        
        <tr>
          <td>
            123aaa1
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
        </tr>
        
        <tr>
          <td>
            2de
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
        </tr>
        
        <tr>
          <td>
            ((((
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
        </tr>
        
        <tr>
          <td>
            20110230
          </td>
          
          <td>
            20110230
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            20110230.0000000000
          </td>
          
          <td>
            20110230
          </td>
        </tr>
        
        <tr>
          <td>
            20110228
          </td>
          
          <td>
            20110228
          </td>
          
          <td>
            2011-02-28
          </td>
          
          <td>
            00:00.0
          </td>
          
          <td>
            00:00.0
          </td>
          
          <td>
            00:00.0
          </td>
          
          <td>
            20110228.0000000000
          </td>
          
          <td>
            20110228
          </td>
        </tr>
        
        <tr>
          <td>
            14:58
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            1900-01-01
          </td>
          
          <td>
            1900-01-01 14:58:00.0000000
          </td>
          
          <td>
            1900-01-01 14:58:00.000
          </td>
          
          <td>
            14:58:00.0000000
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
        </tr>
        
        <tr>
          <td>
            16000228
          </td>
          
          <td>
            16000228
          </td>
          
          <td>
            1600-02-28
          </td>
          
          <td>
            1600-02-28 00:00:00.0000000
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            00:00.0
          </td>
          
          <td>
            16000228.0000000000
          </td>
          
          <td>
            16000228
          </td>
        </tr>
        
        <tr>
          <td>
            0.12345678901
          </td>
          
          <td>
            0.12345678901
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            NULL
          </td>
          
          <td>
            0.123456789
          </td>
          
          <td>
            NULL
          </td>
        </tr>
      </tr>
    </tr>
  </table>
</div>

Pretty neat, as you can see if you for example try to convert 16000228 to a datetime you will get 0 since it falls out of the acceptable datetime range, for datetime2 and date you do get a value back. The conversion to numeric(30,10) also shows that the value is truncated after 10 decimals. This function is pretty handy since you won't get the conversion errors you would get if you try to convert it with the regular convert function

You can also use CASE or IIF to return if the value can or cannot be converted, below is an example of both

**CASE**

sql
SELECT 
    CASE WHEN TRY_CONVERT(float,'bla') IS NULL 
    THEN 'Cast failed'
    ELSE 'Cast succeeded'
END 
UNION
SELECT 
    CASE WHEN TRY_CONVERT(float,'1') IS NULL 
    THEN 'Cast failed'
    ELSE 'Cast succeeded'
END 
```

——–
  
Cast failed
  
Cast succeeded

**IIF**

sql
SELECT IIF(TRY_CONVERT(float,'bla')IS NULL,'Cast failed','Cast succeeded')
UNION
SELECT IIF(TRY_CONVERT(float,'1')IS NULL,'Cast failed','Cast succeeded')
```

——–
  
Cast failed
  
Cast succeeded

Be aware that if you pass in a NULL, then NULL is returned

sql
select  TRY_CONVERT( numeric(30,10),null)
```



TRY_CONVERT is something that was long overdue and it will eliminate a lot of issues, no more need for custom IsNumeric and IsInt functions, this one does it all.