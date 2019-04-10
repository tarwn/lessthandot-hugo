---
title: Adding External Tools to SQL Server
author: David Forck (thirster42)
type: post
date: 2011-01-27T14:03:00+00:00
ID: 1015
excerpt: 'At my company, security for IT personnel is a bit interesting.  Each IT employee has two accounts, a regular account, and a "1" account.  The one account is our regular account with a 1 appended to it.  How this works is our regular accounts have normal&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/adding-external-tools-to-sql/
views:
  - 11311
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
At my company, security for IT personnel is a bit interesting.  Each IT employee has two accounts, a regular account, and a “1” account.  The one account is our regular account with a 1 appended to it.  How this works is our regular accounts have normal access to everything, then our 1 accounts have elevated privileges.  I'm still not sure if I agree with this model but it's what I'm stuck with.

Part of the problem with this security model is that most of my work with sql server is done through my 1 account, but I'm supposed to log in with my regular account.  In XP this was a lot of fun (sarcasm) to manage.  In Windows 7 it's a bit easier because you can right-click and run as admin.  However, when you open and close the same application 10-20 times a day, you get pretty tired with typing in your username and password for your 1 account.  One of the main applications that I was constantly opening and closing was Redgate's SQL Compare.

Well, one day I was just doing some exploring in SQL Server Management Studio, and found a menu item under the Tools section called “External Tools”.  Curious, I went into it.  Apparently with this dialog you can add a reference to an application installed on your machine and open that application from inside SSMS.  I added the redgate tool and then launched it.  Amazingly, it didn't prompt me for a user name and password, so I can only assume that it runs under the context of what you're running SSMS on your local machine.  This was pretty cool, so I added the three main redgate applications that I use.  This has saved me a lot of time having to type in my user name and password.

So, here's the steps to add an application as an external tool

  1. Open Management Studios
  2. Go to Tools – External Tools
  3. Click Add
  4. Type in a Title for the new tool
  5. Click the elipse button next to command, and navigate to the .exe file of your application and click open
  6. Click ok to save your changes
  7. Go to Tools, and above the External Tools option you should now see a list of the tools that you've added

Well, I hope this come in handy to everyone.  Let me know what other external tools you add.