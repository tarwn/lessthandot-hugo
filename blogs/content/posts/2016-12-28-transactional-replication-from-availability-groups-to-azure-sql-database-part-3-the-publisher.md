---
title: 'Transactional Replication from Availability Groups to Azure SQL Database: Part 3 – The Publisher'
author: Jes Borland
type: post
date: 2016-12-28T15:00:18+00:00
ID: 4923
url: /index.php/uncategorized/transactional-replication-from-availability-groups-to-azure-sql-database-part-3-the-publisher/
views:
  - 4141
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
This is part 3 of a 5-part series.

  * <a href="/?p=4896" target="_blank">Part 1 – Planning</a>
  * <a href="/?p=4906" target="_blank">Part 2 – The Distributor</a>
  * <a href="/?p=4923" target="_blank">Part 3 – The Publisher</a>
  * <a href="/?p=4945" target="_blank">Part 4 – The Subscriber</a>
  * <a href="/?p=4960" target="_blank">Part 5 – Testing</a>

&nbsp;

### Scenario

Publishers: servers SQL2014AG1 and SQL2014AG2, database AGTest

Distributor: stand-alone server, SQL2014demo

Subscriber: Azure SQL Database – server jessqldb2, database ReplicationTest

### Setting up the replication publisher

You'll need to start by configuring the AG current primary as Publisher. (In this demo, this is SQL2014AG2.) Then, you'll need to configure the distributor that we set up in step 2 to use the AG listener name, rather than the server name.

Log into the primary – SQL2014AG2.

Expand Replication, right-click Local Publications, select New Publication.

Click Next on Welcome.

Distributor – select Use the following server and click Add. Connect to the distributor you configured in the previous step. Click Next.

[<img class="aligncenter size-full wp-image-4925" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-1.png" alt="publisher 1" width="503" height="453" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-1.png 503w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-1-300x270.png 300w" sizes="(max-width: 503px) 100vw, 503px" />][1]

Administrative Password – enter the distributor admin password you created in the previous step. Click Next. (If you add multiple publications from one server, you only do this the first time.)

[<img class="aligncenter size-full wp-image-4926" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-2.png" alt="publisher 2" width="506" height="457" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-2.png 506w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-2-300x270.png 300w" sizes="(max-width: 506px) 100vw, 506px" />][2]

Publication database – choose your database. Click Next.

[<img class="aligncenter size-full wp-image-4927" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-3.png" alt="publisher 3" width="506" height="454" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-3.png 506w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-3-300x269.png 300w" sizes="(max-width: 506px) 100vw, 506px" />][3]

Publication type – because this is going to Azure SQL DB, it must be Transactional. Select your option. Click Next.

[<img class="aligncenter size-full wp-image-4928" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-4.png" alt="publisher 4" width="502" height="451" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-4.png 502w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-4-300x269.png 300w" sizes="(max-width: 502px) 100vw, 502px" />][4]

Articles – choose which database objects you want to replicate. Click Next.

[<img class="aligncenter size-full wp-image-4929" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-5.png" alt="publisher 5" width="505" height="457" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-5.png 505w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-5-300x271.png 300w" sizes="(max-width: 505px) 100vw, 505px" />][5]

Filter Table Rows – if you need to filter, click Add. Otherwise, click Next.

[<img class="aligncenter size-full wp-image-4930" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-6.png" alt="publisher 6" width="507" height="454" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-6.png 507w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-6-300x268.png 300w" sizes="(max-width: 507px) 100vw, 507px" />][6]

Snapshot Agent – choose to create a snapshot immediately. Click Next.

[<img class="aligncenter size-full wp-image-4931" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-7.png" alt="publisher 7" width="509" height="453" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-7.png 509w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-7-300x266.png 300w" sizes="(max-width: 509px) 100vw, 509px" />][7]

Agent Security – click Security Settings to choose what account the snapshot and log reader agents will run under. Click OK. Click Next.

