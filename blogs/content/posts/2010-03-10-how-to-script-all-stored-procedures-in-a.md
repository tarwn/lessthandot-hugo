---
title: How to script all stored procedures in a database
author: Naomi Nosonovsky
type: post
date: 2010-03-10T19:20:03+00:00
ID: 722
excerpt: |
  In the forums I frequent, the question of scripting all stored procedures in a database arises very often, the most recent encounter is in this MSDN thread script out many stored procedures at once?
  
  A few years ago, I asked this question myself in ww&hellip;
url: /index.php/datamgmt/datadesign/how-to-script-all-stored-procedures-in-a/
views:
  - 25993
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
In the forums I frequent, the question of scripting all stored procedures in a database arises very often, the most recent encounter is in this MSDN thread [script out many stored procedures at once?][1]

A few years ago, I asked this question myself in www.UniversalThread.com forum and Borislav Borissov helped me to come up with the following solution:

sql
set nocount on
DECLARE @Test TABLE (Id INT IDENTITY(1,1), Code varchar(max))

INSERT INTO @Test (Code)
SELECT 'IF object_ID(N''[' + schema_name(schema_id) + '].[' + Name + ']'') IS NOT NULL 
           DROP PROCEDURE ['+ schema_name(schema_id) +' ].[' + Name + ']' + char(13) + char(10) + 'GO' + char(13) +char(10) +
           OBJECT_DEFINITION(OBJECT_ID) + char(13) +char(10) + 'GO' + char(13) + char(10)
            from sys.procedures
            where is_ms_shipped = 0

DECLARE @lnCurrent int, @lnMax int
DECLARE @LongName varchar(max)

SELECT @lnMax = MAX(Id) FROM @Test
SET @lnCurrent = 1
WHILE @lnCurrent <= @lnMax
      BEGIN
            SELECT @LongName = Code FROM @Test WHERE Id = @lnCurrent
            WHILE @LongName <> ''
               BEGIN
                   print LEFT(@LongName,8000)
                   SET @LongName = SUBSTRING(@LongName, 8001, LEN(@LongName))
               END
            SET @lnCurrent = @lnCurrent + 1
      END
```
The output of this code produces a script of all stored procedures in a database.

Obviously, there are many applications of this code – you may try appending comments to this script, for example, or change your procedures in some way.

There are alternative ways of scripting all stored procedures, as suggested by
  
Adam Haines in the referenced thread:

Option 1: Use the scripting wizard Right-click the db –> tasks –> Generate scripts –> go through the wizard.

Option 2: Open the stored procedures folder in SSMS (in the object explorer details window). (You can also press F7 to do so, see [this blog][2] for details). You can use shift+click to select all the stored procedures and you can then right-click and script them to a file.

Options 3: The simplest of them. 

```
bcp "SELECT definition + char(13) + 'GO' FROM MyDatabase.sys.sql_modules s INNER JOIN MyDatabase.sys.procedures p ON [s].[object_id] = [p].[object_id] WHERE p.name LIKE 'Something%'" queryout "c:SP_scripts.sql" -S MyInstance -T -t -w
```
Hope this may help.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/80fc88a2-bd74-4bd7-aee2-ceb804441bb5
 [2]: /index.php/DataMgmt/DBProgramming/scripting-all-jobs-on-sql-server-2005-20
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22