---
title: When is DTEXEC installed?
author: Koen Verbeeck
type: post
date: 2013-02-12T11:12:00+00:00
ID: 1988
excerpt: "Which minimal components do you need to install to run SSIS packages on a server? Let's find out!"
url: /index.php/datamgmt/dbprogramming/mssqlserver/when-is-dtexec-installed/
views:
  - 36341
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - SSIS
tags:
  - dtexec
  - installation
  - integration services
  - service
  - sql server
  - ssis

---
<p style="text-align: justify;">
  Or in other words, what do I need to install during SQL Server setup in order to end up with a fully operational SSIS server with a minimal surface area configuration? I came up with this blog post after I participated in the following thread at the MSDN forum: <a href="http://social.msdn.microsoft.com/Forums/en-US/sqlintegrationservices/thread/4f29e3a3-fc8a-465c-8acc-12ecedfcb24c">“What&#8217;s the minimum configuration to run dtexec on windows 2003 server?”</a> <a href="http://msdn.microsoft.com/en-us/library/ms162810(v=SQL.105).aspx">DTEXEC</a> is the command line utility used behind the scenes to run SSIS packages. The question asker thought only the client tools needed to be installed, but I was not sure of this. Time to investigate!
</p>

<p style="text-align: justify;">
  <strong>Methodology</strong>
</p>

<p style="text-align: justify;">
  I created two easy packages, called SimpleTest and AdvancedTest. SimpleTest reads a flat file, adds a timestamp and writes the results to another flat file.
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DTEXEC_install/simplepackage.PNG?mtime=1360669546"><img src="/wp-content/uploads/users/koenverbeeck/DTEXEC_install/simplepackage.PNG?mtime=1360669546" alt="" width="152" height="206" /></a>
</div>

<span style="text-align: justify;">The AdvancedTest package reads the same flat file, but uses a term extraction component to do a term based analysis and it writes the results to a flat file. This package is used to see if there’s any difference when an Enterprise-only  component is used (see </span><a style="text-align: justify;" href="http://msdn.microsoft.com/en-us/library/cc645993(v=sql.105).aspx">Features Supported by the Editions of SQL Server 2008 R2</a><span style="text-align: justify;">).</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DTEXEC_install/advancedpackage.PNG?mtime=1360669554"><img src="/wp-content/uploads/users/koenverbeeck/DTEXEC_install/advancedpackage.PNG?mtime=1360669554" alt="" width="288" height="299" /></a>
</div>

<span style="text-align: justify;">The server is a Windows 2008R2 64-bit server. The edition of SQL Server being installed is SQL Server 2008R2 Developer edition.</span>

<p style="text-align: justify;">
  To test if DTEXEC is installed and if it works correctly, the following commands are used in a command window:
</p>

> _<span style="font-size: small;">dtexec /F “c:SimpleTest.dtsx” > SimpleLog.txt</span>_

<p style="text-align: justify;">
  and
</p>

> _<span style="font-size: small;">dtexec /F “c:AdvancedTest.dtsx” > AdvancedLog.txt</span>_

<p style="text-align: justify;">
  These commands execute a package located on the file system and output the results to a text file.
</p>

<p style="text-align: justify;">
  <strong>Client Tools only</strong>
</p>

<p style="text-align: justify;">
  The first install is the client tools, with which I mean BIDS (Business Intelligence Development Studio) and SSMS (SQL Server Management Studio). Later on I learned the question asker meant everything listed under <em>Shared Features</em>, which is a whole lot more than just the client tools. First I installed the Management Tools only.
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DTEXEC_install/ManagementTools_Install.png?mtime=1360669453"><img src="/wp-content/uploads/users/koenverbeeck/DTEXEC_install/ManagementTools_Install.png?mtime=1360669453" alt="" width="490" height="369" /></a>
</div>

 <span style="text-align: justify;"></span>

<p style="text-align: justify;">
  I took a look in the Program Folders, but I only found a 32-bit DTEXEC. Is this enough to run a package?
</p>

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/koenverbeeck/DTEXEC_install/ManagementTools_Result.png?mtime=1360669459" alt="" width="435" height="189" />
</p>

