---
title: Merge Replication Baseline Collector
author: Ted Krueger (onpnt)
type: post
date: 2012-07-18T16:22:00+00:00
excerpt: |
  I’ve created a new project on Codeplex for an SSIS package that performs collections in order to create a baseline of merge replication:  “Merge Replication Baseline Collector”
  The baseline can be used for all types of situations.
  A few…
  -          Q&hellip;
url: /index.php/datamgmt/dbadmin/merge-replication-baseline-collector/
views:
  - 6355
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSIS

---
I’ve created a new project on Codeplex for an SSIS package that performs collections in order to create a baseline of merge replication:  “[Merge Replication Baseline Collector][1]”

The baseline can be used for all types of situations.

A few…

&#8211;          Questionable peak replication times

&#8211;          Assistance in analyzing upgrade paths

&#8211;          Adding or removing subscribers or re-publishers and impacts

&#8211;          Spike reporting and predictive analysis on synchronizing events

&#8211;          Determining better time periods for bulk data loads or mass updates

The project has the SSIS package available for download and has been marked as, Beta.  This package has been through thorough testing but I am hopeful many of you have a chance to test it and report back and issues and or valued comments on the baseline collections.

I am in the process of upgrading the package to SSIS 2012, also.  This will take into account more efficient parameter use and configurations.

 [1]: https://mergebaselinecollect.codeplex.com/