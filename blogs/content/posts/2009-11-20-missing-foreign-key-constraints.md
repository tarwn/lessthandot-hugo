---
title: Missing foreign key constraints
author: George Mastros (gmmastros)
type: post
date: 2009-11-20T11:32:30+00:00
ID: 639
excerpt: 'References are at the heart of a database.  It is possible to create a beautiful database with perfectly working front end code that always, 100% of the time, does the right thing with your data.  But, writing code is hard.  Very hard!  Your data is oft&hellip;'
url: /index.php/datamgmt/datadesign/missing-foreign-key-constraints/
views:
  - 157704
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
References are at the heart of a database. It is possible to create a beautiful database with perfectly working front end code that always, 100% of the time, does the right thing with your data. But, writing code is hard. Very hard! Your data is often the most important asset you own. You need to protect it with every bit of technology you can find. At the heart of protecting your data is referential integrity. What does this mean? It means that you shouldn&#8217;t be missing data, ever! 

The code below will check for columns that have ID in the name of the column where that column is not part of a primary key or foreign key constraint. Often times, this represents a missing constraint, but not always. The code presented below exists to highlight potential problems. You must still determine if this potential problem is real, and then act accordingly.

**How to detect this problem:**

sql
SELECT  C.TABLE_SCHEMA,C.TABLE_NAME,C.COLUMN_NAME
FROM    INFORMATION_SCHEMA.COLUMNS C          
        INNER Join INFORMATION_SCHEMA.TABLES T            
          ON C.TABLE_NAME = T.TABLE_NAME    
          And T.TABLE_TYPE = 'Base Table'
          AND T.TABLE_SCHEMA = C.TABLE_SCHEMA        
        LEFT Join INFORMATION_SCHEMA.CONSTRAINT_COLUMN_USAGE U            
          ON C.TABLE_NAME = U.TABLE_NAME            
          And C.COLUMN_NAME = U.COLUMN_NAME
          And U.TABLE_SCHEMA = C.TABLE_SCHEMA
WHERE   U.COLUMN_NAME IS Null          
        And C.COLUMN_NAME Like '%id'
ORDER BY C.TABLE_SCHEMA, C.TABLE_NAME, C.COLUMN_NAME
```
**How to correct it:** Correcting this problem seems simple at first. Just declare your foreign keys, right? Well, it&#8217;s not so simple. You see, there could be code running that deletes all the necessary data from the related tables. If you have code that deletes data in related tables in the wrong order, you will get referential constraint errors. Similar problems can occur with updates and inserts. The order in which you do things is important when you have referential constraints.

**Level of severity:** High

**Level of difficulty:** High