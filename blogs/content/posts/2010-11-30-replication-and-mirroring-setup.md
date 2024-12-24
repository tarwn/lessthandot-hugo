---
title: Mirroring and Transactional Replication setup – Auto Failover
author: Ted Krueger (onpnt)
type: post
date: 2010-11-30T12:36:20+00:00
ID: 961
excerpt: Since mirroring has come to the SQL Server feature set, High Availability (HA) has become a much easier, inexpensive and available option. Prior to mirroring, third party software and hardware were the main options to achieve HA. This was due to replication never being accepted as a high availability option. With any solution like SQL Server, growth over time builds a better and more stable solution. This has been the fact with many features of SQL Server, including replication. Replication had some issues in previous versions of SQL Server. Maintaining the uptime and stability was not a trivial task. With replication, there is a more in-depth knowledge requirement to maintain it, while mirroring does not require as much in-depth knowledge to provide a solid solution. Do think that mirroring does not include a strict discipline of skills in order to make it a successful HA solution. Mirroring is simplistic from the outside, both in setup and maintainability. However, troubleshooting mirroring is a critical aspect to making it work for HA. In order to be successful, knowledge of the internals of mirroring is as important as replication.
url: /index.php/datamgmt/dbprogramming/replication-and-mirroring-setup/
views:
  - 30039
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - mirroring
  - replication
  - sql server 2005
  - sql server 2008
  - transactional replication

---
Since mirroring has come to the SQL Server feature set, High Availability (HA) has become a much easier, inexpensive and available option. Prior to mirroring, third party software and hardware were the main options to achieve HA. This was due to replication never being accepted as a high availability option. With any solution like SQL Server, growth over time builds a better and more stable solution. This has been the fact with many features of SQL Server, including replication. Replication had some issues in previous versions of SQL Server. Maintaining the uptime and stability was not a trivial task. With replication, there is a more in-depth knowledge requirement to maintain it, while mirroring does not require as much in-depth knowledge to provide a solid solution. Do think that mirroring does not include a strict discipline of skills in order to make it a successful HA solution. Mirroring is simplistic from the outside, both in setup and maintainability. However, troubleshooting mirroring is a critical aspect to making it work for HA. In order to be successful, knowledge of the internals of mirroring is as important as replication.

What happens when replication is already in use for what it is more well-known for; syncing data between like data sources? When replication is already in use, mirroring is still a successful HA solution. This includes a high safety model with automatic failover. There are a few things that need to be done to make replication and mirroring function well together. These include the order of setup, and the settings and configurations for replication specifically.

> Resources: Books online (BOL) has a starting point for mirroring with. http://technet.microsoft.com/en-us/library/ms151799.aspx
  
> This resource also provides links to configuring replication with mirroring and log shipping with mirroring. All important reading and highly recommend outside of this article. 

**Setup**

If you followed the link above, BOL provides a basic listing of the order to setup mirroring with replication.

  1. Configure the Publisher.
  2. Configure database mirroring.
  3. Configure the mirror to use the same Distributor as the principal.
  4. Configure replication agents for failover.
  5. Add the principal and mirror to Replication Monitor.

We will follow these steps while adding in a few catches and added details that need to be configured to ensure processing flows while not in a failover situation and after a failover situation.

**Mirroring and Replication Landscape**

In the end, the solution shown in the following diagram will be achieved. 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_01.gif" alt="" title="" width="602" height="380" />
</div></p> 

From the above diagram, you can see that three servers are involved. Server A acts as the principal as well as the publisher. 

  * Principal: The primary server in mirroring
  * Mirror: The secondary server in mirroring
  * Publisher: The primary source of replication
  * Subscriber: The subscription to the published data in replication

To follow the setup order, the first task is to setup the publisher in transactional replication. This server also acts as the principal in mirroring (which will be setup later).

> AdventureWorks will be used in this setup and can be found <http://sqlserversamples.codeplex.com/>
  
> The SQL Server versions that are being used in this article are as follows
  
> Server A: SQL Server 2008 R2 – Principal and Publisher – ONPNT_XPSXPS2008R2
  
> Server B: SQL Server 2008 R2 – Mirror and Distributor – ONPNT_XPSR2Distributor
  
> Server C: SQL Server 2005 – Subscriber – ONPNT_XPS
  
> Setting up the distributor

Replication setup can be achieved with the built in wizards from SSMS as well as the replication stored procedures packed with SQL Server. If using the wizard to set up replication, it is highly recommended that the "script to file" option is selected in the last screen of the wizard. This will script out the complete configuration the wizard has done utilizing the stored procedures behind the scenes. This acts as a quick way to set replication back up if the system is lost. Copy these files to a share that is located somewhere else and is also part of a disk to disk HA or DR solution.

