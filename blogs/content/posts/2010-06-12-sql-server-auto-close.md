---
title: SQL Server and the Auto Close Setting
author: Ted Krueger (onpnt)
type: post
date: 2010-06-12T10:26:29+00:00
ID: 819
excerpt: 'Today I woke up to a little over a hundred emails from one of my database servers letting me know that my resources were jumping around like a kangaroo.  In the mix of those emails I also had alerts thrown stating, [database_name_withheld] has a status of Suspect, Cleanly Shutdown.  I actively monitor the state of the database being open or closed (which also shows status of suspect, recovering etc...)   I recommend the same so you catch these situations.  When I read the emails, I knew exactly what the setting was that had been set to true.  "Auto Close"'
url: /index.php/datamgmt/dbprogramming/sql-server-auto-close/
views:
  - 26292
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - auto close
  - deprecated list
  - sql community
  - sql server

---
Today I woke up to a little over a hundred emails from one of my database servers letting me know that my resources were jumping around like a kangaroo. Actually, more like a boxing match with one…

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/autoclose_2.gif" alt="" title="" width="380" height="263" />
</div>

In the mix of those emails I also had alerts thrown stating, 

> _[database\_name\_withheld] has a status of Suspect, Cleanly Shutdown_

I actively monitor the state of the database being open or closed (which also shows status of suspect, recovering etc…) I recommend the same so you catch these situations. When I read the emails, I knew exactly what the setting was that had been set to true. “Auto Close”

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/autclose.gif" alt="" title="" width="494" height="184" />
</div>

Why is this bad? Buck Woody ([Blog][1] | [Twitter][2]) tells us in his blog, “_[SQL Server Best Practices: AutoClose Should be Off][3]_“

Basically, in the case of these alerts from my database server, what Buck explained is exactly what happened. This database that was created by some vendor software had the setting of Auto Close on. The software has a service running in the background and would poll for some data every five minutes or so and then close the connection it opened. Yes, that is a good thing that it closes the connections. But, what was happening was the resources on the server were going crazy due to consumption and releasing them every five minutes. Literally over and over again it would do this. This actually caused problems with the other databases and did exactly the opposite as you would think releasing resources would do.

Luckily Auto Close is on the, “[Kill][4]” list and will be gone soon (but not soon enough). In my experience I haven’t found a use for it unless a database is opened once a month. Personally, I don’t have any databases like that but maybe they are really out there.

Final thought – If you have a database that was recently created and you find Auto Close set to True, change it to False. It is a best practice, won’t hurt anything and will prevent things like this situation from coming up. Now lets see if I can restart this Saturday morning.

 [1]: http://blogs.msdn.com/b/buckwoody/
 [2]: http://twitter.com/buckwoody
 [3]: http://blogs.msdn.com/b/buckwoody/archive/2009/06/24/sql-server-best-practices-autoclose-should-be-off.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms135094(SQL.90).aspx