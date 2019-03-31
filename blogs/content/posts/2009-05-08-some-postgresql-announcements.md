---
title: Some PostgreSQL Announcements
author: SQLDenis
type: post
date: 2009-05-08T15:50:09+00:00
url: /index.php/datamgmt/datadesign/some-postgresql-announcements/
views:
  - 2754
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - npgsql
  - pgadmin
  - postgresql

---
Today there were some PostgreSQL announcements, these announcements are below

**pgAdmin v1.10.0 Beta 3 is now available**

pgAdmin v1.10.0 Beta 3 is now available for testing in source, Windows
  
and Mac OS X distributions from:

http://www.postgresql.org/ftp/pgadmin3/release/v1.10.0-beta3/

**New version of PostgreSQL 8.3 Live CD released**
  
PostgreSQL live CD, which is based on Fedora 10. The major
  
change is PostgreSQL 8.3.7 . It includes the PostgreSQL related packages
  
that built on http://yum.pgsqlrpms.org . This new version includes
  
one new package (pgstat2), and also many updates to the existing
  
packages.

Details are here:

http://yum.pgsqlrpms.org/livecd.php

**Npgsql 2.0.5 released!**
  
Npgsql is a .Net Data provider written 100% in C# which allows .net
  
programs to talk to postgresql backends. Npgsql is licensed under BSD.
  
More info can be obtained from http://www.npgsql.org

This release is a minor bug fix for the stable 2.0 series.

The biggest highlights are:

Fixes for entity framework support
  
Fix for a regression when using prepared statements with parameters
  
with size specified.
  
Fix for the famous bug of Seek not supported exception