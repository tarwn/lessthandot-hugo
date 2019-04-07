---
title: Stripping rows out of file imports
author: Ted Krueger (onpnt)
type: post
date: 2010-04-22T10:31:20+00:00
url: /index.php/datamgmt/datadesign/stripping-rows-out-of-file-imports/
views:
  - 10163
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Scott Stauffer ([blog][1] | [twitter][2]) asked a question on #sqlhelp a few days ago and thought I’d go into actually doing an example on the task he was trying to accomplish. 

Below is a snapshot of the tweet.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/buffer_1.gif" alt="" title="" width="313" height="99" />
</div></p> 

My first thought was to throw in a script component to transformation. With each row input into the buffer the row could be parsed to validate the header. If the unique pattern in the string was found, then lock a variable and assign the value to it



## Let’s get some things out of the way



The script component will read every row. That in itself will run slower than other methods such as directly importing without a transformation. This is the beauty of SSIS though and the 6 ways to do anything. In our situation we have, a script task in front of the data flow task could be utilized to grab the header and assign it to the variable while deleting it. This would be much more efficient. 

Special circumstances do call for methods that may perform slower at times to provide stability and validation processing. The reasoning for showing this method is to provide the validation on the rows from regular expressions and pattern matches. This will provide a higher amount of freedom on resources as well by reading the rows into the buffer and out. When methods such as the script task are used to modify flat files prior to imports, the entire file is pulled into memory and given some large imports, this can be a hardship to manage on mid-size servers. 

Let’s look at the two methods that are mentioned above. The first will be the script component to match the string value we need.
  

  
Create a package named header_Split and the following supporting tables in your lab database
  


<pre>CREATE TABLE [dbo].[header](
	[Column 0] [varchar](50) NULL
) ON [PRIMARY]
GO
CREATE TABLE [dbo].[header_split](
	[Column 0] [varchar](50) NULL
) ON [PRIMARY]
GO</pre>

Create a variable at the package scope level named, global.
  

  
Our global connections for the package will be one flat file connection named, “importer” and an OLEDB connection named as the instance. Our flat file will contain the following text
  


<pre>header <grab me> string
test
test
test
test</pre>

Bring over a data flow task and let’s name it, “DF Flat File Pump”

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/buffer_2.gif" alt="" title="" width="394" height="124" />
</div>

In the data flow task, bring over a flat file source and configure it to use our importer connection.
  

  
Bring over a script component now and when the “Select Script Component Type” appears, select the “Transformation” option.
  

  
In the Script Transformation Editor, select our one column (default: Column 0)

Go into the, “Input and Outputs” window and select the output. 

If the names are all left as the defaults, the output will be titled, Output 0.

Change the ExclusionGroup to 1. 

Add a new Output next, Output 1. 

In the new output change the ExclusionGroup group to 1 as well and set the SynchronousInputID to the Input 0 ID. This will be listed in the dropdown.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/buffer_3.gif" alt="" title="" width="543" height="287" />
</div>

Select the Script window and add into the ReadWriteVariables the variable, “global”
  

  
Go into the studio by clicking the, “design Script” next.
  

  
The code to perform the action is below. It will utilize Regular Expressions to first find the match to the unique value we need and then assign it to the variable global. In order to assign a variable in the script component without throwing Post Execute errors, we must lock the variable by using the VariableDispenser.LockOnForWrite function.
  


<pre>Imports System
Imports System.Data
Imports System.Math
Imports Microsoft.SqlServer.Dts.Runtime
Imports Microsoft.SqlServer.Dts.Pipeline.Wrapper
Imports Microsoft.SqlServer.Dts.Runtime.Wrapper
Imports System.Text.RegularExpressions

Public Class ScriptMain
    Inherits UserComponent

    Public Overrides Sub Input0_ProcessInputRow(ByVal Row As Input0Buffer)
        If Regex.IsMatch(Row.Column0, "<[w]{4}s[w]{2}>") Then
            Dim mismatch As Match
            Dim pattern As New Regex("<[w]{4}s[w]{2}>")
            mismatch = pattern.Match(Row.Column0)

            If mismatch.Length > 0 Then
                Dim var As IDTSVariables90
                Me.VariableDispenser.LockOneForWrite("global", var)
                var("global").Value = mismatch.Value
                var.Unlock()
            End If
            Row.DirectRowToOutput0()
        Else
            Row.DirectRowToOutput1()
        End If
    End Sub

End Class</pre>

Drag and drop two OLEDB Destination’s into the data flow and rename them “data pump” and “headers”. We are only creating the headers destination for our testing purposes to validate the headers coming out and for later utilization if needed.
  

  
Connect the Output 1 to the data pump destination and map the column to the column in the header_split table. Connect the Output 0 to the headers destination and map the output column to the one column in the header table then.
  

  
Execute the package.
  


<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/buffer_4.gif" alt="" title="" width="458" height="403" />
</div>

The results are showing the output that we found as a header based on our regular expression pattern match flows to the headers destination. At the same time, the data we need flows to the data pump destination.
  

  
Querying this in SSMS shows 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/buffer_5.gif" alt="" title="" width="335" height="281" />
</div></p> 

The major problem with this process is the fact we are processing row by row while putting the row under a microscope. Imagine this in a 300M row import. It wouldn’t go very well but would go if needed.

The next solution that will be much easier to implement, maintain and gain performance will follow in a second part to this post. We will utilize a script task to manipulate the file before hand, assign the variable, delete the header and import the data as needed.

 [1]: http://www.sqlservertoolbox.com/
 [2]: http://twitter.com/SQLSocialite