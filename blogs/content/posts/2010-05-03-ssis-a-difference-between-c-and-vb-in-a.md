---
title: 'SSIS A difference between C# and VB in a script task'
author: SQLDenis
type: post
date: 2010-05-03T23:43:05+00:00
ID: 779
url: /index.php/datamgmt/dbprogramming/ssis-a-difference-between-c-and-vb-in-a/
views:
  - 19080
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - IBM DB2
  - Microsoft SQL Server Admin
tags:
  - 'c#'
  - ssis
  - vb

---
I ran into this myself a couple of months ago and decided to write a short blog post about it in case you happen to run into this. SQL Server 2008 lets you use Visual Basic or C# as the scripting language in a Script Task. If you decided to translate some of your older packages from VB to C# be aware that there are some subtle differences and things don't translate one to one.

If this is your code in Visual Basic

```vb
Dim FileName As String
 FileName = (Dts.Variables("varFileName").Value).ToString()

 MsgBox(FileName)
```

And if you change it to this in C#

```csharp
string FileName;
FileName = (Dts.Variables("varFileName").Value).ToString();

MessageBox.Show(FileName);
```

You will get the following friendly error.
  
**Non-invocable member &#8216;Microsoft.SqlServer.Dts.Tasks.ScriptTask.ScriptObjectModel.Variables' cannot be used like a method.**

Do you see what the error is in the code? 

In C# you need to use brackets, not parentheses so instead of the top line of code, you need to use the bottom one

Dts.Variables<span class="MT_red">(</span>“varFileName”<span class="MT_red">)</span>.Value
  
Dts.Variables<span class="MT_green">[</span>“varFileName”<span class="MT_green">]</span>.Value

Here is the code block in C#

```csharp
string FileName;
FileName = (Dts.Variables["varFileName"].Value).ToString();

MessageBox.Show(FileName);
```

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22