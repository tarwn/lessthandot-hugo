---
title: Five things wouldn’t miss in SQL Server
author: Ted Krueger (onpnt)
type: post
date: 2010-05-11T16:20:14+00:00
ID: 788
excerpt: 'Everyone took all the good things already so I’m sitting here with the leftovers.  Still a few things I call pet peeves and I wouldn’t miss.  So here is the big five that came to mind...'
url: /index.php/datamgmt/datadesign/sql-meme-tagged-5-to-hang/
views:
  - 5808
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - meme
  - sql server
  - tagged

---
Everyone took all the good things already so I’m here with the leftovers but still things I call the pet peeves and I wouldn’t miss. So here is the big five that came to mind

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/bullet.gif" alt="" title="" width="50" height="42" align="left" />
</div>

### Default Configurations

OK, defaults help everyone. Mostly they help them not actually think about what they are doing. My first call to the drop spree is roughly 90% of all the defaults. Make us think about it! Is making us enter something really that bad of a thing? Maybe it will catch another 1% of the installers if they actually have to think. They may actually say, “Hmm…wonder what that means. I’ll go find out before putting a 1MB in that growth box”. We have the ability to optimize our own default settings so leave it to us. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/bullet.gif" alt="" title="" width="50" height="42" align="left" />
</div>

### MSDTC usage

Can we please come up with something that is actually stable by now??? All I think I have to say about that. Really, all there is to say about it 😉

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/bullet.gif" alt="" title="" width="50" height="42" align="left" />
</div>

### MAX Memory settings

SQL Server is an intelligent beastly database server. I mean, smart! So why does SQL Server let people put 2TB into the max memory settings when they have 4GB of RAM? Drop that! And if I catch any of you leaving 2147483647 as the default, your DBA days are numbered. SQL Server will act as fast as a cookie monster with memory cookies if you let it. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/bullet.gif" alt="" title="" width="50" height="42" align="left" />
</div>

### Everyone will hate me and disagree on this, but can we get rid of db\_datareader and db\_datawriter already?

Security isn’t that complex. Really, it isn’t. They need SELECT on this so you bottle it role and they have it. How many times do you just click db\_datareader when they “just” need read rights? The same thing goes for db\_datawriter. Well, they need write access to the DB so give it to them. Do they need write to ALL of it? No one can give me the excuse that your security would simply be far too complex to setup or you have hundreds of thousands of users. Hey, if you use all the containers and roles in which you setup right, it actually makes it easier to maintain 😉 Really, if a db\_datareader or db\_datawriter is needed, then create it. Don’t leave it as a big red button to be pushed far too many times.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/bullet.gif" alt="" title="" width="50" height="42" align="left" />
</div>

### Last but nowhere near least; xp_cmdshell.

We have far too many good tools like CLR now to need that bugger. Let’s start to use them and just take this thing out for good. Bring back the blank SA passwords if you leave this thing open to people executing it. 

I&#8217;m tagging Wendy Pastrick ([Twitter][1] | [Blog][2]), Aaron Lowe ([Twitter][3] | [Blog][4]) and Jes Borland ([Twitter][5] | [Blog][6])

 [1]: http://twitter.com/wendy_dance
 [2]: http://wendyverse.blogspot.com/
 [3]: http://twitter.com/Vendoran
 [4]: http://www.aaronlowe.net/
 [5]: http://twitter.com/grrl_geek
 [6]: http://jesborland.wordpress.com/