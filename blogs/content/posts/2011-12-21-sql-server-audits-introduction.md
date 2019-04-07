---
title: 'SQL Server Audits: Introduction'
author: SQLArcher
type: post
date: 2011-12-21T05:38:00+00:00
ID: 1456
excerpt: "Previously DBA's and in some cases even developers had to write custom triggers or set up various profiler traces to monitor what was happening in their SQL Server instances. When measures like this were left to the junior staff joining the company, it&hellip;"
url: /index.php/datamgmt/dbadmin/sql-server-audits-introduction/
views:
  - 2662
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
Previously DBA&#8217;s and in some cases even developers had to write custom triggers or set up various profiler traces to monitor what was happening in their SQL Server instances. When measures like this were left to the junior staff joining the company, it held potential disastrous complications in various forms ranging from bad performance due to the overhead or storage issues when traces are not monitored and archived. Some of these issues could take hours to resolve when the steps taken to audit the system is not documented and the root cause obscured in the form of witty triggers firing relentlessly.

Then came SQL Server 2008&#8230; One of the numerous &#8220;enhancements&#8221; or features introduced in SQL Server 2008 are the Audit Specifications. The new component is built on top of the extended events feature, and there for provides a lightweight auditing mechanism.

In this series which will continue over the next few days, I will cover some of the basics to set up auditing such as requirements, use cases, and some general audits that will poke their heads out in the environment. 

To start the series off, let&#8217;s look at the components that makes up the auditing feature.

Both the target and server specification can be found under the instance security list, and the database level specification can be found under the security lists for the various individual databases.

The three main components that make up the auditing feature is;

### Server Audit Specification

This specification is specifically aimed at events on an instance level which includes login attempts, DBCC commands, backup and restores, and other instance level events.

### Database Audit Specification

This specification is aimed at events related to all database level actions, like T-SQL statements, stored procedure executions, object modifications in terms of schema changes, etc.

### Targets

The new auditing mechanism supports three targets; file, security log, and application log. This determines where your audit records end up as well as some additional actions as to what SQL Server should do when certain conditions are met i.e. shut down SQL when a failed login attempt occurs.

When using a file target, the ACL should be set up to allow only the required accounts access, the SQL Service account, person or group that will act as the administrators which in both cases will require READ/WRITE to the directory.

The application log is less secure as it can be read by anyone who successfully authenticates on the server. There for it is recommended to use the security log when writing to the event log.

To log the events to the security log, the <span class="MT_under">generate security audits policy</span> needs to be granted to the SQL Server service account. A detailed process can be found [here][1].

In the following posts we will cover creating a basic audit specifications on both server and database levels. We will also look at how to manage the audits, as well as some additional analysis we can do with the information that is captured.

 [1]: http://msdn.microsoft.com/en-us/library/cc645889.aspx