(I could write an entire blog post about this, but that is better left to people more well-versed in replication than myself. I know that I don't want to use the SQL Server Agent service account – I want to create an AD account that has been granted the permissions needed as described in  <a href="https://msdn.microsoft.com/en-us/library/ms151227.aspx" target="_blank">Replication Agent Security Model</a>.)

[<img class="aligncenter size-full wp-image-4932" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-8.png" alt="publisher 8" width="553" height="474" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-8.png 553w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-8-300x257.png 300w" sizes="(max-width: 553px) 100vw, 553px" />][8]

&nbsp;

[<img class="aligncenter size-full wp-image-4933" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-9.png" alt="publisher 9" width="508" height="454" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-9.png 508w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-9-300x268.png 300w" sizes="(max-width: 508px) 100vw, 508px" />][9]

Wizard Actions – choose both options. Click Next.

[<img class="aligncenter size-full wp-image-4934" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-10.png" alt="publisher 10" width="504" height="452" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-10.png 504w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-10-300x269.png 300w" sizes="(max-width: 504px) 100vw, 504px" />][10]

Script File Properties – choose a location to save the script. Review the other options. Click Next.

[<img class="aligncenter size-full wp-image-4935" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-11.png" alt="publisher 11" width="503" height="452" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-11.png 503w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-11-300x269.png 300w" sizes="(max-width: 503px) 100vw, 503px" />][11]

Complete the wizard – enter a Publication name. Click Finish.

[<img class="aligncenter size-full wp-image-4936" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-12.png" alt="publisher 12" width="502" height="453" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-12.png 502w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-12-300x270.png 300w" sizes="(max-width: 502px) 100vw, 502px" />][12]

All options should have green checkmarks.

[<img class="aligncenter size-full wp-image-4937" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-13.png" alt="publisher 13" width="506" height="453" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-13.png 506w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-13-300x268.png 300w" sizes="(max-width: 506px) 100vw, 506px" />][13]

After initializing, check the Snapshot Agent and Log Reader Agent for success. (To do so, go to Replication, right-click the publication name, and select Snapshot Agent Status and Log Reader Agent Status.) I ran into problems with the Snapshot account not having high enough permissions in the databases (it needs db_owner), and then not having enough permissions on the snapshot folder (it needs Full). (This forum post, answered by Hilary Cotter, helped: <https://social.msdn.microsoft.com/Forums/sqlserver/en-US/899857db-e38e-4026-a34c-2a8c2628c6fc/access-denied-to-sql-replication-snapshot-folder?forum=sqlreplication>.)

### Configure distribution on all other publishers

Because my database is in an AG, I need to make sure that the distributor is configured on all the replicas in the AG. Note: you only have to do this step for the first publication – not each subsequent publication.

In this example, I'll need to configure SQL2014AG1, the other replica.

Connect to SQL2014AG1. Right-click Replication and select Configure Distribution.

Welcome – click Next.

Distributor – select Use the following server. Click Add. Connect to the previously-created distributor. Click Next.

[<img class="aligncenter size-full wp-image-4938" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-14.png" alt="publisher 14" width="573" height="452" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-14.png 573w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-14-300x236.png 300w" sizes="(max-width: 573px) 100vw, 573px" />][14]

Administrative password – enter the distribution admin password. Click Next.

[<img class="aligncenter size-full wp-image-4939" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-15.png" alt="publisher 15" width="571" height="455" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-15.png 571w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-15-300x239.png 300w" sizes="(max-width: 571px) 100vw, 571px" />][15]

Wizard Actions – check both boxes, click Next.

[<img class="aligncenter size-full wp-image-4940" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-16.png" alt="publisher 16" width="573" height="454" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-16.png 573w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-16-300x237.png 300w" sizes="(max-width: 573px) 100vw, 573px" />][16][
  
][17] 

Script File Properties – choose a file location and name. Review other options. Click Next.

[<img class="aligncenter size-full wp-image-4941" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-17.png" alt="publisher 17" width="572" height="457" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-17.png 572w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-17-300x239.png 300w" sizes="(max-width: 572px) 100vw, 572px" />][17]

Complete the wizard – click Finish. All options should have green checkmarks.

[<img class="aligncenter size-full wp-image-4942" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-18.png" alt="publisher 18" width="579" height="459" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-18.png 579w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-18-300x237.png 300w" sizes="(max-width: 579px) 100vw, 579px" />][18]

### Create linked server from each replica to distribution server

This step also needs to be done only once, for the first publication.

Connect to each replica. Run this script.

<pre style="padding-left: 30px">USE master;
GO</pre>

<pre style="padding-left: 30px">EXEC sys.sp_addlinkedserver @server = 'DistributorServerName';
GO</pre>

After doing that, in Object Explorer, expand Server Objects > Linked Servers. Right-click the linked server and select Test Connection. Make sure that is successful.

### Redirect the original publisher name to the listener name

This needs to be done for every publication you set up where the database is in an AG.

This step also needs to be done from the distributor server – you can't do this from SSMS on another server or your workstation. You must RDP to the distributor and run these commands – in SSMS or even SQL CMD.

Connect to the distributor.

Run the following command.

<pre style="padding-left: 30px">USE distribution;
GO</pre>

<pre style="padding-left: 30px">EXEC sys.sp_redirect_publisher @original_publisher = 'SQL2014AG2', @publisher_db = 'AGTest', @redirected_publisher = 'JesTestAGListen';
GO</pre>

Expected output is "Commands completed successfully."

Then, you want to confirm this worked. To do so, run the following command.

<pre style="padding-left: 30px">DECLARE @redirected_publisher sysname;</pre>

<pre style="padding-left: 30px">EXEC sys.sp_validate_replica_hosts_as_publishers @original_publisher = 'SQL2014AG2', @publisher_db = 'AGTest', @redirected_publisher = 'JesTestAGListen';
GO</pre>

Expected output is "Commands completed successfully."

After following all of these steps, your publication is ready. You can now set up the subscriber(s).

 [1]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-1.png
 [2]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-2.png
 [3]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-3.png
 [4]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-4.png
 [5]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-5.png
 [6]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-6.png
 [7]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-7.png
 [8]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-8.png
 [9]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-9.png
 [10]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-10.png
 [11]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-11.png
 [12]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-12.png
 [13]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-13.png
 [14]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-14.png
 [15]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-15.png
 [16]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-16.png
 [17]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-17.png
 [18]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/12/publisher-18.png