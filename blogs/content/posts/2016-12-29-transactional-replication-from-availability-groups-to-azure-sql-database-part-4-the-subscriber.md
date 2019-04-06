---
title: 'Transactional Replication from Availability Groups to Azure SQL Database: Part 4 – The Subscriber'
author: Jes Borland
type: post
date: 2016-12-29T15:00:14+00:00
url: /index.php/uncategorized/transactional-replication-from-availability-groups-to-azure-sql-database-part-4-the-subscriber/
views:
  - 3944
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
This is part 4 of a 5-part series.

  * <a href="http://blogs.ltd.local/?p=4896" target="_blank">Part 1 &#8211; Planning</a>
  * <a href="http://blogs.ltd.local/?p=4906" target="_blank">Part 2 &#8211; The Distributor</a>
  * <a href="http://blogs.ltd.local/?p=4923" target="_blank">Part 3 &#8211; The Publisher</a>
  * <a href="http://blogs.ltd.local/?p=4945" target="_blank">Part 4 &#8211; The Subscriber</a>
  * <a href="http://blogs.ltd.local/?p=4960" target="_blank">Part 5 &#8211; Testing</a>

&nbsp;

### Scenario

Publishers: servers SQL2014AG1 and SQL2014AG2, database AGTest

Distributor: stand-alone server, SQL2014demo

Subscriber: Azure SQL Database &#8211; server jessqldb2, database ReplicationTest

### Setting up the replication subscription

This subscription is going to use an Azure SQL Database.

Go to the AG primary replica. (In this demo, this is SQL2014AG2.)

Expand Replication. Expand Local Publications. Right-click the publication and select New Subscription.

Publication – select the publication and click Next.

[<img class="aligncenter size-full wp-image-4947" src="/wp-content/uploads/2016/12/subscription-1.png" alt="subscription 1" width="573" height="460" srcset="/wp-content/uploads/2016/12/subscription-1.png 573w, /wp-content/uploads/2016/12/subscription-1-300x240.png 300w" sizes="(max-width: 573px) 100vw, 573px" />][1]

Distribution Agent Location – select Run all agents at the Distributor (push subscriptions). Click Next.

[<img class="aligncenter size-full wp-image-4948" src="/wp-content/uploads/2016/12/subscription-2.png" alt="subscription 2" width="576" height="456" srcset="/wp-content/uploads/2016/12/subscription-2.png 576w, /wp-content/uploads/2016/12/subscription-2-300x237.png 300w" sizes="(max-width: 576px) 100vw, 576px" />][2]

Subscribers – here we will add the SQL DB. Click Add Subscriber > Add SQL Server Subscriber.

[<img class="aligncenter size-full wp-image-4949" src="/wp-content/uploads/2016/12/subscription-3.png" alt="subscription 3" width="569" height="455" srcset="/wp-content/uploads/2016/12/subscription-3.png 569w, /wp-content/uploads/2016/12/subscription-3-300x239.png 300w" sizes="(max-width: 569px) 100vw, 569px" />][3]

Enter the Azure SQL Server name, the login, and the password. Click Options. Go to Connection Properties. Enter the database. Click Connect.

[<img class="aligncenter size-full wp-image-4950" src="/wp-content/uploads/2016/12/subscription-4.png" alt="subscription 4" width="493" height="525" srcset="/wp-content/uploads/2016/12/subscription-4.png 493w, /wp-content/uploads/2016/12/subscription-4-281x300.png 281w" sizes="(max-width: 493px) 100vw, 493px" />][4]

Make sure there is a checkmark next to the subscriber name. Use the drop-down to select a Subscription Database. Click Next.

[<img class="aligncenter size-full wp-image-4951" src="/wp-content/uploads/2016/12/subscription-5.png" alt="subscription 5" width="575" height="454" srcset="/wp-content/uploads/2016/12/subscription-5.png 575w, /wp-content/uploads/2016/12/subscription-5-300x236.png 300w" sizes="(max-width: 575px) 100vw, 575px" />][5]

Distribution Agent Security – click the ellipses on the right side.