Ensure that SQL Server Replication is installed on the SQL Server instance. If it is not, use the setup installation wizard from your media to install.
  
On Server B, acting as our remote distributor, setup is required prior to configuring Server A for publication of the tables we want to replicate. To set the distributor up, follow these steps.

Using the wizard: right click Replication in the tree view in SSMS and select, configure Distribution...

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_02.gif" alt="" title="" width="390" height="90" />
</div>

Setup steps included in the wizard will be as follows:

  1. Distributor: Acts as its own Distributor
  2. Snapshot folder: default for this lab but share on network for true production environment
  3. Distribution Database: leave defaults to your disk configurations
  4. Publishers: leave distributor as publisher and add Server A as a publisher. XPS2008R2 in this test
  5. Distributor password: Enter the service credentials. Needs access to Server A and B and share
  6. Wizard Actions: Check both configure and generate scripts
  7. Execute setup

Each step shown below ordered left to right

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_03.gif" alt="" title="" width="720" height="654" />
</div>

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_04.gif" alt="" title="" width="719" height="327" />
</div></p> 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_05.gif" alt="" title="" width="504" height="307" />
</div>

**Move to the publisher now.**

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_06.gif" alt="" title="" width="330" height="98" />
</div>

In the wizard:

  1. Distributor: change the distributor to the remote distributor we setup prior
  2. Administrative Password: enter the administrator password to gain access to the remote distributor
  3. Publication Database: select AdventureWorks as the database
  4. Publication Type: select Transactional publication for the publication type
  5. Articles: select the articles (tables: Address, AddressType, Contact, ContactType and CountryRegion)
  6. Filtered table rows: no filters
  7. Snapshot Agent: check "Create a snapshot immediately and keep the snapshot available to initialize subscriptions"
  8. Agent Security: select the security settings as the "Run under the SQL Server Agent service account"
  9. Wizard Options: finish by selecting the Generate scripts option.

These steps are shown in order below ordered left to right

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_07.gif" alt="" title="" width="695" height="640" />
</div>

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_08.gif" alt="" title="" width="698" height="318" />
</div>

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_09.gif" alt="" title="" width="709" height="638" />
</div>

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_10.gif" alt="" title="" width="356" height="320" />
</div>

Recap: At this point we have a distributor role on R2Distributor, Server B; and a publication role on XPS2008R2, Server A. 

To finish the replication setup, create a subscription to the publication on ONPNT_XPS, Server C. To do this, connect to the SQL Server in SSMS, right click the subscriptions and select New Subscriptions...

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_11.gif" alt="" title="" width="361" height="132" />
</div>

Select XPS2008R2 (your own server name replaced here) as Publisher

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_12.gif" alt="" title="" width="498" height="238" />
</div>

Through the wizard, make the following selections.

  1. Distribution Agent Location: Run all agents at the Distributor
  2. Subscribers: Subscriber (ONPNT_XPS), Subscription Database (AdventureWorks)
  3. Distribution Agent Security: Impersonate process account 
  4. Synchronization Schedule: Run Continuously
  5. Initialize Subscriptions: Immediately
  6. Wizard Actions: Create subscriptions and Generate scripts

Each step shown below ordered left to right

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_13.gif" alt="" title="" width="727" height="662" />
</div>

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_14.gif" alt="" title="" width="732" height="332" />
</div>

We've successfully setup transactional replication at this point and can move to setting up the mirroring session. The mirror will exist between Server A (Replication Publisher) and Server B (Replication Distributor). To set mirroring up, refer to the following blog on [setting mirroring up with Developer Edition][1].

Once the mirror setup is completed, replication must be configured further to ensure a failover in the mirror is also followed with a failover in the replication. To accomplish this, the agent profiles for all replication agents must have the –PublisherFailoverPartner set. This ensures that in the event of a failover, the agents know what the mirror is.

To set the agents, use the sp\_add\_agent_parameter procedure

```sql
exec sp_add_agent_parameter @profile_id = 1, @parameter_name = N'-PublisherFailoverPartner', @parameter_value = N'ONPNT_XPSR2Distributor'
exec sp_add_agent_parameter @profile_id = 2, @parameter_name = N'-PublisherFailoverPartner', @parameter_value = N'ONPNT_XPSR2Distributor'
exec sp_add_agent_parameter @profile_id = 3, @parameter_name = N'-PublisherFailoverPartner', @parameter_value = N'ONPNT_XPSR2Distributor'
exec sp_add_agent_parameter @profile_id = 9, @parameter_name = N'-PublisherFailoverPartner', @parameter_value = N'ONPNT_XPSR2Distributor'
```

The profile ID can be obtained by querying the [MSAgent_Profiles][2] table in the MSDB database. The agent types that will be contained in this table are:

1 = Snapshot Agent
  
