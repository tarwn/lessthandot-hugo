---
title: Displaying .Net Build Warnings in TeamCity
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-01-15T13:41:00+00:00
excerpt: |
  I like it when I kick off a build and there aren't any warnings. Unfortunately I'm forgetful and it's always easier to edit the code now then it is 3 months later (when I remember to look at the warnings).
  
  This post will cover capturing MSBuild warnings in TeamCity and displayign the results in the dashboard, a custom chart, the build log, a raw text artifact, and a custom report tab in the run results.
url: /index.php/enterprisedev/application-lifecycle-management/displaying-net-build-warnings-in/
views:
  - 28370
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
tags:
  - .net
  - continuous integration
  - teamcity

---
I like it when I kick off a build and there aren&#8217;t any warnings. Unfortunately I&#8217;m forgetful and it&#8217;s always easier to edit the code now then it is 3 months later (when I remember to look at the warnings). When I put together [my sample Continuous Delivery project][1], I was using Jenkins, which provided plugins for capturing warnings. It was nice to have visual feedback when I added a new warning, see how many were outstanding, have a list of outstanding warnings available on demand, and when I had a few minutes and fixed some of them, positive feedback by watching the warning chart slowly go down.

<div style="background-color: #CCFFCC; border: 2px solid #BBEEBB; border-width: 2px 2px 2px 14px; margin: .5em; padding: .5em">
  <strong>June 2016 Update:</strong> Good news! Mitch Terlisner took this original idea and improved on it, then shared those updates. You can see the updated version (using TeamCity 9) and instructions here: <a href="/index.php/enterprisedev/application-lifecycle-management/improved-teamcity-net-build-warnings/" title="Improved TeamCity .Net Build Warnings">Improved TeamCity .Net Build Warnings</a>
</div>

When I switch modes and work in [TeamCity][2], I miss having that information available, with no extra steps from me. Despite several searches, though, I was never able to find a plugin that duplicated that behavior I liked in the Jenkins plugin. Turns out that TeamCity makes it pretty easy to roll your own, with just a little bit of powershell and some built-in features.

In this post I am going to cover capturing the warnings from an MSBuild build step, adding that warning count to the main dashboard, adding a statistics chart for the warning count over time, adding a condensed list to the end of the build log, adding the formatted list as a build artifact, and adding a custom report tab to report the warnings for each build. 

Because who doesn&#8217;t need five different ways to see their warnings?

## Capturing the Build Warnings

Since I am using MSBuild, the build warnings have a consistent pattern and MSBuild itself has an option to log out to a logger. We can add this attribute in either the build step or the Build Parameters. My preference is using the parameters of the build step in case I have multiple MSBuild calls in the build.

Parameter to add to MSBuild: <code class="codespan">/l:FileLogger,Microsoft.Build.Engine;logfile=%BuildLogFilename%</code>

<div style="text-align: center; font-size: 90%; color: #666666; margin: .5em">
  <img src="http://www.tiernok.com/LTDBlog/TeamCityBuildWarnings/MSBuildParameter.png" alt="Adding the MS Build Parameter" style="border: 1px solid #666666;" /><br /> Adding the MS Build Parameter
</div>

Each time MSBuild runs, it will log it&#8217;s output to the specified file. We can use powershell to extract the warnings from the output, like so:

<pre>Param(
    [parameter(Mandatory=$true)]
    [alias("f")]
    $FilePath
)

$warnings = @(Get-Content -ErrorAction Stop $FilePath |       # Get the file content
                Where {$_ -match '^.*warning CS.*$'} |        # Extract lines that match warnings
                %{ $_.trim() -replace "^s*d+&gt;",""  } |      # Strip out any project number and caret prefixes
                sort-object | Get-Unique -asString)           # remove duplicates by sorting and filtering for unique strings</pre>

Once we have the warnings extracted, we can move on to decide how we want them delivered. 

_Each section below will continue to add on to this script until it contains all the pieces we need to meet the display goals at the beginning._

