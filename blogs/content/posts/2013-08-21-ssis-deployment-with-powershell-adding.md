---
title: 'SSIS Deployment with PowerShell: Adding Environment References'
author: Koen Verbeeck
type: post
date: 2013-08-21T13:04:00+00:00
excerpt: This blog post explains how you can add environment references in a PowerShell deployment script for SSIS 2012.
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/ssis-deployment-with-powershell-adding/
views:
  - 35886
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSIS
tags:
  - catalog
  - deployment
  - powershell
  - sql server
  - ssis
  - syndicated

---
<p style="text-align: justify;">
  With the release of the revamped Integration Services in SQL Server 2012, a bunch of new deployment methods were introduced for the project deployment model. My article <a href="http://www.sqlservercentral.com/articles/Integration+Services+(SSIS)/101240/">SSIS Deployments with SQL Server 2012</a> gives an overview of these deployment methods. One of these methods is using PowerShell to deploy your project to the SSIS Catalog. Matt Masson (<a href="http://www.mattmasson.com/">blog</a> | <a href="https://twitter.com/mattmasson">twitter</a>) has an excellent blog post on the subject: <a href="http://www.mattmasson.com/2012/06/publish-to-ssis-catalog-using-powershell/">Publish to SSIS Catalog using PowerShell</a>.
</p>

<p style="text-align: justify;">
  However, there’s one small step missing in the deployment script posted by Matt. I’ll use this blog article to post the entire deployment script, so that I have a full script online as an easy reference.
</p>

<p style="text-align: justify;">
  I have a very simple SSIS project that I want to deploy to the Catalog. It contains only one package that transfers data from AdventureWorks to another database. It has two connection managers: one for each database. Nothing fancy here.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SSISDeploymentPowershell/ssisproject.png?mtime=1377025062"><img src="/wp-content/uploads/users/koenverbeeck/SSISDeploymentPowershell/ssisproject.png?mtime=1377025062" alt="" width="542" height="385" /></a>
</p>

<span style="text-align: justify;">When I deploy the project, I want to hook the OLE_TEST connection manager to an environment, so that I can easily change the destination server and/or database.</span>

<p style="text-align: justify;">
  The original script from Matt’s blog – modified for my project – looks like this:
</p>

<pre># Variables
$SSIS_server ="localhost"
$ProjectFilePath = "E:TestSSIS2012PowerShell_TestPowerShell_TestbinDevelopmentPowerShell_Test.ispac"

$ProjectName = "PowerShell_Test"
$FolderName = "PowerShellTest"
$EnvironmentName = "Test2"
 
# Load the IntegrationServices Assembly
[System.Reflection.Assembly]::LoadWithPartialName("Microsoft.SqlServer.Management.IntegrationServices") | Out-Null;
 
# Store the IntegrationServices Assembly namespace to avoid typing it every time
$ISNamespace = "Microsoft.SqlServer.Management.IntegrationServices"
 
Write-Host "Connecting to server ..."
 
# Create a connection to the server
$sqlConnectionString = "Data Source=" + $SSIS_server + ";Initial Catalog=master;Integrated Security=SSPI;"
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $sqlConnectionString
 
# Create the Integration Services object
$integrationServices = New-Object $ISNamespace".IntegrationServices" $sqlConnection

$catalog = $integrationServices.Catalogs["SSISDB"]
 
Write-Host "Creating Folder " $FolderName " ..."
 
# Create a new folder
$folder = New-Object $ISNamespace".CatalogFolder" ($catalog, $FolderName, "Folder description")
$folder.Create()
 
Write-Host "Deploying " $ProjectName " project ..."
 
# Read the project file, and deploy it to the folder
[byte[]] $projectFile = [System.IO.File]::ReadAllBytes($ProjectFilePath)
$folder.DeployProject($ProjectName, $projectFile)
 
Write-Host "Creating environment ..."
 
$environment = New-Object $ISNamespace".EnvironmentInfo" ($folder, $EnvironmentName, "Description")
$environment.Create()            
 
Write-Host "Adding server variables ..."
 
# Adding variable to our environment
# Constructor args: variable name, type, default value, sensitivity, description
$environment.Variables.Add("ServerName", [System.TypeCode]::String, $SSIS_server, $false, "ServerName")
$environment.Variables.Add("DatabaseName",[System.TypeCode]::String, "Test2", $false,"DatabaseName")
$environment.Alter()
 
Write-Host "Adding environment reference to project ..."
 
# making project refer to this environment
$project = $folder.Projects[$ProjectName]
$project.References.Add($EnvironmentName, $folder.Name)
$project.Alter() 
 
Write-Host "All done."</pre>

<p style="text-align: justify;">
  The script deploys the project to the Catalog, creates an environment and links the environment to the project. The environment has two variables: one to set the server name and one to set the database. Let’s inspect the results. When you right-click on the project, you can choose <em>Configure</em>. In the Configure window, you can see the connection managers used in the project in the Connection Managers tab. When we take a look at the Initial Catalog property of the OLE_TEST connection manager, you can see it is not yet linked to an environment variable.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SSISDeploymentPowershell/configureProject_before.png?mtime=1377025062"><img src="/wp-content/uploads/users/koenverbeeck/SSISDeploymentPowershell/configureProject_before.png?mtime=1377025062" alt="" width="785" height="536" /></a>
