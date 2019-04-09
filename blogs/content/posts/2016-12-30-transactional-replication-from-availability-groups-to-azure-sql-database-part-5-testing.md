---
title: 'Transactional Replication from Availability Groups to Azure SQL Database: Part 5 – Testing'
author: Jes Borland
type: post
date: 2016-12-30T15:00:30+00:00
ID: 4960
url: /index.php/uncategorized/transactional-replication-from-availability-groups-to-azure-sql-database-part-5-testing/
views:
  - 3653
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
This is part 5 of a 5-part series.

  * <a href="/?p=4896" target="_blank">Part 1 &#8211; Planning</a>
  * <a href="/?p=4906" target="_blank">Part 2 &#8211; The Distributor</a>
  * <a href="/?p=4923" target="_blank">Part 3 &#8211; The Publisher</a>
  * <a href="/?p=4945" target="_blank">Part 4 &#8211; The Subscriber</a>
  * <a href="/?p=4960" target="_blank">Part 5 &#8211; Testing</a>

&nbsp;

### Scenario

Publishers: servers SQL2014AG1 and SQL2014AG2, database AGTest

Distributor: stand-alone server, SQL2014demo

Subscriber: Azure SQL Database &#8211; server jessqldb2, database ReplicationTest

### Time to test!

Congratulations, you've configured a remote distributor, configured all of your AG replicas as publishers, and configured your SQL Database as a subscriber! Now you want to ensure that transactions are replicating to the database, and that they continue to do so if there is a failover in the AG.

### Testing that replication works

This is easiest to do in a development database, where you can add data.

Connect to the AG primary, which is the publisher &#8211; run a query against a table and note results.

Connect to the subscriber, run the same query, and verify the results are the same.

Connect to the publisher. If you have the ability, enter a new value into a table. If not, find a table this is frequently updated, and note a new value that has been entered.

Go to the subscriber. Query for the new or changed value.

### Testing that on AG failover, replication continues to work

Perform a manual failover from your primary replica to a secondary replica.

Connect to the AG primary, which is the publisher &#8211; run a query against a table and note results.

Connect to the subscriber, run the same query, and verify the results are the same.

Connect to the publisher. If you have the ability, enter a new value into a table. If not, find a table this is frequently updated, and note a new value that has been entered.

Go to the subscriber. Query for the new or changed value.

### Troubleshooting

Troubleshooting replication is outside the scope of this blog series. If you find that things aren't working as expected, read the article <a href="https://www.simple-talk.com/sql/database-administration/monitoring-transactional-replication-in-sql-server/" target="_blank">Monitoring Transactional Replication in SQL Server</a>, and review the <a href="http://www.sqlservercentral.com/stairway/72401/" target="_blank">SQLServerCentral.com Stairway to Replication </a>article <a href="http://www.sqlservercentral.com/articles/Stairway+Series/72452/" target="_blank">Level 10: Troubleshooting</a>.

Troubleshooting Availability Groups is also outside the scope of this blog series. If you need help, start with <a href="https://msdn.microsoft.com/en-us/library/dn135328(v=sql.110).aspx" target="_blank">AlwaysOn Availability Groups Troubleshooting and Monitoring Guide</a>.