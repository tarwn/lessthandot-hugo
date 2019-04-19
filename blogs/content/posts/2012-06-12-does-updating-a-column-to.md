---
title: 'Does updating a column to itself get logged? #TSQL2sday 31'
author: Ted Krueger (onpnt)
type: post
date: 2012-06-12T12:04:00+00:00
ID: 1649
excerpt: |
  Does updating a column to itself get logged?
  T-SQL Tuesday #31, hosted by Aaron Nelson this month, asked the SQL community to write about logging.  Logging is an open door into many topics and could pertain to error logging, logging with monitoring, SQ&hellip;
url: /index.php/datamgmt/dbadmin/does-updating-a-column-to/
views:
  - 7738
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
[][1]

[<img style="float: left;" src="data:image/png;base64,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" alt="" />T-SQL Tuesday #31][1], hosted by Aaron Nelson this month, asked the SQL community to write about logging.  Logging is an open door into many topics and could pertain to error logging, logging with monitoring, SQL Server logs, feature logs, grocery store logs of how much you spent and how you inserted it into SQL Server to average out your spending for a month...you get the picture.  Originally, I had written about a different topic but recently, I was asked a question that belongs in the T-SQL Tuesday logging theme and I wanted to share it with everyone.

I was asked recently if an update that updates a column to the same value is sent to the transaction log, essentially causing a dirty page.  I don't usually answer a question with, “It Depends” but this one really does require that as an answer.  The question was really good too and I'd like to share it as it will show the value in what we will cover.  The question was, “In Merge Replication, will an update on a column being replicated, affect the change count of the article, replicate the change, as well as log the update in the transaction log?”

The one good thing about telling someone, It Depends, for an answer is you can always follow it up with the reasons why it really does.  Let's take a look at the reasons the answer to this question, do ghost records cause logging events, depends on the table that is being updated.

It Depends on...

The structure of a table being updated will make the difference.

  1. If the update changes the Primary Key – keep in mind composite keys
  2. If a table is a HEAP table
  3. If LOB columns are involved

The one thing I do want to point out on the specific question that was asked: even knowing what we will go over will show when a row is logged to the transaction log, the scenarios change slightly when Merge Replication is involved.  The reason for this is that Merge Replication relies on triggers that will insert rows into tables that allow replication to monitor and track changes.  Those inserts, updates and deletes performed for Merge Replication become logging events themselves.  We will not show those scenarios with replication involved because, going over the scenarios themselves will show if the replication events would be logged.  I know, that sounded odd but let me explain...

Before I go any further, I want to point out an article written by Paul White, “[The Impact of Non-Updating Updates][2]”.   Paul goes over these scenarios in much more detail than this blog will and is a resource that I've used the last few years to return the same answers as I will here.  Paul is one the most brilliant SQL Server MVPs I know.  When reading his articles, you'll not only gain answers but a wealth of knowledge into why those answers are uncovered.   Paul's article is where I referred the person that sparked this great conversation as well.

**Primary Key**

The first reason listed is referring to the existence of a primary key on the table making a difference in if an update, updates the same value that already exists, causes a transaction log entry.  The reason here may be quite clear.  The primary key is the structure of the data rows so it makes sense that any change, in any way, would require logging to ensure that structure truly has changed or not.

For the examples below, [Process Monitor][3] will be used to monitor the ldf file for activity and the undocumented function fn_dblog, will be reviewed to show the activity as it occurs in the transaction log.

Below, the example will run through showing updates referring to a primary key or clustered index that affects logging.

```sql
CREATE TABLE tblPrimaryKeyUpdate (
	ID1 INT
	,ID2 INT
	,COLVAL VARCHAR(10) PRIMARY KEY (
		ID1
		,ID2
		)
	)
Insert one row into the table.
INSERT INTO tblPrimaryKeyUpdate
SELECT 1
	,2
	,'Case 1'
```

We do not need any more data than the one row to complete the test.

Run a CHECKPOINT to ensure everything that was just logged to the transaction log is flushed.

```sql
CHECKPOINT
```


 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-143.png?mtime=1339509144"><img src="/wp-content/uploads/blogs/DataMgmt/-143.png?mtime=1339509144" alt="" width="624" height="47" /></a>
</div>

 

Review the transaction log contents using fn_dblog function.

```sql
SELECT * FROM fn_dblog(NULL, NULL)
```


 

The above results show the checkpoint and only in the transaction log currently.  Now that the transaction log is cleared of any previous changes, the table and row inserted into tblPrimaryKeyUpdate can be updated.  In this test, we will update the column, COLVAL to itself.

```sql
UPDATE tblPrimaryKeyUpdate 
SET COLVAL = COLVAL
```


From the results shown in process monitor

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-144.png?mtime=1339509144"><img src="/wp-content/uploads/blogs/DataMgmt/-144.png?mtime=1339509144" alt="" width="624" height="156" /></a>
</div>

 

We see that there was no activity on the ldf file from the update statement that was executed.   Rerun the statement to review the results from fn_dblog.  From the results, you'll see that the transaction log has not logged any new rows.  This is due to the update statement not altering either column that is part of the composite key.  Next, update one of the ID columns to the same value.

```sql
UPDATE tblPrimaryKeyUpdate
SET ID1 = ID1
```

Rerun fn_dblog again and review the results.

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-145.png?mtime=1339509144"><img src="/wp-content/uploads/blogs/DataMgmt/-145.png?mtime=1339509144" alt="" width="624" height="108" /></a>
</div>

 

As shown above, the results show the transaction log did in fact have rows inserted into it in order to track the changes to the column ID1.  We can see the transaction from the starting LOP\_BEGIN\_XACT through the LCX\_MARK\_AS\_GHOST, LCX\_CLUSTERED and LOP\_COMMIT\_XACT.

**HEAP Table**

The second scenario that is taken into account is, is the table a HEAP table?  Given the storage of a HEAP table, we could come to the same conclusion as the primary key scenario.  Given the changes and the need to retain the fact, the table structure may change; the rows need to be logged.

For example, create a HEAP table as shown below.

```sql
CREATE TABLE tblHEAPUpdate (
	ID1 INT
	,ID2 INT
	,COLVAL VARCHAR(10)
	)
```

Use the same insert and update statements from the examples working on tblPrimaryKeyUpdate by changing the table name.  Make sure a CHECKPOINT is executed again prior to the update statements.

```sql
CREATE TABLE tblHEAPUpdate (
	ID1 INT
	,ID2 INT
	,COLVAL VARCHAR(10)
	)

INSERT INTO tblHEAPUpdate
SELECT 1
	,2
	,'Case 1'

CHECKPOINT

UPDATE tblHEAPUpdate
SET COLVAL = COLVAL
```

Rerun the select on fn_dblog and review the results.

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-146.png?mtime=1339509144"><img src="/wp-content/uploads/blogs/DataMgmt/-146.png?mtime=1339509144" alt="" width="624" height="80" /></a>
</div>

 

Reviewing process monitor shows activity on the physical ldf file as well.  The results in process monitor also relate back to the update, updating 3 rows all together.

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-147.png?mtime=1339509144"><img src="/wp-content/uploads/blogs/DataMgmt/-147.png?mtime=1339509144" alt="" width="624" height="78" /></a>
</div>

 

Above, there is a different result based on the table being a HEAP.  The update was recorded even knowing it was updating the row and column with the same value.

**LOB**

The last scenario involves the existence of a LOB column and the update.  I am going to let you test that scenario and hope you read Paul's blog on this very topic and examples.  I don't want this short review of the scenarios to take away from something that went through the scenarios in much more depth.  The end result is, if a LOB column update exceeds the 8000 bytes or 4000 Unicode, the event is written to the transaction log.

**Summary**

Knowing what is being logged, how, and why can be extremely valuable.  Reviewing and monitoring these types of situations can help decisions to be made, like the one that prompted this discussion.  The information gathered can also be used in transaction log sizing efforts, decisions on when a transaction log backup should be run in a full recovery model, and an overall deeper understanding of how the transaction log is being utilized by SQL Server and the database.

 [1]: http://sqlvariant.com/2012/06/t-sql-tuesday-31-logging/
 [2]: http://sqlblog.com/blogs/paul_white/archive/2010/08/11/the_2D00_impact_2D00_of_2D00_update_2D00_statements_2D00_that_2D00_don_2D00_t_2D00_change_2D00_data.aspx
 [3]: http://technet.microsoft.com/en-us/sysinternals/bb896645.aspx