---
title: sp_indexinfo an enhanced index information procedure
author: SQLDenis
type: post
date: 2008-07-07T14:11:09+00:00
ID: 80
url: /index.php/datamgmt/datadesign/sp_indexinfo-an-enhanced-index-informati/
views:
  - 3162
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Tibor Karaszi has created a very useful index information stored procedure for SQL Server 2005 and up.
  
This stored procedure will tell you the following&#8221;

What indexes exists for a or each table(s)
  
Clustered, non-clustered or heap
  
Columns in the index
  
Included columns in the index
  
Unique or nonunique
  
Number rows in the table
  
Space usage
  
How frequently the indexes has been used 

Check it out here: http://www.karaszi.com/SQLServer/util\_sp\_indexinfo.asp