2 = Log Reader Agent
  
3 = Distribution Agent
  
4 = Merge Agent
  
9 = Queue Reader Agent

Since profile IDs may be different on different installations, verify your profile ID by check this table. Once the parameters are set, use the [MSagent_parameters][3] table to verify by checking the parameter_name column for the value -PublisherFailoverPartner.

```sql
SELECT * FROM MSagent_parameters WHERE parameter_name = '-PublisherFailoverPartner'
```

If we were in a merge replication setup, profile 4 would also need to be set.

The procedure sets the mirror name as the failover parameter. Run this on the distributor (which is also acting as the mirror in our case).

**Verification and failover test**

Testing is everything when you have a more complex HA setup, such as mirroring with replication. Taking testing in stages will allow for problem areas to be quickly resolved, rather than testing the end result and backtracking through your configurations.

At this point mirroring and replication is functioning together. The principal and mirror are acting in a synchronized state and replication is acting based off the distributor. To test this setup, test mirroring by altering a record in AdventureWorks on the principal.

```sql
SELECT AddressLine1 FROM person.Address WHERE AddressID = 1
```

The statement above results in, "1970 Napa Ct". Update the AddressLine1 value to, "1970 Napa Court" and ensure the changes flow to the subscriber of the publication.

```sql
UPDATE Person.Address SET AddressLine1 = '1970 Napa Court'
WHERE AddressID = 1
```

Check the subscriber by running the same select. You should see the changes made to the same record. In the replication monitor we can also see the commands sent

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_15.gif" alt="" title="" width="607" height="249" />
</div>

Our next step it to test a complete failover of the mirror and then make this same test to ensure that replication also fails over as it is detected.

Right click the AdventureWorks database on the Principal and select properties. Go to the mirroring page and click the Failover button

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_16.gif" alt="" title="" width="628" height="244" />
</div>

Click Yes to the warning on failover. The wizard screen will close when the failover is completed. 

At this point replication is disconnected from the publisher on running commands. We can see this by looking at the replication monitor and seeing retry attempts

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_17.gif" alt="" title="" width="628" height="104" />
</div>

The default amount of time that is monitored for replication to failover is around 35 seconds. Once replication agents failover to the mirror set in the failover partner parameter, the agent status in the monitor will resume the running state.

To test the failover, run the same test as we performed earlier but in reverse order. Update the mirror that is now acting as the principal and follow the replicated commands through to the subscriber to ensure the changes were committed there.

In this case, update the same value back to the Ct over Court value.

```sql
UPDATE Person.Address SET AddressLine1 = '1970 Napa Ct'
WHERE AddressID = 1
```

Check on the subscriber now that the changes flowed as they should have

```sql
SELECT AddressLine1 FROM person.Address WHERE AddressID = 1
```

We see that the failover was successful and the value committed as, "1970 Napa Ct" and the distributor in the replication monitor shows the commands sent.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror_repl/mirroring_replicaton_18.gif" alt="" title="" width="628" height="218" />
</div>

Once verified, fail the mirroring session back to the original state and test again.

**Primary concerns**

The primary concern with putting replication to work on a database that is mirrored will fall into latency. Replication latency will increase when certain mirroring operating modes are set or in the case a mirror is in a lengthy synchronizing state. This is more the case when High Performance mirroring is set. 

In some cases when replication is heavily used, multiple distributors are also utilized to balance loads across physical SQL Servers. With mirroring and replication, the distributor must be the same for both the principal and the mirror. This is due to the state of the mirror and requiring it to mimic the principal at all times. 

Finally, unsupported replication types include: Immediate update Subscribers, Oracle publishers, peer-to-peer publishers and republishing. 

This and other important notes that should be considered when working with mirroring and replication on the same database can be found in [this BOL document][4].

This entire solution can be scripted out both by the use of the Generate Scripts option and by altering the scripts to utilize parameters you pass in. It is recommended that you at least save the generated scripts from the configurations so the setup can be restored quickly. This includes mirroring scripts for the endpoints. 

You can see a close example of scripting the replication setup on mirrored databases [here][5] on SQLServerCentral.com.

Thanks to Kendal Van Dyke ([Twitter][6] | [Blog][7]) (aka, the replication king) for the technical review of the article.

 [1]: /index.php/DataMgmt/DBAdmin/sql-server-2008-mirroring-setup
 [2]: http://msdn.microsoft.com/en-us/library/ms178540.aspx
 [3]: http://msdn.microsoft.com/en-us/library/ms188339.aspx
 [4]: http://technet.microsoft.com/en-us/library/ms151799.aspx
 [5]: http://www.sqlservercentral.com/scripts/Replication/70508/
 [6]: http://twitter.com/sqldba
 [7]: http://www.kendalvandyke.com/