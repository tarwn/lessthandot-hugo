---
title: Continuous Delivery – Adding the Load Testing Stage
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-02-17T10:48:00+00:00
excerpt: "Adding load testing to my continuous build process provides several benefits for a fairly cheap entry fee. As the development process progresses, I'll have a baseline and know if I add something to the application that impacts the performance. I'll also be able to accurately discuss it's performance when asked, instead of guessing."
url: /index.php/enterprisedev/application-lifecycle-management/continuous-delivery-adding-the-load/
views:
  - 12324
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
tags:
  - continuous delivery
  - jenkins
  - load testing
  - mvc music store
  - wcat

---
Adding load testing to my continuous build process provides several benefits for a fairly cheap entry fee. As the development process progresses, I&#8217;ll have a baseline and know if I add something to the application that impacts the performance. I&#8217;ll also be able to accurately discuss it&#8217;s performance when asked, instead of guessing. And if I want to increase the performance, that same baseline will serve as a guide on my progress. 

Not every application needs to process 100,000 transactions/second, but the cost of guessing how well our application will perform tends to catch up with us.

In the [previous post][1], I walked through using WCAT to define and execute a load test scenario against the site from my [continuous delivery project][2]. In this post, I&#8217;ll take those WCAT scripts and incorporate them into Jenkins as a new build step in my delivery pipeline, not only running the load test but capturing the results so I can show them over time.

## Prep the Server

Before I can start configuring my new build step, I need to prep the server. Following the steps in my prior post, I&#8217;ll download the WCAT msi to the build server and install it, then register the build server as a WCAT client:

<pre>cscript //H:cscript
wcat.wsf –terminate –update –clients localhost</pre>

And, of course, wait for the obligatory reboot to go through.

## Creating the Build Job

Initially I want a build job that I can run manually, to let me tweak the test execution, results capture, and plotting. The overall plan for the job will be to:

  * Get the load test scripts from a dedicated source repository
  * Pull in the compiled artifacts from the first step (continuous integration)
  * Deploy the artifacts to a remote IIS web application folder
  * Smoke test the deployment
  * Run the Load Test against the delpoyed application
  * Capture the results of the Smoke Test and Load Test

_While I originally expected the load test portion to be the trickiest, the reality was that plotting the results ended up being the hardest part of the whole adventure._

First up is defining the parameter for the CI build number:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/LoadTest_Config1.png" title="Load Test Job - Build Parameter" /><br /> Load Test Job &#8211; Build Parameter
</div>

Then I need to add in the code repository that I&#8217;m hosting the Load Test scripts from the last post in:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/LoadTest_Config2.png" title="Load Test Job - Load Test Scripts" /><br /> Load Test Job &#8211; Load Test Scripts
</div>

I&#8217;ve also added the [EnvFile plugin][3] to allow me to store critical information, like server names and passwords, in an external settings file:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/LoadTest_Config3.png" title="Load Test Job - External Settings" /><br /> Load Test Job &#8211; External Settings
</div>

Then using the [Copy Artifact][4] plugin, I&#8217;ll add a build step to retrieve the zipped deployable website from the CI build step:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/LoadTest_Config4.png" title="Load Test Job - Copy Artifacts" /><br /> Load Test Job &#8211; Copy Artifacts
</div>

With those retrieved, I can now deploy them to my remote server using MS Deploy and execute the VBScript file I created to smoke test the deployed site, each as windows batch command steps:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/LoadTest_Config4c.png" title="Load Test Job - Deploy + Smoke Test" /><br /> Load Test Job &#8211; Deploy + Smoke Test
</div>

_Note: These commands and scripts are available in the [Deploy and Smoke Test post][5]_

The actual command to execute the load test is nicely wrapped in a the Run.cmd file, so I can add a batch command to execute that:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/LoadTest_Config4b.png" title="Load Test Job - Run Load Test" /><br /> I only included this screenshot to be consistent with the rest of the steps 🙂
</div>

The last steps archive the log.xml file that WCAT produces, 

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/LoadTest_Config5.png" title="Load Test Job - Archive Log File" /><br /> Load Test Job &#8211; Archive Log File
</div>

capture the results of the smoke test, 

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/LoadTest_Config6.png" title="Load Test Job - Capture Smoke Test Results" /><br /> Load Test Job &#8211; Capture Smoke Test Results
</div>

