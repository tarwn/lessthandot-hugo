---
title: Altering the State of the Database Can Cause DMVs to Clear
author: Paul Timmerman
type: post
date: 2000-11-30T00:00:00+00:00
ID: 2052
excerpt: |
  For a number of these DMVs, MSDN states:
  "The counters are initialized to empty whenever the SQL Server (MSSQLSERVER) service is started. In addition, whenever a database is detached or is shut down (for example, because AUTO_CLOSE is set to ON), all r&hellip;
draft: true
url: /?p=2052
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
For a number of these DMVs, such as [sys.dm\_db\_index\_usage\_stats][1], MSDN states:

**"The counters are initialized to empty whenever the SQL Server (MSSQLSERVER) service is started. In addition, whenever a database is detached or is shut down (for example, because AUTO_CLOSE is set to ON), all rows associated with the database are removed."**

 [1]: http://msdn.microsoft.com/en-us/library/ms188755.aspx