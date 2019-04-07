---
title: Deploying Database Changes with PowerShell
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-05-17T07:36:00+00:00
ID: 2079
excerpt: "Recently, while working on a personal project, I found myself needing a lightweight way to deploy database changes to multiple environments. In the past I have used a wide range of methods, ranging from applying the changes manually to applying changes via a diff tool (SQL Compare), to automatically applying manually created change scripts, to automatically applying diff scripts that were automatically generated, to working directly in production..er, pretend you didn't see that one."
url: /index.php/datamgmt/dbprogramming/deploying-database-changes-with-powershell/
views:
  - 28095
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - deployment
  - powershell
  - sql

---
Recently, while working on a personal project, I found myself needing a lightweight way to deploy database changes to multiple environments. In the past I have used a wide range of methods, ranging from applying the changes manually to applying changes via a diff tool (SQL Compare), to automatically applying manually created change scripts, to automatically applying diff scripts that were automatically generated, to working directly in production..er, pretend you didn&#8217;t see that one. 

## Why not \___\___\___ tool?

There are a lot of tools out there to handle database deployments, but this is a small project that I am building incrementally as a minimum viable product. Rather than tie up a bunch of time researching and experimenting with database deployment tools early on, I decided to do something simple that would work for the time being and free me up to work on the actual product. 

What I want from the deployment is to:

  1. Spend as little time on this as possible
  2. Work against SQL Azure and a local 2008 R2 Server
  3. Call it from TeamCity for a local server or a remote one
  4. Produce readable output for TeamCity logs
  5. Create the databases and users from the ground up
  6. Include randomly generated data
  7. Manage scripts for 2 independent databases in the same build
  8. Allow real SQL (I&#8217;m not scared of SQL and I don&#8217;t want to learn a code abstraction just to deploy changes)
  9. Not worry about rollbacks. I am deploying small changes and if something breaks I&#8217;ll be charging forward
 10. Not expose credentials, as the code will be visible to the public
 11. Be replaceable. I might replace it with a tool one day, so keep the deployment logic separate from the application

Looking back at this list and what I eventually created, I probably could have used something like <a href="https://github.com/brunomlopes/dbdeploy.net" "dbdploy on github">DBDeploy</a>. The scripts I created ended up taking a very similar approach.

## The Deployment Scripts

My deployment consists of 4 PowerShell scripts:

  * **ApplyDatabaseUpdates.ps1** &#8211; Responsible for generically applying changes from a folder to a specified database
  * **UpdateCoreDatabase.ps1** &#8211; Responsible for the application&#8217;s Core Database, calls ApplyDatabaseUpdates
  * **UpdateSampleDatabase.ps1** &#8211; Response for the application&#8217;s Sample Database, calls ApplyDatabaseUpdates
  * **RunLocally.ps1** &#8211; Executes the two Update scripts against the local database on my development machine(s)

This project has been spread out over 6 months, intermixed with life, other projects, blog posts, etc. Along the way I also upgraded my local development machine to SQL Server 2012 but my main test database server is on 2008 still and my release environment is Azure Database/SQL Azure/(whatever the name is this week).

### ApplyDatabaseUpdates.ps1

The purpose of the ApplyDatabaseUpdates script is to apply all of the scripts in a specified folder to the specified server. To do this it creates a tracking table on the target database, then iterates through the contents of the folder, building a script containing any files that were not previously logged in the tracking table.

<div style="text-align:center; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/DatabaseDeployment/SQLScripts.png" alt="Core DB Scripts folder" /><br /> Core Database Scripts Folder
</div>

The deployment script wraps the contents of each script file in an EXECUTE statement, followed by an INSERT to add it to the tracking table for the database.

**[ApplyDatabaseUpdates.ps1][1]**

```powershell
function ApplyDatabaseUpdates
{
    param (
        [parameter(Mandatory=$true)]
        [string]
        $UpdatesFolder,
        [parameter(Mandatory=$true)]
        [string]
        $Server,
        [parameter(Mandatory=$true)]
        [string]
        $Database,
        [parameter(Mandatory=$true)]
        [string]
        $AdminUserName,
        [parameter(Mandatory=$true)]
        [string]
        $AdminPassword
    )

    $path = (Get-Location).Path

    # For SQL 2008 - load the modules
    try{    
        if ( (Get-PSSnapin -Name SqlServerCmdletSnapin100 -ErrorAction SilentlyContinue) -eq $null -and (Get-PSSnapin -Registered -Name SqlServerCmdletSnapin100 -ErrorAction SilentlyContinue) -ne $null){
            Add-PSSnapin SqlServerCmdletSnapin100 -ErrorAction SilentlyContinue
            Add-PSSnapin SqlServerProviderSnapin100 -ErrorAction SilentlyContinue
        }
    }
    catch{
        Write-Error "Powershell Script error: $_" -EA Stop
    }

    #updates tracking
    try{
        Write-Host "Creating Update Tracking Table If Not Exists"
        Invoke-Sqlcmd -Query "IF NOT EXISTS (SELECT 1 FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_NAME = 'UpdateTracking') CREATE TABLE UpdateTracking (UpdateTrackingKey int IDENTITY(1,1) PRIMARY KEY, Name varchar(255) NOT NULL, Applied DateTime NOT NULL);" -ServerInstance "$Server" -Username "$AdminUserName" -Password "$AdminPassword" -Database "$Database" -ErrorAction Stop
        Write-Host "Done"
    }
    catch{
        Write-Error "Powershell Script error: $_" -EA Stop
    }

    #database updates
    $outputPath = "$pathUpdatesBatch.sql"
    $stream = [System.IO.StreamWriter] "$outputPath"
    $fileUpdates = Get-ChildItem "$UpdatesFolder"
    $datestamp = $(get-date -f "yyyy-MM-dd HH:mm")

    $stream.WriteLine("/* SQL Core Updates - Updated $datestamp */")
    $stream.WriteLine("BEGIN TRANSACTION")

    foreach($file in $fileUpdates)
    {
        $name = ($file.Name)
        $namewe = ([System.IO.Path]::GetFileNameWithoutExtension($name))

        $stream.WriteLine("")
        $stream.WriteLine("/* File: $name */")
        $stream.WriteLine("IF NOT EXISTS (SELECT 1 FROM UpdateTracking WHERE Name = '$namewe')")
        $stream.WriteLine("BEGIN")

        $stream.WriteLine("`tPrint 'Applying Update: $namewe'")
        $stream.WriteLine("`tEXEC('")
        (Get-Content "$UpdatesFolder$name") | % {$_ -replace "'", "''"} | % {$stream.WriteLine("`t`t$_")}
        $stream.WriteLine("`t');")

        $stream.WriteLine("`tINSERT INTO UpdateTracking(Name, Applied) SELECT '$namewe', GETUTCDATE();")
        $stream.WriteLine("END")
    }

    $stream.WriteLine("COMMIT TRANSACTION")
    $stream.Close()
    Write-Host "Update Script Created."

    Write-Host "Running updates..."

    Invoke-SqlCmd -InputFile "$outputPath" -ServerInstance "$Server" -Username "$AdminUserName" -Password "$AdminPassword" -Database "$Database" -Verbose -ErrorAction Stop

    Remove-Item "$outputPath"

    Write-Host "Updates completed."
}
```
<i style="display: block; padding: 1em; margin: 1em; background-color: #eeeeee">Note: this has only been run in the context of my personal project. That means don&#8217;t copy, paste, and run it immediately against your production environment. Running stuff blindly from the internet is known as both a bad idea and a career limiting maneuver.</i>

### Update\____Database.ps1

My application has two databases which it will access via different accounts. I want the ability to rebuild these databases from scratch as well as manage their credentials from an external system (in this case, TeamCity). If the worst should happen and these databases are compromised or overwritten in some fashion, I want to be able to recreate them with new credentials, account names, the works. 

To make life more difficult, many of these commands have to be executed individually in order to work with Azure Databases.

Both scripts detect if their specified database exists and, if not, create them. The UpdateSampleDatabase is capable of recreating the database in Azure, provided some extra options are passed in to it (the Core Database script is missing this bit, unfortunately):

**Excerpt from [UpdateSampleDatabase.ps1][2]:**

```powershell
# ...

    #database
        Write-Host "Checking database exists...";
        $result = Invoke-Sqlcmd -Query "SELECT [name] FROM [sys].[databases] WHERE [name] = N'$database'" -ServerInstance "$Server" -Username "$AdminUserName" -Password "$AdminPassword" -Database "master" -ErrorAction Stop
        if($result.name){
            Write-Host "Database already exists";
        }
        else{
            Write-Host "Creating Database: $database"

            Invoke-Sqlcmd -Query "CREATE DATABASE $database" -ServerInstance "$Server" -Username "$AdminUserName" -Password "$AdminPassword" -Database "master" -ErrorAction Stop
            Invoke-Sqlcmd -Query "ALTER DATABASE $database SET RECOVERY SIMPLE" -ServerInstance "$Server" -Username "$AdminUserName" -Password "$AdminPassword" -Database "master" -ErrorAction Stop
            Write-Host "Created."
        }

    # ...
```
They also generate the users specified by the build server (which will also be dynamically added into the relevant web.config files for the website):

**Excerpt from [UpdateCoreDatabase.ps1][3]:**

```powershell
# ...

    #user
    try{
        Write-Host "Creating User: $NewUserName"
        $result = Invoke-Sqlcmd -Query "SELECT [name] FROM sys.sql_logins WHERE name = '$NewUserName'" -ServerInstance "$Server" -Username "$AdminUserName" -Password "$AdminPassword" -Database "master" -ErrorAction Stop
        if($result.name){
            Write-Host "Login already exists"
        }
        else{
            Write-Host "Creating login..."
            Invoke-Sqlcmd -Query "CREATE LOGIN $NewUserName WITH PASSWORD = '$NewPassword'" -ServerInstance "$Server" -Username "$AdminUserName" -Password "$AdminPassword" -Database "master" -ErrorAction Stop
            Write-Host "Login Created."
        }

        $result = Invoke-Sqlcmd -Query "SELECT [name] FROM sys.sysusers WHERE name = '$NewUserName'" -ServerInstance "$Server" -Username "$AdminUserName" -Password "$AdminPassword" -Database "$Database" -ErrorAction Stop
        if($result.name){
            Write-Host "User already exists"
        }
        else{
            Write-Host "Creating user..."
            Invoke-Sqlcmd -Query "CREATE USER $NewUserName FOR LOGIN $NewUserName WITH DEFAULT_SCHEMA = dbo" -ServerInstance "$Server" -Username "$AdminUserName" -Password "$AdminPassword" -Database "$Database" -ErrorAction Stop
            Invoke-Sqlcmd -Query "EXEC sp_addrolemember 'db_datareader','$NewUserName'; EXEC sp_addrolemember 'db_datawriter','$NewUserName'" -ServerInstance "$Server" -Username "$AdminUserName" -Password "$AdminPassword" -Database "$Database" -ErrorAction Stop
            Write-Host "User Created."
        }
    }
    catch{
        Write-Error "Powershell Script error: $_" -EA Stop
    }

    # ...
```
Once the database and users are created, the SampleDatabase script produces a replacement for one of it&#8217;s script files that will contain some randomized data. The original file is a placeholder and produces an error if it hasn&#8217;t been replaced.

**Excerpt from [UpdateSampleDatabase.ps1][2]:**

```powershell
# ...

# ---------------------------------- Content Generation ---------------------------------------------
# Scripts to generate content dynamically and update the appropriate update script

    #generate customers table content
    $CustomersContentPath = "$UpdatesFolder 002_CustomersData.sql"
    try{
        Write-Host "Generating content script for dbo.Customers"
        $girlsnames = ("&lt;ns&gt;&lt;n&gt;" + [string]::Join("&lt;/n&gt;&lt;n&gt;",(Get-Content "$pathDatagirlsforenames.txt")) + "&lt;/n&gt;&lt;/ns&gt;").Replace("'","''")
        $boysnames =  ("&lt;ns&gt;&lt;n&gt;" + [string]::Join("&lt;/n&gt;&lt;n&gt;",(Get-Content "$pathDataboysforenames.txt")) + "&lt;/n&gt;&lt;/ns&gt;").Replace("'","''")
        $lastnames =  ("&lt;ns&gt;&lt;n&gt;" + [string]::Join("&lt;/n&gt;&lt;n&gt;",(Get-Content "$pathDatasurnames.txt")) + "&lt;/n&gt;&lt;/ns&gt;").Replace("'","''")
    
        (Get-Content "$pathDataBulkImportNames.AzureFriendly.sql") `
                        | % {$_ -replace "{{GIRLSNAMES}}", $girlsnames} `
                        | % {$_ -replace "{{BOYSNAMES}}", $boysnames} `
                        | % {$_ -replace "{{LASTNAMES}}", $lastnames} `
                        | Set-Content -path "$CustomersContentPath"
    }
    catch{
        Write-Error "Powershell Script error: $_" -EA Stop
    }

    # ...
