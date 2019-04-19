---
title: SQL Server Case/When Data Type problems
author: George Mastros (gmmastros)
type: post
date: 2008-12-04T19:48:38+00:00
ID: 236
url: /index.php/datamgmt/dbprogramming/mssqlserver/sql-server-case-when-data-type-problems/
views:
  - 26437
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
When you write a case/when statement in T-SQL, it's critically important that you cause all return values to have the same data type. If you don't do this, you open yourself up to conversion problems. This problem can occur even if some branches of the code are never executed.

When SQL Server compiles the code, it analyzes all branches of execution and determines the return data type. It does not matter that your particular query does not use a particular branch of execution. If it's there, it will be considered.

This article describes data type precedence that sql server uses:
    
<http://msdn.microsoft.com/en-us/library/ms190309.aspx>

Here is some code that highlights this issue:

```sql
Declare @Data VarChar(20)

Set @Data = ''

Select Case When @Data Is NULL Then NULL
            When @Data = ''    Then 'Data is empty'
            When 0=1           Then 1
            End
```
Since @Data = ”, the code should return 'Data is empty'. In this case, it doesn't because the 0=1 branch causes SQL Server to 'attempt' to convert each return value to an integer, and fails. 

When mixing numbers and strings, SQL Server prefers (based on data type precedence) to convert data to numbers. This is a little unfortunately because all number data can be converted to a string, but not all strings can be converted to a number. 

The fix for this particular problem is to convert everything to a string. Basically, if you want even just one branch of a Case/When statement to return a string, then you should make sure all branches return a string.

```sql
Declare @Data VarChar(20)

Set @Data = ''

Select Case When @Data Is NULL Then NULL
            When @Data = ''    Then 'Data is empty'
            When 0=1           Then Convert(VarChar(10), 1)
            End
```
Now, each branch returns a string (or NULL, which doesn't matter), so there will not be any problems with running this code.