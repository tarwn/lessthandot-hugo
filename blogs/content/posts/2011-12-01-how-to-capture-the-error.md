---
title: How to capture the error output from a stored procedure when calling another stored procedure in SQL Server?
author: SQLDenis
type: post
date: 2011-12-01T15:22:00+00:00
excerpt: |
  This is a quick post, this question was asked recently
  
  I have a stored procedure which is being called by some business objects and it's working fine. I want to extend this stored procedure to call a new stored procedure (basically it will insert som&hellip;
url: /index.php/datamgmt/datadesign/how-to-capture-the-error/
views:
  - 7768
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - error
  - how to
  - output parameter
  - sql server 2005
  - sql server 2008
  - tip

---
This is a quick post, this [question][1] was asked recently

> I have a stored procedure which is being called by some business objects and it&#8217;s working fine. I want to extend this stored procedure to call a new stored procedure (basically it will insert some of the passed in data into another table), but this isn&#8217;t working. How can I get the error output back from both stored procedures?

This is pretty simple to do if you capture the output from the first procedure and then you can use an output parameter to pass it back. Here is a very simple version of how it would work

This is our first proc, as you can see I hardcoded the value 1 so that we are sure this is returned from the first proc

<pre>CREATE PROC prtest1
AS
RETURN 1 --@@error
GO</pre>

This is our second proc, as you can see I hardcoded the value 2 so that we are sure this is returned from the first proc. You can also see that I store the return value from the stored procedure prtest1 in the @error and this is an output parameter

<pre>CREATE PROC prtest2 @error INT OUTPUT
AS
EXEC @error = prtest1
RETURN 2 --@@error
GO</pre>

Here is now how you bring both statuses back by using a combination of a return value and an output parameter. this part _EXEC @SecondProc = prtest2_ will get the return value. 

This part _@error = @FirstProc OUTPUT_ will get the value that is returned from the output parameter

<pre>DECLARE @SecondProc INT, @FirstProc INT
EXEC @SecondProc = prtest2 @error = @FirstProc   OUTPUT

SELECT @SecondProc  AS [2nd],@FirstProc AS [1st]</pre>

Output
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

<pre>2nd	1st
2	1	</pre>

I prefer to return errors that I capture from within the proc as output parameters, I will also use the _ERROR_MESSAGE()_ function to return something that makes more sense to the user than a number. Below is an example

<pre>BEGIN TRY
     SELECT 1/0
END TRY

BEGIN CATCH
     SELECT ERROR_MESSAGE(), @@ERROR
END CATCH</pre>

Output
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
Divide BY zero ERROR encountered. 8134

What is better for the end user 8134 or &#8220;Divide BY zero ERROR encountered.&#8221;?

 [1]: http://stackoverflow.com/questions/8171359/capture-the-error-output-from-a-stored-procedure-when-calling-another-stored-pro/8171572#8171572