[<img class="aligncenter size-full wp-image-4952" src="/wp-content/uploads/2016/12/subscription-6.png" alt="subscription 6" width="569" height="459" srcset="/wp-content/uploads/2016/12/subscription-6.png 569w, /wp-content/uploads/2016/12/subscription-6-300x242.png 300w" sizes="(max-width: 569px) 100vw, 569px" />][6][
  
][7] 

Distribution Agent Security – three pieces. First select an account to run the distribution agent process to sync the sub. Ideally, this is a specific domain user.

Second, select the account to connect to the distributor.

Third, select an account to connect to the subscriber. If this is a SQL DB, this **must** be a SQL login that is a member of the db_owner role in the database.

Click OK.

[<img class="aligncenter size-full wp-image-4953" src="/wp-content/uploads/2016/12/subscription-7.png" alt="subscription 7" width="553" height="677" srcset="/wp-content/uploads/2016/12/subscription-7.png 553w, /wp-content/uploads/2016/12/subscription-7-245x300.png 245w" sizes="(max-width: 553px) 100vw, 553px" />][7]

Click Next.

Synchronization Schedule – choose Run continuously. Click Next.

[<img class="aligncenter size-full wp-image-4954" src="/wp-content/uploads/2016/12/subscription-8.png" alt="subscription 8" width="572" height="453" srcset="/wp-content/uploads/2016/12/subscription-8.png 572w, /wp-content/uploads/2016/12/subscription-8-300x237.png 300w" sizes="(max-width: 572px) 100vw, 572px" />][8]

Initialize Subscriptions – make sure Initialize is checked, choose Immediately. Click Next.

[<img class="aligncenter size-full wp-image-4955" src="/wp-content/uploads/2016/12/subscription-9.png" alt="subscription 9" width="576" height="451" srcset="/wp-content/uploads/2016/12/subscription-9.png 576w, /wp-content/uploads/2016/12/subscription-9-300x234.png 300w" sizes="(max-width: 576px) 100vw, 576px" />][9]

Wizard Actions – select both options. Click Next.

[<img class="aligncenter size-full wp-image-4956" src="/wp-content/uploads/2016/12/subscription-10.png" alt="subscription 10" width="573" height="454" srcset="/wp-content/uploads/2016/12/subscription-10.png 573w, /wp-content/uploads/2016/12/subscription-10-300x237.png 300w" sizes="(max-width: 573px) 100vw, 573px" />][10]

Script File Properties – give the file a name. Review the other options. Click Next.

[<img class="aligncenter size-full wp-image-4957" src="/wp-content/uploads/2016/12/subscription-11.png" alt="subscription 11" width="579" height="451" srcset="/wp-content/uploads/2016/12/subscription-11.png 579w, /wp-content/uploads/2016/12/subscription-11-300x233.png 300w" sizes="(max-width: 579px) 100vw, 579px" />][11]

Click Finish. All steps should have a green checkmark next to them.

[<img class="aligncenter size-full wp-image-4958" src="/wp-content/uploads/2016/12/subscription-12.png" alt="subscription 12" width="573" height="456" srcset="/wp-content/uploads/2016/12/subscription-12.png 573w, /wp-content/uploads/2016/12/subscription-12-300x238.png 300w" sizes="(max-width: 573px) 100vw, 573px" />][12]

To verify it&#8217;s working, expand Replication > Local Publications > Publication Name, and you should see your subscription. Right-click and select View Synchronization Status to confirm it&#8217;s applying the snapshot.

Your replication subscriber is now set up. The next step is to verify that all the pieces work by testing both replication and AG failover.

 [1]: /wp-content/uploads/2016/12/subscription-1.png
 [2]: /wp-content/uploads/2016/12/subscription-2.png
 [3]: /wp-content/uploads/2016/12/subscription-3.png
 [4]: /wp-content/uploads/2016/12/subscription-4.png
 [5]: /wp-content/uploads/2016/12/subscription-5.png
 [6]: /wp-content/uploads/2016/12/subscription-6.png
 [7]: /wp-content/uploads/2016/12/subscription-7.png
 [8]: /wp-content/uploads/2016/12/subscription-8.png
 [9]: /wp-content/uploads/2016/12/subscription-9.png
 [10]: /wp-content/uploads/2016/12/subscription-10.png
 [11]: /wp-content/uploads/2016/12/subscription-11.png
 [12]: /wp-content/uploads/2016/12/subscription-12.png