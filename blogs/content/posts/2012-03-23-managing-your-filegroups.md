---
title: Managing your filegroups
author: David Forck (thirster42)
type: post
date: 2012-03-23T12:29:00+00:00
ID: 1571
excerpt: |
  It’s 4 PM; do you know what’s in your filegroups are?
  
  I found myself having this inner monologue the other day after I pushed a database form dev to test.  On the dev server I had split the database into two filegroups, one to store the data for the&hellip;
url: /index.php/datamgmt/datadesign/managing-your-filegroups/
views:
  - 5774
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
It’s 4 PM; do you know what's in your filegroups?

I found myself having this inner monologue the other day after I pushed a database form dev to test. On the dev server I had split the database into two filegroups, one to store the data for the staging tables, and one to store the data for the end results. The files essentially looked like this:

![Database Files][1]

I realized that I hadn’t generated the file groups or extra files on the second server. I created them and then used this query to find out where the tables and indexes were:

sql
select distinct
	s.name as SchemaName,
	t.name as TableName,
	i.name as IndexName,
	fg.name as FileGroupName
from sys.tables t
	inner join sys.indexes i
		on t.object_id=i.object_id
	inner join sys.schemas s
		on t.schema_id=s.schema_id
	inner join sys.filegroups fg
		on i.data_space_id=fg.data_space_id
order by s.name, t.name, fg.name
```

In SSMS, as long as you don’t have a lot of data in the table, the tables (aka the clustered indexes) are easy to move. Just right click- design on the table and change the filegroup (and text/image filegroup) to secondary and save. 

![Properties][2]

Behind the scenes SSMS will create a new table with the same structure on the filegroup, copy the data, drop the old table and rename the new table to the appropriate name. Now do you see why it matters how much data you have? If you’ve got a lot of data then you need to drop the Clustered index, move it, and then recreate the Clustered Index.

sql
ALTER TABLE dbo.Blah DROP CONSTRAINT PK_Blah WITH (MOVE TO Secondary)
GO
ALTER TABLE dbo.Blah ADD CONSTRAINT PK_Blah PRIMARY KEY(blah1)
GO
```

Now you can rerun the query to see where all the indexes are. If you have any non-clustered indexes you’ll notice that they didn’t get moved to the secondary filegroup when you moved the table. GRR! However, this could be a good thing if you want to seperate out the reeds for your non-clustered and clustered indexes. For my purposes I chose to keep them on the same filegroup.

To move the stragglers using SSMS right click-properties on the index, go to storage, change the filegroup, and click ok. This should move the index.

![Index Properties][3]

Behind the scenes SQL Server will just run a create statement, utilizing the DROP_EXISTING command to delete the index if it already exists:

sql
CREATE NONCLUSTERED INDEX [IDX_Blah] ON [dbo].[Blah] 
(
	[ColA] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, SORT_IN_TEMPDB = OFF, IGNORE_DUP_KEY = OFF, DROP_EXISTING = ON, ONLINE = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON, FILLFACTOR = 95) ON [Secondary]
```

So now you should be able to figure out what filegroups all of your objects are on and be able to move them. For further reading on filegroups read [SQL Server Filegroups: The What, The Why and The How
  
][4]

 [1]: /wp-content/uploads/blogs/DataMgmt/thirster42/FG1.png "Database Files"
 [2]: /wp-content/uploads/blogs/DataMgmt/thirster42/FG2.png "Properties"
 [3]: /wp-content/uploads/blogs/DataMgmt/thirster42/FG3.png "Index Properties"
 [4]: /index.php/DataMgmt/DBAdmin/sql-server-filegroups-the-what