## Condensed Warning List in Build Log

The powershell script that is extracting warnings will need to run as a build step in the appropriate build configuration. This means that displaying a formatted list of warnings at the end of the build log is as simple as outputting that list from the powershell script we are building.

<pre>$count = $warnings.Count
Write-Host "MSBuild Warnings - $count warnings ==================================================="
$warnings | % { Write-Host " * $_" }</pre>

This will output a section at the bottom of the build log that contains our warnings, like so:

<div style="text-align: center; font-size: 90%; color: #666666; margin: .5em">
  <img src="http://www.tiernok.com/LTDBlog/TeamCityBuildWarnings/LogOutput.png" alt="Warnings in the Bottom of a Build Log" style="border: 1px solid #666666;" /><br /> Warnings in the Bottom of a Build Log
</div>

Which I suppose is fine, but doesn&#8217;t really add that much value over the ones listed further up the log by MSBuild itself.

## Condensed Warning List in Archived Text File

Now that I have formatted warnings, it&#8217;s pretty easy to create a file with those warnings and archive it. First I&#8217;ll update the script to take an output parameter and add some file output:

<pre>Param(
    [parameter(Mandatory=$true)]
    [alias("f")]
    $FilePath,
    [parameter()]
    [alias("o")]
    $RawOutputPath,
)

# ...

# file output
if($RawOutputPath){
    $stream = [System.IO.StreamWriter] $RawOutputPath
    $stream.WriteLine("Build Warnings")
    $stream.WriteLine("====================================")
    $stream.WriteLine("")
    $warnings | % { $stream.WriteLine(" * $_") }
    $stream.Close()
}</pre>

Then I&#8217;ll configure the project to capture that output file as an artifact:

<div style="text-align: center; font-size: 90%; color: #666666; margin: .5em">
  <img src="http://www.tiernok.com/LTDBlog/TeamCityBuildWarnings/ArtifactConfig_RawOutput.png" alt="Artifact Configuration" style="border: 1px solid #666666;" /><br /> Artifact Configuration
</div>

Et voila, the file shows up in my archived items:

<div style="text-align: center; font-size: 90%; color: #666666; margin: .5em">
  <img src="http://www.tiernok.com/LTDBlog/TeamCityBuildWarnings/Artifact_Display.png" alt="List of archived items from a run" style="border: 1px solid #666666;" /><br /> List of archived items from a run
</div>

And I have a clean, archived list of my warnings:

<div style="text-align: center; font-size: 90%; color: #666666; margin: .5em">
  <img src="http://www.tiernok.com/LTDBlog/TeamCityBuildWarnings/Artifact_File.png" alt="Display of archived text file" style="border: 1px solid #666666;" /><br /> Display of archived text file
</div>

But, really, we can do better.

## Warning count in build status

Part of the goal was to be able to see the warning count change with no extra work, the best place I can think of to meet this is the final build status on each build.

Before:

<div style="text-align: center; font-size: 90%; color: #666666; margin: .5em">
  <img src="http://www.tiernok.com/LTDBlog/TeamCityBuildWarnings/BuildStatusBefore.png" alt="Build status on dashboard" style="border: 1px solid #666666;" /><br /> Build status on dashboard
</div>

TeamCity provides support for [setting the build status from a build script][3]. By adding some output to the powershell script, like so:

<pre>#TeamCity output
Write-Host "##teamcity[buildStatus text='{build.status.text}, Build warnings: $count']"</pre>

Each successful build will also display the number of warnings that were captured.

<div style="text-align: center; font-size: 90%; color: #666666; margin: .5em">
  <img src="http://www.tiernok.com/LTDBlog/TeamCityBuildWarnings/BuildStatusAfter.png" alt="Build status on dashboard, with warnings" style="border: 1px solid #666666;" /><br /> Build status on dashboard, with warnings
</div>

Better, but what about historical values? And I still don&#8217;t like that text file artifact.

## Warning Count as a Custom Chart

