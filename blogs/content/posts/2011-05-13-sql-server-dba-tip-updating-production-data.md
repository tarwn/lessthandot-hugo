---
title: SQL Server DBA Tip 14 – SQL Server General – Updating Production Data
author: Ted Krueger (onpnt)
type: post
date: 2011-05-13T10:56:00+00:00
ID: 1172
excerpt: 'Updating large volumes of production data is a common task for a DBA.  This task falls into the level of responsibility that has been given to the DBA role in faith that the proper steps are taken to ensure the integrity of the data before and after the&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-updating-production-data/
views:
  - 6069
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <a href="/media/blogs/All/-22.png?mtime=1305291213"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-22.png?mtime=1305291213" alt="" width="139" height="121" align="left" /></a>
</div>

Updating large volumes of production data is a common task for a DBA.  This task falls into the level of responsibility that has been given to the DBA role in faith that the proper steps are taken to ensure the integrity of the data before and after the update.  One problem that can arise from years of performing updates of this kind; at times a DBA may let their guard down and write an update quickly or with too much confidence and not take the needed steps to ensure the update, successful or not, is keeping the integrity of the data and recovery points in mind.

As hard as it is to hold back on even the simple updates that a DBA may have to perform, every measure of protecting the valued data should be taken.  There are some basic steps that can be used as a guide line to meet this guideline.

  1. Having a point-in-time backup
  2. Temporary retention of the data prior to updating
  3. Error Handling
  4. Timing an update
  5. Integrity – Knowing the entire picture and all that is affected

**Having a point-in-time backup** ****

A backup point to use as a recovery point can be in the form of any normal recovery plan that is already in place.  This may be log shipping, normal backups (Full, Differential and Log backups), Mirroring (sometimes in low transactional databases that are mirrored, pausing the mirror is an option) and INSERT INTO or other forms of copying the data. 

This step should be common knowledge to the DBA, but also checked prior to performing the update.  Ensure logs are being backed up and that full restore tests have been successful.  If mirroring is an option, find the lowest data change period to do so.

**Temporary retention of the data prior to updating**

If the volume of data is small and SELECT INTO a table that will be temporarily retained until validation of the update is completed, is a great option. Just be warned that TempDB usage is a concern with this method.  The value in making sure that the data as it was prior to the update is retained is for validation, quick recovery and reporting.  SQL Server Integration Services is a very good choice in this stage.  Bulk loading data into temporary databases and tables can be done extremely quickly with SSIS.  Making SSIS dynamic to the point a table or SQL Statement is the only requirement to move the data adds great value to creating an efficient, temporary copying process.

**Error Handling**

No UPDATE, DELETE or INSERT should be performed without wrapping the statements in error handling.  Even a basic shell for error handling can be mobile from script to script for changing data.

For example:

```sql
BEGIN TRY
            BEGIN TRY 
	UPDATE MONEY_TABLE SET SALARY = 200000                                                  
            END TRY
           
            BEGIN CATCH
                  INSERT INTO TABLE_THAT_HOLDS_UPDATE_ERRORS (ERROR)
                  VALUES (ERROR_MESSAGE());
            END CATCH;  
END TRY
BEGIN CATCH 
      RAISERROR ('Critical error during Update',16,1);
END CATCH;

```

A basic try catch statement around this update would prevent the worst case of corrupting the data and compromising integrity of the data.  Now the example has no logic or reason to it.  In a situation where enumerating row-by-row data to perform updates, this is extremely effective error handling.  Mass updating based on updates from join statements can also be placed into a try catch.  The entire statement's failure is then logged in a table or other means that fit the process. 

Error handling should be thought through.  Protect your data from every point of failure and ensure there are no possibilities for failures to follow through to the production databases.

**Timing updates**

Timing updates, deletes and mass inserts is another critical step in performing these DBA level changes to data.  High level locking in SQL Server will occur when changes are made that take time, or simply change a high volume of data.  These locks may cause business operations to slow, and in some cases, stop.  Ensure that this situation is avoided or, at a minimum, causes the least amount of negative impact to the business. 

**Integrity – Knowing the entire picture and all that is affected**

Very seldom will a database have tables that are completely unrelated.  That becomes a completely different tip using the right tool for the right type of storage.  As such, relational data can be directly affected at the time of performing changes to data by means other than the applications that are written for them.  Ensure that all other tables that are related by constraints or basic key references that are not true definitions in SQL Server, are thought out and integrity is retained through the changes. 

**Conclusion**

Updates, inserts and deletes are inherently dangerous transactions when performed out of the normal processing set in place.  These transactions are the responsibility of the DBA to perform safely and without damaging results.  Never treat any of these transactions as too simple to take seriously, cut corners and not put time into ensuring they will function the way they are meant to. 

Making mistakes is a part of growing as a DBA (and any other profession).  When mistakes are made, learn from them in knowing how important the steps like the ones mentioned in this article are.  It's as important not to be hard on yourself to the point the failure does not help the future as it is to know these steps should be done. 

 

<div class="image_block">
  <a href="/media/blogs/All/-23.png?mtime=1305291213"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-23.png?mtime=1305291213" alt="" width="207" height="156" /></a>
</div>

<p style="text-align: center;">
  Test everything and happy DBAing and being an all-around awesome nerd!
</p>