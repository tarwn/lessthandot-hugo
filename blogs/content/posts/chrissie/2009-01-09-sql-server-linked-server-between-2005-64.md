---
title: SQL server Linked server between 2005 64bits and a 2000 32 bits server.
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-09T16:57:51+00:00
ID: 279
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sql-server-linked-server-between-2005-64/
views:
  - 10779
categories:
  - Microsoft SQL Server Admin
tags:
  - 32 bits
  - 64 bits
  - linked server
  - sql server 2000
  - sql server 2005

---
Today I wanted to import the last bits of data from the old server. The new server is a 2005 64 bits SQL server and the old server is a 2000 32 bits. I&#8217;m no DBA and everything is internal so I don&#8217;t really care about service packs and I can&#8217;t remember which has which allthough I&#8217;m sure the 2000 box has the latest SP but since they aren&#8217;t even connected to the internet updating isn&#8217;t easy. 

So today I setup a linked server between the two. I used my desktop to connect to the new server and then setup the linked server. And surprise surprise it didn&#8217;t work. This was more or less the error I got. 

> **OLE DB provider &#8220;SQLNCLI&#8221; for linked server &#8220;servername&#8221; returned message &#8220;Communication link failure&#8221;.
  
> Msg 10054, Level 16, State 1, Line 0
  
> TCP Provider: An existing connection was forcibly closed by the remote host.
  
> Msg 18456, Level 14, State 1, Line 0
  
> Login failed for user &#8216;NT AUTHORITYANONYMOUS LOGON&#8217;.**

I actually never solved this problem. I just gave ANONYMOUS LOGON rights on the database and deleted it shortly after. It is internal remember. That solved that error message. On to the next error message.

> **OLE DB provider &#8220;SQLNCLI&#8221; for linked server &#8220;servername&#8221; returned message &#8220;Unspecified error&#8221;.
  
> OLE DB provider &#8220;SQLNCLI&#8221; for linked server &#8220;servername&#8221; returned message &#8220;The stored procedure required to complete this operation could not be found on the server. Please contact your system administrator.&#8221;.
  
> Msg 7311, Level 16, State 2, Line 1
  
> Cannot obtain the schema rowset &#8220;DBSCHEMA\_TABLES\_INFO&#8221; for OLE DB provider &#8220;SQLNCLI&#8221; for linked server &#8220;servername&#8221;. The provider supports the interface, but returns a failure code when it is used.**

This one was solved by doing a google. And finding [this][1].

At the same time Ted (onpnt) gave me [this solution][2] and he the asked why I hadn&#8217;t told him it was a 64 bit 2005 and the 2000 was 32 bit. Well Ted you didn&#8217;t ask ;-). So it&#8217;s all Teds fault it took me more then an hour to figure this out. Well that&#8217;s my story anyway. 

The solution is to create this procedure.

```tsql
create procedure sp_tables_info_rowset_64
@table_name sysname,
@table_schema sysname = null,
@table_type nvarchar(255) = null
as
declare @Result int set @Result = 0
  exec @Result = sp_tables_info_rowset @table_name, @table_schema, @table_type```
because I was using the ANONYMOUS LOGON I had to give that exec rights on that newly created SP. After that everything worked like it should. 

A little bird also told me that the windows authentication didn&#8217;t work because of to many jumps. Next time I&#8217;ll try to solve that if possible. 

I would like to thank George and Ted for their patience and help in solving this problem.

 [1]: http://blog.sqlauthority.com/2007/05/04/sql-server-fix-error-msg-7311-level-16-state-2-line-1-cannot-obtain-the-schema-rowset-dbschema_tables_info-for-ole-db-provider-sqlncli-for-linked-server-linkedservername/
 [2]: http://blog.raffaeu.com/archive/2008/06/19/sql-2005-and-linked-server-cannot-obtain-the-schema-rowset.aspx