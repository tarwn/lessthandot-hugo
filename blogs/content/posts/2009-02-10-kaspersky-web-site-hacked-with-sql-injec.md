---
title: Kaspersky Web Site Hacked With SQL Injection, How Embarrassing Is This?
author: SQLDenis
type: post
date: 2009-02-10T12:50:57+00:00
url: /index.php/datamgmt/datadesign/kaspersky-web-site-hacked-with-sql-injec/
views:
  - 9533
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - acunetix
  - kaspersky
  - security
  - sql injection

---
We all know that Kaspersky is a security firm and they make a very nice product, you can see a list of their products here: http://www.kaspersky.com/

What I found out today on [twitter][1] is that their site got hacked by a SQL Injection attack. The tool that was used was the [Acunetix Web Security Scanner][2]. Tools are used by admins to protect their sites but the same tools are also used by hackers to attack your site. I have written about another such tool Metasploit before here: [Do you use Metasploit to check if your servers are vulnerable?][3]
  
People should really take advantage of these tools to check their own site before the hackers do. I will create a post one of these days with all the tools you can use to check for SQL attacks. 

So what actually happened with Kaspersky? Here is what ChannelWeb had to say about this

> A security vulnerability in Moscow-based Kaspersky Lab&#8217;s U.S. Web site was made public after a hacker launched a SQL attack and posted listings of tables contained on the security company&#8217;s site.
  
> The hacker, known as Unu, posted screen shots as well as a list of tables Feb. 7 to a blog after hacking into the security company&#8217;s Web site via a simple SQL injection attack that allowed information to be exposed by entering secret username and password information.
> 
> &#8220;Kaspersky is one of the leading companies in the security and antivirus market. It seems as though they are not able to secure their own databases,&#8221; the hacker said on a hackerblog.org posting. &#8220;Alter one of the parameters and you have access to EVERYTHING: users, activation codes, lists of bugs, admins, shop, etc.&#8221;

You can read that whole article here: http://www.crn.com/security/213401972

 [1]: http://twitter.com/kbriankelley/status/1195496252
 [2]: http://www.acunetix.com/
 [3]: /index.php/SysAdmins/OS/do-you-use-metasploit-to-check-if-your-s