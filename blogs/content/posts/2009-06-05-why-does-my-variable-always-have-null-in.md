---
title: Why does my variable always have NULL in it?
author: Ted Krueger (onpnt)
type: post
date: 2009-06-05T18:22:54+00:00
ID: 458
url: /index.php/datamgmt/datadesign/why-does-my-variable-always-have-null-in/
views:
  - 5073
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
I'm sure one of us already Wiki'd this or put it in a blog but since I just had to weed through some code to figure out why something wasn't working I figured I'd say it again.

PLEASE and I stress, PLEASE, make sure you initialize your variable in TSQL or at the least, use IsNull or coalesce to prevent this famous and all annoying issue from killing your hard work

sql
Declare @AllImportantVarToRunThisScript varchar(30)
Declare @MyVarThatIKnowHasValue varchar(30)

Set @AllImportantVarToRunThisScript = (Select 'MyPretendCharColumn') + @MyVarThatIKnowHasValue

Select @AllImportantVarToRunThisScript
```
Oops! You just lost the game and crashed and burned. Take care of your NULL's!!!!

sql
Declare @AllImportantVarToRunThisScript varchar(30)
Declare @MyVarThatIKnowHasValue varchar(30)

Set @AllImportantVarToRunThisScript = (Select 'MyPretendCharColumn') + IsNull(@MyVarThatIKnowHasValue,'')
--Or
Set @AllImportantVarToRunThisScript = (Select 'MyPretendCharColumn') + coalesce(@MyVarThatIKnowHasValue,'')
--Or really hectic
Set @AllImportantVarToRunThisScript = (Select 'MyPretendCharColumn') 
				+ (Select Case When @MyVarThatIKnowHasValue Is Null Then '' End)

Select @AllImportantVarToRunThisScript
```
Ok, the last one is just silly and it can get even worse but it's better than getting NULL 😉