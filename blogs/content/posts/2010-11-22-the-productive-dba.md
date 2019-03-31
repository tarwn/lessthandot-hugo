---
title: The Productive DBA
author: ptheriault
type: post
date: 2010-11-22T13:28:57+00:00
excerpt: 'Over the past 12 years I’ve received a lot of great suggestions from some very good DBAs on how to best be productive and organized.  One of the best pieces of advice I ever read was out of SQL Server magazine a few years back on how to be organized.  T&hellip;'
url: /index.php/datamgmt/datadesign/the-productive-dba/
views:
  - 7880
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration

---
Over the past 12 years I’ve received a lot of great suggestions from some very good DBAs on how to best be productive and organized. One of the best pieces of advice I ever read was out of SQL Server magazine a few years back on how to be organized. That advice was to create yourself a DBA repository! What is a DBA repository you ask… Well it’s simple, it’s the one single place where you keep ALL of the things you need to do your job, scripts, logs, executables, trace files, reports. It’s anything and everything you would use daily. So what’s my repository like? I have a small server that I loaded all my diagnostic and SQL Tools on where I keep a share named DBA. In that share I have the following folders, Executable, DR, Diagrams, Documents, SQL Scripts, SSAS, SSIS, SSRS, Trace, Bugs and Fixes and HA. 

Some of the folders are obvious but let me go through a few of them. In my executable folder I save every .exe file that I have purchased or downloaded. I don’t usually keep executables if we haven’t installed them. That cuts down on the size and old exe versions. In the DR folder is everything I need for a successful DR cutover. If you are in the middle of a disaster you won’t look very good if you’re googleing for scripts or instructions on how to failover your db to the DR server. Having all that information at your fingertips will not only make you more productive but your boss will appreciate how effective you are. 

Every script I write no matter how large or small is saved in the SQL Scripts folder by application. I can’t stress the importance of saving everything your write. You never know when you will need that script or a version of it again. You can save a ton of time if you don’t have to rewrite scripts. Also again, in an emergency you must have everything you’ll need at your fingertips to react quickly and effectively. I have a subfolder just dedicated to SQL Server perf mon scripts. I use them daily! In the SSAS, SSIS, SSRS folder I save all my projects from BIDS. I have these folder linked to source control too so those projects are checked into SVN. In the Bugs and Fixes folder I save everything from every issue I’ve opened with Microsoft or other vendors. This includes email conversations, scripts and the eventual resolution. It makes correcting the problem much faster if it ever arises again. The HA folder is everything I need to setup and maintain High Availability.

The last thing I do on each database server is to setup a mapped drive to this share and create a local DBA share. In the local DBA share I keep scripts that are specific to that database server. Basically it’s a smaller version of the DBA repository.

The DBA repository has become my most valuable tool and has saved the day more than once. Having the repository will help you keep a cool head in an emergency and help you get the answers you need quickly.