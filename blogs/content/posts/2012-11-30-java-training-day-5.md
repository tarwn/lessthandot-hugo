---
title: Java Training Day 5
author: SQLDenis
type: post
date: 2012-12-01T00:41:00+00:00
excerpt: |
  Last day of the training and I am glad it is over...this stuff is draining
  
  Here is what was covered today
  
  Network Programming
  Low Level TCP/IP Protocols
  IPv4 and IPv6
  UDP Multicast
  TCP/IP
  
  Message Based protocols
  HTTP
  Connections framework&hellip;
url: /index.php/enterprisedev/application-lifecycle-management/java-training-day-5/
views:
  - 17102
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
tags:
  - io
  - java
  - javabean
  - jsp
  - jvm
  - sockets

---
Last day of the training and I am glad it is over&#8230;this stuff is draining

Here is what was covered today

**Network Programming**
  
Low Level TCP/IP Protocols
  
IPv4 and IPv6
  
UDP Multicast
  
TCP/IP

Message Based protocols
  
HTTP
  
Connections framework using URL (Unified Resource Locator)

Remote Objects
  
RMI (Remote Method Invocation)
  
CORBA (Common Object Request Broker Architecture)

Optional packages supporting additional protocols
  
SOAP, Mail etc etc

We talked about port numbers, here are some common ones that you might know

20 & 21: File Transfer Protocol (FTP)
  
22: Secure Shell (SSH)
  
23: Telnet remote login service
  
25: Simple Mail Transfer Protocol (SMTP)
  
53: Domain Name System (DNS) service
  
80: Hypertext Transfer Protocol (HTTP)
  
110: Post Office Protocol (POP3)
  
119: Network News Transfer Protocol (NNTP)
  
143: Internet Message Access Protocol (IMAP)
  
161: Simple Network Management Protocol (SNMP)
  
443: HTTP Secure (HTTPS)

**Threads**
  
In an operating process, each running program is basically a process
  
The operating system schedules processes for execution
  
Each process has its own (virtual) address space
  
Communications between processes is hampered by context switching

A thread is a flow of execution within a java process
  
The JVM schedules therads for execution
  
Threads share access to java objects
  
Communication between threads is quick

On multi-CPU/multi-core machines, several threads can execute at the same time

**Executing with threads**
  
One thread is started automatically to execute main()
  
The main() method may start additional threads
  
Single threaded applications run until main() terminates
  
Multi threaded applications run until all threads have terminated or if one of the threads calls System.exit()

**Static methods manipulate the current thread**
  
currentThread() returns a refernce to the thread that is currently executing
  
yield() pauses the currently executing thread
  
sleep(int ms) blocks the currently executing thread for a specific milliseconds

**Instance methods that manipulate a particular thread**
  
start() starts thread execution
  
getName() gets the name of the thread
  
interrupt() throws an exception or sets the interrupt status
  
join() waits for the thread to die
  
setPriority set the priority for a thread 

The run() method will hold the code to be executed by a thread

The stop(), suspend() and resume() methods are deprecated

Local variables are never shared between threads, threads can share memory on the heap like static variables, instance variables and members of arrays
  
We covered synchronization, thread scheduling

**JDBC**
  
WE covered the regular DB related stuff, how to connect to a DB, explanation what the 4 type of jdbc drivers are, executing, statements, prepared statements and stored procedures

**Web programming**
  
We covered servlets, JSP, javabeans, struts, mvc&#8230;pretty much similar stuff I was doing in 2001 but now we have many more frameworks to makes things easier