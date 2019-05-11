---
title: Another reason to show off your Tech – Happy Pi()
author: Ted Krueger (onpnt)
type: post
date: 2012-03-14T11:38:00+00:00
ID: 1563
excerpt: 'As most of the technology world knows, today is 3.14.2012.  Or, as most of us would see it, Pi. This morning it so happens, I ran across some nasty hard-coded values in some code.  This made me think....hmmm...I bet you could use MONTH() and DAY() in yo&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/another-reason-to-show-off/
views:
  - 3193
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
<span style="font-family: verdana,geneva;">As most of the technology world knows, today is 3.14.2012.  Or, as most of us would see it, <a href="http://en.wikipedia.org/wiki/Pi">Pi</a>.</span> <span style="font-family: verdana,geneva;">This morning it so happens, I ran across some nasty hard-coded values in some code.  This made me think....hmmm...I bet you could use MONTH() and DAY() in your code and it would be horrid but completely valid today!  Then, like me, today the sifting of where those hard-coded values are would begin.</span>

<span style="font-family: verdana,geneva;">As a "techy" and knowing LessThanDot has many vistiors and members that get a kick out of these completely insane thoughts, I thought it would be fun to get some comments on how you would use today and the significance of the Pi value being todays date.  If you are up to it, leave a comment and have fun with it.  It's PI Day!</span>

<span style="font-family: verdana,geneva;"><br /></span>

```sql
SELECT 'Happy ' + CAST(CONVERT(DECIMAL(5,2),ROUND(PI(),2,1)) AS VARCHAR(4)) + ' Day!'
SELECT 'Happy ' + CAST(MONTH(GETDATE()) AS VARCHAR(2)) + '.' + CAST(DAY(GETDATE()) AS VARCHAR(2)) + ' Day!'
```