TeamCity also provides the ability to add custom charts based on either built-in or [custom statistics][4]. Custom statistics are reported similar to the build status output above:

<pre>Write-Host "##teamcity[buildStatisticValue key='buildWarnings' value='$count']"</pre>

Adding a [custom chart][5] requires us to dig into the configurations of TeamCity. I&#8217;m going to add a chart that will be displayed for any build that provides the warning count number above, so I&#8217;ll open the <code class="codespan">[teamCity data dir]/config/main-config.xml</code> file and add the following section:

<pre>&lt;graph title="Build Warnings" hideFilters="showFailed" seriesTitle="Warning" format=""&gt;
    &lt;valueType key="buildWarnings" title="Warnings"/&gt;
&lt;/graph&gt;</pre>

This will add a chart to the Statistics tab of the build. After a few builds this is what I have:

<div style="text-align: center; font-size: 90%; color: #666666; margin: .5em">
  <img src="http://www.tiernok.com/LTDBlog/TeamCityBuildWarnings/WarningChart.png" alt="Build Warning Statistics" style="border: 1px solid #666666;" /><br /> Build Warning Statistics
</div>

It probably would look better if I hadn&#8217;t built with the same number of warnings each time, but you get the point. The mouse hover works just like the built-in charts, linking to the run status for the individual point.

Ok, getting better, but I think we can take it one step further. 

## Adding a Custom Build Warnings Tab

So far we have improved methods of seeing the warning count and watching how it changes over time, but the actual list still leaves something to be desired. Luckily, TeamCity supports [custom report tabs][6] in the Build Results. This gives us an easily accessible place to put the warnings and, since it uses HTML, better formatting options than the text file.

First I need to update the powershell script to output the HTML file. TeamCity will be picking up an entire folder for the report tab, so I could add some external CSS or image files for my report, but I&#8217;ll leave that for another day.

<pre># html report output
$check = Test-Path -PathType Container BuildWarningReport
if($check -eq $false){
    New-Item 'BuildWarningReport' -type Directory
}
$stream = [System.IO.StreamWriter] "BuildWarningReport/index.html"
$stream.WriteLine("&lt;html&gt;&lt;head&gt;&lt;/head&gt;&lt;body&gt;&lt;h1&gt;$count Build Warnings&lt;/h1&gt;")
$stream.WriteLine("&lt;ul&gt;")
$warnings | % { $stream.WriteLine("&lt;li&gt;$_&lt;/li&gt;") }
$stream.WriteLine("&lt;/ul&gt;")
$stream.WriteLine("&lt;/body&gt;&lt;/html&gt;")
$stream.Close()</pre>

I&#8217;ve added HTML output to the script with a hardcoded output location that ensures the report directory exists before writing the index.html page. I&#8217;ve hardcoded this value to reduce the amount of thinking &#8216;ll need to do as I add this to other projects (keeps it consistent from output name to artifact setting to report tab configuration).

The next step is to configure the project to capture the folder as an artifact:

<div style="text-align: center; font-size: 90%; color: #666666; margin: .5em">
  <img src="http://www.tiernok.com/LTDBlog/TeamCityBuildWarnings/ArtifactConfig_Report.png" alt="Artifact configuration" style="border: 1px solid #666666;" /><br /> Artifact configuration
</div>

Then the last step is to modify the TeamCity configuration to recognize that when I output archives like that, I want to treat them as a report. To do this I add the following chunk of XML to my <code class="codespan">[TeamCity data directory]/config/main-config.xml</code> file (per the documentation link above):

<pre>&lt;report-tab title="Build Warnings" basePath="BuildWarningReport" startPage="index.html" /&gt;</pre>

And there we go, the custom report tab is available in the build results:

<div style="text-align: center; font-size: 90%; color: #666666; margin: .5em">
  <img src="http://www.tiernok.com/LTDBlog/TeamCityBuildWarnings/WarningsTab.png" alt="Build Warnings tab in Run Results" style="border: 1px solid #666666;" /><br /> Build Warnings tab in Run Results
