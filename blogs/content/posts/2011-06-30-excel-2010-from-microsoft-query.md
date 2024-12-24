---
title: Excel 2010 Data Source "From Microsoft Query" Missing DSNs
author: Jes Borland
type: post
date: 2011-06-30T14:42:00+00:00
ID: 1236
excerpt: The case of the missing System DSN, and how I cracked it.
url: /index.php/datamgmt/dbprogramming/excel-2010-from-microsoft-query/
views:
  - 12518
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
This is a problem I ran into recently. I have a 64-bit operating system. 

![][1]

I'm running Excel 2010, which is 32-bit. There is also a 64-bit version. (You can get this information by going to File > Help.)

![][2]

I went to Programs > Administrative Tools > Data Sources (ODBC). I set up a System DSN. I tested it and it works fine. Hurray! 

![][3]

Then I opened Excel 2010 and went to Data > From Other Sources > From Microsoft Query. The DSN isn't there. What happened? 

![][4]

I scratched my head over this for a while, and then emailed my team. One of my senior DBAs had run into this before and pointed me to this Microsoft KB article: <http://support.microsoft.com/kb/942976>. On a 64-bit computer, the DSN will be set up as 64-bit. However, a 32-bit application can't see a 64-bit DSN. Excel 2010 is a 32-bit application.
  
To solve this problem, I needed to set up a 32-bit DSN. I did that by going running the 32-bit ODBC Data Source Administrator tool from C:WindowsSysWOW64odbcad32.exe. I created the same DSN, with _32Bit in the name. 

 ![][5]

I went back to Excel > Data > From Other Sources > From Microsoft Query. My 32-bit DSN is in the list. 

 ![][6]

What I learned is that 64-bit operating systems contain both 64-bit and 32-bit ODBC administration tools. You must use the appropriate tool for the application you are working with.

 [1]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/DSN6.JPG?mtime=1309464482
 [2]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/DSN5.JPG?mtime=1309464482
 [3]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/DSN1.JPG?mtime=1309446389 "null"
 [4]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/DSN2.JPG?mtime=1309446389 "null"
 [5]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/DSN3.JPG?mtime=130944638 "null"
 [6]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/DSN4.JPG?mtime=1309446389 "null"