and clutter up my twitter feed. 

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/LoadTest_Config7.png" title="Load Test Job - Twitter Checkbox" /><br /> Twitter Checkbox
</div>

With that I have a fully functional build job that deploys a website on demand and load tests it. 

And now the really hard part. Doing something with the load test results (you think I&#8217;m being sarcastic, but just wait).

## Displaying the Results

This was an interesting project and, oddly enough, went like so many IT projects do. We get 90% of the way done and then find out we have another 180% to do.

There is really only one plugin in Jenkins that I could find to handle plotting for free form data. I did look into trying to misuse the JMeter plugin (like I repurpose the JUnit one to capture my smoke test results), but at a glance it looked like JMeter provide the detailed data without the summaries, while WCAT is just giving me the summaries. 

### The Plot Plugin (Duh Duh Duuuuh)

The [Plot Plugin][6] is finicky, poorly documented, and has some hinkiness in the Jenkins display that I haven&#8217;t seen from other plugins. It annoyed me enough that I actually started reading through the source code in [github][7] to get help figuring out some of it&#8217;s behavior (and then after seeing it, briefly considered trying to relearn java long enough to add some much needed fixes). 

Among it&#8217;s other weaknesses, the plot plugin doesn&#8217;t allow you to set data labels, it automatically uses the tag names of the XML. Which doesn&#8217;t work so well when your XML nodes all have the same name. 

Like the WCAT results.

Argh.

So after much wrestling, I decided to map the WCAT data to a new XML file purely for the purposes of feeding the plot plugin (and to also reconfigure it so I could enter all the points for a plot from one XPath query, don&#8217;t get me started on forcing me to re-enter the filename for each XPath statement for the same plot).

**Transform.xsl**

<pre><?xml version="1.0"?&gt;

<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0"&gt;

<xsl:template match="/"&gt;
	<result&gt;
		<persecond&gt;
			<TransactionsPerSecond&gt;<xsl:value-of select='//section[@name="summary"]/table[@name="summarydata"]/item/data[@name="tps"]' /&gt;</TransactionsPerSecond&gt;
			<RequestsPerSecond&gt;<xsl:value-of select='//section[@name="summary"]/table[@name="summarydata"]/item/data[@name="rps"]' /&gt;</RequestsPerSecond&gt;
		</persecond&gt;
		<total&gt;
			<Transactions&gt;<xsl:value-of select='//section[@name="details"]/table[@name="requeststats"]/item[1]/data[@name="transactions"]' /&gt;</Transactions&gt;
			<Requests&gt;<xsl:value-of select='//section[@name="details"]/table[@name="requeststats"]/item[1]/data[@name="requests"]' /&gt;</Requests&gt;
			<Errors&gt;<xsl:value-of select='//section[@name="summary"]/table[@name="summarydata"]/item/data[@name="terrors"]' /&gt;</Errors&gt;
		</total&gt;
		<responsetime&gt;
			<Average&gt;<xsl:value-of select='//section[@name="details"]/table[@name="histogram"]/item[2]/data[@name="response_time_avg"]' /&gt;</Average&gt;
			<Minimum&gt;<xsl:value-of select='//section[@name="details"]/table[@name="histogram"]/item[2]/data[@name="response_time_min"]' /&gt;</Minimum&gt;
			<NinetyFivePercent&gt;<xsl:value-of select='//section[@name="details"]/table[@name="histogram"]/item[2]/data[@name="response_time_95"]' /&gt;</NinetyFivePercent&gt;
			<NinetyNinePercent&gt;<xsl:value-of select='//section[@name="details"]/table[@name="histogram"]/item[2]/data[@name="response_time_99"]' /&gt;</NinetyNinePercent&gt;
			<Maximum&gt;<xsl:value-of select='//section[@name="details"]/table[@name="histogram"]/item[2]/data[@name="response_time_max"]' /&gt;</Maximum&gt;
		</responsetime&gt;
	</result&gt;
</xsl:template&gt;

</xsl:stylesheet&gt;</pre>

And then I can use a quick VBScript file to execute the XSL against the WCAT log.xml file:

**Transform.vbs**

<pre>Dim xml, xsl
set xml = CreateObject("Microsoft.XMLDOM")
xml.async = false
xml.load("log.xml")

'Load the XSL
set xsl = CreateObject("Microsoft.XMLDOM")
xsl.async = false
xsl.load("transform.xsl")

Dim fso, fs
Set fso = CreateObject("Scripting.FileSystemObject")
Set fs = fso.CreateTextFile("result.xml",true)
fs.Write(Replace(xml.transformNode(xsl),"<?xml version=""1.0"" encoding=""UTF-16""?&gt;",""))
fs.Close
Set fs = Nothing
Set fso = Nothing</pre>

Which nets me a results file, like so:

**result.xml**

<pre><result&gt;
	<persecond&gt;
		<TransactionsPerSecond&gt;1.80</TransactionsPerSecond&gt;
		<RequestsPerSecond&gt;29.56</RequestsPerSecond&gt;
	</persecond&gt;
	<total&gt;
		<Transactions&gt;216</Transactions&gt;
		<Requests&gt;3547</Requests&gt;
		<Errors&gt;3</Errors&gt;
	</total&gt;
	<responsetime&gt;
		<Average&gt;270</Average&gt;
		<Minimum&gt;0</Minimum&gt;
		<NinetyFivePercent&gt;912</NinetyFivePercent&gt;
		<NinetyNinePercent&gt;1536</NinetyNinePercent&gt;
		<Maximum&gt;2563</Maximum&gt;
	</responsetime&gt;
</result&gt;</pre>

The last piece of the equation is to configure the Plot in the load test job. Here is an example of one graph configuration (the full set is pretty long):

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/LoadTest_Config8.png" title="Load Test Job - Plot Settings" /><br /> Load Test Job &#8211; Plot Settings
</div>

Each plot has an XPath statement that corresponds to one of the sections of the result XML file, that way I have unique names for the values when it stores the data and I have labels on the plots. Running the job a few times to build up data and my graph looks like this:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/load_diagram.png" title="Load Test Job - Response Time Diagram" /><br /> Load Test Job &#8211; Response Time Diagram
</div>

_Note: I suspect the fact that I got bored and was watching Netflix for the last few tests had some effect on the values_

And there we go, I now have a job that I can run on demand that will load test my site.

## Incorporate into Build Pipeline

The last step of this whole load test adventure is incorporating the load test job I&#8217;ve created into my build pipeline. 

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/pipeline_load.png" title="Continuous Delivery Pipeline with Load Test Step" /><br /> Continuous Delivery Pipeline with Load Test Step
</div>

The only changes necessary to insert the Load Test job into the pipeline is to modify my &#8220;ASPNet MVC Music Store Interface Tests&#8221; build job to trigger a parametrized build of this new load test and in the load test, check the &#8220;Build Pipeline Plugin -> Manually Execute Downstream Project&#8221; option and specify the &#8220;Deploy to QA&#8221; build step as the target. 

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://www.tiernok.com/LTDBlog/ContinuousDelivery/pipeline_wload.png" title="Continuous Delivery Pipeline w/ Load Testing Step" /><br /> Continuous Delivery Pipeline w/ Load Testing Step
</div>

With the steps rewired, the pipeline now incorporates the Load Testing job.

## Next Steps

While the plotting portion of this project took longer than I expected, I still managed to do the whole thing in under 8 hours, and that includes writing part of the first blog and the fact that I was working on it in pieces, in between sleeping, feeding the baby, and so on. 

Not every project needs to be the fastest, most responsive application on the planet, but given how cheaply you can add some basic numbers into your process, the net gain of knowing exactly how your application is performing and the heads up when you do something that really breaks performance are both valuable and fairly inexpensive to implement.

 [1]: /index.php/EnterpriseDev/application-lifecycle-management/implementing-wcat-for-load-testing "Using WCAT to Load Test"
 [2]: http://wiki.ltd.local/index.php/Eli%27s_Continuous_Delivery_Project "Continuous Delivery Wiki page"
 [3]: https://wiki.jenkins-ci.org/display/JENKINS/Envfile+Plugin "Jenkins EnvFile Plugin"
 [4]: https://wiki.jenkins-ci.org/display/JENKINS/Copy+Artifact+Plugin "Jenkins Copy Artifact Plugin"
 [5]: /index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-project-deploy-and "Continuous Delivery - Deploy and Smoke Test"
 [6]: https://wiki.jenkins-ci.org/display/JENKINS/Plot+Plugin "Jenkins Plot Plugin"
 [7]: https://github.com/jenkinsci/plot-plugin "Jenkins Plot Plugin on GitHub"