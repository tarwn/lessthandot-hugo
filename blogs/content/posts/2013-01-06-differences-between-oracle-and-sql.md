---
title: Differences between Oracle and SQL Server when working with NULL and blank values
author: SQLDenis
type: post
date: 2013-01-06T09:45:00+00:00
ID: 1899
excerpt: |
  If you ever have to start working with Oracle you have to keep in mind that NULLs and blank values don't work exactly the same as in SQL Server. Let's take a look at some examples
  
  Create this table and insert some rows
  create table TestNull(Col2 var&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/differences-between-oracle-and-sql/
views:
  - 11728
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - Oracle
  - Oracle Admin
tags:
  - gotcha
  - 'null'
  - oracle
  - sql server
  - tip

---
If you ever have to start working with Oracle you have to keep in mind that NULLs and blank values don't work exactly the same as in SQL Server. Let's take a look at some examples

Create this table and insert some rows

```plsql
create table TestNull(Col2 varchar(100));
insert into TestNull values(NULL);
insert into TestNull values('Bla');
insert into TestNull values('');
insert into TestNull values(' ');
```

As you can see we inserted four rows, one row is null, one row is blank, one row has a space and one row has Bla.

Now let's run the following query

```plsql
SELECT Col2,
  NVL(Col2,'EmptyOrNull') a,
  COALESCE(Col2,'EmptyOrNull') b,
  ascii(col2) c
FROM TestNull;
```

Here are the results in a html table

<div class="tables">
  <table border="1">
    <tr>
      <th>
        COL2
      </th>
      
      <th>
        A
      </th>
      
      <th>
        B
      </th>
      
      <th>
        C
      </th>
    </tr>
    
    <tr>
      <td>
        null
      </td>
      
      <td>
        EmptyOrNull
      </td>
      
      <td>
        EmptyOrNull
      </td>
      
      <td>
        null
      </td>
    </tr>
    
    <tr>
      <td>
        Bla
      </td>
      
      <td>
        Bla
      </td>
      
      <td>
        Bla
      </td>
      
      <td>
        66
      </td>
    </tr>
    
    <tr>
      <td>
        null
      </td>
      
      <td>
        EmptyOrNull
      </td>
      
      <td>
        EmptyOrNull
      </td>
      
      <td>
        null
      </td>
    </tr>
    
    <tr>
      <td>
      </td>
      
      <td>
      </td>
      
      <td>
      </td>
      
      <td>
        32
      </td>
    </tr>
  </table>
</div>

Here is an image of the same results
  
[<img alt="Oracle SQL Developer Results" title="Oracle SQL Developer Results" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleResults.PNG?mtime=1357471831" width="318" height="135" />][1]

See what happened, Oracle changed the blanks to NULLs. 

We can easily test this theory, let's create a table with a column that has a not null constraint

```plsql
create table TestNull2(Col2 varchar(100) not null);
```

Now of course if you try to insert a NULL it will blow up

```plsql
insert into TestNull2 values(NULL);
```

Here is the error
  
_SQL Error: ORA-01400: cannot insert NULL into (“SYSTEM”.”TESTNULL2&#8243;.”COL2&#8243;)
  
01400. 00000 – “cannot insert NULL into (%s)”_

Inserting Bla works without a problem

```plsql
insert into TestNull2 values('Bla');
```

What about a blank, what will happen now?

```plsql
insert into TestNull2 values('');
```

_SQL Error: ORA-01400: cannot insert NULL into (“SYSTEM”.”TESTNULL2&#8243;.”COL2&#8243;)
  
01400. 00000 – “cannot insert NULL into (%s)”_

As you can see the blank gets converted to a NULL and you get the same error. This is very different from SQL Server.

Will a space succeed?

```plsql
insert into TestNull2 values(' ');
```

A space is no problem.

**Coalesce differences**
  
Just be aware that coalesce won't work the same either. Oracle doesn't have isnull but it has the nvl function instead

Run the following two statements

```plsql
select nvl('','No') as a
from dual;
```

```plsql
select coalesce('','No') as a
from dual;
```

In both cases you are getting No back from the function, as you can see a blank is treated as null.

Be aware of these differences between Oracle and SQL Server, you could have some strange results back from queries if you assume it works the same

We will take a look at that strange table _dual_ in another post

 [1]: /wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleResults.PNG?mtime=1357471831