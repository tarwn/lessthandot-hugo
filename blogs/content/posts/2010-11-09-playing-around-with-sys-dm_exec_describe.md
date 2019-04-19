---
title: Playing around with sys.dm_exec_describe_first_result_set and sys.dm_exec_describe_first_result_set_for_object
author: SQLDenis
type: post
date: 2010-11-09T16:28:31+00:00
ID: 941
excerpt: |
  If you have a stored procedure which returns two result sets up till now there was now way to get the meta data easily about the first result set. Let's take a look what is new in SQL Server Denali, first create this very simple stored procedure.
  
  cre&hellip;
url: /index.php/datamgmt/dbprogramming/playing-around-with-sys-dm_exec_describe/
views:
  - 5726
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - ctp
  - database
  - sql server denali

---
If you have a stored procedure which returns two result sets up till now there was now way to get the meta data easily about the first result set. Let's take a look what is new in SQL Server Denali, first create this very simple stored procedure.

```sql
create procedure prTest
as
select 1 as a, 'B' as b

select 'A' as z, 3 as d
Go
```

## sys.dm\_exec\_describe\_first\_result\_set\_for_object

Run the following query

```sql
select * 
from sys.dm_exec_describe_first_result_set_for_object(OBJECT_ID('prTest'),1)
```

Here is partial output

<div class="tables">
  <table>
    <tr>
      <th>
        is_hidden
      </th>
      
      <th>
        column_ordinal
      </th>
      
      <th>
        name
      </th>
      
      <th>
        is_nullable
      </th>
      
      <th>
        system_type_id
      </th>
      
      <th>
        system_type_name
      </th>
      
      <th>
        max_length
      </th>
      
      <th>
        precision
      </th>
      
      <th>
        scale
      </th>
    </tr>
    
    <tr>
      <td>
      </td>
      
      <td>
        1
      </td>
      
      <td>
        a
      </td>
      
      <td>
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
      </td>
      
      <td>
        2
      </td>
      
      <td>
        b
      </td>
      
      <td>
      </td>
      
      <td>
        167
      </td>
      
      <td>
        varchar(1)
      </td>
      
      <td>
        1
      </td>
      
      <td>
      </td>
      
      <td>
      </td>
    </tr>
  </table>
</div>

As you can see you get the name, type, precision, length and more about the resultset

## sys.dm\_exec\_describe\_first\_result_set

This query is more interesting because it will look at a dynamic query. Let's say your query is the following _'SELECT * FROM sysobjects SELECT 3'_ passed in as a parameter

Here is how that would work

```sql
declare @n nvarchar(100) = N'SELECT * FROM sysobjects SELECT 3'

SELECT *
FROM sys.dm_exec_describe_first_result_set(@n, NULL, 1);
```

Here is partial output

<div class="tables">
  <table>
    <tr>
      <th>
        is_hidden
      </th>
      
      <th>
        column_ordinal
      </th>
      
      <th>
        name
      </th>
      
      <th>
        is_nullable
      </th>
      
      <th>
        system<br />_type_id
      </th>
      
      <th>
        system_type<br />_name
      </th>
      
      <th>
        max_length
      </th>
      
      <th>
        precision
      </th>
      
      <th>
        scale
      </th>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        1
      </td>
      
      <td>
        name
      </td>
      
      <td>
        0
      </td>
      
      <td>
        231
      </td>
      
      <td>
        nvarchar(128)
      </td>
      
      <td>
        256
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
        0
      </td>
      
      <td>
        2
      </td>
      
      <td>
        id
      </td>
      
      <td>
        0
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        3
      </td>
      
      <td>
        xtype
      </td>
      
      <td>
        0
      </td>
      
      <td>
        175
      </td>
      
      <td>
        char(2)
      </td>
      
      <td>
        2
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
        0
      </td>
      
      <td>
        4
      </td>
      
      <td>
        uid
      </td>
      
      <td>
        1
      </td>
      
      <td>
        52
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
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        5
      </td>
      
      <td>
        info
      </td>
      
      <td>
        1
      </td>
      
      <td>
        52
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
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        6
      </td>
      
      <td>
        status
      </td>
      
      <td>
        1
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        7
      </td>
      
      <td>
        base_schema_ver
      </td>
      
      <td>
        1
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        8
      </td>
      
      <td>
        replinfo
      </td>
      
      <td>
        1
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        9
      </td>
      
      <td>
        parent_obj
      </td>
      
      <td>
        0
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        10
      </td>
      
      <td>
        crdate
      </td>
      
      <td>
        0
      </td>
      
      <td>
        61
      </td>
      
      <td>
        datetime
      </td>
      
      <td>
        8
      </td>
      
      <td>
        23
      </td>
      
      <td>
        3
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        11
      </td>
      
      <td>
        ftcatid
      </td>
      
      <td>
        1
      </td>
      
      <td>
        52
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
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        12
      </td>
      
      <td>
        schema_ver
      </td>
      
      <td>
        1
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        13
      </td>
      
      <td>
        stats_schema_ver
      </td>
      
      <td>
        1
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        14
      </td>
      
      <td>
        type
      </td>
      
      <td>
        1
      </td>
      
      <td>
        175
      </td>
      
      <td>
        char(2)
      </td>
      
      <td>
        2
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
        0
      </td>
      
      <td>
        15
      </td>
      
      <td>
        userstat
      </td>
      
      <td>
        1
      </td>
      
      <td>
        52
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
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        16
      </td>
      
      <td>
        sysstat
      </td>
      
      <td>
        1
      </td>
      
      <td>
        52
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
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        17
      </td>
      
      <td>
        indexdel
      </td>
      
      <td>
        1
      </td>
      
      <td>
        52
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
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        18
      </td>
      
      <td>
        refdate
      </td>
      
      <td>
        0
      </td>
      
      <td>
        61
      </td>
      
      <td>
        datetime
      </td>
      
      <td>
        8
      </td>
      
      <td>
        23
      </td>
      
      <td>
        3
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        19
      </td>
      
      <td>
        version
      </td>
      
      <td>
        1
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        20
      </td>
      
      <td>
        deltrig
      </td>
      
      <td>
        1
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
        0
      </td>
    </tr>
    
    <tr>
      <td>
        0
      </td>
      
      <td>
        21
      </td>
      
      <td>
        instrig
      </td>
      
      <td>
        1
      </td>
      
      <td>
        56
      </td>
      
      <td>
        int
      </td>
      
      <td>
        4
      </td>
      
      <td>
        10
      </td>
      
      <td>
        0
      </td>
    </tr>
  </table>
</div>

As you can see you get the name, type, precision, length and more about the resultset, this is pretty neat especially for a query stored in a parameter.

When I wrote this post documentation was not yet available, so some of this stuff is written with a lot of guessing.

Click on the [SQL Server Denali][1] tag to see all our SQL Server Denali related posts

 [1]: /index.php/All/sql+server+denali: