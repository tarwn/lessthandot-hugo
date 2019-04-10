---
title: Setting up SQL Server with ASP.NET MVC
author: SQLDenis
type: post
date: 2010-01-17T12:18:28+00:00
ID: 677
url: /index.php/webdev/serveradmin/msiis/setting-up-sql-server-with-asp-net-mvc/
views:
  - 27740
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Microsoft IIS
tags:
  - asp.net mvc
  - sql server 2008

---
If you download [ASP.NET MVC][1] from Microsoft, install it and create a MVC website you might run into a couple of gotchas in regards to the database. First of all ASP.NET MVC use SQL Server Express by default in the connection string. So if you were to run your ASP.NET MVC website, then clicking on login you would get this error:

_<span class="MT_smaller">A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified</span>)_

A little more info below that error will tell you that it is trying to create a SQL Server Express database. Here is that info

_<span class="MT_smaller">SQLExpress database file auto-creation error: </p> 

<p>
  The connection string specifies a local Sql Server Express instance using a database location within the applications App_Data directory. The provider attempted to automatically create the application services database because the provider determined that the database does not exist. The following configuration requirements are necessary to successfully check for existence of the application services database and automatically create the application services database:
</p>

<p>
  If the applications App_Data directory does not already exist, the web server account must have read and write access to the applications directory. This is necessary because the web server account will automatically create the App_Data directory if it does not already exist.
</p>

<p>
  If the applications App_Data directory already exists, the web server account only requires read and write access to the applications App_Data directory. This is necessary because the web server account will attempt to verify that the Sql Server Express database already exists within the applications App_Data directory. Revoking read access on the App_Data directory from the web server account will prevent the provider from correctly determining if the Sql Server Express database already exists. This will cause an error when the provider attempts to create a duplicate of an already existing database. Write access is required because the web server accounts credentials are used when creating the new database.
</p>

<p>
  Sql Server Express must be installed on the machine.<br /> The process identity for the web server account must have a local user profile. See the readme document for details on how to create a local user profile for both machine and domain accounts.</span></em>
</p>

<p>
  So I don't have the Express version of SQL Server on my machine. The first thing I have to do is open the Web.config file and change the connection string from this
</p>

<pre lang="xml"><connectionStrings>
		<add name="ApplicationServices" 
			connectionString="data source=.SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;
		User Instance=true" providerName="System.Data.SqlClient"/>
	</connectionStrings>
</pre>

<p>
  to this
</p>

<pre lang="xml"><connectionStrings>
		<add name="ApplicationServices" 
			connectionString="data source=.;Integrated Security=SSPI;Initial Catalog=aspnetdb"/>
	</connectionStrings></pre>

<p>
  Now let's try again by clicking on the login page. Still a problem for me, now I get the following error
</p>

<p>
  <strong>Cannot open database “aspnetdb” requested by the login. The login failed.<br /> Login failed for user 'Denis-PCDenis'.</strong>
</p>

<p>
  Okay this makes sense since I don't have this database, I need to create it. There are 2 ways you can do this. Either navigate to C:WindowsMicrosoft.NETFrameworkv2.0.50727 and execute aspnet_regsql.exe or run the Visual Studio command prompt.
</p>

<p>
  Start –> All Programs –> Microsoft Visual Studio 2008 –> Visual Studio Tools –> Visual Studio 2008 Command Prompt
</p>

<p>
  After the Visual Studio Command Prompt is up type aspnet_regsq and hit enter
</p>

<p>
  Below is a screenshot of what it looks like if you run it from the command prompt
</p>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev//Setup1.png" alt="" title="" width="678" height="399" />
</div>

<p>
  Click Next on the first screen of the wizard, this will bring you to the screen shown below
</p>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev//Setup2.png" alt="" title="" width="579" height="450" />
</div>

<p>
  Leave the top option selected and click Next, this will bring you to the screen shown below
</p>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev//Setup3.png" alt="" title="" width="588" height="454" />
</div>

<p>
  In the Server field type your SQL Server name or type a dot if it is on your local server, type a username and password if you use sql authentication or select windows authentication if you use that. Leave the last option as it is and click Next. Now you will see a summary screen, click Next on the summary screen and then if everything went as it should you should see a Database has been created or modified screen, click finish to exit the wizard.
</p>

<p>
  Now you can go back to your ASP.NET MVC site, hit F5 in Visual Studio and click Log On, click on the register link, fill out the fields and you should see something like the screen below.
</p>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev//Setup4.png" alt="" title="" width="553" height="300" />
</div>

<p>
  Congratulations, you have now completed a small step toward you first ASP.NET MVC site, you now have a functional site but you need to add Models, Controllers and Views.
</p>

 [1]: http://www.asp.net/mvc/