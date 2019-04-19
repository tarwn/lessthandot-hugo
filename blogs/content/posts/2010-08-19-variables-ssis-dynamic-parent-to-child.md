---
title: 'Making SSIS Dynamic: Passing variables between packages'
author: Ted Krueger (onpnt)
type: post
date: 2010-08-19T11:03:16+00:00
ID: 879
excerpt: 'So far, we showed how to manipulate connections so we can freely move packages from server to server without the need to open them in BIDS and change connections.  This allows us to manage packages easily, not only from a development stand point, but allows for teams to pass along our packages and then move them without being required to either open them or have BIDS to change these critical connections.'
url: /index.php/datamgmt/datadesign/variables-ssis-dynamic-parent-to-child/
views:
  - 45666
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
So far, we showed [how to manipulate connections][1] so we can freely move packages from server to server without the need to open them in BIDS and change connections. This allows us to manage packages easily, not only from a development stand point, but allows for teams to pass along our packages and then move them without being required to either open them or have BIDS to change these critical connections.

Another method to push dynamically setting package executions is to utilize multiple packages. Using multiple packages in an overall process allows for reuse of packages themselves. This also allows us to manage processes that are much larger than normal. Consider a process that handles dozens of tasks that prepare and manipulate data so it can be uploaded into several different unique databases. To further promote this, imagine these databases are remotely distributed across your wide area network. By setting a parent package to handle the data forming and validations, we can then pass result sets down to several other packages preset as basic Data Flow packages to import a set result set into a database.

We could do this in one package by configuring each connection including flat files, sources and destinations. However, we could also do this by means of passing to a package the destination variables themselves. This way we can reuse a package for multiple other tasks if we wanted to.

**Showing Parent to Child in action**

To show how we can do this, we will use another form of configurations management to pass variables from the parent package to the child package. Let's build an example to pass ServerName and DefaultCatalog for another connection.

First, add a new package to the existing solution we started manipulating the connection with configuration files on. If you did not run through that example, create a new solution and add a new package. Leave the default, package name. Now create another package and again, leave the default, package1.dtsx name.

Drag an Execute Package Task over into the Control Flow. Double click the new task to launch the Execute Package Task Editor. Select the Package window from the left panel, select File System from the Location list and then drop down Connection and click, new connection

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_10.gif" alt="" title="" width="513" height="127" />
</div>

In the File Connection Manager Editor, click the Browse button. By default, new packages that are created as files are stored in the product folder. If this doesn't default to that location, browse to your projects folder. Select Package1.dtsx for the file and click OK to save the new package connection.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_11.gif" alt="" title="" width="499" height="397" />
</div>

Open Package1.dtsx in the designer now and add a connection to the Connection Managers area. If you have never added a connection to an SSIS package, right click the Connection Managers area in the middle lower area of the designer. Select the type of connection you want to use then. You can use the previous connection created in the last article if it is easier.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_12.gif" alt="" title="" width="499" height="124" />
</div>

Next we will need the same variables (names are not required to be the same from parent to child). Go to the Variables dialog. The Variables editor is located to the left of the default BIDS environment and can be viewed by clicking the, Variables tab near the lower part of the window. Create two variables, ServerName and DefaultCatalog, both with a data type of String. Leave the default values empty.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_13.gif" alt="" title="" width="223" height="91" />
</div>

Now we are ready to use the variables to set the connection properties. Highlight the connection in the Connection Managers window so the properties dialog shows all the available properties we can set. Click the expressions textbox to open up the expression editor. (as shown below)

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_14.gif" alt="" title="" width="628" height="215" />
</div>

Set ConnectionString to the following expression

```
"Data Source=" + @[User::ServerName]  + ";Initial Catalog=" + @[User::DefaultCatalog]   + ";Provider=SQLNCLI10.1;Integrated Security=SSPI;"
```

Then make ServerName and InitialCatalog the value of the matching variables.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_15.gif" alt="" title="" width="758" height="242" />
</div>

In order to pass the variables to the child package, Package1.dtsx, we need to go into the child package and configure the variables from the parent package to be assigned to the variables local to the child.

To do this, right click an empty space in the Control Flow window and select Package Configurations. Click Add to open a new configuration in the Package Configuration Organizer window that is launched.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_16.gif" alt="" title="" width="421" height="201" />
</div>

In the Configuration Type select, Parent package variable and then enter the name of the variable to manipulate. Once this is completed, click next so we can set the object level properties.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_17.gif" alt="" title="" width="320" height="317" />
</div>

Scroll to the Package–>Variables section and then highlight Value. This is what will physically and dynamically set the Value of the variable.

Once completed, you should have the following listening in the configurations. Note that ServerSourceConfig is a configuration file from the previous article.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_18.gif" alt="" title="" width="628" height="163" />
</div>

Now that we've setup and configured the relationships from the parent variables to the child variables, we can test this.

I like script tasks in the IDE as a quick test for variables values. It is just one of those old school methods. So add a script task to the child package, Pacakge1.dtsx. Enter in the ReadOnlyVariables

```
User::DefaultCatalog,User::ServerName
```

And then use this code

```SQL
public void Main()
        {
            MessageBox.Show(Dts.Variables["ServerName"].Value.ToString());
            MessageBox.Show(Dts.Variables["DefaultCatalog"].Value.ToString());
    MessageBox.Show(Dts.Connections["DestinationConnection_Server2008R2"].ConnectionString.ToString());
            Dts.TaskResult = (int)ScriptResults.Success;
        }
```
</p> 

Save both packages and then execute the, Execute Package Task in Package.dtsx
  
You should be presented with the message box showing your values for server name and initial catalog. This moves into the dynamic expressions for the connection string of the connection.

More importantly, the connection string was dynamically generated

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dynossis_19.gif" alt="" title="" width="370" height="168" />
</div>

**Conclusion for now**

We've only scratched the surface so far on making communications from parent to child packages flow along with true dynamic values so we can reuse packages. This and several other techniques make developing SSIS packages flow from system to system and even provide the ability for automated DR strategies with simply storing a configuration file just for the purpose of DR (or HA).

 [1]: /index.php/DataMgmt/DBAdmin/making-ssis-dynamic-configuration-manage