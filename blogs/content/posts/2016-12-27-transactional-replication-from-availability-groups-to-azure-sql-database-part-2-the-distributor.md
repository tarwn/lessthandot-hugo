---
title: 'Transactional Replication from Availability Groups to Azure SQL Database: Part 2 – The Distributor'
author: Jes Borland
type: post
date: 2016-12-27T15:00:42+00:00
ID: 4906
url: /index.php/uncategorized/transactional-replication-from-availability-groups-to-azure-sql-database-part-2-the-distributor/
views:
  - 4078
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
This is part 2 of a 5-part series.

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

### Setting up the replication distributor

The first step in this process is to set up the remote distributor. As I mentioned in the first blog, you do not want your distribution database on one of the AG replicas. You need to set this up on a server that is not part of the AG.

Start by logging on to the distributor server – in this demo, SQL2014demo.

Right-click Replication and select Configure Distribution.

Click Next on Welcome

Distributor – select 'Server' will act as its own Distributor. Click Next.

[<img class="aligncenter wp-image-4912 size-full" src="/wp-content/uploads/2016/12/distribution-1.png" alt="distribution 1" width="503" height="456" srcset="/wp-content/uploads/2016/12/distribution-1.png 503w, /wp-content/uploads/2016/12/distribution-1-300x271.png 300w" sizes="(max-width: 503px) 100vw, 503px" />][1]

Snapshot folder – enter a network path that _all_ the service accounts that run SQL Server engine and Agent on all the servers in this configuration have full rights to. Click Next.

[<img class="aligncenter size-full wp-image-4913" src="/wp-content/uploads/2016/12/distribution-2.png" alt="distribution 2" width="501" height="455" srcset="/wp-content/uploads/2016/12/distribution-2.png 501w, /wp-content/uploads/2016/12/distribution-2-300x272.png 300w" sizes="(max-width: 501px) 100vw, 501px" />][2]

Distribution Database – enter database name and file location. Click Next.

[<img class="aligncenter size-full wp-image-4914" src="/wp-content/uploads/2016/12/distribution-3.png" alt="distribution 3" width="503" height="451" srcset="/wp-content/uploads/2016/12/distribution-3.png 503w, /wp-content/uploads/2016/12/distribution-3-300x268.png 300w" sizes="(max-width: 503px) 100vw, 503px" />][3]

Publishers – use the Add button to connect to **each** of the servers that will be a publisher. If this is an AG configuration, it must be **every** replica in the AG. Click Next.

[<img class="aligncenter size-full wp-image-4915" src="/wp-content/uploads/2016/12/distribution-4.png" alt="distribution 4" width="509" height="455" srcset="/wp-content/uploads/2016/12/distribution-4.png 509w, /wp-content/uploads/2016/12/distribution-4-300x268.png 300w" sizes="(max-width: 509px) 100vw, 509px" />][4]

Distributor password – enter a secure administrative password. **Save it in a password vault**! Click Next.

[<img class="aligncenter size-full wp-image-4916" src="/wp-content/uploads/2016/12/distribution-5.png" alt="distribution 5" width="503" height="456" srcset="/wp-content/uploads/2016/12/distribution-5.png 503w, /wp-content/uploads/2016/12/distribution-5-300x271.png 300w" sizes="(max-width: 503px) 100vw, 503px" />][5]

Wizard Actions – check both boxes. Click Next.

[<img class="aligncenter size-full wp-image-4917" src="/wp-content/uploads/2016/12/distribution-6.png" alt="distribution 6" width="506" height="453" srcset="/wp-content/uploads/2016/12/distribution-6.png 506w, /wp-content/uploads/2016/12/distribution-6-300x268.png 300w" sizes="(max-width: 506px) 100vw, 506px" />][6]

Script file properties – choose a location to save the script. Review other options. Click Next.

[<img class="aligncenter size-full wp-image-4918" src="/wp-content/uploads/2016/12/distribution-7.png" alt="distribution 7" width="501" height="453" srcset="/wp-content/uploads/2016/12/distribution-7.png 501w, /wp-content/uploads/2016/12/distribution-7-300x271.png 300w" sizes="(max-width: 501px) 100vw, 501px" />][7]

Complete the wizard – click Finish. All options should have green checkmarks.

[<img class="aligncenter size-full wp-image-4919" src="/wp-content/uploads/2016/12/distribution-8.png" alt="distribution 8" width="500" height="453" srcset="/wp-content/uploads/2016/12/distribution-8.png 500w, /wp-content/uploads/2016/12/distribution-8-300x271.png 300w" sizes="(max-width: 500px) 100vw, 500px" />][8]

&nbsp;

Your remote distributor is now configured. You'll need the server name, database name, and admin password as you set up publishers and subscribers. Next, you'll configure your publishers.

 [1]: /wp-content/uploads/2016/12/distribution-1.png
 [2]: /wp-content/uploads/2016/12/distribution-2.png
 [3]: /wp-content/uploads/2016/12/distribution-3.png
 [4]: /wp-content/uploads/2016/12/distribution-4.png
 [5]: /wp-content/uploads/2016/12/distribution-5.png
 [6]: /wp-content/uploads/2016/12/distribution-6.png
 [7]: /wp-content/uploads/2016/12/distribution-7.png
 [8]: /wp-content/uploads/2016/12/distribution-8.png