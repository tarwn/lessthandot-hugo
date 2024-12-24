---
title: Don't use Default Database in SQL Server
author: George Mastros (gmmastros)
type: post
date: 2000-11-30T00:00:00+00:00
ID: 1983
excerpt: "When you create a login with SQL Server, you can specify the default database for that login, but you shouldn't.  Here's why."
draft: true
url: /?p=1983
categories:
  - Database Administration
  - Database Programming
tags:
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - sqlcop
  - tables

---
When you create a login with SQL Server, you can specify the default database for that login. The default database is used when you connect to SQL Server without specifying a database.

The problem with this scenario is that the database that was set as the default could be taken offline or detached. When this happens, you will be unable to log in to SQL Server unless you specify the database.

In my opinion, it is best practice to set the default database for every login to Master. If the master database is not available, you have a bigger problem than default databases.

To check the logins with an invalid default database:

```sql
SELECT	L.name, L.dbName
FROM	master.sys.syslogins L
		Inner Join sys.databases D
			On L.dbname = d.name
WHERE	L.dbname <> 'master'
		And D.state_desc <> 'ONLINE'
```

To check the logins that have a database other than master:

```sql
SELECT	L.name, L.dbName
FROM	master.sys.syslogins L
WHERE	L.dbname <> 'master'
```

To change the default database to master:

```sql
ALTER LOGIN [YourLoginNameHere] WITH DEFAULT_DATABASE = [master]
```

Changing the default database for a login is super easy