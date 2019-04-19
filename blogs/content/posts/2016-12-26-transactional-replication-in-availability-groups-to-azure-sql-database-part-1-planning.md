---
title: 'Transactional Replication from Availability Groups to Azure SQL Database: Part 1 – Planning'
author: Jes Borland
type: post
date: 2016-12-26T15:00:35+00:00
ID: 4896
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/transactional-replication-in-availability-groups-to-azure-sql-database-part-1-planning/
views:
  - 7370
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
Throughout the last few years, I've worked with SQL Server Availability Groups, Transactional Replication, and Azure SQL Databases. Recently, I had the challenge and opportunity to work on a project that involved all three at the same time. The goal was to take six databases that were in a SQL Server 2012 Availability Group and replicate them to Azure SQL Databases.

Both replication of a database in an AG and replication from SQL Server to SQL Database come with several caveats, limitations, and challenges. In this five-post blog series, I hope to share with you the lessons I've learned so you can do this correctly from the beginning.

  * <a href="/?p=4896" target="_blank">Part 1 – Planning</a>
  * <a href="/?p=4906" target="_blank">Part 2 – The Distributor</a>
  * <a href="/?p=4923" target="_blank">Part 3 – The Publisher</a>
  * <a href="/?p=4945" target="_blank">Part 4 – The Subscriber</a>
  * <a href="/?p=4960" target="_blank">Part 5 – Testing</a>

&nbsp;

### Getting Started

Determine what you need to accomplish and if there is a simpler way to do it. This is a complicated solution with a lot of moving parts. Many things can go wrong or break. In this situation, we needed to have on-premises data in Azure to be consumed by other Azure services and for analytics. It also had to stay on-premises for transactional applications. The other option for moving the data on a regular basis was <a href="https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-sql-data-sync" target="_blank">Azure SQL Data Sync</a>, which has been in preview for five (long) years – I didn't want to use a preview technology, especially one that's been in preview mode for so long.

You should have some familiarity with each of these features before combining them. Write up an architecture document ahead of time that will include all the relevant information you need as you're setting this up – replica names, publication properties, distributor properties, subscriber information, SQL DB information. Download my <a href="/?p=4899" target="_blank">Replication Setup Checklist</a> to be prepared.

Read through this series, and these other blog posts, to be prepared. I've referenced these blogs many time:

  * <a href="https://msdn.microsoft.com/en-us/library/hh710046.aspx" target="_blank">Configure Replication for Always On Availability Groups</a> (MSDN)
  * <a href="https://blogs.msdn.microsoft.com/alwaysonpro/2014/01/30/setting-up-replication-on-a-database-that-is-part-of-an-alwayson-availability-group/" target="_blank">Setting up Replication on a database that is part of an AlwaysOn Availability Group</a> (AlwaysOn Support Team)
  * <a href="https://msdn.microsoft.com/en-us/library/mt589530.aspx" target="_blank">Replication to SQL Database</a> (MSDN)
  * <a href="http://johnsterrett.com/2016/07/26/azure-sql-database-live-migrations/" target="_blank">Azure SQL Database Live Migrations</a> (John Sterrett)

Lastly, reach out for help if needed. The SQL Server community helped me many, many times while I set this up. Special thanks to Kendal Van Dyke (<a href="http://www.kendalvandyke.com/" target="_blank">blog </a>| <a href="https://twitter.com/SQLDBA" target="_blank">Twitter</a>), Drew Furgiuele (<a href="http://port1433.com/" target="_blank">blog </a>| <a href="https://twitter.com/Pittfurg" target="_blank">Twitter</a>), and Andy Mallon (<a href="https://www.am2.co/" target="_blank">blog </a>| <a href="https://twitter.com/AMtwo" target="_blank">Twitter</a>) for their help.

### Prep Work

There are tasks you'll need to take care of in SQL Server, the AG, and the SQL DB before you can begin.

This blog series assumes you already have an AG set up – it won't go through the setup of that. It also assumes you have an Azure SQL server and a SQL Database created – it won't go through that setup either.

Ideally, the publishers, distributor, and subscribers will all be the same version and edition of SQL Server. If not, you have to configure from the highest-version server, or you will get errors.

There are minimum version and service pack requirements for replicating to a SQL DB, as noted in the article <a href="https://msdn.microsoft.com/en-us/library/mt589530.aspx" target="_blank">Replication to SQL Database</a>. Make sure all SQL Servers are patched to the correct version.

Make sure replication components are installed on all SQL Server instances before beginning. You can verify by running this query. The desired result is 1.

<pre style="padding-left: 30px">USE master;
GO</pre>

<pre style="padding-left: 30px">DECLARE @installed int;
EXEC @installed = sys.sp_MS_replication_installed;
SELECT @installed;</pre>

In the AG, all the replicas must be readable. If they aren't, the distributor can't read them, and the setup won't work.

The distribution database should not be on a replica server. If that server is lost and the HA of the AG kicks in, you've lost a huge part of your replication strategy. Your distribution database needs to reside on a SQL Server that is outside of your AG.

The service accounts for the engine and Agent on all publishers, distributor, and subscribers must be Windows accounts. Don't do this with NT Service accounts. Also, make sure the accounts have minimum permissions or you may get SSPI errors. (Described at <https://cmatskas.com/fixing-error-cannot-generate-sspi-context-after-changing-sql-service-account/>.)

The service account of the Log Reader Agent must be a db_owner in the publication database. As a matter of fact, there are a whole lot of rules about service accounts and permissions. Read <a href="https://msdn.microsoft.com/en-us/library/ms151227.aspx" target="_blank">Replication Agent Security Model</a> and apply all these rules. Do not just automatically make accounts sysadmin and local admins. It will be hard and frustrating – but your setup needs to be secure.

On SQL Servers, make sure port 1433 is open on firewalls.

When replicating to an Azure SQL Database:

You must have a user set up in that database that is member of the db_owner role.

Make sure the IP address of every publisher and distributor is allowed through the firewall.

Using SSMS, verify you can connect from each publisher and distributor to the SQL DB. Resolve any errors before continuing.

Use the “Deploy Database to Microsoft Azure SQL Database” wizard to find any incompatibilities within the database. If there are stored procedures that cross-reference another database, for example, that isn't supported in SQL Database and those objects can't be replicated.

With all those pieces in place, let's get started!

### Scenario

Publishers: servers SQL2014AG1 and SQL2014AG2, database AGTest

Distributor: stand-alone server, SQL2014demo

Subscriber: Azure SQL Database, server jessqldb2, database ReplicationTest