<p style="text-align: justify;">
  When the DTEXEC statements are executed in a command window, we are greeted with the following error message:
</p>

> _<span style="font-size: 9pt;">Description: To run a SSIS package outside of Business Intelligence Development Studio you must install Standard Edition of Integration Services or higher.</span>_

<p style="text-align: justify;">
  So no luck with this method. When DTEXECUI is launched, everything is grayed out and another straightforward error message tells us we really do have to install Integration Services.
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DTEXEC_install/ManagementTools_DTEXECUI.png?mtime=1360669438"><img src="/wp-content/uploads/users/koenverbeeck/DTEXEC_install/ManagementTools_DTEXECUI.png?mtime=1360669438" alt="" width="411" height="348" /></a>
</div>

<span style="text-align: justify;">However, the Import/Export wizard is also installed. When I import a flat file to another flat file – the workflow used by the SimpleTest package – the wizard executes successfully:</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DTEXEC_install/ManagementTools_importwizard.png?mtime=1360669446"><img src="/wp-content/uploads/users/koenverbeeck/DTEXEC_install/ManagementTools_importwizard.png?mtime=1360669446" alt="" width="389" height="393" /></a>
</div>

<span style="text-align: justify;">The only reason DTEXEC was installed is to run the Import/Export wizard. Installing BIDS as well doesn’t solve the issue, as expected. You can run packages within BIDS, but I guess nobody is excited about staying up all night in order to manually kick off SSIS packages. So basically we have installed a lightweight development environment, but not a server.</span>

<p style="text-align: justify;">
  <strong>SQL Server only</strong>
</p>

<p style="text-align: justify;">
  Stubborn as I am, I ignore the logical error messages I got earlier and I install the SQL Server database engine, without any shared feature whatsoever. This time the 64-bit DTEXEC is installed. We’re making progress. When I start the SimpleTest package, the log shows the following:
</p>

> <p style="text-align: justify;">
>   <span style="font-size: 9pt;"><em>The component &#8220;(DER) Add timestamp&#8221; (38) cannot run on installed (64-bit) of Integration Services. It requires Standard Edition (64-bit) or higher.</em></span>
> </p>

<p style="text-align: justify;">
  The error doesn’t make much sense, since I haven’t installed Integration Services at all and Developer Edition is a higher edition than Standard (in functionality, not in price). Running the AdvancedTest package results in the following:
</p>

> <p style="text-align: justify;">
>   <span style="font-size: 9pt;"><em>Warning: 2013-01-16 14:49:02.56  Code: 0xC0048000<br /> &nbsp;&nbsp; Source: (DFT) Throw data around (DFT) Throw data around (SSIS.Pipeline)<br /> &nbsp;&nbsp; Description: The registry key &#8220;SOFTWAREClassesCLSID{119D450D-E2A3-4DB0-A7BC-ACDE2536673E}DTSInfo&#8221; cannot be opened.<br /> End Warning<br /> Warning: 2013-01-16 14:49:02.56   Code: 0x8004801E<br /> &nbsp;&nbsp; Source: (DFT) Throw data around (DFT) Throw data around (SSIS.Pipeline)<br /> &nbsp;&nbsp; Description: Cannot find the &#8220;CurrentVersion&#8221; value for component {119D450D-E2A3-4DB0-A7BC-ACDE2536673E}. The CurrentVersion value for the component cannot be located. This error occurs if the component has not set its registry information to contain a CurrentVersion value in the DTSInfo section. This message occurs during component development, or when the component is used in a package, if the component is not registered properly.<br /> End Warning<br /> Error: 2013-01-16 14:49:02.56   Code: 0xC0048020<br /> &nbsp;&nbsp; Source: (DFT) Throw data around (DFT) Throw data around (SSIS.Pipeline)<br /> &nbsp;&nbsp; Description: The version of component &#8220;(TEX) Extraction FirstName&#8221; (77) is not compatible with this version of the DataFlow.<br /> End Error<br /> Error: 2013-01-16 14:49:02.56   Code: 0xC0048020<br /> &nbsp;&nbsp; Source: (DFT) Throw data around SSIS.Pipeline<br /> &nbsp;&nbsp; Description: The version of component &#8220;(TEX) Extraction FirstName&#8221; (77) is not compatible with this version of the DataFlow.<br /> End Error<br /> …<br /> Error: 2013-01-16 14:49:02.56  Code: 0xC0048021<br /> &nbsp;&nbsp; Source: (DFT) Throw data around (TEX) Extraction FirstName [77]<br /> &nbsp;&nbsp; Description: The component is missing, not registered, not upgradeable, or missing required interfaces. The contact information for this component is &#8220;&#8221;.<br /> End Error<br /> Error: 2013-01-16 14:49:02.56   Code: 0xC0047017<br /> &nbsp;&nbsp; Source: (DFT) Throw data around SSIS.Pipeline<br /> &nbsp;&nbsp; Description: component &#8220;(TEX) Extraction FirstName&#8221; (77) failed validation and returned error code 0xC0048021.<br /> End Error<br /> </em></span>
> </p>

