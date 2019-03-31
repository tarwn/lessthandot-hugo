---
title: Delay Validation with SSIS Tasks
author: Ted Krueger (onpnt)
type: post
date: 2010-04-05T12:10:59+00:00
excerpt: 'When setting DelayValidation, also take care in making sure the setting will not have adverse affects on the package.  If another property in the task is critical and should be evaluated prior to the package execution, we can look to other methods to accomplish the same task.'
url: /index.php/datamgmt/dbprogramming/delays-can-be-good/
views:
  - 16117
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
tags:
  - integrated services
  - sql server
  - ssis

---
## Delaying the inevitable

Over the weekend a new SSIS package put into production had failed several times. Upon reviewing the package the problem was due to a Send Mail Task causing the entire package to fail. Even knowing the Send Mail Task was a non-critical step in the entire process flow of the package; the task prevented and collapsed the entire process. 

This error and step was found due to logging being setup on the job. A log file is creating in a shared folder in which the resource files are located for this package. In reviewing the log file the main error was

> _OnError,{servername},{account},Send Mail Task,{05CCBACE-5378-498E-86C6-E6E2E3F2F0CB},{D8067146-55AB-41F3-B9EF-5C276A05A435},4/4/2010 7:27:20 AM,4/4/2010 7:27:20 AM,-1073594105,0x,There were errors during task validation._

## Let’s review the job

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_delayval_1.gif" alt="" title="" width="501" height="431" />
</div>

The job is basic and has one mission and goal to accomplish. That goal is to run the two data flow tasks sequentially and upon successful completion, a mail task handles notifying the operator of the jobs success or failure status. Each task will cause a failure in the entire package preventing unwanted corruption of the data flows sequential nature. The reason the Send Mail Task will fail the package is due to the files that are going out with the task are the key to the package being successful. This package emails end users and we don’t want the users receiving false emails. Going further into the Send Mail Task that was failing, there is an expression set to attach two flat files that the data flow tasks create and populate with the data pulled from a database. The file names are acquired by using variables in the package so the export process is dynamic in nature. This is where the problem was. Given the variables as expressions to find the file paths, the package parser was going ahead as it should through each task and validating it was accurate before execution. When the parser would evaluate the variables in the expression, it would fail with the error we found in the log file. 

## To fix this

The fix for this expression evaluation problem is to set the delayed validation setting on the task. You can do this by highlighting the task and changing the DelayValidation property to True over the default, false value.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_delayval_2.gif" alt="" title="" width="290" height="233" />
</div>

After changing this setting the expression will not be evaluated until the task it executed. At this step in the process the variables have been populated by their own expressions and the data flow tasks have completed successfully so the files are created and located in the correct share. 

## Closing

This is a basic setting that can affect the flow of a package at execution time drastically. When setting DelayValidation, also take care in making sure the setting will not have adverse affects on the package. If another property in the task is critical and should be evaluated prior to the package execution, we can look to other methods to accomplish the same task.