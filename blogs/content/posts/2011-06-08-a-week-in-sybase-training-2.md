---
title: A week in Sybase Training, what did I learn, day 3
author: SQLDenis
type: post
date: 2011-06-08T18:00:00+00:00
ID: 1212
excerpt: |
  This is day 3 of my training. It will be a scorching 100 degrees in NYC today so I will not walk around at all today.
  
  Auto Expand
  
  tempdb
url: /index.php/datamgmt/datadesign/a-week-in-sybase-training-2/
views:
  - 7900
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - ase
  - sql server
  - sybase
  - training

---
This is day 3 of my training. It will be a scorching 100 degrees in NYC today so I will not each lunch in Bryant Park today.

#### Auto Expand

This is what is knows as autogrow in SQL Server. by default Sybase does not have this turned on, as a matter of fact, you have to run a script that is shipped with the product in order to create the stored procedures that will make it possible for you to use this feature. It seems that within the Sybase crowd, if it is a old school DBA then they don&#8217;t like feature, if it is a new DBA they love it because they have to do less.

There is a proc that is named sp_dbextend that you can use to manage all this stuff

Here is an example that defines the rate of growth as 5% of current value, in each expansion attempt for the database pubs2

sql
sp_dbextend 'modify', 'database', pubs2, logsegment, 'growby', '5%'
```

You can use the sp_dbextend stored procedure to manage devices, databases and thresholds.

I won&#8217;t go into more detail here, you can look that up in Sybase documentation if you are interested.

#### tempdb

I learned some interesting things in regards to tempdb. In Sybase you can have user created temporary databases. You can then either bind a login to a specific tempdb or you can create a group and then each connection will connect to a tempdb within that group in a round robin fashion. You can create 511 temporary databases per server.

Here is how you create a temporary database

sql
create temporary database tempdb_01 on some_tempdb_device = 10
```

Then to add that database to the default group, you would do this

sql
sp_tempdb 'add', tempdb_01, 'default'
```

Now when you connect and there are 15 temporary databases and for some reason you would like to know which one your session will use, you can use @@tempdbid, below is an example

```
select db_name(@@tempdbid)
```

You can also make sure that login Menace will always use the tempdb_01 temporary database by binding that login to that database

sql
sp_tempdb bind, 'LG', 'Menace', 'DB', 'tempdb_01'
```

If you want to check what tempdb a certain process id has assigned, you can use the tempdb_id() function, you would pass in the SPID

All in all interesting stuff&#8230;..however if the original tempdb is down, you are still out of luck
  
In the SQL Server world people have been asking for 1 tempdb per database for a while to get around the tempdb contention issues

#### Security

There are several roles in Sybase just like in SQL Server. Here are some of them

sa_role (System Administrator)
  
sso_role (System Security Officer)
  
oper_role (Server Operator (OPER))
  
sybase\_ts\_role
  
navigator_role

By default the sa account has sa\_role, sso\_role, oper\_role and sybase\_ts_role

There are in total 14 roles, you can find all of them in the syssrvroles table

You can enable or disable roles by using the set role RoleName [on | off]

To check if you have a role or not, you can use the proc_role function, here is an example

sql
select proc_role('sa_role')
```

That will return 1 if you have that role or 0 if you do not

To see all the roles for a login you can use the sp_displaylogin stored procedure.

Here is an example

sql
sp_displaylogin 'sa'
```

You can grant a role or roles to someone by using the grant role command. Here is an example

sql
grant role sso_role, sa_role, oper_role to SQLMenace
```

To take away the sso_role, you would use revoke

sql
revoke role sso_role from SQLMenace
```

Here are some procs to manage logins, if you are a SQL Server person, some of these will look familiar to you.

sp_addlogin
  
sp_droplogin
  
sp_displaylogin
  
sp_locklogin
  
sp_modifylogin
  
sp_password

You can require setup experation, set a minimum password length, set maximum failed logins before a login gets locked out and requiring digits in password.

#### Database access, users and object permissions

This is very similar to SQL Server, it even has that proc we all know sp_helprotect
  
One interesting thins is that if I create Table A with a PK and you create table B with a FK to my table, then I have to give you references permissions on my table or specific columns

Everything else is similar to SQL Server, there is object chaining, there are roles. What does not exists in Sybase(and this has nothing to do with security but we discussed bit) is deferred name resolution&#8230;.the way they do it is creating a dummy table and then dropping it after the proc has been created.