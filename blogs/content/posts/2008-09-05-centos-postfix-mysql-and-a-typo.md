---
title: CentOS, Postfix, MySQL and a Typo
author: damber
type: post
date: 2008-09-05T20:06:52+00:00
ID: 131
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

Today I finally got round to doing some admin on my primary home server, which (amongst many things) is my main mail server. After dusting a few cobwebs away, checking nobody had broken any of the furniture and such, I decided to run yum to get the latest updates (it had been almost a month since the last time I checked 88| but still, at least now it was about to be updated to all the shiny sparkly new things that CentOS had released for me…

And it did, it came up with about 5 or 6 updates, including an update to postfix. Great. So, it finished in about a minute or two and I went on about my evening. All was happy in the land of the ignorant.

Ignorant, that is, until I decided to send an email to my dear old mum to give her an update on the list of laptops I was recommending for her (I know, I'm so thoughtful :D). At which point my squirrelmail (webmail) froze.. you see, I'm currently re-decorating my study, so have moved my main workstation into another room, and as such it's not in use.. and my other non-work laptop is, er, recovering from, er, how do you say it? A little 'rinse' in the bath… so, work laptop it is, and webmail access only… 

I thought maybe it was my damn windows wifi connection at first because it has been such a problem the last few days.. (I can't wait to get back to my linux environments, this windows only stuff is giving me a serious heart condition.) Anyway, after a prod and a poke it seemed that it might be the mail server…

## Determining the problem

So, I checked the mail server logs and discovered…

```text
[root@phantom ~]# tail /var/log/maillog
...
...
phantom postfix/smtpd[8736]: fatal: unsupported dictionary type: mysql
...
```
mmmm… now, that looks familiar.. wasn't that a problem I faced when setting up this mail server? The problem that centos repositories only provide the standard postfix rpm which doesn't have mysql support compiled in? But, it has been working for ages.. why is it failing now ? Hold on.. let me check what exactly was updated again recently…

```text
[root@phantom ~]# tail /var/log/yum.log
...
...
Updated: postfix - 2:2.3.3-2.1.el5_2.i386
...
```
Ahhhh… bugger.:'(

## Path of enlightenment

OK… now I'm thinking that some stupid numpty has released the wrong rpm in the wrong repository, because I know that I set things up originally to exclude the main repositories for postfix updates and only get it from the centosplus repository. Of course I did. I'm sure. I think. mmmmm… ok, let me check…

```text
[root@phantom ~]# vi /etc/yum.repos.d/CentOS-Base.repo
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
exclude=kernel* posfix*   <<<<<<------looky at what we have here..
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
includepkgs=kernel* postfix*
```
Ooops. Can you spot it? :yes: Yep, exactly – every entry apart from the updates was excluding “postfix”, whilst the updates were excluding “posfix”.. :no: 

## Fixing the shame..

That, my friend, is the scourge of bugs everywhere.. the humble typo. ah well. Lesson learnt.. now just to download the previous centosplus package for postfix with mysql built in and re-install it (and fix that damn typo):

```text
[root@phantom ~]# wget http://www.mirrorservice.org/sites/mirror.centos.org/5.2/centosplus/i386/RPMS/postfix-2.3.3-2.el5.centos.mysql_pgsql.i386.rpm
[root@phantom ~]# rpm -Uvh ----replacepkgs ----force postfix-2.3.3-2.el5.centos.mysql_pgsql.i386.rpm
[root@phantom ~]# sed -i 's/posfix/postfix/g' /etc/yum.repos.d/CentOS-Base.repo
```
phew… mail server back up and working again. I thought for a brief second that it was going to be a major problem, but, thankfully, a download + install to rollback and a correction of a typo later.. and I'm back up and running 🙂 A lesson in scrutiny of important configuration data has been learnt..:lalala: until the next time.. ;D