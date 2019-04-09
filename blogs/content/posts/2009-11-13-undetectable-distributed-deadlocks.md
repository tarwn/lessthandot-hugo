---
title: Undetectable Distributed Deadlocks
author: Erik
type: post
date: 2009-11-13T22:59:13+00:00
ID: 629
url: /index.php/datamgmt/dbprogramming/undetectable-distributed-deadlocks/
views:
  - 19598
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
I believe I have discovered a deadlock situation that SQL Server is not able to detect, so a perpetual block occurs.

A deadlock is nothing more than mutual blocking. Blocking is when a process is forced to wait for a resource while another process exclusively accesses it (where the exclusivity is managed through locks). Mutual blocking is when both processes have a lock on a resource the other is asking for. Neither can proceed, but neither will release its lock.

Normally, when SQL server detects this kind of mutual block, it picks one process as the “victim” and kills it, rolling back its work. This also releases any locks it had and allows the other process to acquire its previously blocked request, resolving the deadlock.

But what if the two resources in question are on separate servers? For SQL Server to detect deadlocks, it has to have enough information to see that a pattern of blocks is mutual.

sql
--Query 1
UPDATE B
SET B.Name = A.Name
FROM
   TableA A
   INNER JOIN LinkedServer.DB1.dbo.TableB B ON A.ID = B.ID

--Query 2
UPDATE B
SET B.Name = A.Name
FROM
   LinkedServer.DB1.dbo.TableB B
   INNER JOIN TableA A ON B.ID = A.ID
```

Now, let's say that locks for these two queries are granted in this order: query 1 acquires a lock on TableA before Table B, but query 2 acquires a lock on TableB before TableA. Even though the example I'm giving here may not be the greatest, the possibility is not so unlikely, and I'm sure there are plenty of situations out in the wild where my suggested scenario is possible.

Locks are not granted all at once, nor can one assume that the final lock used for an update is granted first. Many times a less exclusive lock is acquired and then the lock's exclusiveness is increased or specificity is broadened, and that's all that's required to cause a deadlock. See <a href=http://www.sqlmag.com/article/articleid/95538/95538.html>Deadlocks with Custom Sequence</a> for one example of surprising deadlocks occurring because of an unusual transaction isolation level. 

Given the above hypothetical lock order, when each query's process requests a lock on the other table to perform its update, it will be blocked. But each server involved only has half the picture and can only see a simple block, without knowing it's mutual (because the other half is occurring on the other side of the linked server). So it is a perpetual mutual block and no deadlock is detected.

The next time I am working heavily with linked servers you can be sure I'll be thinking about this and coming up with a way to test if such a “distributed deadlock” really can occur, and if so, what to do about it.

In closing, I confess that it's possible that DTC could have provisions for sharing lock information, so perhaps a distributed deadlock will be properly detected. But I have my doubts about that.