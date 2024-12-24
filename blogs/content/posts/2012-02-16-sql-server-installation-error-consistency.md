---
title: SQL Server Installation Error – Consistency validation for SQL Server Registry keys
author: Ted Krueger (onpnt)
type: post
date: 2012-02-16T11:39:00+00:00
ID: 1530
excerpt: |
  I was installing an instance of SQL Server on a VM this afternoon and ran into the following error
  Rule "Consistency validation for SQL Server registry keys" failed.
   
  The error isn't very common, that I knew of.  Before we go about why this error di&hellip;
url: /index.php/datamgmt/datadesign/sql-server-installation-error-consistency/
views:
  - 15873
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I was installing an instance of SQL Server on a VM this afternoon and ran into the following error

Rule "Consistency validation for SQL Server registry keys" failed.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-113.png?mtime=1329268052"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-113.png?mtime=1329268052" width="624" height="344" /></a>
</div>

The error isn't very common, that I knew of.  Before we go about why this error did actually happen and how to fix it in the case of this installation, let's go through troubleshooting an error that occurs in the setups rules validation steps.

**Troubleshooting Rules Validation Steps**

During the setup process for SQL Server, the folder Setup Bootstrap directory is utilized heavily to monitor the state of the installation.  This directory will retain another directory named Log.  In the Log directory, another directory is created to hold all the logs of that setup instance.  This is where we need to go for the logs to troubleshoot this error.  It's important to note that these directories do not get removed when the setup either rolls back or the setup is successful.

Each log directory will be named as follows

Yyyymmdd_hhmmss

In the case of the error regarding the registry key validations, the log that was created was, "C:Program FilesMicrosoft SQL Server100Setup BootstrapLog20120214\_121951".  Within this directory, there are two logs that can be focused on at first, Detail.txt and Detail\_GlobalRules.txt.

If you have installed SQL Server, you will have one or more directories in the Log folder already.  Focus on those to follow along with this troubleshooting exercise.

Open the Detail_GlobalRules.txt and search for the word, "Registry".

In the Detail_GlobalRules.txt file that relates to the error noted in the beginning of this article, the lines that show the rules validation error and related information are found.

The information found here is valuable but in reality, it is only a review of what the installer has already shown us; the error and a slight and not very helpful, description.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-114.png?mtime=1329268053"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-114.png?mtime=1329268053" width="624" height="359" /></a>
</div>

Open the Details.txt file next and search for the same keyword.  In the case below, the keyword was expanded to "Registry Key" to help refine the search.

Here we can see that the actual registry keys that failed the validation are shown.  This is invaluable information!  One of the common fixes to this error that was found on searching related errors on MSDN, was security related.  In some cases, opening regedit to edit the registry and applying security access to the keys listed, resolved the errors.

Although that resolution would be a fix, it was only a clue in this case.  If you look closely, a UNC path was used to access an admin share on another machine for the setup files.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-115.png?mtime=1329268054"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-115.png?mtime=1329268054" width="624" height="276" /></a>
</div>

Looking closer into the Detail.txt log, we can see the reference to AclPermissionsFacet.  This is another clue that the security resolution is on the right track. With the knowledge of the permissions error and the UNC and admin share usage in hand, we can come to a step to attempt as a resolution of our own.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-116.png?mtime=1329268054"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-116.png?mtime=1329268054" width="624" height="83" /></a>
</div>

Since this installation was run from a remote drive by accessing it via the admin share.  E.g. \192.168.1.100f$ and with the machine that had the setup files on it existing in another work group, security was passed to access those files by manually entering an account on the remote machine that could do this.  This is fine for most installations but in this case, the credentials appeared to be used to attempt to alter the registry during the validation rules steps.

**Resolving this issues**

The fix for this particular error was to move the setup files to the local machine that SQL Server was going to be installed on.  Once this was done, re-running the validation rules step was successful given the proper accounts were accessing the entire requirements in files, registry and overall validations, was used.

It is common to have a software folder that holds all of the IT groups installation files and setup executable.  On a domain, this is fine when the user accessing them has the needed security.  Even in this however, some things in the installation process can become an issue.  Running the installation from the machine that software is being installed to is a stable method to prevent these things.  Ensure that is company policies dictate, that all software installation files and setups are removed once you are completed with them.