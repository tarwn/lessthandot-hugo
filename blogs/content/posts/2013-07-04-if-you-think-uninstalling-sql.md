---
title: If you think uninstalling SQL Server is a pain, have fun with Oracle
author: SQLDenis
type: post
date: 2013-07-04T14:23:00+00:00
excerpt: 'Because of a job change next week I have no need for Oracle anymore, I decided to remove it. I know that some people complain that uninstalling SQL Server is a pain, well those people should just be quiet from now on, uninstalling Oracle is much worse.&hellip;'
url: /index.php/datamgmt/dbadmin/oracleadmin/if-you-think-uninstalling-sql/
views:
  - 44021
rp4wp_auto_linked:
  - 1
categories:
  - Oracle
  - Oracle Admin
tags:
  - oracle
  - sql server uninstalling

---
<p>Because of a job change next week I have no need for Oracle anymore, I decided to remove it. I know that some people complain that uninstalling SQL Server is a pain, well those people should just be quiet from now on, uninstalling Oracle is much worse.</p>
<p>In order to uninstall Oracle, you need to run the Oracle Universal Installer. Once you run it, click on the Deinstall Products button</p>
<p>Now you will see something like this</p>
<p><a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleRemove.PNG?mtime=1372946999"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleRemove.PNG?mtime=1372946999" width="438" height="434" /></a></p>
<p>
Click on the Oracle Home that you want to remove and then click on the Remove (not named uninstall or deinstall this time) button. Now you would expect that you would see another window where you could perhaps pick what to remove. But no, you get a dialog box telling you to go ahead and run a BAT file in a certain directory.  <br />
<a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleRemoveBatfile.PNG?mtime=1372947010"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleRemoveBatfile.PNG?mtime=1372947010" width="440" height="144" /></a></p>
<p>Really, you couldn&#8217;t at least have an option to run that file from withing this deinstaller?</p>
<p>Opening that BAT file reveals the following</p>
<pre>@echo off

Rem $Header: install/utl/scripts/db/deinstall.bat /st_install_11.2.0.1.0/3 2010/02/18 22:36:21 ssampath Exp $
Rem
Rem Copyright (c) 2005, 2010, Oracle and/or its affiliates. 
Rem All rights reserved. 
Rem
Rem    NAME
Rem      deinstall.bat - wrapper script that calls deinstall tool.
Rem
Rem    DESCRIPTION
Rem      This script will determine the tool directory, cd to it and call the 
Rem      deinstall.pl script
Rem
Rem    NOTES
Rem      &lt;other useful comments, qualifications, etc.&gt;
Rem
Rem    MODIFIED   (MM/DD/YY)
Rem    ssampath    01/18/10 - Pass bootstrap location to bootstrap.pl and
Rem                           deinstall.pl
Rem    vkoganol    10/14/09 - using deleyedexpansion feature to fix bug8988606.
Rem                           Alternate approach use goto
Rem    ssampath    08/24/09 - XbranchMerge ssampath_cleanup_deinstall_scripts
Rem                           from main
Rem    ssampath    08/20/09 - Comment debug statements
Rem    prsubram    08/17/09 - XbranchMerge
Rem                           prsubram_deinstall_txn_for_including_pl_file from
Rem                           main
Rem    dchriste    03/17/09 - Creation Windows version for new perl deinstall.
Rem    dchriste    03/17/09 - Creation Windows version for new perl deinstall.
Rem    ssampath    04/08/08 - 
Rem    khsingh     02/25/07 - 
Rem    aime        11/15/06 - change PATH.
Rem    aime        08/17/06 - 
Rem    ssampath    04/04/06 - Creation Windows version.
Rem

@setlocal
@setlocal enabledelayedexpansion

FOR /F "tokens=2-4 delims=/ " %%a in ('echo %date%') do (set mydate=%%c-%%a-%%b)
FOR /F "tokens=1-4 delims=.: " %%i in ('echo %time%') do (set mytime=%%i-%%j-%%k)
set dateTime=%mydate%_%mytime%

set INSTALLED_VERSION_FLAG=true

if (%INSTALLED_VERSION_FLAG%)==(true) (
      set oracleHomeVersion=1
      set bootstrapLocation=%TEMP%OraDeinstall%dateTime%
) else (
      set oracleHomeVersion=0
)

REM echo ORACLE_HOME = C:appDenisproduct11.2.0dbhome_1
REM echo oracleHomeVersion = %oracleHomeVersion%

@set currDir=%CD%
REM echo currDir = %currDir%
@set toolPath=%~dp0
REM echo toolPath = %toolPath%

if %oracleHomeVersion% == 1 (
      REM C:appDenisproduct11.2.0dbhome_1 will get instantiated to the value of "installed" home location
      ECHO Checking for required files and bootstrapping ...
      ECHO Please wait ...
      REM Execute boostrap.bat
      C:appDenisproduct11.2.0dbhome_1deinstallutlbootstrap.bat %bootstrapLocation% C:appDenisproduct11.2.0dbhome_1

      REM Execute startup.bat from %TEMP% to prevent "The system cannot find 
      REM the path specified." error because batch processing is line-by-line
      %TEMP%startup.bat %oracleHomeVersion% %bootstrapLocation% C:appDenisproduct11.2.0dbhome_1 %*
) else (
      REM Execute startup.bat
      %toolPath%utlstartup.bat %oracleHomeVersion% %toolPath% %*
)</pre>
<p>Running the file generates a bunch of output and also asks for some input</p>
<p>Here is just part of that as well</p>
<blockquote><p>Specify the storage type used by the Database ASM|FS []: ASM</p>
<p>Database Check Configuration END</p>
<p>Enterprise Manager Configuration Assistant START</p>
<p>EMCA de-configuration trace file location: C:UsersDenisAppDataLocalTempOra<br />
Deinstall2013-07-04_10-04-11logsemcadc_check.log</p>
<p>Checking configuration for database ORCL<br />
Checking configuration for database TEST<br />
Enterprise Manager Configuration Assistant END<br />
Oracle Configuration Manager check START<br />
OCM check log file location : C:UsersDenisAppDataLocalTempOraDeinstall2013<br />
-07-04_10-04-11logs\ocm_check4265.log<br />
Oracle Configuration Manager check END</p>
<p>######################### CHECK OPERATION END #########################</p>
<p>####################### CHECK OPERATION SUMMARY #######################<br />
Oracle Home selected for de-install is: C:appDenisproduct11.2.0dbhome_1<br />
Inventory Location where the Oracle home registered is: C:Program FilesOracle<br />
Inventory<br />
The following Windows and .NET products will be deconfigured from the Oracle hom<br />
e : asp.net,ode.net,odp.net,ntoledb,oramts<br />
Following Single Instance listener(s) will be de-configured: LISTENER<br />
The following databases were selected for de-configuration : ORCL,TEST<br />
Database unique name : ORCL<br />
Storage used : ASM<br />
Database unique name : TEST<br />
Storage used : ASM<br />
Will update the Enterprise Manager configuration for the following database(s):<br />
ORCL</p></blockquote>
<p>First time I ran this it failed, when I rain it again it succeeded but I still have these services on my laptop</p>
<div class="image_block"><a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleRemoveServices.PNG?mtime=1372947653"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleRemoveServices.PNG?mtime=1372947653" width="720" height="209" /></a></div>
<p>When I posted that I was uninstalling Oracle, I got a reply from Tim Ford &#8221; Its easier to just rebuild the server.&#8221;  Maybe he is right??</p>
<p>Here is also a link to someone&#8217;s post about a last resort method http://www.oracle-base.com/articles/misc/manual-oracle-uninstall.php</p>
