---
title: Continuous Delivery – Adding Static Analysis
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-04-27T10:18:00+00:00
ID: 1605
excerpt: "Running static analysis checks can help keep us on track with our standards, identify unused code, identify similar code blocks, and much more. In a manual process, running these tools can be time consuming, costing time to wait for the run complete, more time if we don't abandon them after the first run and have to maintain a schedule, and even more time if someone has to keep a spreadsheet somewhere to compare the results from run to run. Add an automated build process, and we can net the same level of information and trending for a modest setup cost."
url: /index.php/enterprisedev/application-lifecycle-management/continuous-delivery-adding-static-analysis/
views:
  - 21552
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
tags:
  - continuous delivery
  - jenkins
  - mvc music store
  - static analysis

---
Running static analysis checks can help keep us on track with our standards, identify unused code, identify similar code blocks, and much more. In a manual process, running these tools can be time consuming, costing time to wait for the run complete, more time if we don&#8217;t abandon them after the first run and have to maintain a schedule, and even more time if someone has to keep a spreadsheet somewhere to compare the results from run to run. Add an automated build process, and we can net the same level of information and trending for a modest setup cost.

Up until now, all of the additions to my [Continuous Delivery project][1] have been focused on building, testing, and deploying the code. In this post we&#8217;ll discuss the potential benefits and costs of adding static analysis to the pipeline and then walk through the details of adding several of those checks into the sample project.

_Note: The components outlined in this post have actually been part of the continuous delivery pipeline for a few months, driving changes and cleanup commits from [December 21st][2] forward as I continued to build out some of the later components._

## Costs

Even with automation, there is still a cost to static analysis. These tools take time and resources to run. The larger our codebase gets, the more time and memory the tools will require to analyze the code and generate results. We have two dials we can use to help reduce the impact on our end-to-end delivery time: tool selection and process location.

The importance and usefulness of the data should drive which tools we use and where we configure them to run. The goal is to limit the impact on delivery time while selecting the right tools for our projects and development style. If we intend to use the data for standards enforcement or as process constraints then it is going to be more effective as part of the pipeline, but if it is for informational purposes then a parallel step or separate, scheduled build may be more appropriate. Altering the location can be used to run tasks as part of the primary build chain*, in parallel to the main build chain, or even in a separate build, each with higher or lower impact to that end-to-end delivery time.

_* Note: If you map out your build process along with the amount of time it takes for each step and the amount of time that is spent waiting in between steps, you can total the numbers to calculate a total delivery time for defect-free commits. This is helpful when evaluating the costs of adding an additional sequential step, parallel step, or none at all._

## Benefits

Static analysis tools provide visibility into the style and structure of our code. There are tools to help capture TODO comments we&#8217;ve left in the code, tools to analyze the level of copy and paste, code coverage, even tools that offer so many measures they incorporate query engines (like [NDepend][3]). The ability to run them regularly without the ongoing cost is great, but by running them as part of our builds we also gain the ability to apply the results to the build quality, potentially failing the build if the code exhibits too much of a bad behavior. 

When they&#8217;re used correctly, the ability to affect the build quality brings the greatest benefit. Failing the build when the code doesn&#8217;t meet our quality forces us to address the issues immediately. The changes we committed are still fresh in our minds, making this is the fastest (and cheapest) point in time we could clean them up. 

To support this, we&#8217;re forced to define our standards for code quality. When a build fails, we are then making a correction to meet our guaranteed standards, which is easier for our company or project manager to accept than if we had taken it upon ourselves to clean up some code in the middle of a crunch. 

Lastly, it helps prevent us from putting those fixes off and building up a mountain of technical debt that tends to bog down execution on so many projects. We are keeping the code more consistent and doing so at the cheapest time possible.

## Choosing Some Tools

So there are some costs and some benefits, but what does it look like when these tools are wired in and running as part of the build? Let&#8217;s add them and find out.

