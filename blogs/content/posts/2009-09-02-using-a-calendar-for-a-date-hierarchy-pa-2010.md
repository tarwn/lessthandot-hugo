---
title: Using a Calendar for a Date Hierarchy Parameter with SSAS 2008 and SSRS 2008
author: riverguy
type: post
date: 2009-09-02T18:10:52+00:00
url: /index.php/datamgmt/dbprogramming/mssqlserver/using-a-calendar-for-a-date-hierarchy-pa-2010/
views:
  - 14588
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
When designing a Reporting Services report against a relational database, I would argue that developers most often prefer to utilize a Date/Time parameter type and calendar control for date parameter input. This makes it easy for the user to choose a date with the Reporting Services calendar control instead of requiring the user to type a well-formatted date value or providing the user with a long list of dates in a drop-down list.

However, when it comes to SSAS, if a developer chooses to add a parameter against a date hierarchy, the end-user is presented with a drop-down list to scroll through and choose dates. This is with good reason. Many times, the user will have the option to choose from different levels of date aggregation. Choosing an entire year or just one single date could be a valid option within the same report. This scenario would be confusing if not difficult to represent in a calendar control.

That being said, many times reports are more narrowly focused and constrained to a date granularity. In this case, it may be more desirable to allow the user to pick their date with the calendar control. Again, the default behavior when designing the MDX query will present the user with a drop-down containing the members of the date hierarchy. We can change this behavior to present the user with a calendar control instead of a drop-down list.

I&#8217;m working with the Adventure Works 2008 SSAS sample project. In a new SSRS 2008 project, I have added a report to display the Internet Sales Amount by Country, with Date.Date as a parameter. By default, the user interface will look like the image below.

![Image1][1]

Obviously, this is not a very easy user interface to work with. If we right-click on our data source in the Report Data tab, we can see we have an option to &#8220;Show Hidden DataSets.&#8221; This will give us the definition for the hidden dataset which populates our Date.Date parameter. If we copy the query text and paste it into SQL Server Management Studio, we can see that in this case, our parameter&#8217;s value takes on the format of [Date].[Date].&[yyyyMMdd]. The format may be different depending on the individual Date hierarchy setup. So, what we need to do is use a calendar control yet still supply a proper string to our main dataset as a parameter value. We can accomplish this with a VB function within the report.

Add a new parameter to the report named Date. This parameter should have a Data type of Date/Time to ensure that it is rendered to the user as a calendar control. In the properties pane of our Report, we can add the following VB function in the Code window:

_Function GetDateMemberString(DateValue As DateTime) As String
       
Dim RetVal As String = &#8220;&#8221;
       
RetVal = &#8220;[Date].[Calendar].[Date].&[&#8221;
       
RetVal &= Format(DateValue, &#8220;yyyyMMdd&#8221;).ToString & &#8220;]&#8221;
       
Return RetVal
  
End Function_

This code will accept a DateTime value as input, and return a string as output. The output value will have a format which matches our Date hierarchy format.

Next, if we right-click on our main DataSet and view the Parameters tab, we will see that our date parameter is mapped to our original, wizard-generated @DateDate MDX parameter. We can change the Parameter Value expression to the following: **=Code.GetDateMemberString(Parameters!Date.Value)**. 

We can now hide or delete our original, wizard-generated date parameter and re-rerun the report. We should now end up with a user interface as shown below

![Image2][2]

This is definitely much easier to work with than the original version. One other thing to note is that depending on the scope of data in the cube, the query for our main DataSet may need to be altered to remove the CONSTRAINED argument from the StrToSet functions within the query. If a user passes in a date which does not exist in the Date dimension, then SSRS will return an error if we call the StrToSet function with the CONSTRAINED argument.

Please also see related article on this topic [Building OLAP Date Dimensions][3]

 [1]: http://img215.imageshack.us/img215/7002/image1z.jpg "Image1"
 [2]: http://img30.imageshack.us/img30/3941/image2qnd.jpg "Image2"
 [3]: http://www.setfocus.com/TechnicalArticles/Articles/BuildingOLAPDateDimensions.aspx