---
title: Improved TeamCity .Net Build Warnings
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-06-01T21:17:18+00:00
url: /index.php/enterprisedev/application-lifecycle-management/improved-teamcity-net-build-warnings/
views:
  - 6431
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
tags:
  - teamcity

---
A few years back, I posted &#8220;[Displaying .Net Build Warnings in TeamCity][1]&#8220;. Many folks found it useful (and it served as a good reference the last time I needed to re-setup warnings). Recently, Mitch Terlisner reached out to me with a much improved version to share with folks that includes better build status output, an interactive warnings tab, statistics chart, and a custom metric to enable custom failure rules:

**Better Warning Output in build status!**
  


<div id="attachment_4497" style="width: 494px" class="wp-caption alignleft">
  <a href="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_1.png"><img src="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_1.png" alt="Improved output in your build status" width="484" height="117" class="size-full wp-image-4497" srcset="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_1.png 484w, http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_1-300x72.png 300w" sizes="(max-width: 484px) 100vw, 484px" /></a>
  
  <p class="wp-caption-text">
    Improved build status
  </p>
</div>

**Way better (interactive) Warning Output tab!**
  


<div id="attachment_4498" style="width: 1034px" class="wp-caption alignleft">
  <a href="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_2.png"><img src="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_2-1024x310.png" alt="Interactive Build Warning tab with the assembly hierarchy" width="1024" height="310" class="size-large wp-image-4498" srcset="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_2-1024x310.png 1024w, http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_2-300x90.png 300w, http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_2.png 1076w" sizes="(max-width: 1024px) 100vw, 1024px" /></a>
  
  <p class="wp-caption-text">
    Interactive Build Warning tab
  </p>
</div>

**Build Warnings Statistics Chart!**
  


<div id="attachment_4499" style="width: 939px" class="wp-caption alignleft">
  <a href="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_3.png"><img src="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_3.png" alt="A chart in the Statistics tab of the project" width="929" height="271" class="size-full wp-image-4499" srcset="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_3.png 929w, http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_3-300x87.png 300w" sizes="(max-width: 929px) 100vw, 929px" /></a>
  
  <p class="wp-caption-text">
    Statistics Chart
  </p>
</div>

**Custom Failures on Build Warning Count!**
  


<div id="attachment_4500" style="width: 667px" class="wp-caption alignleft">
  <a href="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_4.png"><img src="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_4.png" alt="Custom failure when warning count exceeds a given number" width="657" height="115" class="size-full wp-image-4500" srcset="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_4.png 657w, http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCity_Output_4-300x52.png 300w" sizes="(max-width: 657px) 100vw, 657px" /></a>
  
  <p class="wp-caption-text">
    Custom failures
  </p>
</div>

> This version was shared by Mitch T, on behalf of (and with permission of) Markit.
> 
> A few highlights:
  
> * Supports multiple solutions, and displays warnings in a hierarchy, by solution/project.
  
> * Shows counts of warnings by solution/project/warning code, and shows which warnings are most numerous.
  
> * Interactive &#8211; allows expanding/collapsing at the solution/project, and filtering to new warnings or by warning code(s).
> 
> Mitch Terlisner
  
> <a href="http://www.improving.com" title="www.improving.com" target="_blank">www.improving.com</a> 



## Setup

Here are the setup steps in TeamCity:

### Build Changes

Mitch added these parameters and steps to his build template to apply to multiple builds, another option would be to edit an existing build directly.

Download the powershell script here: [BuildWarningReportGenerator.ps1 Gist][2]

1. Select the &#8220;Parameters&#8221; menu.
  
2. Add a a parameter for the Build Log name that we&#8217;ll generate from MSBuild: 

<pre>Name: BuildLogFile
   Value: BuildLog.Log</pre>

3. Add command line argument we need to pass to MSBuild to do so: 

<pre>Name: MSBuildLogClause
   Value: /l:FileLogger,Microsoft.Build.Engine;logfile=%BuildLogFile%;Append</pre>

[<img src="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Template_1-1024x306.png" alt="Add Parameters" width="800" class="aligncenter size-large wp-image-4501" srcset="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Template_1-1024x306.png 1024w, http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Template_1-300x89.png 300w, http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Template_1.png 1043w" sizes="(max-width: 1024px) 100vw, 1024px" />][3]

