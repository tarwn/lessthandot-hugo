---
title: How to change the SA password in SQL Server
author: SQLDenis
type: post
date: 2012-05-22T12:14:00+00:00
ID: 1634
excerpt: |
  There was a question today How to change my local sql server sa password? i would like to expand on my answer in this post
  
  Before I start I would like you to read this post by ted Krueger first: To SA or not to SA to understand why you should not be&hellip;
url: /index.php/datamgmt/datadesign/how-to-change-the-sa/
views:
  - 17767
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - security
  - sql server 2005
  - sql server 2008

---
There was a question today [How to change my local sql server sa password?][1] i would like to expand on my answer in this post

Before I start I would like you to read this post by ted Krueger first: [To SA or not to SA][2] to understand why you should not be using the SA account.

Now that you know why you should not be using the SA account and you are still using it, let&#8217;s see how you can change the password for the SA account

The easiest way is to login to the server as sa or any other account that has sufficient privileges to change the sa account

here is what the script looks like

sql
USE [master]
GO
ALTER LOGIN [sa] WITH PASSWORD=N'NewPassword'
GO
```

Most likely you will get this error
  
Msg 15118, Level 16, State 1, Line 1
  
Password validation failed. The password does not meet Windows policy requirements because it is not complex enough.

There are two things you can do, the smarter thing would be to pick a complex password with some digits and some characters that are not alphanumeric. Or you can shoot yourself in the foot by turning the check off

sql
USE [master]
GO
ALTER LOGIN [sa] WITH PASSWORD=N'NewPassword', CHECK_POLICY =OFF
GO
```
Now try to login. It is possible that the sa account is disabled, you will see the following message

Login failed for user &#8216;sa&#8217;. Reason: The account is disabled. (Microsoft SQL Server, Error: 18470)

To enable the account, all you have to do is the following

sql
ALTER LOGIN [sa] ENABLE
GO
```

**You do not need to restart the SQL Server service for a password change**. One of the myths that you will hear is that you need to restart SQL Server. When you change from SQL authentication to mixed authentication and vice versa, you do need to restart for the changes to take effect but for a password change it is immediate, no need for a restart!

And if you are old school, you can also do the following, thanks to [Aaron Bertrand][3] for this

sql
EXEC sp_password N'old password', N'new password', N'sa';
```

You can also do all of this from SSMS with the GUI, navigate to the security folder, right click on the sa account, select properties and you will see the following window

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/saChangePassword-1.PNG?mtime=1337695861"><img alt="Change sa password in sql server" src="/wp-content/uploads/blogs/DataMgmt/Denis/saChangePassword-1.PNG?mtime=1337695861" width="686" height="616" /></a>
</div>

Click on the Status page to enable or disable the account

I would encourage you to use T-SQL not the GUI, the day (and I promise you this will happen) will come that sql server will be installed on a box without the tools (Sever core perhaps) and you can only connect to it from the command line with osql, you will be stuck if you don&#8217;t know how to do this with T-SQL

 [1]: http://stackoverflow.com/questions/10703150/how-to-change-my-local-sql-server-sa-password
 [2]: /index.php/DataMgmt/DBAdmin/to-sa-or-not-to-sa
 [3]: http://sqlblog.com/blogs/aaron_bertrand/default.aspx