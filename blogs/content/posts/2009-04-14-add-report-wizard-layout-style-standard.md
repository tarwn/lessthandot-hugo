---
title: Add Report Wizard Layout Style (Standard Company Style)
author: Ted Krueger (onpnt)
type: post
date: 2009-04-14T22:23:06+00:00
url: /index.php/datamgmt/datadesign/add-report-wizard-layout-style-standard/
views:
  - 7446
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
Did you know you can add layout styles to the report wizard and load into existing reports? I&#8217;m referring to the list that contains slate, forest, generic etc.. I always thought this was pretty cool and thought I would share how to do it.

First go into your respected Visual Studio installation directory. I believe the location is the same and has not changed from 7,8 and 9. 

Find this directory

C:Program FilesMicrosoft Visual Studio 8Common7IDEPrivateAssembliesBusiness Intelligence WizardsReportsStylesen

Note there is a backup one level up. I suggest you leave that alone and as a recovery point. If you mess this up, the report wizard will fail on you. Guess that means this is a, &#8220;At your own risk&#8221; post.

Open StyleTemplates.xml for editing.

It&#8217;s pretty straight forward and I bet you already see what to do. For this example I&#8217;ll add a simple style

<pre>&lt;StyleTemplate Name="CompanyStandard"&gt;
		&lt;Label&gt;Company Standard Format&lt;/Label&gt;
		&lt;Styles&gt;
			&lt;Style Name="Title"&gt;
				&lt;BackgroundColor&gt;DarkGray&lt;/BackgroundColor&gt;
				&lt;FontFamily&gt;Verdana&lt;/FontFamily&gt;
				&lt;FontSize&gt;18pt&lt;/FontSize&gt;
				&lt;Color&gt;#000000&lt;/Color&gt;
			&lt;/Style&gt;
			&lt;Style Name="Table"&gt;&lt;/Style&gt;
			&lt;Style Name="Table Header"&gt;
				&lt;BackgroundColor&gt;Gainsboro&lt;/BackgroundColor&gt;
				&lt;FontFamily&gt;Verdana&lt;/FontFamily&gt;
				&lt;FontSize&gt;10pt&lt;/FontSize&gt;
				&lt;FontWeight&gt;Bold&lt;/FontWeight&gt;
				&lt;Color&gt;#000000&lt;/Color&gt;
				&lt;BorderStyle&gt;
					&lt;Default&gt;Solid&lt;/Default&gt;
				&lt;/BorderStyle&gt;
				&lt;BorderColor&gt;
					&lt;Default&gt;DarkGray&lt;/Default&gt;
				&lt;/BorderColor&gt;
			&lt;/Style&gt;
			&lt;Style Name="Detail"&gt;
				&lt;FontFamily&gt;Verdana&lt;/FontFamily&gt;
				&lt;FontSize&gt;9pt&lt;/FontSize&gt;
				&lt;BorderStyle&gt;
					&lt;Default&gt;Solid&lt;/Default&gt;
				&lt;/BorderStyle&gt;
				&lt;BorderColor&gt;
					&lt;Default&gt;DarkGray&lt;/Default&gt;
				&lt;/BorderColor&gt;
			&lt;/Style&gt;
		&lt;/Styles&gt;
	&lt;/StyleTemplate&gt;</pre>

Save that in there and close the XML file.

Now open up Visual Studio and create a new report server project. Create a new report from the wizard and get to the layout style section. You should see Company Standard Format listed now. Thing about this is, I don&#8217;t know about getting a preview. I never spent the time to find out where the configuration was to add this layout to be interrupted. Honestly don&#8217;t think it&#8217;s that important. You&#8217;ll need to test the layout pretty well to get it perfect in order to make it your standard. Once you do though it will save a lot of time editing layouts.

So now my report loads with my new company standard formatting and we gain standards along with time saving configurations 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//ssrs_layout_1.gif" alt="" title="" width="498" height="97" />
</div>