</p>

<span style="text-align: justify;">Instead, it is still linked to the default design-time value. Remark that you can configure a package/project with an environment without actually using parameters. This is because a few properties of a connection manager are linked to parameters behind the scenes. For example, the Initial Catalog property is linked to the parameter with the name </span>_[CM.<connection manager name>.InitialCatalog]_<span style="text-align: justify;">. You can find the parameter name at the top of the </span>_Set Parameter Value_ <span style="text-align: justify;">dialog box.</span>

<p style="text-align: justify;">
  To link the environment variables to the connection manager, we need to add just a few lines to the script:
</p>

<pre>Write-Host "Setting environment variable on package connection string ..."
$ssisPackage = $project.Packages.Item("PowerShellTest.dtsx")

$parServerName = "CM.OLE_Test.ServerName"
$ssisPackage.Parameters[$parServerName].Set("Referenced","ServerName")

$parDatabaseName = "CM.OLE_Test.InitialCatalog"
$ssisPackage.Parameters[$parDatabaseName].Set("Referenced","DatabaseName")

$ssisPackage.Alter()</pre>

<p style="text-align: justify;">
  When you deploy the project to the server with these adjustments, we get the result we want:
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SSISDeploymentPowershell/configureProject_after.png?mtime=1377025061"><img src="/wp-content/uploads/users/koenverbeeck/SSISDeploymentPowershell/configureProject_after.png?mtime=1377025061" alt="" width="747" height="375" /></a>
</p>

<span style="text-align: justify;">The environment variables are now linked to the two properties of the connection manager. When you run the package and choose the environment, the data is transferred to another database different from the one configured in the package, due to the reconfiguring of the connection manager by the environment variables.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SSISDeploymentPowershell/executePackage.png?mtime=1377025062"><img src="/wp-content/uploads/users/koenverbeeck/SSISDeploymentPowershell/executePackage.png?mtime=1377025062" alt="" width="530" height="363" /></a>
</p>

<span style="text-align: justify;">To wrap-up this post, here’s the entire deployment script for easier copy-paste:</span>

<pre># Variables
$SSIS_server ="localhost"
$ProjectFilePath = "E:TestSSIS2012PowerShell_TestPowerShell_TestbinDevelopmentPowerShell_Test.ispac"

$ProjectName = "PowerShell_Test"
$FolderName = "PowerShellTest"
$EnvironmentName = "Test2"
 
# Load the IntegrationServices Assembly
[System.Reflection.Assembly]::LoadWithPartialName("Microsoft.SqlServer.Management.IntegrationServices") | Out-Null;
 
# Store the IntegrationServices Assembly namespace to avoid typing it every time
$ISNamespace = "Microsoft.SqlServer.Management.IntegrationServices"
 
Write-Host "Connecting to server ..."
 
# Create a connection to the server
$sqlConnectionString = "Data Source=" + $SSIS_server + ";Initial Catalog=master;Integrated Security=SSPI;"
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $sqlConnectionString
 
# Create the Integration Services object
$integrationServices = New-Object $ISNamespace".IntegrationServices" $sqlConnection

$catalog = $integrationServices.Catalogs["SSISDB"]
 
Write-Host "Creating Folder " $FolderName " ..."
 
# Create a new folder
$folder = New-Object $ISNamespace".CatalogFolder" ($catalog, $FolderName, "Folder description")
$folder.Create()
 
Write-Host "Deploying " $ProjectName " project ..."
 
# Read the project file, and deploy it to the folder
[byte[]] $projectFile = [System.IO.File]::ReadAllBytes($ProjectFilePath)
$folder.DeployProject($ProjectName, $projectFile)
 
Write-Host "Creating environment ..."
 
$environment = New-Object $ISNamespace".EnvironmentInfo" ($folder, $EnvironmentName, "Description")
$environment.Create()            
 
Write-Host "Adding server variables ..."
 
# Adding variable to our environment
# Constructor args: variable name, type, default value, sensitivity, description
$environment.Variables.Add("ServerName", [System.TypeCode]::String, $SSIS_server, $false, "ServerName")
$environment.Variables.Add("DatabaseName",[System.TypeCode]::String, "Test2", $false,"DatabaseName")
$environment.Alter()
 
Write-Host "Adding environment reference to project ..."
 
# making project refer to this environment
$project = $folder.Projects[$ProjectName]
$project.References.Add($EnvironmentName, $folder.Name)
$project.Alter() 
 
Write-Host "Setting environment variable on package connection string ..."
$ssisPackage = $project.Packages.Item("PowerShellTest.dtsx")

$parServerName = "CM.OLE_Test.ServerName"
$ssisPackage.Parameters[$parServerName].Set("Referenced","ServerName")

$parDatabaseName = "CM.OLE_Test.InitialCatalog"
$ssisPackage.Parameters[$parDatabaseName].Set("Referenced","DatabaseName")

$ssisPackage.Alter()

Write-Host "All done."</pre>