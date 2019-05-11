---
title: Converting a .Net Console Project to an Azure Worker Role Project
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-11-07T11:28:00+00:00
ID: 1780
excerpt: "So lets say you're building a worker role for Azure, but started it out as a Console app for faster local debugging. It comes time to deploy it to Azure for the first time, but there's no option to convert a Console application to a Worker Role project.&hellip;"
url: /index.php/desktopdev/mstech/converting-a-net-console-project/
views:
  - 27730
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - azure
  - worker role

---
So lets say you're building a worker role for Azure, but started it out as a Console app for faster local debugging. It comes time to deploy it to Azure for the first time, but there's no option to convert a Console application to a Worker Role project. We could copy all the code into a new project, but then we not only lose our faster debugging but also the history of commits we have made against the project. 

Luckily converting the existing Console project to be deployable as an Azure Worker Role project is fairly easy.

For the purposes of this post, I have created a solution with an Existing Console project, an Azure Cloud Project, and an Existing Azure Worker Role project (we have to have at least one to create the Azure Cloud Project, it can be deleted later).

<div class="font-size: 85%; color: #666666; text-align: center">
  <img src="http://www.tiernok.com/LTDBlog/AzureWorkerRole/awr_01.png" alt="Solution Explorer" /><br /> Solution Explorer view of the Sample Solution
</div>

If we right click the Azure "Roles" folder, the option to use an existing Worker Role project is not available. Visual Studio enables or disables this option automatically, based on whether we have any assignable projects in the solution that aren't already associated with the Azure project.

<div class="font-size: 85%; color: #666666; text-align: center">
  <img src="http://www.tiernok.com/LTDBlog/AzureWorkerRole/awr_02.png" alt="Add Role menu options" /><br /> "Worker Role Project in Solution" is Disabled
</div>

The first thing we want to do is add a RoleEntryPoint to the console application. To keep this simple, I'm going to convert the Program class to serve as the RoleEntryPoint also.

To add the RoleEntryPoint class, I need to first add some azure references. I'm using Azure 1.8, so this means I'll need the following 4 references:

<div class="font-size: 85%; color: #666666; text-align: center">
  <img src="http://www.tiernok.com/LTDBlog/AzureWorkerRole/awr_03.png" alt="Assembly References Dialog" /><br /> Adding WindowsAzure References
</div>

_Note: The Storage SDK version number for Azure SDK 1.8 is 2.0. I suspect they chose to do this because they made a number of significant changes in the storage library that were not backwards compatible with 1.7._

_Note #2: I noticed that if you add a new worker role through Visual Studio, it uses StorageClient 1.7 still despite this being SDK 1.8. This may be a requirement from the Azure Diagnostics assembly, Diagnostics uses StorageClient and may still be a version behind. I would test this theory, but getting Diagnostics working properly tends to be somewhat painful._

After adding the references, we can then add RoleEntryPoint inheritance to the Program. New Worker Roles have two methods they override from RoleEntryPoint, but the important one is Run (and why this isn't abstract, I couldn't tell you). 

```text
namespace ExistingConsoleApplication
{
	public class Program : RoleEntryPoint
	{
		static void Main(string[] args)	/* Console entry point */
		{
			var ibl = new InterestingBusinessLogic();
			ibl.DoStuff();

			Console.Read();
		}

		public override void Run()	/* Worker Role entry point */
		{
			var ibl = new InterestingBusinessLogic();
			ibl.DoStuff();
		}
	}
}
```
The last thing we need to do is add some information to the project file. I can open the file in Visual Studio by right clicking and selecting "Unload Project", then right clicking again and selecting "Edit _project name_". In the Property Group I need to add a single property named "RoleType" to identify this as a Worker (see the last entry):

```text
<PropertyGroup>
	<Configuration Condition=" '$(Configuration)' == '' ">Debug</Configuration>
	<Platform Condition=" '$(Platform)' == '' ">AnyCPU</Platform>
	<ProjectGuid>{4B7C48CB-9899-47D0-95AB-2FD3C3B739CF}</ProjectGuid>
	<OutputType>Exe</OutputType>
	<AppDesignerFolder>Properties</AppDesignerFolder>
	<RootNamespace>ExistingConsoleApplication</RootNamespace>
	<AssemblyName>ExistingConsoleApplication</AssemblyName>
	<TargetFrameworkVersion>v4.5</TargetFrameworkVersion>
	<FileAlignment>512</FileAlignment>
	<RoleType>Worker</RoleType>
</PropertyGroup>
```
Save and reload the project, and now when we right click on the Azure Cloud Project Roles, the option to associate and existing project is available.

<div class="font-size: 85%; color: #666666; text-align: center">
  <img src="http://www.tiernok.com/LTDBlog/AzureWorkerRole/awr_04.png" alt="Add Role menu options" /><br /> "Worker Role Project in Solution" is Available Now
</div>

After adding the project, we can now run it either as a local console application or in the computer emulator as an emulated worker role.

_Note: Don't forget that the compute emulator must run in elevated administrator mode._

And there you have it, a Console project promoted to Azure Worker Role, preserving any history of commits that were made against the project and still runnable as either a console application or as a worker role.