</div>

Which takes us from no visibility into our warnings, to five different methods of viewing the information.

## Wrap-up

From having to Ctrl+F through the build log all the way to plugin-level output in a few easy steps. After setting this up one time, the only pieces that needed to be repeated for additional builds are the addition of the /logger parameter for MSBuild and the powershell build step to extract the results, and capturing the artifact for the HTML page. All of the output is either built in to the script or applies to the whole server and is displayed whenever the statistics or archive are present in a build.

Here is the finished script:

<pre>Param(
    [parameter(Mandatory=$true)]
    [alias("f")]
    $FilePath,
    [parameter()]
    [alias("o")]
    $RawOutputPath
)

$warnings = @(Get-Content -ErrorAction Stop $FilePath |       # Get the file content
                Where {$_ -match '^.*warning CS.*$'} |        # Extract lines that match warnings
                %{ $_.trim() -replace "^s*d+&gt;",""  } |      # Strip out any project number and caret prefixes
                sort-object | Get-Unique -asString)           # remove duplicates by sorting and filtering for unique strings

$count = $warnings.Count

# raw output
Write-Host "MSBuild Warnings - $count warnings ==================================================="
$warnings | % { Write-Host " * $_" }

#TeamCity output
Write-Host "##teamcity[buildStatus text='{build.status.text}, Build warnings: $count']"
Write-Host "##teamcity[buildStatisticValue key='buildWarnings' value='$count']"

# file output
if($RawOutputPath){
    $stream = [System.IO.StreamWriter] $RawOutputPath
    $stream.WriteLine("Build Warnings")
    $stream.WriteLine("====================================")
    $stream.WriteLine("")
    $warnings | % { $stream.WriteLine(" * $_") }
    $stream.Close()
}

# html report output
$check = Test-Path -PathType Container BuildWarningReport
if($check -eq $false){
    New-Item 'BuildWarningReport' -type Directory
}
$stream = [System.IO.StreamWriter] "BuildWarningReport/index.html"
$stream.WriteLine("&lt;html&gt;&lt;head&gt;&lt;/head&gt;&lt;body&gt;&lt;h1&gt;$count Build Warnings&lt;/h1&gt;")
$stream.WriteLine("&lt;ul&gt;")
$warnings | % { $stream.WriteLine("&lt;li&gt;$_&lt;/li&gt;") }
$stream.WriteLine("&lt;/ul&gt;")
$stream.WriteLine("&lt;/body&gt;&lt;/html&gt;")
$stream.Close()</pre>

To recap, we started with some warning messages randomly scattered across the build log. We ended with the warning count automatically showing in the build status on the dashboard, a nice chart of the number over time, and three different ways to view the detailed list. I hope this proves useful to others as well, now I have to go and fix the sample warnings I added before I forget about them. 🙂

 [1]: http://wiki.ltd.local/index.php/Eli%27s_Continuous_Delivery_Project "Wiki writeup on my Continuous Delivery project"
 [2]: http://www.jetbrains.com/teamcity/ "TeamCity by JetBrains"
 [3]: http://confluence.jetbrains.net/display/TCD7/Build+Script+Interaction+with+TeamCity#BuildScriptInteractionwithTeamCity-ReportingBuildStatus "TeamCity documentation for Build Script Interaction"
 [4]: http://confluence.jetbrains.net/display/TCD7/Build+Script+Interaction+with+TeamCity#BuildScriptInteractionwithTeamCity-ReportingBuildStatistics "TeamCity documentation - reporting custom statistics"
 [5]: http://confluence.jetbrains.net/display/TCD7/Custom+Chart "TeamCity documentation - Custom Statistics Charts"
 [6]: http://confluence.jetbrains.net/display/TCD3/Including+Third-Party+Reports+in+the+Build+Results#IncludingThird-PartyReportsintheBuildResults-Tabs "TeamCity documentation: Including third-party reports as the build-results tabs"