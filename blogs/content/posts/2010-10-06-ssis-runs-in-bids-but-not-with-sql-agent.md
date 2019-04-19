---
title: SSIS runs in BIDS but not with SQL Agent
author: Ted Krueger (onpnt)
type: post
date: 2010-10-06T09:54:16+00:00
ID: 916
excerpt: The title of this article is one that is asked on many occasions around the forums and SQL Server community.  SQL Server Integration Services (SSIS) is the Extract, Transform and Load (ETL) platform behind SQL Server.  There aren't many arguments against SSIS as a great tool, and it has the ability to get the job done as an ETL platform.  With the added complexity of any product, pain is involved while becoming familiar with the intricacies of it.  One pain that comes with any development practice that is performed within one set of environment and system variables and later moved to another set (Transport process) - a process can execute successfully in one location and fail in another.
url: /index.php/datamgmt/dbprogramming/ssis-runs-in-bids-but-not-with-sql-agent/
views:
  - 38624
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql server 2008 r2
  - sql server agent failure
  - sql server integration services
  - ssis

---
The title of this article is one that is asked on many occasions around the forums and SQL Server community. **SQL Server Integration Services** (SSIS) is the **Extract, Transform and Load** (ETL) platform behind SQL Server. There aren't many arguments against SSIS as a great tool, and it has the ability to get the job done as an ETL platform. With the added complexity of any product, pain is involved while becoming familiar with the intricacies of it. One pain that comes with any development practice that is performed within one set of environment and system variables and later moved to another set (Transport process): a process can execute successfully in one location and fail in another. 

The one thing that we have working for us when working on transport processing is a working shell of the development area. This shell contains all the information we need in order to troubleshoot the reasoning for failure on after the transport has been performed. Taking the case of an **SSIS package** (DTSX) executing successfully in your development area and unsuccessfully in the production area leaves the case in which we go back to listing the settings of the package itself and environment itself to determine the cause.

In order for us to start digging into troubleshooting the topic at hand of an SSIS package running in **Business Intelligence Development Studio** (BIDS) but not with the SQL Server Agent, we will first come up with a package to work with. BIDS is nothing more than a shell of Visual Studio with additional projects that allow us to develop for SQL Server. With the installation of client tools, you can obtain the version of BIDS from the installation media version of SQL Server. Some important facts: BIDS only installs as a 32bit version. Visual Studio 2010 will not allow for the manipulation or creation of SSIS (or any feature of SQL Server) objects. The good part to version editing issues in newer full versions of Visual Studio: all versions of BIDS and full installations of Visual Studio run well side-by-side.

Below will outline a process that will consume a flat file with three columns of string type data. Those three columns of data will be sent to distinct output areas based on a header name for each column. The header names will be header1, header2 and header3. The output will be three distinct tables named the same as the header names. For header1, the output will flow to table header1 and so on.

Our execution in BIDS is good at this point and we are successful in the development stage of our process.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis agent fail 1.gif" alt="" title="" width="312" height="199" />
</div></p> 

Now we turn to creating a SQL Server Agent job to call an SSIS step. This step connects to the MSDB in which our package has been deployed to. Upon execution, we are faced with a different result:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis agent fail 2.gif" alt="" title="" width="529" height="170" />
</div></p> 

Now that we have paved the road to troubleshoot this error, we can take a normal approach that most troubleshooting will take.

  1. Validate Security
  2. Validate System Settings
  3. Validate External Resources
  4. Validate Internal Resources
  5. Validate the Package Execution Itself

Notice that validating the actual package is last. Why is that? Well, we know the package can run successfully already given the right system variables behind it. We've provided the proof of concept there from execution being successful in BIDS.

Several of these steps cross over each other. For instances, security will directly affect determining external results (as we will see later). Many times during troubleshooting these types of problems, one solution is referred to many times from the other steps. The important thing to always remember when troubleshooting in a technical setting is to change one thing, document that change and test. Do not fall into the habit of changing several things at once before validating the change actually is the resolution. Answers are hidden from us still when the process is done in that manner.

