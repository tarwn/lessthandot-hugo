---
title: CentOS, Postfix, MySQL and a Typo
author: damber
type: post
date: 2008-09-05T20:06:52+00:00
url: /index.php/sysadmins/os/linux/centos-postfix-mysql-and-a-typo/
views:
  - 17365
rp4wp_auto_linked:
  - 1
categories:
  - Linux
  - RHEL
tags:
  - centos
  - linux
  - mail server
  - mysql
  - postfix
  - typo

---
# A little lesson in typo-trauma

Today I finally got round to doing some admin on my primary home server, which (amongst many things) is my main mail server. After dusting a few cobwebs away, checking nobody had broken any of the furniture and such, I decided to run yum to get the latest updates (it had been almost a month since the last time I checked 88| but still, at least now it was about to be updated to all the shiny sparkly new things that CentOS had released for me&#8230;

And it did, it came up with about 5 or 6 updates, including an update to postfix. Great. So, it finished in about a minute or two and I went on about my evening. All was happy in the land of the ignorant.

Ignorant, that is, until I decided to send an email to my dear old mum to give her an update on the list of laptops I was recommending for her (I know, I&#8217;m so thoughtful :D). At which point my squirrelmail (webmail) froze.. you see, I&#8217;m currently re-decorating my study, so have moved my main workstation into another room, and as such it&#8217;s not in use.. and my other non-work laptop is, er, recovering from, er, how do you say it? A little &#8216;rinse&#8217; in the bath&#8230; so, work laptop it is, and webmail access only&#8230; 

I thought maybe it was my damn windows wifi connection at first because it has been such a problem the last few days.. (I can&#8217;t wait to get back to my linux environments, this windows only stuff is giving me a serious heart condition.) Anyway, after a prod and a poke it seemed that it might be the mail server&#8230;

## Determining the problem

So, I checked the mail server logs and discovered&#8230;

<pre>[root@phantom ~]# tail /var/log/maillog
...
...
phantom postfix/smtpd[8736]: fatal: unsupported dictionary type: mysql
...</pre>

mmmm&#8230; now, that looks familiar.. wasn&#8217;t that a problem I faced when setting up this mail server? The problem that centos repositories only provide the standard postfix rpm which doesn&#8217;t have mysql support compiled in? But, it has been working for ages.. why is it failing now ? Hold on.. let me check what exactly was updated again recently&#8230;

<pre>[root@phantom ~]# tail /var/log/yum.log
...
...
Updated: postfix - 2:2.3.3-2.1.el5_2.i386
...</pre>

Ahhhh&#8230; bugger.:'(

## Path of enlightenment

OK&#8230; now I&#8217;m thinking that some stupid numpty has released the wrong rpm in the wrong repository, because I know that I set things up originally to exclude the main repositories for postfix updates and only get it from the centosplus repository. Of course I did. I&#8217;m sure. I think. mmmmm&#8230; ok, let me check&#8230;

<pre>[root@phantom ~]# vi /etc/yum.repos.d/CentOS-Base.repo
...
...
#released updates
[updates]
name=CentOS-$releasever - Updates
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&amp;arch=$basearch&amp;repo=updates
#baseurl=http://mirror.centos.org/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-5
priority=1
exclude=kernel* posfix*   &lt;&lt;&lt;&lt;&lt;&lt;------looky at what we have here..
...
...
[centosplus]
name=CentOS-$releasever - Plus
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&amp;arch=$basearch&amp;repo=centosplus
#baseurl=http://mirror.centos.org/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=1
gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-5
priority=2
includepkgs=kernel* postfix*</pre>

Ooops. Can you spot it? :yes: Yep, exactly &#8211; every entry apart from the updates was excluding &#8220;postfix&#8221;, whilst the updates were excluding &#8220;posfix&#8221;.. :no: 

## Fixing the shame..

That, my friend, is the scourge of bugs everywhere.. the humble typo. ah well. Lesson learnt.. now just to download the previous centosplus package for postfix with mysql built in and re-install it (and fix that damn typo):

<pre>[root@phantom ~]# wget http://www.mirrorservice.org/sites/mirror.centos.org/5.2/centosplus/i386/RPMS/postfix-2.3.3-2.el5.centos.mysql_pgsql.i386.rpm
[root@phantom ~]# rpm -Uvh ----replacepkgs ----force postfix-2.3.3-2.el5.centos.mysql_pgsql.i386.rpm
[root@phantom ~]# sed -i 's/posfix/postfix/g' /etc/yum.repos.d/CentOS-Base.repo</pre>

phew&#8230; mail server back up and working again. I thought for a brief second that it was going to be a major problem, but, thankfully, a download + install to rollback and a correction of a typo later.. and I&#8217;m back up and running 🙂 A lesson in scrutiny of important configuration data has been learnt..:lalala: until the next time.. ;D