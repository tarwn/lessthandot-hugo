---
title: SQL Server 2008 Adds ISO Week,TZoffset, Nanosecond And Microsecond To Datepart
author: SQLDenis
type: post
date: 2008-06-12T12:53:32+00:00
ID: 57
url: /index.php/datamgmt/datadesign/sql-server-2008-adds-iso-week-tzoffset-n/
views:
  - 17885
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
I saw microsecond and nanosecond already in CTP5 but this is the first time I have seen ISO Week (ISO_WEEK) and Time Zone Offset(TZoffset), maybe I haven't looked hard enough 🙂

Here are the datepart and abbreviations you have to use for the 4 new oness 

microsecond mcs
  
nanosecond ns
  
TZoffset tz
  
ISO_WEEK isowk, isoww

**ISO_WEEK** datepart ISO 8601 includes the ISO week-date system, a numbering system for weeks. Each week is associated with the year in which Thursday occurs. For example, week 1 of 2004 (2004W01) ran from Monday 29 December 2003 to Sunday, 4 January 2004. The highest week number in a year might be 52 or 53.

**TZoffset** The TZoffset (tz) is returned as the number of minutes (signed). The following statement returns a time zone offset of 310 minutes.
  
SELECT DATEPART (TZoffset, 2007-05-10 00:00:01.1234567 +05:10); 

If the datepart argument is TZoffset (tz) and the date argument is not of datetimeoffset data type, NULL is returned.