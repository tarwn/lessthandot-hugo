---
title: Use INDEXKEY_PROPERTY in SQL Server to determine if columns in indexes are sorted ascending or descending
author: SQLDenis
type: post
date: 2009-09-23T18:20:23+00:00
ID: 566
url: /index.php/datamgmt/datadesign/use-indexkey_property-in-sql-server-to-d/
views:
  - 8677
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - functions
  - indexes
  - indexing
  - sql server 2005
  - sql server 2008

---
How can you find out if the columns that are part of the index are sorted descending or ascending in that index? For example when you create the following index

sql
CREATE CLUSTERED  INDEX ix_test_clust ON test (id ASC, col1 DESC)
```

How would you find out without scripting the index if the columns are in descending or ascending order? SQL Server has a function for that, the name of this function is INDEXKEY_PROPERTY
  
Before starting I need to warn you that this will only run on SQL Server 2005 and above, I have not tested this on SQL Server 2000 but if you need it to run on SQL Server 2000 remove sys. in front of the tables...so instead of sys.sysindexes use sysindexes that should do the trick.

Let's see how that works. first create this table in the tempdb

sql
USE tempdb
go
 
CREATE TABLE Test (id INT, col1 VARCHAR(40), col2 INT, col3 INT)
go
```

Now create 2 indexes, one clustered with 2 columns and one non clustered with 4 columns

sql
CREATE NONCLUSTERED  INDEX ix_test ON test (id ASC, col1 DESC,col2 DESC, col3 ASC)
go
 
 
CREATE CLUSTERED  INDEX ix_test_clust ON test (id ASC, col1 DESC)
go
```

First we need to see what the arguments are for the INDEXKEY_PROPERTY function; here is what you can find in Books On Line
  
**INDEXKEY\_PROPERTY ( object\_ID ,index\_ID ,key\_ID ,property )**

**Arguments**

<table border="1" cellpadding="10">
  <tr>
    <td>
      <strong>object_ID</strong>
    </td>
    
    <td>
      Is the object identification number of the table or indexed view. object_ID is int.
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>index_ID</strong>
    </td>
    
    <td>
      Is the index identification number. index_ID is int.
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>key_ID</strong>
    </td>
    
    <td>
      Is the index key column position. key_ID is int.
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>property</strong>
    </td>
    
    <td>
      Is the name of the property for which information will be returned. property is a character string and can be one of the following values.</p> 
      
      <table border="1">
        <tr>
          <td>
            <strong>Value</strong>
          </td>
          
          <td>
            <strong>Description</strong>
          </td>
        </tr>
        
        <tr>
          <td>
            ColumnId
          </td>
          
          <td>
            Column ID at the key_ID position of the index.
          </td>
        </tr>
        
        <tr>
          <td>
            IsDescending
          </td>
          
          <td>
            Order in which the index column is stored.
          </td>
        </tr>
        
        <tr>
          <td colspan="2" align="right">
            1 = Descending 0 = Ascending
          </td>
        </tr>
      </table>
    </td>
  </tr>
</table>

So to use it we need to know a couple of things, we need the table name, the index id and the index key column position.
  
Run the following query which will give you all that info for the table Test, change the table name if you are interested in other tables.

sql
select OBJECT_NAME(k.id) as TableName,i.name as IndexName,c.name as ColumnName,k.keyno,k.indid as IndexID 
from sys.sysindexes i
join sys.sysindexkeys k on i.id = k.id
and k.indid = i.indid
join sys.syscolumns c on k.id = c.id
and k.colid = c.colid
where OBJECT_NAME(k.id) = 'Test'
order by OBJECT_NAME(k.id),i.name,k.keyno
```

Here is the output

<table border="1">
  <tr>
    <td>
      TableName
    </td>
    
    <td>
      IndexName
    </td>
    
    <td>
      ColumnName
    </td>
    
    <td>
      keyno
    </td>
    
    <td>
      IndexID
    </td>
  </tr>
  
  <tr>
    <td>
      Test
    </td>
    
    <td>
      ix_test
    </td>
    
    <td>
      id
    </td>
    
    <td>
      1
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      Test
    </td>
    
    <td>
      ix_test
    </td>
    
    <td>
      col1
    </td>
    
    <td>
      2
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      Test
    </td>
    
    <td>
      ix_test
    </td>
    
    <td>
      col2
    </td>
    
    <td>
      3
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      Test
    </td>
    
    <td>
      ix_test
    </td>
    
    <td>
      col3
    </td>
    
    <td>
      4
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      Test
    </td>
    
    <td>
      ix_test_clust
    </td>
    
    <td>
      id
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
      Test
    </td>
    
    <td>
      ix_test_clust
    </td>
    
    <td>
      col1
    </td>
    
    <td>
      2
    </td>
    
    <td>
      1
    </td>
  </tr>
