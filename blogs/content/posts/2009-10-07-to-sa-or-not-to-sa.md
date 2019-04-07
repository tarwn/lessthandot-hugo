---
title: To SA or not to SA
author: Ted Krueger (onpnt)
type: post
date: 2009-10-07T11:04:00+00:00
ID: 578
url: /index.php/datamgmt/dbprogramming/to-sa-or-not-to-sa/
views:
  - 15450
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
The question was raised again if SA should be disabled. First, it doesn&#8217;t matter if you do not utilize SQL Server Authentication. The account is then disabled in the first place. Most instances have mixed mode enabled however so SA is a major concern and a huge pet peeve of mine.

So to put it lightly&#8230;

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//NoEntry.gif" alt="" title="" width="292" height="301" />
</div>

I want to get some things out of the way first that I believe in and go about while having SA on my database servers

1. I think sa is a good thing and should be left as such provided you handle it
  
2. If the password goes beyond the DBA you should lose your fingers
  
3. Passwords for sa should be changed at a minimum of bi-weekly
  
4. The sa account should not be the all mighty saving grace

I’ll dive into those points starting with leaving SA on the instance at all. First note that in 2005 we were giving the ability to rename Mr. SA. It’s rather nice actually if you have to expose an instance to a DMZ

This is how…

sql
ALTER LOGIN sa DISABLE;
ALTER LOGIN sa WITH NAME = IAMGOD;
ALTER LOGIN IAMGOD ENABLE;
```
The first problem is that SA is an account recognized by the SQL Server team as a systems admin. Now I know in the last few version of SQL Server SA was not utilized. At least from my upgrade experience, I never saw it referenced. That being said it should not be required. I still have it in the back of my head there is no reason not to leave it there “just in case”. That brings us to handling it though.

How many times have you seen this connection string?

```VB
Provider=SQLNCLI10;Server=(local);Database=master;Uid=sa; Pwd=youbloodyidiot;
```

That’s just wrong people! Let me ask this question. Is the application you decided needed SA in the connection strings a DBA? Even the applications I write to get me the one button click maintenance do not connect with SA. And I am the DBA! That alone should tell you the connection is really bad.

So what are the best practices for handling the SA account? I’ve always been frustrated that BOL never really hard a hardened best practice. At least I’ve never read on in the release of best practices on each version of SQL Server

Check 2008 here: http://msdn.microsoft.com/en-us/library/dd283095.aspx

I just told you to leave SA alone basically and have it enabled. A lot of DBAs are going to disagree with me on that and I hope they put their arguments here in the comments. The SA account always makes for interesting conversation. Sense I just told you to leave SA alone however, you MUST handle that god like account. Handling it on my instances is pretty straight forward. 

First, Active Directory group setup for the DBAs is the only ones that have access to the password for SA which is held securely in the password manager. See here for storing critical and sensitive passwords

<blockquote data-secret="AHCITsCkau" class="wp-embedded-content">
  <p>
    <a href="/index.php/datamgmt/datadesign/securing-you-password-for-sql-server-200/">Securing your password for SQL Server 2005 and 2008 and more</a>
  </p>
</blockquote>

<iframe class="wp-embedded-content" sandbox="allow-scripts" security="restricted" style="position: absolute; clip: rect(1px, 1px, 1px, 1px);" src="/index.php/datamgmt/datadesign/securing-you-password-for-sql-server-200/embed/#?secret=AHCITsCkau" data-secret="AHCITsCkau" width="500" height="282" title="&#8220;Securing your password for SQL Server 2005 and 2008 and more&#8221; &#8212; LessthanDot" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

So now that I got it out that only the DBAs should at any time be able to access sa in any way shape or form, what about the password?

Here is how I’ve handled SA and its password in my career. Bi-weekly (every two weeks) I have a process that calls a password generator to create an extremely strong password. This process first updates the encrypted password in the place they are stored and then resets the SA accounts actual password to the new one. 

That does a few things for you. First it resets the password frequently enough it is less likely to be compromised. Two, if it is somehow used by some person that you trusted with your life, whatever they used it on will break. I love that moment when it does to 😉 Out come the lashes with a wet noodle!

Last point I wanted to talk about is the fact that SA should never be the account you go to in order to save your ass. Yes, I really meant to be that direct on that statement. You as a DBA are in charge of securing the database servers. That means you have to secure your path into them for disasters and recovery points as well. This means the thought process must be handled of a complete loss of active directory and most accounts that would be typical or default to SQL Server. The SA account is default and that being said can be a point of disaster itself. In the least you should have an account on SQL Authentication that you can utilize for single user controls or dedicated admin connections. Trust me from experience of having these things break. You don’t want to be google&#8217;ing, “How do I recover the sa password” 

Happy is the DBA that protects from disaster!