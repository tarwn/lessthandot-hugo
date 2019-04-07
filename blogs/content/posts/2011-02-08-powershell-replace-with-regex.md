---
title: 'T-SQL Tuesday#15 – Automation with PowerShell (Replace with RegEx)'
author: Ted Krueger (onpnt)
type: post
date: 2011-02-08T11:46:00+00:00
ID: 1033
excerpt: 'The big man with the camera, Pat Wright (Blog | Twitter) is holding T-SQL Tuesday this month.  The topic this month is Automation with T-SQL or with PowerShell (or a mix of both).  Most know that when I say automation, SSIS is the first thing to come to mind.  I’ve taken SSIS and abused and tuned it to automate just about every DBA task that is known to SQL Server.  I’ve started to take my own advice over the last year though.  I’ve taken PowerShell to heart on how rapidly you can write, execute and automate scripts to handle some of my SSIS packages.  The ultimate goal is always to spend less time writing these automation initiatives and getting them in place so they are working for us.'
url: /index.php/datamgmt/dbprogramming/powershell-replace-with-regex/
views:
  - 16911
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-19.png?mtime=1297172329"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-19.png?mtime=1297172329" width="154" height="154" align="left" /></a>
</div>

The big man with the camera, Pat Wright ([Blog][1] | [Twitter][2]) is holding [T-SQL Tuesday this month][1].  The topic this month is Automation with T-SQL or with PowerShell (or a mix of both).  Most know that when I say automation, SSIS is the first thing to come to mind.  I’ve taken SSIS and abused and tuned it to automate just about every DBA task that is known to SQL Server.  I’ve started to take my own advice over the last year though.  I’ve taken PowerShell to heart on how rapidly you can write, execute and automate scripts to handle some of my SSIS packages.  The ultimate goal is always to spend less time writing these automation initiatives and getting them in place so they are working for us.

**Stripping keywords from T-SQL**

As a DBA, you will find yourself reviewing and falling into the task of modifying long (very long) T-SQL scripts at times.  In some cases these scripts only require you to modify a small thing to meet best practices.  One may be the removal of a hint, table name or procedure name.  Now, T-SQL scripts that go over the hundred line mark are typically not the greatest situation.  To reach a thousand lines is by far a bad situation.  But it happens, and sometimes you have to go line-by-line and replace a keyword.

I was talking to Aaron Nelson ([Blog][3] | [Twitter][4]) the other night about Power Shell and using it with Regular Expressions (RegEx) to manipulate a files contents.  Aaron gave me the idea of using Posh to replace keywords in the above scenario such as NOLOCK.  This fell into the topic of this month’s T-SQL Tuesday topic of automation. 

At first thought, the task seemed simple.  Just replace all NOLOCK matches, right?  Problem started to come in when thinking about the many ways you can add in the NOLOCK hint.

With AdventureWorks, you could do anyone of the following

sql
SELECT * FROM HumanResources.Department WITH (NOLOCK,INDEX(AK_Department_Name))
SELECT * FROM HumanResources.Department WITH (INDEX(AK_Department_Name),NOLOCK)
SELECT * FROM HumanResources.Department NOLOCK
SELECT * FROM HumanResources.Department (NOLOCK)
SELECT * FROM HumanResources.Department WITH (NOLOCK)
```

The RegEx pattern started to get a bit messy to say the least.  When something starts to get overcomplicated and working against me, I like to step back and review the task.  When doing this the simplicity of what needs to happen comes out.  Reviewing the cases that could come up, we see that there are two primary conditions.  The condition in which NOLOCK is in the middle of other hints and the comma is in the pattern.  The other is when NOLOCK is used on its own. 

So the patterns I came up with to match these two conditions are:

Match NOLOCK in an option list with others = ((({0,1}NOLOCK,)|(,NOLOCK({0,1}))

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-21.png?mtime=1297172329"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-21.png?mtime=1297172329" width="440" height="198" /></a>
</div>

Match NOLOCK used as the only option = (WITHs(({0,1}NOLOCK){0,1})|({0,1}NOLOCK){0,1})

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-20.png?mtime=1297172329"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-20.png?mtime=1297172329" width="432" height="193" /></a>
</div>

The view above is from one of my favorite RegEx pattern testing tools over on <http://gskinner.com/RegExr/>

So this covers the conditions identified so far (until we find more as the case with pattern matching).

To use Posh now to strip these values out, we can do the following

```csharp
foreach ($str in Get-Content "C:posh.sql")
{
if([regex]::IsMatch($str, "((({0,1}NOLOCK,)|(,NOLOCK({0,1}))","IgnoreCase")) 
        {$strReplace = [regex]::replace($str, "((NOLOCK,)|(,NOLOCK({0,1}))" , "")
        write $strReplace}
elseif([regex]::IsMatch($str, "(WITHs(({0,1}NOLOCK){0,1})|({0,1}NOLOCK){0,1})","IgnoreCase")) 
        {$strReplace = [regex]::replace($str, "(WITHs(({0,1}NOLOCK){0,1})|({0,1}NOLOCK){0,1})" , "")
        write $strReplace}
}

```
The file posh.sql contains the T-SQL statements from earlier and outputs the statements as valid SELECT statements shown below.

sql
SELECT * FROM HumanResources.Department WITH (INDEX(AK_Department_Name))
SELECT * FROM HumanResources.Department WITH (INDEX(AK_Department_Name))
SELECT * FROM HumanResources.Department
SELECT * FROM HumanResources.Department
SELECT * FROM HumanResources.Department
```
**Conclusion**

Power Shell has added a lot of rapid administration and development power to how you can automate otherwise lengthy tasks.  It took awhile for me to adopt it but once I did, I haven’t looked back in utilizing it fully.  That has made normal tasks that were once thought to be lengthy, more efficient and my Lazy DBA spider senses can be tuned to more important tasks.

With regular Expressions, patterns can always be improved on and bugs uncovered for what they may cover or may not. I would very much like to see better patterns to the above. I attempted to perform the match and replace in one pattern but could not get it to function correctly. So here is your challenge to keep you busy today. I&#8217;m fully confident my developer counterparts will show me a much better pattern.

 [1]: http://sqlasylum.wordpress.com/2011/02/01/invitation-to-t-sql-tuesday-15-automation-in-sql-server/
 [2]: http://twitter.com/sqlasylum
 [3]: http://sqlvariant.com/wordpress/
 [4]: http://twitter.com/sqlvariant