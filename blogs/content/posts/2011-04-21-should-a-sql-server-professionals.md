---
title: Should SQL Server Professionals Learn Oracle?
author: Ted Krueger (onpnt)
type: post
date: 2011-04-21T16:52:00+00:00
ID: 1126
excerpt: |
  Should a SQL Server Professionals know Oracle?
  Yes, absolutely!  I strongly feel that all SQL Server Database Administrators and Developers should not only learn the basics and fundamental internals of Oracle, but should push that to others such as DB2&hellip;
url: /index.php/datamgmt/dbadmin/should-a-sql-server-professionals/
views:
  - 10039
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <a href="/media/blogs/DataMgmt/-40.png?mtime=1303401309"><img src="/wp-content/uploads/blogs/DataMgmt/-40.png?mtime=1303401309" alt="" width="78" height="75" align="left" /></a>
</div>

Yes, absolutely!  I strongly feel that all SQL Server Database Administrators and Developers should not only learn the basics and fundamental internals of Oracle, but should expand that to others such as DB2 and Sybase.  Why?  SQL Server is my mojo after all.  There shouldn’t be much reason to venture into Klingon territory and maybe start a database server uprising.  There actually is one major reason it should be a requirement to know more than SQL Server: Not everyone runs SQL Server. (Although they may want to)

So today we are going to focus on some very basic Oracle setup steps and commands to get us started on merging SQL Server skills into Oracle skills.  By the end of this series, we should be better prepared when the word, “Oracle” comes up in meetings and project discussions.  We really can all get along in the SQL Server and Oracle world.

**Oracle for the SQL Server Pro wanting to learn**

Focusing on Oracle; many people that are working with SQL Server think they simply do not have the ability to get their hands on Oracle due to the HUGE (yes that was a HUGE) costs that come along with it.  SQL Server has Developer Edition for next to nothing at $40 or so dollars.  Then there is SQL Server Express which has just about all the internal engine functionality as all editions do.  That means we get the same internal speed of what SQL Server is capable of with something that is totally free.

Ah, guess what?  If you didn’t already know, there is an [Oracle Express][1].  Yes, it is free to download and use under the typical [Oracle licensing agreements][2].  You also need to create an account that requires a little more information that most are willing to give up but I’ve never been hounded by anyone from Oracle after creating the account.

I found out about Oracle Express many years ago.  I had the need to know more about Oracle so I went out and found the best, and cheapest, way I could accomplish that.  Once I had the basic abilities needed to interact with Oracle, the specific project was much less intimidating and was a success.

For a SQL Server Professional, setting anything up in Oracle can be frustrating and confusing.  There is simply more to it than SQL Server.  Let’s face it; we’ve been spoiled with the installation process SQL Server comes with and the ease of configuration changes.  With that, let’s setup Oracle Express.  Then later, we’ll show a quick SQL Server Integration Services DTSX connection to that Oracle Express Instance and push some data into an Oracle database.

**Installing Oracle Express**

Some key notes that come with Oracle Express (much like SQL Express)

_Any use of the Oracle Database Express Edition is subject to the following limitations;_

  1. _ __Express Edition is limited to a single instance on any server.___
  2. _Express Edition may be installed on a multiple CPU server, but may only be executed on one processor in any server.___
  3. _Express Edition may only be used to support up to 4GB of user data (not including Express Edition system data)._ __
  4. _Express Edition may use up to 1 GB RAM of available memory.___

Setting up Oracle Express

If using Windows; there are two downloads you will need.  I picked the Oracle Universal and then, the pain we all know, the Oracle Client.  We’ll step over the basic setup process of Oracle Express Universal.  Essentially, you can click “_Next”_ for every step with a basic Windows OS to get the install going.  Ensure that the port selections that Oracle installer wants to use are open on your machine.  You can alter them but it is far easier when learning this process to allow the ports and leave the defaults in the installer.

After the installation is complete, launch the Home Page for your Oracle Express Instance to start working with Oracle. 

> Here is the first catch: you will be presented with a login screen requiring a user name and password.  During the installation you should have set the system administrator password.  Now, if you didn’t read that screen thoroughly or it was confusing about which password you were setting; the password was for SYS.  Use that login for this screen to get going.  So, username: SYS password: <one you created>

**Home Page**

The home page is straight forward.  The Administration area has with it the ability to set configurations for storage, memory down to managing database users.  The first thing to do is create a new user.  In my case, I created ONPNT.  Now when I log into the Home Page I don’t have to use SYS.

**Simple commands**

At this point Oracle Express is running and you can create tables and have all kinds of database fun.  Showing how we can interact with Oracle can be done from the SQL Commands page, which is located in the SQL area from the Home Page.   Let’s send a statement through the SQL Commands tool to show how it works.

In the panel where you can add commands, enter “SELECT * FROM SYSTEM.HELP”.  This shows all the rows in the system help table. 

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-41.png?mtime=1303401309"><img src="/wp-content/uploads/blogs/DataMgmt/-41.png?mtime=1303401309" alt="" width="366" height="347" /></a>
</div>

 

Now create a table by running:

CREATE TABLE imports

(Col varchar2(500))

The output is very similar to what you would see in SSMS.

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-42.png?mtime=1303401309"><img src="/wp-content/uploads/blogs/DataMgmt/-42.png?mtime=1303401309" alt="" width="220" height="168" /></a>
</div>

 

**SQL*PLUS**

SQL\*PLUS is a good way to get familiar with Oracle statements and commands.  It is very much like SQLCMD in the feel of the command prompt.  By default, SQL\*PLUS was installed and is ready to use.  To open SQL*PLUS, go to All Programs and then into the Oracle Database 10g Express Edition folder.  In the list, select Run SQL Command Line. 

By default, /nolog is set on the shortcut to SQL*PLUS.  This means that when opened, you will be forced to log into Oracle.  You can change this functionality but I highly recommend not leaving that hole in security and leaving /nolog in place.

This is seen from the target of the shortcut:

C:oraclexeapporacleproduct10.2.0serverBINsqlplus.exe /nolog

Once SQL*PLUS is open, login with the account you created for yourself.  This is done by sending, “connect <username>” and then your password.  To show the definition of the table we created earlier, run the following command.

DESCRIBE imports

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-43.png?mtime=1303401310"><img src="/wp-content/uploads/blogs/DataMgmt/-43.png?mtime=1303401310" alt="" width="624" height="313" /></a>
</div>

 

**Installation and verification completed**

In this post we’ve installed Oracle Express and found out how we can connect and run commands.  So far we have done everything directly on Oracle though and to really utilize it, we need to set things up so SQL Server can connect to it.  This way we can learn how we may have to interact with Oracle as SQL Server Professionals.  The post that follows will go over setting up the Oracle Client and TNSNAMES.ora.  TNSNAMES is a SQL*NET configuration that will essentially be used to direct out communications to the Oracle Instance and database. 

 

 [1]: http://www.oracle.com/technetwork/database/express-edition/downloads/102xewinsoft-090667.html
 [2]: http://www.oracle.com/technetwork/licenses/xe-license-152020.html