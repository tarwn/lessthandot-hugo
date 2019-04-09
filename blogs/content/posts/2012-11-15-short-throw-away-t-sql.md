---
title: Short-Throw-Away T-SQL
author: Ted Krueger (onpnt)
type: post
date: 2012-11-15T14:45:00+00:00
ID: 1789
excerpt: "Throughout the day, I probably write 5 to 15 short scripts to accomplish tasks that really, have no real purpose for future use.  That is, they aren't worth saving.  Typically this is because the script, like the one below, is easier to write than it is&hellip;"
url: /index.php/datamgmt/dbprogramming/short-throw-away-t-sql/
views:
  - 14895
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Throughout the day, I probably write 5 to 15 short scripts to accomplish tasks that really, have no real purpose for future use.  That is, they aren't worth saving.  Typically this is because the script, like the one below, is easier to write than it is to search a folder to find.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/trashbin.jpg?mtime=1352997880"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/trashbin.jpg?mtime=1352997880" width="320" height="213" /></a>
</div>

Just about the time I was going to hit delete I thought; why not post them on [LTD's Wiki][1]?

Well, that is what I'm going to start attempting to do.  Don't get too excited.  There have been some good scripts that have gotten a lot of use, both from me and readers.  Scripts like the [Orphaned fix script][2].  That script really came from the same situation.  I needed a quick script so I wrote it quickly.  Before throwing that one out, I realized it had some value given the steps that it resolves and the repeated need to fix orphaned users in databases.

Another way to get them out there, blogging them.  On that note, today's short-throw-away script is grabbing the count of tables in a database and printing them out in SSMS with the table name.  This was for a quick reply in an email.  Hopefully it will help someone else out at some point.

To add to all of this and the idea, I’m tagging anyone that reads this to do the same on your own blog.  All of those quick hacks in T-SQL or even C# and so on, post them up.  It’s a blog post, it’s sharing and it just might save someone a lot of time.

> Disclaimer: throw away scripts are just that. Untested and not run threw a ringer. Test them first 🙂</p>
sql
set nocount on
declare @int int = 1
declare @count bigint
declare @cmd nvarchar(1500)
declare @tbl table (id int identity(1,1),name sysname)
insert into @tbl select name from sys.tables where type = N'U'

while @int <= (Select count(*) from @tbl)
 begin
  
   set @cmd = 'select @cnt=count(*) from ' + (select name from @tbl where id = @int)
   execute sp_executesql @cmd, N'@cnt int OUTPUT', @cnt=@count OUTPUT
   set @cmd = 'Table: ' + (select name from @tbl where id = @int) + ' has a row count of ' + CAST(@count AS VARCHAR(4000))
   print @cmd
  set @int += 1
 end
 
```


 [1]: http://wiki.ltd.local/index.php/Main_Page
 [2]: http://wiki.ltd.local/index.php/Fix_Orphaned_Database_Users_All_SQL_Server_Versions