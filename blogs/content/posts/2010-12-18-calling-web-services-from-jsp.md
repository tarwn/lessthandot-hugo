---
title: Calling Web Services from JSP
author: Rob Earl
type: post
date: 2010-12-18T14:40:51+00:00
ID: 979
excerpt: |
  This blog will demonstrate the steps to generate a Web Service Client and test JSP files in Eclipse. Finally, it will show a basic JSP example which uses the generated client to call a remote Web Service.
  
  Setup Tomcat
  
  Download and extract Tomcat 6&hellip;
url: /index.php/webdev/serverprogramming/jsp/calling-web-services-from-jsp/
views:
  - 17142
rp4wp_auto_linked:
  - 1
categories:
  - JSP / Servlets
tags:
  - eclipse
  - java
  - jsp
  - web services

---
This blog will demonstrate the steps to generate a Web Service Client and test JSP files in Eclipse. Finally, it will show a basic JSP example which uses the generated client to call a remote Web Service.

## Setup Tomcat

Download and extract [Tomcat 6.0][1]. If you're using Ubuntu and want to use the system installed tomcat, follow [these instructions][2].

## Create an Eclipse Project

Open Eclipse and create a new Dynamic Web Project:

![][3]

Give your project a name (JSPWSClient will do) and click "New" to add a New Server Runtime Environment. Select Apache Tomcat v6.0 and locate the directory you extracted to earlier. 

![][4]

## Create Web Service Client

The client will use the Local Time Web Service from http://ripedevelopment.com/testService.aspx. Set the Service definition for the client to http://www.ripedevelopment.com/webservices/LocalTime.asmx?WSDL and drag the slider on the left to the top (Test Client). 

![][5]

If the server isn't already running the wizard will prompt you to "Start server", do this and, when prompted to generate a Web Service Client Test, accept the defaults to use Web Service Sample JSPs.

Eclipse will then generate a Web Service client and sample JSP files to test it. Once they are generated Eclipse will open the sample JSP client. Using this page the service methods can be tested to ensure connectivity with the service. Examining the source of these files in Project->WebContent->sampleLocalTimeSoapProxy can show us the methods we need to use. 

Below is an example JSP file which will call the Web Service Client and lookup the current time in New York.

```java
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>The Time in New York!</title>
</head>
<body>
<jsp:useBean id="localTimeSoapProxy" scope="session" class="com.ripedev.www.LocalTimeSoapProxy" />
<%
try
{
	String newYorkTime = localTimeSoapProxy.localTimeByZipCode("10001"); // Pass a New York zipcode.
	%>The Time in New York is: <%=newYorkTime %>
	
<%
}
catch(Exception e)
{
%> 
	Exception: <%=e.getMessage() %>
<%	
}
%>
</body>
</html>
```
Save this file to WebContent/NewYorkTime.jsp then navigate to http://localhost:8080/NewYorkTime.jsp and you should see something like:

The Time in New York is: 12/18/2010 11:26:22 AM

 [1]: http://tomcat.apache.org/download-60.cgi
 [2]: http://ubuntuforums.org/showthread.php?p=8541057
 [3]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/robearl/DynamicWebProject.png ""
 [4]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/robearl/Tomcat.png ""
 [5]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/robearl/WebServiceClient.png ""