For the purposes of the sample Continuous Delivery project, I am going to be adding:

  * Compiler Warnings &#8211; Scanning for compiler warnings in the output is the cheap and easy to clean up
  * Open Tasks &#8211; Scanning for HACK, TODO, and REFACTOR tags in the source code
  * Duplicate Code &#8211; Scanning for duplicate code blocks that could be refactored
  * Rule Based Scanning &#8211; Scanning for situations that violate a set of style rules
  * Code Coverage &#8211; Evaluate the test coverage levels on our code to identify areas with low coverage

As we go through each one, I&#8217;ll identify the external tool and/or Jenkins plugins.

### Static Analysis Trends

Several of the plugins below produce data that can be captured and displayed on the job summary screen as trends. They require the [Analysis Collector Plugin][4] as a prerequisite. There are no setup steps to this core plugin, so after installing it we can continue on to the real work below.

### Compiler Warnings

Compiler warnings can be captured easily using the [Warnings Plugin][5]. After adding it to the Jenkins server, we can open the CI job and add the checks in the &#8220;post-build Actions&#8221; section:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Warnings.png" title="Analysis - Compiler Warnings" /><br /> Analysis &#8211; Compiler Warnings
</div>

The plugin has the ability to scan the build output and files in our workspace, with parsers for a number of different tools. In this case I&#8217;ve chosen to capture the MSBuild output, but I could just as easily capture output from something like JSLint (speaking of future projects). A trend of the warning count is added to the job summary screen and a summary of the warnings is displayed on each individual run:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Warnings_Summary.png" title="Analysis - Compiler Warnings Summary" /><br /> Analysis &#8211; Compiler Warnings Summary
</div>

And drilling in we can see the details:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Warnings_Detail.png" title="Analysis - Compiler Warnings Details" /><br /> Analysis &#8211; Compiler Warnings Details
</div>

In this case, we&#8217;re actually looking at the warnings from 49 builds ago. Since the results are incorporated into the build summary, I can access past versions for as long as I keep the job history.

### Open Tasks

Open tasks refer to those little TODO and HACK tags we litter throughout our code, always with the intent of someday coming back and doing something with them. Using the [Task Scanner Plugin][6], the build can scan the code and add these comments to the trends and build details.

The plugin adds a section to the &#8220;post-build Actions&#8221; section, which we can then configure to fit our specific needs:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Tasks.png" title="Analysis - Open Tasks" /><br /> Analysis &#8211; Open Tasks
</div>

In the job, we can specify which tags to capture from the code and the filename pattern to use for code files. There is also an advanced section where we can add thresholds. For instance, this is where I could define that 20 or more cases of the high priority tag should cause the build to fail until we get our HACK&#8217;s back under control.

The output for this plugin is similar to the Compiler Warnings, with a trend on the main project page, a summary in the individual job:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Tasks_Summary.png" title="Analysis - Open Tasks Summary" /><br /> Analysis &#8211; Open Tasks Summary
</div>

And a details page we can drill into:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Tasks_Details.png" title="Analysis - Open Tasks Details" /><br /> Analysis &#8211; Open Tasks Details
</div>

The scan pulls out not just the presence of these tags, but also the content and location of the messages. This makes it easy to quickly scan them and pick one to try and knock off the list.

### Duplicate Code

