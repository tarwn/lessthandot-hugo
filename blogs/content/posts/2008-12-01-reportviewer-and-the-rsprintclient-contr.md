---
title: ReportViewer and the RSPrintClient Control Problems
author: Ted Krueger (onpnt)
type: post
date: 2008-12-01T23:58:43+00:00
url: /index.php/datamgmt/datadesign/reportviewer-and-the-rsprintclient-contr/
views:
  - 7110
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server Admin

---
Ever seen this when you try to use the print client control in SSRS, &#8220;Unable to load client print control&#8221;?

Well if you haven&#8217;t I&#8217;m willing to bet you will. Member pmch22 and myself worked on this little fun error message for the entire day. First the problem seem to have come up a few weeks ago when someone tried to print and received this message. They downloaded a patch and applied it but no luck. I thought I had the problem fixed at the SSRS instance level but that was a long way from the truth. 

Today we set out to kill this little bug and here is what we found. 

If you google this nasty you get fun hits about a Microsoft Windows Update 956391. Some kill bits hotfixes. It sure killed some bits let me tell you. Killed the client print control on the report server 🙁 Simple enough sense the next search is the fix which is update KB954606 to fix the later update. update to fix the update. Don&#8217;t laugh and don&#8217;t say MS sucks because if you&#8217;ve ever developed anything you&#8217;ve had to send them out as well. Well unless all you&#8217;ve developed is a recipe front-end for your mother-in-law to get in good with her. 😉

So this did work. Only problem is it only worked as far as the ReportServer would take you. In pmch22&#8217;s article &#8220;Intranet site for Reporting Services Reports&#8221; (which is an excellent write up) was related to the problem we still faced. For the ReportViewer call it would still give the error on clicking the print icon to launch the ActiveX. I tried reapplying a few things and just before all else failed I found this handy little article &#8220;Client Print Fails to Load After Microsoft Update 956391&#8221; at &#8220;Brian Hartman&#8217;s Report Viewer Blog&#8221;. It was the best explanation of this problem I found thus far. Lucky for me in that blog entry was a list of what the author had tried in step order. There was one step that bolded out at me. The report viewer!!! I should have thought of this in the first place really. Still kicking myself for it. When there is a problem with an object you should always turn to the originating source and verify it is correct before running off on a ghost hunt. 

So if you found yourself with the update mentioned don&#8217;t uninstall it for one. Microsoft releases these for a reason and uninstalling them usually isn&#8217;t the best course. If you&#8217;re smart you have WSUS running and you&#8217;re controlling in a test environment before applying anyhow. The company we are with now does not but they will get there. Growing and growing fast. 

So in an attempt to fix the error try these steps&#8230;
  
1. Verify you have KB956391 installed. If it isn&#8217;t you have other issues
  
2. Verify you updated your web references and re-deploy your site.
  
3. Apply update KB954606 and verify ReportServer can print again.
  
4. Apply the SP1 to your ReportViewer (This is what fixed us after 1,2 and 3).
  
5. Verify your client tools, BIDS, VS.NET etc.. is all updated. Remmber, if your instance is running at .3247 then you should be also to ensure no problems.

We ran through step 5 because this is always good. Being the DBA I failed in not making sure the client tools on one machine were updated. This is improtant to all the users you allow this connectivity so please make sure everyone is at the same playing level.

Again the blog I found and that helped me fix this is @ http://blogs.msdn.com/brianhartman/archive/2008/11/05/client-print-fails-to-load-after-microsoft-update-956391.aspx

I thank that person for taking the time to write it up. 

Listing of related links&#8230;
  
KB954606 http://www.microsoft.com/downloads/details.aspx?familyid=5148B887-F323-4ADB-9721-61E1C0CFD213&displaylang=en

Report Viewer 2005 SP1 http://www.microsoft.com/downloads/details.aspx?familyid=82833F27-081D-4B72-83EF-2836360A904D