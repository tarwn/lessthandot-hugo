---
title: Changing a SQL Server Database Owner
author: Jes Borland
type: post
date: 2011-02-15T13:37:00+00:00
ID: 1029
excerpt: |
  Fact: every SQL Server database has an "owner". Without a database owner, you can't view the database properties. You may run into issues executing sp_helpdb. You may get an error using "EXECUTE AS OWNER". How do you fix this?
url: /index.php/datamgmt/dbadmin/changing-a-sql-server-database/
views:
  - 25574
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Fact: every SQL Server database has an "owner". You can check the owner of a database by running this query: 

```sql
SELECT NAME, SUSER_SNAME(owner_sid) 
FROM   sys.databases 
WHERE NAME = 'DatabaseName'
```

However, there may come a day when you run into this error: 

> There was error outputting database level information for ServerName.DatabaseName.
  
> Property Owner is not available for Database '[DatabaseName]'. This property may not exist for this object, or may not be retrievable due to insufficient access rights.

When you log into SQL Server Management Studio, right-click the database, and select Properties, you'll see this dialog box: 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/DBOwnerError.JPG?mtime=1296767797"><img alt="" src="/wp-content/uploads/users/grrlgeek/DBOwnerError.JPG?mtime=1296767797" width="626" height="198" /></a>
</div>

Without a database owner, you can't view the database properties. You may run into issues executing sp_helpdb. You may get an error using "EXECUTE AS OWNER". How do you fix this? 

**The Old Way: sp_changedbowner** 

Normally, I would use sp_changedbowner. Why? It's familiar. It's comfortable, like my favorite socks. But there's a new way, and it's time for me to learn that. (Also, Microsoft has indicated [it will be removed in a future version][1].) 

**The New Way: ALTER AUTHORIZATION**

I had to fumble around a bit to find this command. I am familiar with using ALTER DATABASE SET... to change many database facets. However, in looking through Books Online, I didn't see a way to use this to change the database owner. I dug a little further, and found [ALTER AUTHORIZATION][2]. 

The BOL syntax is: 

`ALTER AUTHORIZATION<br />
   ON [ class_type:: ] entity_name<br />
   TO { SCHEMA OWNER | principal_name }</p>
<p>class_type ::=<br />
    {<br />
        OBJECT | ASSEMBLY | ASYMMETRIC KEY | CERTIFICATE<br />
    | CONTRACT | TYPE | DATABASE | ENDPOINT | FULLTEXT CATALOG<br />
    | FULLTEXT STOPLIST | MESSAGE TYPE | REMOTE SERVICE BINDING<br />
    | ROLE | ROUTE | SCHEMA | SERVICE | SYMMETRIC KEY<br />
    | XML SCHEMA COLLECTION<br />
    }`

So, to use this: My "class\_type" is DATABASE, my "entity\_name" is the database name, and my "principal name" is my login. My complete statement looks like this:

```sql
ALTER AUTHORIZATION ON DATABASE::AdventureWorks TO grrlgeek
```

I ran that successfully, and there are no more errors. 

I can check the owner was changed by running my sys.databases query again. 

```sql
SELECT NAME, SUSER_SNAME(owner_sid) 
FROM   sys.databases 
WHERE NAME = 'DatabaseName'
```

Lesson learned: don't be afraid of new things!

 [1]: http://msdn.microsoft.com/en-us/library/ms178630.aspx
 [2]: http://msdn.microsoft.com/en-us/library/ms187359.aspx