[Code duplication][7] is a code smell that adds a number of risks to your codebase, violating the DRY ([Don&#8217;t Repeat Yourself][8]) principle.

There are a number of tools out there to detect duplication in code, in this case I chose to use [Simian][9] and import and display the results via the [Violations Plugin][10], which is also used below for analyzing the code against common problems and style rules.

First, we need to run simian against our codebase to produce a report. I&#8217;ve added a windows batch command as part of the build steps to run this command, placing it after the unit tests but before the test deployment. 

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Simian_Command.png" title="Analysis - Simian Execution" /><br /> Analysis &#8211; Simian Execution
</div>

I&#8217;ve told Simian to run against all *.cs files in my project directory, excluding the sample data file that is used to generate a sample Entity Framework model. At the tail end I force it to return an exit code of 0 so the job will continue to run the later steps and post-build analysis. Simian will helpfully return a failure exit code when it finds violations, but that&#8217;s not useful for this case.

In the &#8220;post-build Action&#8221; section, I&#8217;ve provided the path to the output file Simian creates:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Simian_PostBuild.png" title="Analysis - Simian - Post Build" /><br /> Analysis &#8211; Simian &#8211; Post Build
</div>

We also have the ability to define thresholds for good, stormy, and unstable, based on the number of duplicate blocks found.

In the summary screen for the job and each individual run screen, the DRY plugin will add a trend that displays the number of duplicate blocks found over time.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Simian_Summary.png" title="Analysis - Simian - Summary Trend" /><br /> Analysis &#8211; Simian &#8211; Summary Trend
</div>

As we drill into the details, we get a list of the files where duplicates occur, and can then drill down into those to see the actual code that was found.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Simian_Details.png" title="Analysis - Simian - Detail Trend" /><br /> Analysis &#8211; Simian &#8211; Detail Trend
</div>

In this case it has found an interface and an implementation of that interface that have a block of 11 lines of code in common.

### Code Standards

When it comes to code standards, the two main engines I had to choose from were [Gendarme][11] and [FxCop][12]. The [Violations Plugin][10] can handle either, so I opted for Gendarme (I believe it was faster for my specific scenario).

Like Simian above, I need to first run the executable against the codebase, then consume the generated report post-build to feed the trends and details.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Gendarme_Command.png" title="Analysis - Gendarme - Batch Command" /><br /> Analysis &#8211; Gendarme &#8211; Batch Command
</div>

I&#8217;ve added the command for Gendarme after the unit tests and before the Simian command above, pointing it at the dll for the project and specifying I want XML output. As with Simian, I&#8217;ve forced the batch to return an exit code of 0 so the run will continue through to process the later steps.

also like simian, we specify the location of the XML file in the Violations section of the &#8220;post-Build Actions&#8221;.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Simian_PostBuild.png" title="Analysis - Gendarme - Post Build" /><br /> Analysis &#8211; Gendarme &#8211; Post Build
</div>

The Violations plugin overlays the Gendarme results on the chart we saw above:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Simian_Summary.png" title="Analysis - Gendarme - Summary Trend" /><br /> Analysis &#8211; Gendarme &#8211; Summary Trend
</div>

And the detail results are also very similar:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Gendarme_Details.png" title="Analysis - Gendarme - Detail Trend" /><br /> Analysis &#8211; Gendarme &#8211; Detail Trend
</div>

However once we drill in, we not only get the code view but also can hover over the identified warning locations for details on the rule the gendarme is reporting on.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Gendarme_Details2.png" title="Analysis - Gendarme - Code Details" /><br /> Analysis &#8211; Gendarme &#8211; Code Details
</div>

The early downward trend above corresponds to the commits starting on [December 21st][2], when I started cleaning up some of the worst offenders.

### Code Coverage

Code coverage identifies the level of test coverage in our codebase by monitoring the tests as they run against our assemblies. For this step I used [OpenCover][13] combined with [ReportGenerator][14] to convert the output into an HTML report, and the [HTML Publisher Plugin][15] to incorporate the generated reports into the job results.

This one took more steps for setup:

  * Download or clone the OpenCover source from Github
  * Build using the included Build.bat script
  * Copy the compiled binary to my build server
  * Copy the ReportGenerator folder to server
  * Replace existing MS Test batch command with call to run it from OpenCover and process results with ReportGenerator (below)

```text
mkdir "%WORKSPACE%testresults"
"C:AdditionalScriptsOpenCoverOpenCover.Console.exe" -target:"C:Program Files (x86)Microsoft Visual Studio 10.0Common7IDEmstest.exe"  -targetargs:"/resultsfile:"%WORKSPACE%testresultsMyTests.Results.xml" /testcontainer:"%WORKSPACE%MvcMusicStoreTestsbinReleaseMvcMusicStoreTests.dll" /nologo"  -mergebyhash -filter:"+[MvcM*]*" -output:"%WORKSPACE%testresultsopencovertests.xml"
"C:AdditionalScriptsReportGeneratorReportGenerator.exe" "%WORKSPACE%testresultsopencovertests.xml" "%WORKSPACE%testresultshtml" HtmlSummary
"C:AdditionalScriptsReportGeneratorReportGenerator.exe" "%WORKSPACE%testresultsopencovertests.xml" "%WORKSPACE%testresultshtml" Html
```
This command (1) creates the test results folder, (2) runs OpenCover with arguments to locate the MSTest executable, flags to use with MSTest, a filter to limit execution to the main assemblies, and an output filename. The last lines (3) run the ReportGenerator executable against the results, producing the HtmlSummary report and (4) detailed HTML reports for each file. 

Checking the &#8220;Publish HTML reports&#8221; option in &#8220;post-build Actions&#8221;, we can specify the folder, index page, and title to use for the generated reports.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Coverage_PostBuild.png" title="Analysis - Coverage - PostBuild" /><br /> Analysis &#8211; Coverage &#8211; PostBuild
</div>

This adds a sidebar link to the job summary, which links to the summary we generated and the detail pages associated with it.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Coverage_Button.png" title="Analysis - Coverage Link" /><br /> Analysis &#8211; Coverage Link
</div>

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Analysis_Coverage_Summary.png" title="Analysis - Coverage Summary Report" /><br /> Analysis &#8211; Coverage Summary Report
</div>

Each of the links has a details report behind it that includes code statistics, like cyclomatic complexity, and a copy of the code with markers to indicate covered vs uncovered lines.

## Summary

The addition of these tools to my project was done as part of the CI build, placing them sequentially into the process. This works with a smaller project but needs to be evaluated more carefully in a larger project.

The results from these tests have helped me clean my code up, identify gaps in the test coverage when I accidentally created them, and given me a good amount of historical knowledge about the quality of the code itself. The setup time was not that expensive and I can continue to take advantage of the benefits in every future build I do.

 [1]: http://wiki.ltd.local/index.php/Eli%27s_Continuous_Delivery_Project "Wiki page on Eli's Continuous Delivery project"
 [2]: https://bitbucket.org/tarwn/mvcmusicstore.main/changeset/b81657c2f260 "First style clean-up commit"
 [3]: http://www.ndepend.com/ "NDepend Homepage"
 [4]: https://wiki.jenkins-ci.org/display/JENKINS/Analysis+Collector+Plugin "Jenkins Analysis Collector Plugin"
 [5]: https://wiki.jenkins-ci.org/display/JENKINS/Warnings+Plugin "Jenkins Warnings Plugin"
 [6]: https://wiki.jenkins-ci.org/display/JENKINS/Task+Scanner+Plugin "Jenkins Task Scanner plugin"
 [7]: http://en.wikipedia.org/wiki/Duplicate_code "Code duplication on Wikipedia"
 [8]: http://www.artima.com/intv/dry.html "Don't Repeat Yourself - heh"
 [9]: http://www.harukizaemon.com/simian/ "Simian homepage"
 [10]: https://wiki.jenkins-ci.org/display/JENKINS/Violations "Violations Plugin"
 [11]: http://www.mono-project.com/Gendarme "Gendarme at Mono Project"
 [12]: http://en.wikipedia.org/wiki/FxCop "Wikipedia page for FxCop"
 [13]: https://github.com/sawilde/opencover "OpenCover on Github"
 [14]: http://reportgenerator.codeplex.com/ "ReportGenerator on COdeplex"
 [15]: https://wiki.jenkins-ci.org/display/JENKINS/HTML+Publisher+Plugin "HTML Publisher Plugin"