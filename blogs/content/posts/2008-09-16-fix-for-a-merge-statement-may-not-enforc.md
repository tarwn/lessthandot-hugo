---
title: FIX for A MERGE statement may not enforce a foreign key constraint when the statement updates a unique key column that is not part of a clustering key bug
author: SQLDenis
type: post
date: 2008-09-16T11:07:06+00:00
url: /index.php/datamgmt/datadesign/fix-for-a-merge-statement-may-not-enforc/
views:
  - 3869
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - bug
  - hotfix
  - sql server 2008
  - t-sql

---
**FIX: A MERGE statement may not enforce a foreign key constraint when the statement updates a unique key column that is not part of a clustering key and there is a single row as the update source in SQL Server 2008**

In Microsoft SQL Server 2008, a foreign key constraint may not be enforced when the following conditions are true:
  
• A MERGE statement is issued.
  
• The target column of the update has a nonclustered unique index.

Microsoft has created the first Hotfix for SQL Server 2008 which will fix this issue.

You can read more about this bug including how you can obtain the fix here: http://support.microsoft.com/kb/956718