Next we&#8217;ll want to add build steps to purge any prior versions of the file, run the log during MS Build calls, and then evaluate the log file for warnings at the end of the run.

[<img src="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Template_2.png" alt="Build Steps" width="839" height="341" class="aligncenter size-full wp-image-4502" srcset="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Template_2.png 839w, http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Template_2-300x121.png 300w" sizes="(max-width: 839px) 100vw, 839px" />][4]

**1. Purge Build Log**

<pre>Type: Command Line
Name: Purge BuildLogFile
Run:  Custom Script
Script: if exist %BuildLogFile% del %BuildLogFile%</pre>

**2. Existing MSBuild Task**
  
Append to the CommandLine Input: %MSBuildLogClause%

**3. Evaluate Build Warnings**

<pre>Type: Powershell
Name: Generate Build Warnings Report
Working Dir: &lt;working dir the powershell script is in&gt;
Script: Source code
Source:</pre>

<pre>if(test-path .\BuildWarningReportGenerator.ps1){
   .\BuildWarningReportGenerator.ps1 -BuildLogPath "&lt;path to your MS Build working dir&gt;\%BuildLogFile%" -BuildCheckoutDirectoryPath "&lt;path from script directory to build checkout directory&gt;\" -BuildArtifactRepositoryUrl "%teamcity.serverUrl%/repository/download/%system.teamcity.buildType.id%/"
}

# ** Requires two trailing lines to work correctly</pre>

<pre>Mode: Put Script into PowerShell stdin with "-Command =" argument
Advanced Settings: &lt;input type="checkbox" checked&gt; Add -NoProfile argument</pre>



### Report Tab

To add a report tab with the warning information in TeamCity:

[<img src="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Report1.png" alt="Add Report Tab" width="895" height="453" class="aligncenter size-full wp-image-4503" srcset="http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Report1.png 895w, http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Report1-300x151.png 300w" sizes="(max-width: 895px) 100vw, 895px" />][5]

1. Open the Project and click &#8220;Edit Project Settings&#8221; and select the Report Tab
  
2. Click the &#8220;Create new build report tab&#8221; button
  
3. Enter settings and press Save

<pre>Tab title: Build Warnings
Start page: .teamcity/BuildWarningReport.zip!BuildWarningReport.html</pre>



### Chart

You can add a chart to an individual build configuration via the UI, but you have to edit a TeamCity configuration if you want to add it to a Build Template or a Project. Mitch included the instructions for the config so we can do an edit once, apply many approach:

Edit config/projects/[your project folder]/pluginData/plugin-settings.xml, and merge in these settings:

<pre>&lt;settings&gt;
	&lt;buildtype-graphs&gt;
		&lt;graph title="Build Warnings" defaultFilters="" hideFilters="" id="customGraph1" seriesTitle="Serie"&gt;
			&lt;valueType key="buildWarnings" title="buildWarnings" color="#ffcc00" /&gt;
		&lt;/graph&gt;
	&lt;/buildtype-graphs&gt;
&lt;/settings&gt;</pre>



### Custom Metric

Recording the warning count in a custom metric will enable you to create a custom build failure based on the warning count:

Edit config/main-config.xml, and merge in these settings

<pre>&lt;server&gt;
	&lt;build-metrics&gt;
		&lt;statisticValue key="buildWarnings" description="number of build warnings"/&gt;
	&lt;/build-metrics&gt;
&lt;/server&gt;</pre>



## Closing

I wanted to thank Mitch again for sending back the improvements he made on the original. This is a level or two above my original post and I&#8217;m already applying some of the changes to my own builds.

 [1]: /index.php/enterprisedev/application-lifecycle-management/displaying-net-build-warnings-in/ "Displaying .Net Build Warnings in TeamCity"
 [2]: https://gist.github.com/tarwn/bfd08f42226463871389a766fa40258c
 [3]: http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Template_1.png
 [4]: http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Template_2.png
 [5]: http://blogs.ltd.local/wp-content/uploads/2016/05/TeamCityWarnings_Report1.png