<p style="text-align: justify;">
  First we get a whole bunch of warning related to the registry, followed by errors related to the Term Extraction component, leading to a validation failure at the end. The Enterprise-only component leads to different errors, but the result is the same: we cannot run SSIS packages using DTEXEC.
</p>

<p style="text-align: justify;">
  So why is DTEXEC installed? First of all, to run the 64-bit version of the Import/Export wizard, but also to support maintenance plans, who use SSIS behind the scenes. Since SQL Server 2005 sp2 or SQL Server 2008 CU1/sp1, it is not necessary to install SSIS to create maintenance plans. Read more about this in the blog post <a href="http://sqlblog.com/blogs/tibor_karaszi/archive/2009/08/26/do-maintenance-plans-require-ssis.aspx">Do maintenance plans require SSIS?</a> by Tibor Karaszi (<a href="http://sqlblog.com/blogs/tibor_karaszi/default.aspx">blog</a> | <a href="https://twitter.com/TiborKaraszi">twitter</a>).
</p>

<p style="text-align: justify;">
  <strong>Integration Services only</strong>
</p>

<p style="text-align: justify;">
  Now let’s try to do the sane thing here and install SSIS, without any other option specified.
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DTEXEC_install/ssis_only_install.png?mtime=1360669468"><img src="/wp-content/uploads/users/koenverbeeck/DTEXEC_install/ssis_only_install.png?mtime=1360669468" alt="" width="439" height="322" /></a>
</div>

<span style="text-align: justify;">DTEXEC is installed, as expected of course, alongside the Integrations Services service. Running DTEXEC through the command line gives us the result we were hoping for:</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DTEXEC_install/SSISonly_serviceON_dtexec_success.png?mtime=1360669481"><img src="/wp-content/uploads/users/koenverbeeck/DTEXEC_install/SSISonly_serviceON_dtexec_success.png?mtime=1360669481" alt="" width="518" height="245" /></a>
</div>

<span style="text-align: justify;">Success at last! DTEXECUI isn’t installed, indicating this is a client component only. This is OK, because you probably won’t ever use it on a server.</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DTEXEC_install/SSISonly_nodtexecui.png?mtime=1360669475"><img src="/wp-content/uploads/users/koenverbeeck/DTEXEC_install/SSISonly_nodtexecui.png?mtime=1360669475" alt="" width="373" height="211" /></a>
</div>

<span style="text-align: justify;">One final question remains: do we need the SSIS service? The answer is short: no. According to </span><a style="text-align: justify;" href="http://support.microsoft.com/kb/942176">Microsoft</a><span style="text-align: justify;">, the SSIS service only extends the functionality of SSMS, in the sense that it manages the storage of SSIS packages and that it monitors the running packages. So you can safely stop the service. However, you need to disable it as well; otherwise the service is started again once you run a package.</span>

<p style="text-align: justify;">
  <strong>Conclusion</strong>
</p>

<p style="text-align: justify;">
  Only when you want to set up a dedicated SSIS Server will you need to install Integration Services on its own, and if you do this you’ll need to store your packages on the file system. You can disable the Integration Services service; it is not needed to execute a package. You also might want to install the client connectivity tools as well and any other component you might need to connect to your sources.
</p>