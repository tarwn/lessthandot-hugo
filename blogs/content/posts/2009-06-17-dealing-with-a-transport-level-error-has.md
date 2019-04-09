---
title: Dealing with A transport-level error has occurred when sending the request to the server errors
author: SQLDenis
type: post
date: 2009-06-17T10:52:41+00:00
ID: 474
url: /index.php/datamgmt/datadesign/dealing-with-a-transport-level-error-has/
views:
  - 10670
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - connection
  - how to
  - sql server 2005
  - sql server 2008

---
This question was asked yesterday in our forums
  
[A transport-level error has occurred when sending the][1]….. by Impal3r

> When i have a query window open In SSMS and I step away for a while and then I run the query I get the following error
> 
> A transport-level error has occurred when sending the request to the server
> 
> what I do then is disconnect from the server, connect again and then I can open anew query window to run the query again. This seems tedious and not really productive. Is there a better way to get around this?

I actually discovered quite by accident a while back how to resolve this. If you have SSMS open with a query in your query window, then you go away for two hours and when you come back you hit F5. What happens then is that you will see the following message
  

  
_**Msg 10054, Level 20, State 0, Line 0
  
A transport-level error has occurred when sending the request to the server.
  
(provider: TCP Provider, error: 0 &#8211; An existing connection was forcibly closed by the remote host.)**_
  

  
What you can do is connect to the server again and try running your query and that will solve it. There is a faster/easier way…..just hit F5 or the execute icon again and the query will also run. That is much easier isn't it?



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewtopic.php?f=17&t=6296
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22