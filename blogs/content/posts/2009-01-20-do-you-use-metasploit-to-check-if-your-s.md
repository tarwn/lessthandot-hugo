---
title: Do you use Metasploit to check if your servers are vulnerable?
author: SQLDenis
type: post
date: 2009-01-20T12:04:11+00:00
ID: 292
url: /index.php/sysadmins/os/do-you-use-metasploit-to-check-if-your-s/
views:
  - 4524
rp4wp_auto_linked:
  - 1
categories:
  - Operating Systems
tags:
  - buffer overflow
  - hackers
  - metasploit
  - security

---
Do you use Metasploit to check if your servers are vulnerable? If not then you should take a look at it. 

**What is Metasploit?**
  
The Metasploit Framework is a development platform for creating security tools and exploits. The framework is used by network security professionals to perform penetration tests, system administrators to verify patch installations, product vendors to perform regression testing, and security researchers world-wide. The framework is written in the Ruby programming language and includes components written in C and assembler.

**What does Metasploit do?**
  
The framework consists of tools, libraries, modules, and user interfaces. The basic function of the framework is a module launcher, allowing the user to configure an exploit module and launch it at a target system. If the exploit succeeds, the payload is executed on the target and the user is provided with a shell to interact with the payload.

Here are some thing you can check with this

**Scanning for MD5-signed SSL Certificates**
  
Allows the Metasploit framework to be used as a SSL certificate scanner. This provides a simple way to identify SSL certificates in use that were signed with the MD5 algorithm and need to re-issued

**Fuzzing Flash For Fun (ASNative)**
  
The vulnerable function is exposed through the System.Product namespace but is also accessible by using the ASnative API directly. 

The ASnative API is interesting because it offers a different way to call builtin functions, and the only way to call certain undocumented functions. ASnative functions are indexed in a two-dimensional array, with the first index being between 1 and 3000, while the second index is usually below 300. The &#8220;escape&#8221; function has an index value of (100,0)

Remember a H4x0r already is using Metasploit and other tools to penetrate and take over your servers by take advantage of buffer overflows and other vulnerabilities.

You can download Metasploit here: http://www.metasploit.com/framework/

Also check out [Oracle Pwnage with the Metasploit Oracle Modules Part 2][1] and many more exploits on http://carnal0wnage.blogspot.com

 [1]: http://carnal0wnage.blogspot.com/2008/11/oracle-pwnage-with-metasploit-oracle_17.html