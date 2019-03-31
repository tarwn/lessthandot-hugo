---
title: 'Making SSIS Dynamic: Configuration Management'
author: Ted Krueger (onpnt)
type: post
date: 2010-08-18T09:52:32+00:00
excerpt: "Every task that is out there to enable SSIS to be more, 'dynamic', ensures that we can develop reusable and more manageable solutions.  Variables and configuration files along with all package configurations are all part in allowing packages to become dynamic in this way.  You may be thinking right now that it would be cool to something like a date variable so you could use it in an Execute SQL Statement or such.  That and a lot of other common uses are indeed, all part of making a package dynamic.  However, we can push this much further and bring out the essential foundation of configurations and variables in working freely from environment to environment."
url: /index.php/datamgmt/datadesign/making-ssis-dynamic-configuration-manage/
views:
  - 27645
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - configuration files
  - configuration variables
  - dtsx
  - parent child packages
  - sql server 2008
  - ssis

---
Every task that is out there to enable SSIS to be more, &#8216;dynamic&#8217;, ensures that we can develop reusable and more manageable solutions. Variables and configuration files along with package configurations are all part in allowing packages to become dynamic in this way. You may be thinking right now that it would be cool to have something like a date variable so you could use it in an Execute SQL Statement or such. That and a lot of other common uses are indeed, part of making a package dynamic. However, we can push this much further and bring out the essential foundation of configurations and variables in working freely from environment to environment.

To get going, let’s look at one of the most common settings you will have internal to your packages: database connections. The connection management in SSIS allows us to completely set all the key properties of the connections internally from the use of configuration management.

**The example:**

Create a new package to start adding and editing some tasks. In the package, create an ADO.NET connection named, ServerSourceConnection. Set this connection to an available instance and save the connection. To configure this to be manipulated with a configuration file, right click an empty space in the Control Flow window and select, Package Configurations…

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_1.gif" alt="" title="" width="319" height="227" />
</div>

Click the check box to &#8220;Enable package configurations&#8221; and then click, Add.

Clicking the add button will launch the Package Configuration Wizard (PCW). The Wizard and many others in SSIS cover all the settings very well without having to go through a lot of manual work. Remember, we want to be efficient. Don’t be afraid of using the wizards for configuring SSIS. 

In the PCW, we want to select an XML configuration file as our type of configuration. Next, if you have a preexisting configuration file to add to, you can enter it into the configuration file name box. If you do not, simply enter a physical path and file name where you want the package stored for now.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_2.gif" alt="" title="" width="628" height="227" />
</div>

Click next to move onto the properties that can be set. For the example, we want to configure the connection ServerSourceConnection. To do this we need to know server name, default catalog, connection string and, if you are not using Windows authentication, the username and password. 

> <span class="MT_orange">Note: It is not recommended to store the passwords here. Although the encryption of the passwords will be performed on saving the package, it is still a good idea to secure the package and connection so the databases by forcing this to be entered later.</span> 

In the properties dialog, expand Connection Managers and then expand into the ServerSourceConnection. This will expose the properties we can check that we want to be included in the configuration file. Check ConnectionString, InitialCatalog and Servername. Then click Next to exit the properties section. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_3.gif" alt="" title="" width="458" height="323" />
</div></p> 

After hitting next, all that remains is to set the name of the configuration and save the configuration file. We’ll use, ServerSourceConfigFile and then hit Finish to save and exit the wizard.

So far we haven’t changed anything as far as the connection goes. If you open the connection, you will see the same settings you originally saved. If we execute a basic Execute SQL Task, we will see in the Message panel that SSIS attempts to connect to our configuration file and read any differentiating configurations that we have set from it. This is because the configuration file will obtain the settings that were previously set in the connection (or other objects you set) and place them in the values of the configuration file XML ConfigurationValue #text areas. 

We want to show this in real-time though so let’s change the XML configuration file to point to a different instance than the one we set in the Connection.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_4.gif" alt="" title="" width="628" height="209" />
</div>

Above we changed the values in the connection string to handle the instance name, the default catalog and the server name. Notice that we didn’t change the default catalog in the connection string. This is to show that the catalog setting of the property outside the connection string will still override the actual value in the connection string itself. 

There is one catch here we need to go over. Configuration files can be wrong while in development and you have not saved or executed a package. (In a way, a timing issue with what the state and context your package is while creating it) If your configuration file is found to be wrong and the connection cannot be made, SSIS will look to what you’ve set internally in the normal configuration of the connection string. This is primarily a problem with the IDE. So at this point, save everything before executing and pushing on.

**Showing Execution**

Of course we want to make sure this is all reading from the configuration file, so let&#8217;s enter an incorrect database in the configuration file to show a failure. 

In your configuration file, enter a database that does not exist.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_5.gif" alt="" title="" width="610" height="82" />
</div>

Of course, if for some reason you decided last week to create a database named, IdoNotExist, try something else.

Now add logging to the package to capture the information as the execution of the package flows. 

Do this by right clicking an empty space in the Control Flow and selecting, Logging…

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_6.gif" alt="" title="" width="351" height="126" />
</div>

Enable logging by selecting the Package container and click Add for a new Text File provider. In the Details tab of the logging provider, check Diagnostic. This will enable us to see log entries when the connection is read.

Now that we have logging setup, we can watch the connection being called. 

To test that, set the following basic Execute SQL Task up.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_7.gif" alt="" title="" width="628" height="324" />
</div>

> <span class="MT_red">Note: We are selecting from the sys.databases which requires heightened security rights on the server state. If you do not have these rights, SELECT from any table. The task and test here is to simply return a small amount of data to validate our configuration changes.</span></p>
Execute the task and follow along in the Message panel

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_8.gif" alt="" title="" width="628" height="252" />
</div>

Reading our log, we can see that the incorrect catalog was used. If you open the configuration of the connection now from the connection manager window, the incorrect database would have been set there as well at run-time. 

> ConnectionString: Data Source=LKFW0133TK2005;Initial Catalog=IdoNotExist;Integrated Security=True;

**OK, make it green now**

Switch the default catalog back to master or an existing database you have rights to connect to and run the package again

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_9.gif" alt="" title="" width="628" height="148" />
</div>

**Accomplishments**

The main objective we accomplished today is to provide a way to move this and other packages from servers like development, staging, user acceptance testing and production. This all being done without any need to open the package but instead, edit our XML configuration file and pointing to the other servers and databases. 

Next, we’ll discuss variables between packages. By passing variables between packages we can create a highly dynamic flow and ability to reuse template packages at will for tasks. Look for that article soon.</p>