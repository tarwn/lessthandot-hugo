---
title: Installing SQL Server 2008 express on Windows XP SP 2 without reading the manual
author: Christiaan Baes (chrissie1)
type: post
date: 2009-02-08T16:11:51+00:00
ID: 319
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/installing-sql-server-2008-express-on-wi/
views:
  - 31976
categories:
  - Microsoft SQL Server Admin
tags:
  - sql server express 2008

---
Today I was going to write a blogpost about persisting the simple domainmodel using Nhibernate and Fluent Nhibernate (I will have to write this later). It turned out to be quite and adventure. First I finaly installed [R#][1] on this machine I&#8217;ve been having the license since last octobre just haven&#8217;t used it yet on this or any other machine. That installation was pretty painless I must say.

Then I needed to install some kind of database. And I like shiny and new so I installed (or wanted to install) [SQL server express 2008][2] on my Windows XP SP2 which is fully supported on this OS. It turned out to be a 5 hour plus ordeal and installing one program after the other. But lets not forget it&#8217;s free. So I&#8217;m not complaining just venting. First thing it said (well after a couple of minutes of unzipping it&#8217;s install files in some guid folder) was that I needed the [3.5 SP1 framework][3]. Silly me only had 3.5. So I did that and restarted. At that moment I lost my first version of the blogpost. My bad. I only copied it to the clipboard.

Then I tried to install SQL Server Express again. And after deflating the files again in another GUID folder. It told me I should install [Windows installer 4.5][4]. Which of course I did. 

Another restart later. I doubleclicked the SQL Server Express 2008 installer again and this time it actually started installing (I wonder what happened to the just\_install\_and\_stop\_nagging\_me\_button, I really miss that button). But no such luck. After the second next it said I had to install powershell for windows and the buttons back and next were disabled so after installing [Powershell][5] I had to restart the SQL Server 2008 Express edition again. And It was happy now. So I clicked the Third next button and now it complained that I had the wrong version of Visual Studio 2008. I needed [SP1][6] for that too. So I installed that in two goes. 

But not there yet. I got this errormessage.

> TITLE: Microsoft SQL Server 2008 Setup
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
> 
> The following error has occurred:
> 
> The Windows Installer service cannot update the system file c:WINDOWSsystem32msxml6r.dll because the file is protected by Windows. You may need to update your operating system for this program to work correctly. 
> 
> For help, click: http://go.microsoft.com/fwlink?LinkID=20476&ProdName=Microsoft+SQL+Server&EvtSrc=setup.rll&EvtID=50000&ProdVer=10.0.1600.22&EvtType=0xF45F6601%25401201%25401

Which led me to this page

> Details
  
> Product:
  
> SQL Server
  
> ID:
  
> 50000
  
> Source:
  
> setup.rll
  
> Version:
  
> 10.0
  
> Component:
  
> SQL Server Native Client
  
> Message:
  
> A network error occurred while attempting to read from the file &#8216;%.*ls&#8217;.
  
>    
  
> Explanation
  
> An attempt was made to install (or update) SQL Server Native Client on a computer where SQL Server Native Client is already installed, and where the existing installation was from an MSI file that was not named sqlncli.msi.
  
>    
  
> User Action
  
> To resolve this error, uninstall the existing version of SQL Server Native Client. To prevent this error, do not install SQL Server Native Client from an MSI file that is not named sqlncli.msi.

Seriously. Are they trying to be funny? Would I ever install something that doesn&#8217;t have the name sqlncli.msi? I don&#8217;t think so.

So I uninstelled the two native clients on my machine and tried to install again. And finaly it worked

So after all this I have SQL server Express 2008 on my machine. What an adventure.

Now back to our regular program.

 [1]: http://www.jetbrains.com/resharper/
 [2]: http://www.microsoft.com/express/sql/download/
 [3]: http://www.microsoft.com/downloads/details.aspx?familyid=ab99342f-5d1a-413d-8319-81da479ab0d7&displaylang=en
 [4]: http://www.microsoft.com/downloadS/details.aspx?familyid=5A58B56F-60B6-4412-95B9-54D056D6F9F4&displaylang=en#filelist
 [5]: http://www.microsoft.com/downloads/thankyou.aspx?familyId=6ccb7e0d-8f1d-4b97-a397-47bcc8ba3806&displayLang=en&hash=Eo0%2fiCVc0%2bajjm3KEUaYpJi%2fzkkLfuYY1rGRI6sPyEhB7Z7hnfL6Tmfv92v6xehtaS5pH4BSyTXtYgabtj5NJQ%3d%3d
 [6]: http://www.microsoft.com/downloads/details.aspx?FamilyId=FBEE1648-7106-44A7-9649-6D9F6D58056E&displaylang=en