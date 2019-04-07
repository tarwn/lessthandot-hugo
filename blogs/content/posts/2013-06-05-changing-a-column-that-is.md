---
title: Changing a column that is already populated into a column that uses a sequence
author: SQLDenis
type: post
date: 2013-06-05T19:38:00+00:00
ID: 2102
excerpt: |
  Just got a question on my A first look at sequences in SQL Server Denali post.
  
  The question is the following: How can you add a new column to existing table (already populated) and make it a sequence column?
  
  Let's take a look. First create this ve&hellip;
url: /index.php/datamgmt/dbprogramming/changing-a-column-that-is/
views:
  - 12718
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - how to
  - sequence
  - sql server 2012

---
Just got a question on my [A first look at sequences in SQL Server Denali][1] post.

The question is the following: _How can you add a new column to existing table (already populated) and make it a sequence column?_

Let&#8217;s take a look. First create this very simple table

sql
CREATE TABLE bla (id INT)
INSERT BLA VALUES(1)
INSERT BLA VALUES(2)
INSERT BLA VALUES(3)
INSERT BLA VALUES(4)
INSERT BLA VALUES(5)
```

What you want to do is have the sequence start at 6, here is how to do that.

sql
CREATE SEQUENCE GlobalCounter
    AS INT
    MINVALUE 1
    NO MAXVALUE
    START WITH 6;
GO
```

Use START WITH to indicate that the sequence should start at 6

The next step is to add a default to the column, this default would use the sequence

sql
ALTER TABLE bla
ADD CONSTRAINT id_default_sequence
DEFAULT NEXT VALUE FOR GlobalCounter FOR ID;
```

Now if we do an insert, followed by a select

sql
INSERT bla DEFAULT VALUES

SELECT * FROM Bla

```
Here are the results

id
  
1
  
2
  
3
  
4
  
5
  
6

As you can see the value 6 was inserted

There you have it, a simple way to change a column to a sequence column

 [1]: /index.php/DataMgmt/DataDesign/a-first-look-at-sequences-in-sql-server