In our approach listing we can evaluate a new list directly related to SSIS.

  1. Authentication – Basics
  2. ProtectionLevel – Internal Package Security
  3. Variable access and Configuration file access. Environmental variables, Registry, Configuration files
  4. Supporting objects
  5. 32-bit vs. 64-bit
  6. Double HOP – loss of authentication
  7. External calls (not configuration file based)
  8. OS level calls when using DTExec.exe and security

Now that we have our starting point we can show the failure and common resolutions to each of them.

**Authentication basics** revolve around anything that deals with the credentials that are being used to execute the package itself. The SQL Server Agent by default runs under a service account. In many instances this service account is set as a domain account. That domain account is also commonly created specifically for the task of running the agent. While running the package in BIDS, the execution is performed under the users' account that is logged into the machine at that time. In our packages case that is an account that is set in the local administrators group. In the package we created, there are two primary points in which the authentication of the account used to execute the package will be required. The first is a share that was created to hold all the import files that are read during the import process. That share was created specifically to secure the sensitive files. While the share was accessed successfully by the means of the local administrators group and the account logged in while working in BIDS, the SQL Server Agent Service Account is now being used to attempt retrieval of the files.

  * Fixing the case of the SQL Server Agent Account access has two paths. 
  * Alter the share to add the security required so the account will pass through successfully

Use a proxy on the step that is calling the SSIS package. The proxy will use a designated account that already has access to the share. Although bad practice, the account that was used to develop the package can be used for test purposes. This is considered bad practice because a user account is considered unstable, primarily in the sense that it can change at will. One volatile variable to use of a user account for a proxy is the saved password while setting the proxy up. This password may change at will based on the users' actions while not automating the process of updating the proxy settings.

Another security point is based on the internal security of the package itself and how passwords for things such as connection managers are saved. This is done by using the setting, ProtectionLevel. By default this is set to, “EncryptSensitiveWithUserKey”. By using this key the user profile that created the package is taken to encrypt anything needed. The primary failure with this being left as the default and not handling the effects will be shown when moving the package from one machine to another while the user profile that created the package is not the one that opens it for execution.

To resolve the ProtectionLevel related errors there are several options. The best practice would be to reevaluate the ProtectionLevel setting itself and determine the proper setting to use. For troubleshooting purposes, setting this value to, “DontSaveSensitive” and executing the package will verify if the ProtectionLevel used is the cause.

> <span class="MT_red">Note: Using the Protection Level of DontSaveSensitive is not considered a best practice due to this setting bypassing all encryption of sensitive data in the package.</span> 

Refer to [BOL entry][1] for further clarification on the ProtectionLevel setting and options available.

Following the pattern of security related failures, configurations in SSIS may also be a direct security related cause of a package failing.

> <span class="MT_red">Note: Before going on; SSIS configurations can and will be bypassed if set but not available for local settings in the package. Be sure to set internal values while using configurations to allow for the package to fail if the configuration is required for successful processing.</span> 

There are several ways SSIS can store configurations to force reusability and the dynamic setting of properties and values to ensure our package processing is done as we intended it to.

**Configuration Types**

  1. XML Configuration Files
  2. Environment Variables;
  3. Registry Entry
  4. Parent Package Variables
  5. SQL Server Tables

We have two security paths to troubleshoot with configuration files. 

  * Security access to the local system based on the type of configuration saved to the local system.
  * Security access to the SQL Server Instance that has been used to store the configurations.

To resolve these types of problems that cause agent failures deals with the same changes to either heightened rights to the service account or proxy utilization. Altering the configurations or types of configurations used in the package can be a resolution. Altering what works may not be the best course if there was a given security reason the type was used.

