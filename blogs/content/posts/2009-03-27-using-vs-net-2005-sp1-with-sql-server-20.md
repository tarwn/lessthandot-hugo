---
title: Using VS.NET 2005 SP1 with SQL Server 2008 connectivity
author: Ted Krueger (onpnt)
type: post
date: 2009-03-27T11:02:27+00:00
ID: 368
url: /index.php/datamgmt/datadesign/using-vs-net-2005-sp1-with-sql-server-20/
views:
  - 4582
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
When I first installed SQL Server 2008 in my environment one of the first things I did was open up VS.NET 2005
  
and start messing around with connectivity to the instance. That failed miserably. Errors were abound!
  
All hope is not lost though...

The last thing I want to do is install side by side versions of VS.NET. The IDE is heavy and sucks the life out of
  
any developer machine. I'm sure I'm not the only one that does not want to think about what version I'm clicking
  
when creating packages. Yes that is laziness in the purest form and I'm proud of it. My efficiency as a DBA and
  
support developer is very good and rewarded. 

So Microsoft was kind enough to allow me to use my full VS.NET 2005 SP1 installation to develop on my newly configured
  
SQL Server 2008 instances. Thank you!!!

The update is called, “Microsoft Visual Studio 2005 Service Pack 1 Update for Microsoft SQL Server 2008 Support”.
  
This is similar to the DTS legacy support update that we all had to throw in when we had those nagging 2000 instances
  
floating around and the business just would not allow upgrading time to SSIS. We still had to support DTS so alas
  
again they were kind enough not to completely ruin our lives and make us work 24/7 for a month to upgrade to SSIS
  
prior to upgrading 2000 to 2005. 

To grab the update go to
  
http://www.microsoft.com/downloads/details.aspx?FamilyID=e1109aef-1aa2-408d-aa0f-9df094f993bf&displaylang=en

Remember you need SP1 on VS.NET 2005 installed. Personally I think 2005 was the best release of the IDE for .NET so
  
my hard head is sticking with it until they really put the pressure on me. 

The install process is a next...next...finish one as usual. You should now be able to connect to SQL Server 2008
  
“almost error free”. I say almost because there is still the nice little gotcha listed on the download page of

This update does not support the following features for SQL Server 2008:
  
Creating and editing table schemas in Table Designer or Database Diagrams. As a workaround you may use the table
  
designer feature in SQL Server Management Studio 2008 to edit table schemas in SQL Server 2008. 

So be forewarned on that 🙂