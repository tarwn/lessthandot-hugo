---
title: How to rename a column in a SQL Server table without using the designer
author: SQLDenis
type: post
date: 2008-06-23T14:23:43+00:00
ID: 69
url: /index.php/datamgmt/datadesign/how-to-rename-a-column-in-a-sql-server-t/
views:
  - 4226
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
If you have a table and you want to rename a column without using the designer, how can you do that? 

First create this table 

```
CREATE TABLE TestColumnChange(id int) 
INSERT TestColumnChange VALUES(1) 
```

```
SELECT * FROM TestColumnChange 
```

As you can see the select statement returns id as the column name. you can use ALTER table ALTER Column to change the dataype of a column but not the name. 

Here is what we will do, execute the statement below 

```
EXEC sp_rename 'TestColumnChange.[id]', 'value', 'COLUMN' 
```

Now do the select, you will see that the column name has changed 

```
SELECT * FROM TestColumnChange 
```

That is it, very simple