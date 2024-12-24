---
title: Add Report Wizard Layout Style (Standard Company Style)
author: Ted Krueger (onpnt)
type: post
date: 2009-04-14T22:23:06+00:00
ID: 387
url: /index.php/datamgmt/datadesign/add-report-wizard-layout-style-standard/
views:
  - 7446
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
Did you know you can add layout styles to the report wizard and load into existing reports? I'm referring to the list that contains slate, forest, generic etc.. I always thought this was pretty cool and thought I would share how to do it.

First go into your respected Visual Studio installation directory. I believe the location is the same and has not changed from 7,8 and 9. 

Find this directory

C:Program FilesMicrosoft Visual Studio 8Common7IDEPrivateAssembliesBusiness Intelligence WizardsReportsStylesen

Note there is a backup one level up. I suggest you leave that alone and as a recovery point. If you mess this up, the report wizard will fail on you. Guess that means this is a, "At your own risk" post.

Open StyleTemplates.xml for editing.

It's pretty straight forward and I bet you already see what to do. For this example I'll add a simple style

```xml
<StyleTemplate Name="CompanyStandard">
		<Label>Company Standard Format</Label>
		<Styles>
			<Style Name="Title">
				<BackgroundColor>DarkGray</BackgroundColor>
				<FontFamily>Verdana</FontFamily>
				<FontSize>18pt</FontSize>
				<Color>#000000</Color>
			</Style>
			<Style Name="Table"></Style>
			<Style Name="Table Header">
				<BackgroundColor>Gainsboro</BackgroundColor>
				<FontFamily>Verdana</FontFamily>
				<FontSize>10pt</FontSize>
				<FontWeight>Bold</FontWeight>
				<Color>#000000</Color>
				<BorderStyle>
					<Default>Solid</Default>
				</BorderStyle>
				<BorderColor>
					<Default>DarkGray</Default>
				</BorderColor>
			</Style>
			<Style Name="Detail">
				<FontFamily>Verdana</FontFamily>
				<FontSize>9pt</FontSize>
				<BorderStyle>
					<Default>Solid</Default>
				</BorderStyle>
				<BorderColor>
					<Default>DarkGray</Default>
				</BorderColor>
			</Style>
		</Styles>
	</StyleTemplate>
```
Save that in there and close the XML file.

Now open up Visual Studio and create a new report server project. Create a new report from the wizard and get to the layout style section. You should see Company Standard Format listed now. Thing about this is, I don't know about getting a preview. I never spent the time to find out where the configuration was to add this layout to be interrupted. Honestly don't think it's that important. You'll need to test the layout pretty well to get it perfect in order to make it your standard. Once you do though it will save a lot of time editing layouts.

So now my report loads with my new company standard formatting and we gain standards along with time saving configurations 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//ssrs_layout_1.gif" alt="" title="" width="498" height="97" />
</div>