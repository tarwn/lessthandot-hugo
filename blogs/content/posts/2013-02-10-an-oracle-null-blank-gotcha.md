---
title: An Oracle NULL/Blank gotcha when coming from SQL Server
author: SQLDenis
type: post
date: 2013-02-10T19:43:00+00:00
ID: 1986
excerpt: |
  In my Differences between Oracle and SQL Server when working with NULL and blank values  post I already showed you how blanks and NULLS are handled differently between Oracle and SQL Server. Today I found another interesting tidbit.
  
  I you have a varc&hellip;
url: /index.php/datamgmt/dbprogramming/mssqlserver/an-oracle-null-blank-gotcha/
views:
  - 6516
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Oracle
tags:
  - gotcha
  - 'null'
  - oracle
  - sql server
  - tip

---
In my [Differences between Oracle and SQL Server when working with NULL and blank values][1] post I already showed you how blanks and NULLS are handled differently between Oracle and SQL Server. Today I found another interesting tidbit.

I you have a varchar or char datatype in SQL Server and you store a blank, you get back a blank or padded spaces.

sql
DECLARE @Test1 varchar(10) = ''
DECLARE @Test2 char(10) = ''

SELECT @Test1,@Test2
```

The output is one blank and ten spaces. 

You can verify this by using the `DATALENGTH` function .

sql
DECLARE @Test1 varchar(10) = ''
DECLARE @Test2 char(10) = ''

SELECT datalength(@Test1),datalength(@Test2)
```

Ouput

<pre>0     10</pre>

In Oracle...not the same

If you run this

```plsql
SET SERVEROUTPUT ON
 DECLARE 
    Test1 varchar(10):='';
    Test2 char(10):='';
BEGIN
  IF Test1 IS NULL 
  THEN
  DBMS_OUTPUT.PUT_LINE('Test1 is null');
  ELSE
  DBMS_OUTPUT.PUT_LINE('Test1 is NOT null');
  END IF;
  IF Test2 IS NULL 
  THEN
  DBMS_OUTPUT.PUT_LINE('Test2 is null');
  ELSE
  DBMS_OUTPUT.PUT_LINE('Test2 is NOT null');
  END IF;
END;
```

Output

<pre>anonymous block completed
Test1 is null
Test2 is NOT null</pre>

As you can see the varchar variable becomes NULL while the nchar variable gets padded

However when inserting into a table this becomes a little bit different with Oracle

Running this in SQL Server

sql
CREATE TABLE TestNull(Col1 CHAR(10),Col2 VARCHAR(10));
INSERT INTO TestNull VALUES(NULL,NULL);
INSERT INTO TestNull VALUES('','');

SELECT * FROM TestNull;
```

Output

<pre>Col1	Col2
NULL	NULL
          	</pre>

You get a row with NULLS and a row with blanks, same as with the variables

Running that in Oracle

```plsql
CREATE TABLE TestNull(Col1 CHAR(10),Col2 VARCHAR(10));
INSERT INTO TestNull VALUES(NULL,NULL);
INSERT INTO TestNull VALUES('','');

SELECT * FROM TestNull;
```

Here is what you get

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleOutput.PNG?mtime=1360532250"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleOutput.PNG?mtime=1360532250" width="290" height="229" /></a>
</div>

As you can see when inserting NULL or a blank into a char column into a table, it does NOT get padded like it did with the variable
  
If converting code between these two database systems be aware of these kind of things

 [1]: /index.php/DataMgmt/DBProgramming/Oracle/differences-between-oracle-and-sql