Earlier in this article it was mentioned that in order to develop with BIDS and SQL Server project types, SQL Tools is required to be installed. This includes SSIS objects (as well as the service for interacting with SSIS from wizards and SSMS). When developing SSIS in one location and deploying to another, supporting objects may be missing. This is common when using DTEXEC to call your package and not ensuring all support is available for the entire process you have developed.

Resolution is as with the development machine, to install the required SSIS components. This problem is rare as deploying to an instance to execute SSIS packages that does not have SSIS installed is not common.

Windows Server prior to 2008 R2 came in both **32-bit and 64-bit versions**. These two platforms are very different in internal processing and resource allocations. With the need for more resources in developer machines, 64 bit Windows versions are almost completely taking over as a normal platform at the desktop level. This poses another problem to cross platform transports. The problem arises with the change in platform from 32 bit and 64 bit platform change in transports.

_Example: Developer starts to write a package to import a flat file much like the one used earlier in this article. Once the Data Flow task has been created and the source is set, OLEDB destinations are utilized._

In the situation above, the package will not execute on the developers machine while under the 64 bit platform. Resolving that is a project setting to set the execution of 64 bit mode to false. (Essentially executing with the 32 bit components) More importantly, when this package is executed successfully in BIDS and then transferred to a Windows Server 64 bit version, the package will again fail by using the agent with default settings.

To resolve this problem of transport and running in 32 bit mode on a 64 bit Windows Server version: SSIS tools install be default a 32 bit version of DTEXEC which can be used to call a package in 32 bit mode. This can also be done by using a setting in the agent step itself found in the Execution Options, “Use 32 bit runtime”.

**Double Hop** problems exist when windows authentication is used and the process in which passing credentials from a calling mechanism to a second forces another request to a third resource or server.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis agent fail 3.gif" alt="" title="" width="473" height="131" />
</div></p> 

In this situation, the resolution is to either change the landscape or resolve with implementing Kerberos Authentication. 

Errors that may or may not accompany double hop situations are typically involving failed authentication while sending a valid windows account but resulting in the _NT AUTHORITY/ANONYMOUS LOGON_

More detailed information on [Kerberos setup][2]

**External (or remote) Resources Calls** and Operating System level calls occur with the same level of problems as a lot of the steps we have covered already; security. We bring in a new layer of problems with external resources in network related blocking. A well maintained and secured network will only allow the communication that is documented and known. Calling resources such as FTP or Web Services that exist outside the internal network may go through different policies from a development machine than a server is allowed to perform. Local firewall settings also play a large part in this. Windows Firewall and all other virus and firewall software that is deployed should be a point of interest when returning failures that revolve around failed access of these resources.

Resolving these problems may or may not be a long process. Network and server groups must be enlisted to either set the configurations that are known already or determine the access that is being attempted. Once this is done, if the configuration is a known change, the change itself may be quick. If it requires additional resources in figuring out how to make the change and retain the stability and security of the server, time may be prolonged even more.

**OS level** calls when using DTExec.exe is a topic that brings us full circle to the SQL Server Agent Service Account again. Like many objects that are utilized locally from the operating system, DTEXEC is located by default in the SQL Server Binn directory under the DTS folders. In some cases, the agent service account may have a problem with accessing this directory.

Resolving this is to provide the rights to the service account. Although this problem is rare, it is a bullet point to rule out. Given the interaction with most services that the SQL Server Agent enlists in order to run successfully with other tasks, this problem may be found in an earlier error on other processing. Start up may even fail and then the problem would be found and resolved at that point.

**Conclusion**

The common problems and resolutions covered will help in stepping through SSIS packages failing to run when scheduled in the SQL Server Agent. Cases do arise when much more in-depth troubleshooting is required and these resolutions do not apply. When they are exhausted, stay on track with, one change at a time fundamentals.

 [1]: http://msdn.microsoft.com/en-us/library/ms141747.aspx
 [2]: http://technet.microsoft.com/en-us/library/cc780469(WS.10).aspx