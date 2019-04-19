---
title: Restoring multiple transaction log backups
author: Ted Krueger (onpnt)
type: post
date: 2009-07-22T14:56:12+00:00
ID: 519
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/restoring-multiple-transaction-log-backu/
views:
  - 12141
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
If you've ever had to restore log files and not use the restore to a point in time option, I'm sure you found it to be a pain executing all the restore statements.

A good tip when you have to do this is to use vbscript or some other scripting technology to create your own .sql file that you can just execute and restore the .trn files 

So for example, I use Quest Litespeed for compression (see [here][1] for more info). This means I have a few procedures at my disposal in which I can use to perform my restores from compressed backups. So given a restore script for [Litespeed][2] like this

```sql
exec master.dbo.xp_restore_log @database = N'dbname' ,
@filename = N'pathbackup.trn',
@filenumber = 1,
@with = N'STATS = 10',
@with = N'NORECOVERY',
@with = N'MOVE N''file.mdf'' TO pathfile.mdf''',
@with = N'MOVE N''log.ldf'' TO pathlog.ldf''',
@affinity = 0,
@logging = 0
GO
```
we can take this code and with a little scripting generate a the call on each .trn file found in a particular folder

```VB
Dim fso, folder, files, results,source  
Dim db

Set fso = CreateObject("Scripting.FileSystemObject")  
source = Wscript.Arguments.Item(0)  
db = Wscript.Arguments.Item(1)  


If source = "" Or db = "" Then      
	Wscript.Echo "Check your source or database value passed please and try again"      
	Wscript.Quit  
End If  
Set results = fso.CreateTextFile("RestoreScript.sql", True)  
Set folder = fso.GetFolder(source)  
Set files = folder.Files    

For each f In files   
     If InStr(f.Name,".trn") > 0 Then
	results.WriteLine("exec master.dbo.xp_restore_log @database = N'" & db & "' , " & _
			"@filename = N'" & f.Path & "', " & _
			"@filenumber = 1, " & _
			"@with = N'STATS = 10', " & _
			"@with = N'NORECOVERY', " & _
			"@with = N'MOVE N''file.mdf'' TO N''pathfile.mdf''', " & _
			"@with = N'MOVE N''log.ldf'' TO N''pathlog.ldf''', " & _
			"@affinity = 0, " & _
			"@logging = 0 " & _
			VbCrLf & "GO") 
     End If
Next  

results.Close
```
Now just run that and you'll get your .sql you can execute and be done with it. I usually only do this on special occasions so there aren't parameters like date range and such passed but you could easily add those in and only grab a certain listing of files.

 [1]: /index.php/DataMgmt/DBAdmin/title-8
 [2]: http://www.quest.com/litespeed-for-sql-server/