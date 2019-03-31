---
title: Why does my variable always have NULL in it?
author: Ted Krueger (onpnt)
type: post
date: 2009-06-05T18:22:54+00:00
url: /index.php/datamgmt/datadesign/why-does-my-variable-always-have-null-in/
views:
  - 5073
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
I&#8217;m sure one of us already Wiki&#8217;d this or put it in a blog but since I just had to weed through some code to figure out why something wasn&#8217;t working I figured I&#8217;d say it again.

PLEASE and I stress, PLEASE, make sure you initialize your variable in TSQL or at the least, use IsNull or coalesce to prevent this famous and all annoying issue from killing your hard work

<pre>Declare @AllImportantVarToRunThisScript varchar(30)
Declare @MyVarThatIKnowHasValue varchar(30)

Set @AllImportantVarToRunThisScript = (Select 'MyPretendCharColumn') + @MyVarThatIKnowHasValue

Select @AllImportantVarToRunThisScript</pre>

Oops! You just lost the game and crashed and burned. Take care of your NULL&#8217;s!!!!

<pre>Declare @AllImportantVarToRunThisScript varchar(30)
Declare @MyVarThatIKnowHasValue varchar(30)

Set @AllImportantVarToRunThisScript = (Select 'MyPretendCharColumn') + IsNull(@MyVarThatIKnowHasValue,'')
--Or
Set @AllImportantVarToRunThisScript = (Select 'MyPretendCharColumn') + coalesce(@MyVarThatIKnowHasValue,'')
--Or really hectic
Set @AllImportantVarToRunThisScript = (Select 'MyPretendCharColumn') 
				+ (Select Case When @MyVarThatIKnowHasValue Is Null Then '' End)

Select @AllImportantVarToRunThisScript</pre>

Ok, the last one is just silly and it can get even worse but it&#8217;s better than getting NULL 😉