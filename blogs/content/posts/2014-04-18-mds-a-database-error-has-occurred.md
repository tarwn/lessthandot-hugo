---
title: 'MDS: A Database Error Has Occurred'
author: Koen Verbeeck
type: post
date: 2014-04-18T09:20:36+00:00
ID: 2583
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/mds-a-database-error-has-occurred/
views:
  - 14762
rp4wp_auto_linked:
  - 1
categories:
  - Master Data Services (MDS)
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - batch
  - error
  - mds
  - syndicated

---
I recently came across the following error message when I tried to look at the batches in the Integration Management section of MDS:

<pre parse="no">515: A database error has occurred. Please contact your system administrator</pre>

[<img class="alignnone size-full wp-image-2585" alt="koen_mdserror" src="/wp-content/uploads/2014/04/koen_mdserror.png" width="299" height="120" />][1]

Too bad I am the system administrator on that machine...

Anyway, after some searching it came out the MDS stored procedure _udpEntityStagingAllBatchesByModelGet_ throws a bit of a temper tantrum if one of the batch tags used in the staging process of a leaf member is NULL. You can check this in the table **mdm.tblStgBatch**.

Simple delete the offending batches and you're good to go!

sql
DELETE FROM mdm.tblStgBatch
WHERE BatchTag IS NULL;
```


 [1]: /wp-content/uploads/2014/04/koen_mdserror.png