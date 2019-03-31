---
title: New Dynamic Management Views in SQL Server 2014 CTP1
author: SQLDenis
type: post
date: 2013-06-25T07:59:00+00:00
excerpt: |
  There are 26 new Dynamic Management Views in SQl Server 2014 CTP1.
  
  Here is a list of these DMVs in alphabetical order
  
  sys.dm_db_merge_requests
  sys.dm_db_xtp_checkpoint
  sys.dm_db_xtp_checkpoint_files
  sys.dm_db_xtp_gc_cycle_stats
  sys.dm_db_xtp_h&hellip;
url: /index.php/datamgmt/dbprogramming/new-dynamic-management-views-in/
views:
  - 8889
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dmv
  - dynamic management view
  - hekaton
  - sql server 2014

---
There are 26 new Dynamic Management Views in SQl Server 2014 CTP1.

Here is a list of these DMVs in alphabetical order

sys.dm\_db\_merge_requests
  
sys.dm\_db\_xtp_checkpoint
  
sys.dm\_db\_xtp\_checkpoint\_files
  
sys.dm\_db\_xtp\_gc\_cycle_stats
  
sys.dm\_db\_xtp\_hash\_index_stats
  
sys.dm\_db\_xtp\_index\_stats
  
sys.dm\_db\_xtp\_memory\_consumers
  
sys.dm\_db\_xtp\_object\_stats
  
sys.dm\_db\_xtp\_table\_memory_stats
  
sys.dm\_db\_xtp_transactions
  
sys.dm\_io\_cluster\_shared\_volumes
  
sys.dm\_os\_buffer\_pool\_extension_configuration
  
sys.dm\_resource\_governor\_resource\_pool_volumes
  
sys.dm\_xe\_database\_session\_event_actions
  
sys.dm\_xe\_database\_session\_events
  
sys.dm\_xe\_database\_session\_object_columns
  
sys.dm\_xe\_database\_session\_targets
  
sys.dm\_xe\_database_sessions
  
sys.dm\_xtp\_consumer\_memory\_usage
  
sys.dm\_xtp\_gc\_queue\_stats
  
sys.dm\_xtp\_gc_stats
  
sys.dm\_xtp\_memory_stats
  
sys.dm\_xtp\_system\_memory\_consumers
  
sys.dm\_xtp\_threads
  
sys.dm\_xtp\_transaction\_recent\_rows
  
sys.dm\_xtp\_transaction_stats

The DMVs that start with sys.dm\_db\_xtp or sys.dm_xtp are Hekaton (In-Memory OLTP) specific DMVs
  
The DMVs that start with sys.dm\_db\_xtp return information about individual Hekaton enabled databases, The DMVs that start with sys.dm\_xtp\_* provide information about the instance.

There are also a bunch of Extended Events DMVs as well

In total there are now 204 DMVs in SQL Server 2014