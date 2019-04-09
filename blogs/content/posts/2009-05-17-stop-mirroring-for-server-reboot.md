---
title: Stop mirroring for server reboot
author: Ted Krueger (onpnt)
type: post
date: 2009-05-17T23:23:49+00:00
ID: 433
url: /index.php/datamgmt/datadesign/stop-mirroring-for-server-reboot/
views:
  - 7916
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Read something just now (I'll leave that link out in respect for others) where a individual asked how they can reboot a server without having to manually fail-back all the mirrored databases once it is back up. The type of mirroring was high availability so the witness would of course fail the principle to the mirror if the decided the server had to be restarted. The answer that seemed to be accepted was to 

sql
ALTER DATABASE <database_name> SET PARTNER OFF;
```
Honestly I was surprised the author of the question didn't come back in a flaming fury once he had to set his mirrors all back up sense that statement removes mirroring. I was curious so I google'd the SET PARTNER OFF and this seems to be a common answer to it. Wow! All I could come up with. Either the DB's are so small it wasn't an issue or the transactions out of sync were so small the mirroring simply started back up after reconfiguring it.

For anyone that finds this and wants to know, you should pause mirroring

sql
ALTER DATABASE <database_name> SET PARTNER SUSPEND
```
Then use the RESUME to start mirroring back up. Really, I almost feel like this is a copy/paste from BOL. It's pretty well documented [SQL Server 2005][1] and for [SQL Server 2008][2]. 

Don't forget logs can be an issue when doing things like this on mirrors. If you are going to be off or failed over for a long period of time with the principle down, it may be a good choice to force a reconfigure. If you're DB's are pushing the 100GB and into the TB range, backup and restore along with the tail of the log can be a longer task that it sounds like. It is a trivial one, but a long and vulnerable one to say the least. 

I still feel sorry for that person that actually ran the PARTNER OFF statement. Goes to show you really should be careful with some of the answers you get off forums these days. I mean if anything, wouldn't common sense say, shut the witness down if you really didn't know how to pause mirroring? Then at least that thing that says go can't say anything 😉

 [1]: http://msdn.microsoft.com/en-us/library/ms190664(SQL.90).aspx
 [2]: http://msdn.microsoft.com/en-us/library/ms190664.aspx