```
This generated data is a necessary part of the application that I initially replaced on each deployment, but now only use on new database deployments. Keeping it random and replaceable prevents the application code from making any assumptions about the data in this table.

The final step for both scripts is to run the ApplyDatabaseUpdates function on their respective folders and databases.

### RunLocally.sample.ps1

The RunLocally.sample.ps1 script will bring a local development environment all the way up to the latest version without requiring me to type out a long series of arguments and credentials. It is basically just a list of hardcoded variables and then calls to the two Update\___\___Database.ps1 scripts. 

**RunLocally.sample.ps1:**

```Powershell
# 1) Copy this file to RunLocally.ps1
# 2) Open RunLocally.ps1 + substitute meaningful values for the variables below (update web.config connection strings also)
# 3) [Cross your fingers and] Run it 

$DbServer = "localhost"
$DbAdminUsername = "admin"
$DbAdminPassword = "password"

$DbSampleDatabase = "SampleDB"
$DbSampleReadUsername = "readuser"
$DbSampleReadPassword = "password"

$DbCoreDatabase = "CoreDB"
$DbCoreUsername = "coreuser"
$DbCorePassword = "password"

.UpdateSampleDatabase.ps1 -s $DbServer -d $DbSampleDatabase -nu $DbSampleReadUsername -np $DbSampleReadPassword -au $DbAdminUsername -ap $DbAdminPassword -DeleteGeneratedContentAfter $true

.UpdateCoreDatabase.ps1 -s $DbServer -d $DbCoreDatabase -nu $DbCoreUsername -np $DbCorePassword -au $DbAdminUsername -ap $DbAdminPassword
```
The reason it is a sample file is because the real one is going to be different for my desktop and laptop and I knew if they were under source control I would constantly be accidentally committing them and having to change back and forth as I switched systems. To use it, I create a copy of the sample file, rename it to RunLocally.ps1 (which is ignored via the .gitignore for the project) and fill in the real values.

## Future Plans

I&#8217;m not a fan of the libraries that try to abstract away the SQL in SQL deployments. I know SQL and don&#8217;t need to spend the time learning a library abstraction that, at best, can offer me no better control and ease of use then raw SQL. Many developers feel less than confident in their SQL skills and an abstracted library seems to reduce the need to learn SQL, but in reality the less you know about SQL the harder it will be to determine if the abstraction is doing what you think it is doing, and doing it in a safe and performant manner (and frequently the answer is &#8220;it&#8217;s not&#8221;).

This set of scripts evolved as a I built the project. In the future I&#8217;ll probably switch over to something like DbDeploy, as I mentioned earlier. Having the scripts in file system folders makes it easy to glance over the list to see what has changed, makes them easily accessible in my git repositories, can be copied and pasted (or opened and saved) directly from SSMS, and requires no extra tooling.

_The scripts above are part of the deployment process for a personal project I have been working on called [SQLisHard.com][4]. Launching in the next few weeks (quietly), the site is designed to help people interactively learn or improve their SQL skills and help build the knowledge and confidence that comes from writing and solving real SQL problems._

 [1]: https://github.com/tarwn/SQLisHard/blob/master/Database/ApplyDatabaseUpdates.ps1 "View on github"
 [2]: https://github.com/tarwn/SQLisHard/blob/master/Database/UpdateSampleDatabase.ps1 "View on github"
 [3]: https://github.com/tarwn/SQLisHard/blob/master/Database/UpdateCoreDatabase.ps1 "View on github"
 [4]: http://SQLisHard.com