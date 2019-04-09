---
title: SQL Server 2008 cannot be installed if you have Visual Studio 2008 RTM installed
author: SQLDenis
type: post
date: 2008-08-07T13:28:17+00:00
ID: 103
url: /index.php/datamgmt/datadesign/sql-server-2008-cannot-be-installed-if-y/
views:
  - 5658
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql server 2008

---
Visual Studio 2008 does not support having both Visual Studio 2008 without a service pack and Visual Studio 2008 with SP1 installed on the same computer. Because certain SQL Server 2008 features install components that are also part of the release version of Visual Studio 2008 SP1, SQL Server 2008 requires Visual Studio 2008 with SP1. If Visual Studio 2008 without a service pack is installed instead, it may not work correctly after you install SQL Server 2008.

More about this in this knowledgebase article

<http://support.microsoft.com/kb/956139>
  
There is also an item on connect submitted by Steve Kass: <http://connect.microsoft.com/SQLServer/feedback/ViewFeedback.aspx?FeedbackID=360930>

So I guess I have to wait till August 11th when VS 2008 goes RTM to install it. I don't want to install VS 2008 SP1 Beta so close before RTM

<img src="http://i34.tinypic.com/350js03.jpg" border="0" alt="Image and video hosting by TinyPic" />