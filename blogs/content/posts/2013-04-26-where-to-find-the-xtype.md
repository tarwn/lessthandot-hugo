---
title: Where to find the xtype info for SQL Server in a table
author: SQLDenis
type: post
date: 2013-04-26T14:48:00+00:00
ID: 2074
excerpt: |
  If you look at the sys.sysobjects view, you will see an xtype column listed
  
  Object type. Can be one of the following object types:
  AF = Aggregate function (CLR)
  C = CHECK constraint
  D = Default or DEFAULT constraint
  F = FOREIGN KEY constraint
  L&hellip;
url: /index.php/datamgmt/dbprogramming/where-to-find-the-xtype/
views:
  - 25203
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - howto
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - table
  - tip

---
If you look at the sys.sysobjects view, you will see a xtype column listed

Object type. Can be one of the following object types:
  
AF = Aggregate function (CLR)
  
C = CHECK constraint
  
D = Default or DEFAULT constraint
  
F = FOREIGN KEY constraint
  
L = Log
  
FN = Scalar function
  
FS = Assembly (CLR) scalar-function
  
FT = Assembly (CLR) table-valued function
  
IF = In-lined table-function
  
IT = Internal table
  
P = Stored procedure
  
PC = Assembly (CLR) stored-procedure
  
PK = PRIMARY KEY constraint (type is K)
  
RF = Replication filter stored procedure
  
S = System table
  
SN = Synonym
  
SQ = Service queue
  
TA = Assembly (CLR) DML trigger
  
TF = Table function
  
TR = SQL DML Trigger
  
TT = Table type
  
U = User table
  
UQ = UNIQUE constraint (type is K)
  
V = View
  
X = Extended stored procedure

However there is no table in SQL Server that holds this info&#8230;.or is there?

I answered [this question][1] today and decided to share here as well

Here is how you can find that info, you can use my favorite table spt_values

sql
SELECT name
FROM master..spt_values
WHERE type = 'O9T'
AND number  = -1
```

This is the output

AF: aggregate function
  
AP: application
  
C : check cns
  
D : default (maybe cns)
  
EN: event notification
  
F : foreign key cns
  
FN: scalar function
  
FS: assembly scalar function
  
FT: assembly table function
  
IF: inline function
  
IS: inline scalar function
  
IT: internal table
  
L : log
  
P : stored procedure
  
PC : assembly stored procedure
  
PK: primary key cns
  
R : rule
  
RF: replication filter proc
  
S : system table
  
SN: synonym
  
SQ: queue
  
TA: assembly trigger
  
TF: table function
  
TR: trigger
  
U : user table
  
UQ: unique key cns
  
V : view
  
X : extended stored proc

Now if you want to split it into two columns, you can use the LEFT and RIGHT functions together with the PATINDEX function

sql
SELECT LEFT(name,PATINDEX('%:%',name)-1) AS xtype,
RIGHT(name, (LEN(name) - PATINDEX('%:%',name))) AS Description
FROM master..spt_values
WHERE type = 'O9T'
AND number  = -1
```

Here is the result

<pre>xtype	Description
AF	 aggregate function
AP	 application
C 	 check cns
D 	 default (maybe cns)
EN	 event notification
F 	 foreign key cns
FN	 scalar function
FS	 assembly scalar function
FT	 assembly table function
IF	 inline function
IS	 inline scalar function
IT	 internal table
L 	 log
P 	 stored procedure
PC 	 assembly stored procedure
PK	 primary key cns
R 	 rule
RF	 replication filter proc
S 	 system table
SN	 synonym
SQ	 queue
TA	 assembly trigger
TF	 table function
TR	 trigger
U 	 user table
UQ	 unique key cns
V 	 view
X 	 extended stored proc</pre>

 [1]: http://stackoverflow.com/questions/16243857/is-there-a-table-that-holds-the-listing-of-xtype-descriptions