</table>

Now with the output from above can easily call the INDEXKEY_PROPERTY function, we use the IsDescending property to find out the sort order, below are the function calls for the two indexes we created.

sql
--index ix_test 
SELECT INDEXKEY_PROPERTY ( OBJECT_ID('Test') , 2 , 1 , 'isdescending' )
SELECT INDEXKEY_PROPERTY ( OBJECT_ID('Test') , 2 , 2 , 'isdescending' )
SELECT INDEXKEY_PROPERTY ( OBJECT_ID('Test') , 2 , 3 , 'isdescending' )
SELECT INDEXKEY_PROPERTY ( OBJECT_ID('Test') , 2 , 4 , 'isdescending' )
```

sql
-- index ix_test_clust
SELECT INDEXKEY_PROPERTY ( OBJECT_ID('Test') , 1 , 1 , 'isdescending' )
SELECT INDEXKEY_PROPERTY ( OBJECT_ID('Test') , 1 , 2 , 'isdescending' )
```

Of course we are not amateurs and nobody wants to type all that stuff, we will just take the query from before and put the following in the select statement _INDEXKEY_PROPERTY (k.id,k.indid ,c.colid,'isdescending') as IsDescending_
  
Run the code below to see how that works.

sql
select INDEXKEY_PROPERTY (k.id,k.indid ,k.keyno,'isdescending') as IsDescending,OBJECT_NAME(k.id) as TableName,i.name as IndexName,c.name as ColumnName,c.colid,k.indid as IndexID 
from sys.sysindexes i
join sys.sysindexkeys k on i.id = k.id
and k.indid = i.indid
join sys.syscolumns c on k.id = c.id
and k.colid = c.colid
where OBJECT_NAME(k.id) = 'Test'
order by OBJECT_NAME(k.id),i.name,k.keyno
```

Here is the output

<table border="1">
  <tr>
    <td>
      IsDescending
    </td>
    
    <td>
      TableName
    </td>
    
    <td>
      IndexName
    </td>
    
    <td>
      ColumnName
    </td>
    
    <td>
      keyno
    </td>
    
    <td>
      IndexID
    </td>
  </tr>
  
  <tr>
    <td>
    </td>
    
    <td>
      Test
    </td>
    
    <td>
      ix_test
    </td>
    
    <td>
      id
    </td>
    
    <td>
      1
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      1
    </td>
    
    <td>
      Test
    </td>
    
    <td>
      ix_test
    </td>
    
    <td>
      col1
    </td>
    
    <td>
      2
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      1
    </td>
    
    <td>
      Test
    </td>
    
    <td>
      ix_test
    </td>
    
    <td>
      col2
    </td>
    
    <td>
      3
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
    </td>
    
    <td>
      Test
    </td>
    
    <td>
      ix_test
    </td>
    
    <td>
      col3
    </td>
    
    <td>
      4
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
    </td>
    
    <td>
      Test
    </td>
    
    <td>
      ix_test_clust
    </td>
    
    <td>
      id
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
      1
    </td>
    
    <td>
      Test
    </td>
    
    <td>
      ix_test_clust
    </td>
    
    <td>
      col1
    </td>
    
    <td>
      2
    </td>
    
    <td>
      1
    </td>
  </tr>
</table>

Of course if you can do that you can also very simply write a query that returns all the columns that are sorted descending in any index by making the WHERE clause the following: where INDEXKEY_PROPERTY (k.id,k.indid ,k.keyno,'isdescending') = 1. Run the query below to see how that works

sql
select INDEXKEY_PROPERTY (k.id,k.indid ,k.keyno,'isdescending') as IsDescending,OBJECT_NAME(k.id) as TableName,i.name as IndexName,c.name as ColumnName,k.keyno,k.indid as IndexID 
from sys.sysindexes i
join sys.sysindexkeys k on i.id = k.id
and k.indid = i.indid
join sys.syscolumns c on k.id = c.id
and k.colid = c.colid
where INDEXKEY_PROPERTY (k.id,k.indid ,k.keyno,'isdescending') = 1
```



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22