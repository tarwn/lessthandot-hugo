---
title: A Look Inside SQL Server Row and Page Compression
author: Jes Borland
type: post
date: 2012-12-19T18:56:00+00:00
ID: 1868
excerpt: Have you ever been curious about how row and page compression in SQL Server works?
url: /index.php/datamgmt/dbprogramming/how-sql-server-data-compression/
views:
  - 36087
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Take a look at the books in your office. Think about how much space they take up. Now pick one up and page through it. Is there a lot of white space? Some pages may only have a couple paragraphs on them. There are a lot of words and letters repeated. If there was a way to compress the book, it would be much lighter to store and carry around, wouldn't it?

The same principle applies to your SQL Server databases. Pages have free space on them. They also have bits of data that are repeated. A great feature of SQL Server is data compression, which will reduce the size of a database on disk, making storage and administration easier.

SQL Server 2008 (and newer versions) **Enterprise Edition** includes two types of data compression: row and page. The following demos were done in SQL Server 2012 Enterprise Edition in order to show the value data compression has and efficiency gains that can be had by utilizing this enterprise-level feature.' The demos will also show key performance factors to be aware of that may, or may not, be effected by data compression.

### Row Compression

Row compression reduces the amount of space rows take up on a page in several ways:

  * Metadata overhead is reduced. 
  * It uses the least amount of storage possible for some data types. Some numeric data types are reduced in size, and some character data types remove padding. For a full list of which data types can be compressed, and the method used, read [Row Compression Implementation][1]. 
  * NULL and 0 values take up no space on a page. 

Let's see this in action. I'll walk you through creating and populating a table, checking the size of the table, and viewing the size of the rows with DBCC PAGE. Then, I'll enable row compression, rebuild the table, and revisit the page to see what's changed.

First, I'll create a table with several data types INT, CHAR(50), DATETIME, MONEY, and DECIMAL, to show the effects of compression on each.

```sql
USE AdventureWorks2012; 
GO  
CREATE TABLE TestRowCompression 
( ItemID INT , 
  ItemName CHAR(50) , 
  DateAdded DATETIME , 
  UnitPrice MONEY , 
  ItemLength DECIMAL , 
  ItemWidth DECIMAL  ) 
WITH ( DATA_COMPRESSION = NONE);
```

Then, I'll insert data into the table. I'm using a variety of values, including 0 and NULL, to show how that is compressed.

```sql
INSERT INTO TestRowCompression 
VALUES ( 100000, 'Small Red Lego Brick', '2012/12/4', '0.75', 0, 0 ); 
INSERT INTO TestRowCompression 
VALUES ( 100001, 'Medium Blue Lego Brick', '2012/12/04', NULL, 1.5, .5 ); 
INSERT INTO TestRowCompression  
VALUES ( 12, 'Green Plate', '2012/12/01 08:34:56', '500.00', 12, 12 );
```

Let's see how the data appears when selected in a normal query.

```sql
SELECT ItemID , 
 ItemName ,  
 DateAdded , 
 UnitPrice , 
 ItemLength , 
 ItemWidth 
FROM TestRowCompression;
```

<img src="/wp-content/uploads/users/grrlgeek/compression%201.JPG?mtime=1355883902" alt="" width="562" height="112" />

What I want to know now is how much space these rows are taking up in the database, and on the page. I can use [sp_spaceused][2] to find the reserved and used disk space of the table.

```sql
sp_spaceused 'dbo.TestRowCompression';
```

<img src="/wp-content/uploads/users/grrlgeek/compression%202.JPG?mtime=1355883902" alt="" width="422" height="71" />

Here, I can see that there are three rows, and the data is taking up 8KB of space that's one page. How do I tell how much space each row is using? I can view the page, using DBCC PAGE, to find that information.

> _Note_: if you're not familiar with DBCC IND and DBCC PAGE, I recommend reading [Using DBCC PAGE and DBCC IND to find out if page splits ever roll back][3] by Paul Randal.

I'll use DBCC IND to find the file number and page number.

```sql
DBCC IND ('AdventureWorks2012', 'TestRowCompression', 0);
```

<img src="/wp-content/uploads/users/grrlgeek/compression%203.JPG?mtime=1355883902" alt="" width="768" height="62" />

I'll run DBCC PAGE, using the data page – PageType 1, PagePID 23816 – to view the data on the page.

```sql
DBCC TRACEON (3604); 
GO 
DBCC PAGE ('AdventureWorks2012', 1, 23816, 3); 
GO
```

I'm going to skim past the header and go to the information for the first row. Here, in the top right, I can see the entire row uses 95 bytes. You can see the bytes used by each field by reviewing 'Length (physical)'. The ItemID field is an INT, which takes 4 bytes for storage. The ItemName field is declared as CHAR(50), and even though it is only 20 characters in length, it's using 50 bytes. ItemLength and ItemWidth take up 9 bytes each, even though they are values of 0. This is the way data is normally stored on pages.

<img src="/wp-content/uploads/users/grrlgeek/compression%204.JPG?mtime=1355883902" alt="" width="610" height="518" />

Reviewing the data for the third record, we can see that also takes up 95 bytes, even though the values for ItemID and ItemName are shorter in length.

<img src="/wp-content/uploads/users/grrlgeek/compression%205.JPG?mtime=1355883902" alt="" width="618" height="485" />

If I am looking to reduce the space used by data in my database, I could enable row compression to do so. Before you start rebuilding all your tables in a development environment, use the stored procedure [sp\_estimate\_data\_compression\_savings][4] to determine what level of compression can be achieved. This will help you determine how much space can be saved, and if it is worth making the change.

```sql
EXEC sp_estimate_data_compression_savings 'dbo', 'TestRowCompression', NULL, NULL, 'ROW';
```

<img src="/wp-content/uploads/users/grrlgeek/compression%206.JPG?mtime=1355884192" alt="" width="950" height="36" />

Compressing this table would offer very little benefit, since at this time there are only three rows. However, I decide I want to enable row compression, to manage future data growth.

> _Note_: this will rebuild the table you are working with. Depending on the size of your table, this may take some time. Always test this in a development environment first.

```sql
ALTER TABLE dbo.TestRowCompression REBUILD 
WITH (DATA_COMPRESSION = ROW);
```

I'll run sp_spaceused again to compare the space used for the table.

```sql
sp_spaceused 'dbo.TestRowCompression';
```

<img src="/wp-content/uploads/users/grrlgeek/compression%207.JPG?mtime=1355884192" alt="" width="407" height="44" />

The data size has remained the same, but the unused size has increased. But, what this doesn't tell me is how much space is SQL Server using for each field in each row? What effect has row compression had on the rows? Let's view the page again.

I will run DBCC IND again to get the page number. Then, I run DBCC PAGE to view the results. 

```sql
DBCC IND ('AdventureWorks2012', 'TestRowCompression', 0);
DBCC TRACEON (3604);
GO
DBCC PAGE ('AdventureWorks2012', 1, 183656, 3);
GO
```

I can see things have changed drastically! The overall row size is reduced to 42 bytes. ItemID is only using 3 bytes. ItemName is now using 20 bytes. DateAdded has been reduced from 8 to 7 bytes. UnitPrice has decreased from 8 to 2 bytes. ItemLength and ItemWidth are using 0 bytes.

<img src="/wp-content/uploads/users/grrlgeek/compression%208.JPG?mtime=1355884192" alt="" width="643" height="507" />

Looking at the third row, I can see even less space is being used ' 36 bytes. Here, the INT field, ItemID, is reduced to 1 byte, and ItemName is using only 11 bytes.

<img src="/wp-content/uploads/users/grrlgeek/compression%209.JPG?mtime=1355884192" alt="" width="630" height="484" />

This proves that row compression can be a powerful tool for reducing the space taken up by data on pages! Here are a few examples, ranging from Person.Person barely compressing, to dbo.bigProduct showing over 50% compression.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/compression%2015.JPG?mtime=1355884466" alt="" width="637" height="490" />
</p>

 ****

 ****

### Page Compression

The next level of data compression is page compression. The leaf level of tables and indexes will have three operations performed on it, in order:

  * Row compression 
  * Prefix compression 
  * Dictionary compression 

We've already covered row compression. How do prefix and dictionary work?

#### Prefix Compression

This type will look at the data stored in each column. It will find a pattern in the beginning ' the prefix ' of each value, store that in a section of the page called the compression information structure (CIS), and reference the prefix in the row, instead of the entire value.

Let's use names as an example, since that is something you could have with many repeating values.

<table border="1" cellspacing="0" cellpadding="0">
  <tr>
    <td colspan="3" width="638" valign="top">
      <p style="text-align: center;">
        Header
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        Jenna
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jen
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jennifer
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        Jennie
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jenny
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jeren
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        Jeri
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jessica
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jess
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        Jes
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Joseph
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jonathan
      </p>
    </td>
  </tr>
</table>

After prefix compression is applied, the prefixes are stored in the CIS and then referenced in the rows.

<table border="1" cellspacing="0" cellpadding="0">
  <tr>
    <td colspan="3" width="638" valign="top">
      <p style="text-align: center;">
        Header
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        Jen (<span style="color: #ff0000;"></span>)
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jes (<span style="color: #339966;">1</span>)
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jo (<span style="color: #3366ff;">2</span>)
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        <span style="color: #ff0000;"></span>na
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #ff0000;"></span>
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #ff0000;"></span>nifer
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        <span style="color: #ff0000;"></span>nie
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #ff0000;"></span>ny
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jeren
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        Jeri
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #339966;">1</span>sica
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #339966;">1</span>s
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        <span style="color: #339966;">1</span>
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #3366ff;">2</span>seph
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #3366ff;">2</span>nathan
      </p>
    </td>
  </tr>
</table>

#### Dictionary Compression

Dictionary compression takes this one step further and replaces repeating values on the page, regardless of whether they are at the beginning of the value, in the middle, or at the end.

<table border="1" cellspacing="0" cellpadding="0">
  <tr>
    <td colspan="3" width="638" valign="top">
      <p style="text-align: center;">
        Header
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        Jen (<span style="color: #ff0000;"></span>)
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jes (<span style="color: #339966;">1</span>)
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        Jo (<span style="color: #3366ff;">2</span>)
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        er (<span style="color: #00ff00;">3</span>)
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        ni (<span style="color: #cc99ff;">4</span>)
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        na (<span style="color: #ff00ff;">5</span>)
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        <span style="color: #ff0000;"></span><span style="color: #ff00ff;">5</span>
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #ff0000;"></span>
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #ff0000;"></span><span style="color: #cc99ff;">4</span>f<span style="color: #00ff00;">3</span>
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        <span style="color: #ff0000;"></span><span style="color: #cc99ff;">4</span>e
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #ff0000;"></span>ny
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        J<span style="color: #00ff00;">3</span>en
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        J<span style="color: #00ff00;">3</span>i
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #339966;">1</span>sica
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #339966;">1</span>s
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="213" valign="top">
      <p>
        <span style="color: #339966;">1</span>
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #3366ff;">2</span>seph
      </p>
    </td>
    
    <td width="213" valign="top">
      <p>
        <span style="color: #3366ff;">2</span><span style="color: #ff00ff;">5</span>than
      </p>
    </td>
  </tr>
</table>

To show the effects, let's copy the data from TestRowCompression into a new table.

```sql
SELECT ItemID , 
 ItemName , 
 DateAdded , 
 UnitPrice , 
 ItemLength , 
 ItemWidth 
INTO''' TestPageCompression 
FROM''' TestRowCompression;
```

I'll alter the table to use page compression and check the size.

```sql
ALTER TABLE dbo.TestPageCompression REBUILD 
WITH (DATA_COMPRESSION = PAGE);
sp_spaceused 'dbo.TestPageCompression';
```

<img src="/wp-content/uploads/users/grrlgeek/compression%2010.JPG?mtime=1355884192" alt="" width="416" height="68" />

Right now, this is no different from when I applied row compression. Because this is a small table, with few repeating values, this does not surprise me. I have another table in my database, [dbo.bigTransactionHistory][5], which is larger and may offer better results. Let's look.

```sql
sp_spaceused 'dbo.bigTransactionHistory';
```

<img src="/wp-content/uploads/users/grrlgeek/compression%2011.JPG?mtime=1355884637" alt="" width="504" height="74" />

We can see the table has approximately 1100 MB of data.

First, I will apply row compression and check the size.

```sql
ALTER TABLE dbo.bigTransactionHistory REBUILD 
WITH (DATA_COMPRESSION = ROW);
sp_spaceused 'dbo.bigTransactionHistory';
```

<img src="/wp-content/uploads/users/grrlgeek/compression%2012.JPG?mtime=1355884638" alt="" width="492" height="77" />

The space used by data is now down to 725 MB.

Now I will apply page compression to see if this can be reduced further.

```sql
ALTER TABLE dbo.bigTransactionHistory REBUILD 
WITH (DATA_COMPRESSION = PAGE);
sp_spaceused 'dbo.bigTransactionHistory';
```

<img src="/wp-content/uploads/users/grrlgeek/compression%2013.JPG?mtime=1355884638" alt="" width="493" height="70" />

It has indeed reduced the space used, down to 472 MB. This is roughly 58% space savings.

Using DBCC PAGE, I can view the CIS.

```sql
DBCC IND ('AdventureWorks2012', 'bigTransactionHistory', 0);
DBCC TRACEON (3604);
GO 
DBCC PAGE ('AdventureWorks2012', 1, 380976, 3); 
GO
```

The compression information is stored below the header and above the rows.

<img src="/wp-content/uploads/users/grrlgeek/compression%2014.JPG?mtime=1355884638" alt="" width="816" height="152" />

Page compression can also greatly reduce the space your database is using on disk. Here are a few examples of tables that have had page compression applied. In these examples, space savings range from 38% to 61%.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/compression%2016.JPG?mtime=1355884817" alt="" width="526" height="333" />
</p>

> _Question_:'if I have enabled row or page compression, can I take a backup of the database, which is on Enterprise Edition, and restore it to a server that is Standard Edition? No, that can't be done. Paul Randal demonstrates this in [Does my database contain Enterprise-only features?][6] Be aware of this, and the need to manage and be aware of your compression settings, if this could happen in your environment.

### There Has To Be A Negative, Right? 

Data compression sounds fantastic, and it is. But, as Robert Heinlein would say, [TANSTAAFL][7]. The price you pay for your data taking up less space on disk is higher CPU usage. When the data is written to the table, or indexes are rebuilt, additional CPU will be needed to compress it. If your system currently experiences CPU pressure, it's not a good idea to enable compression on it.

### Resources

SQL Server Technical Paper – [Data Compression: Strategy, Capacity Planning and Best Practices][8]

 [1]: http://msdn.microsoft.com/en-us/library/cc280576.aspx
 [2]: http://msdn.microsoft.com/en-us/library/ms188776.aspx
 [3]: http://www.sqlskills.com/blogs/paul/post/inside-the-storage-engine-using-dbcc-page-and-dbcc-ind-to-find-out-if-page-splits-ever-roll-back.aspx
 [4]: http://msdn.microsoft.com/en-us/library/cc280574.aspx
 [5]: http://sqlblog.com/blogs/adam_machanic/archive/2011/10/17/thinking-big-adventure.aspx
 [6]: http://www.sqlskills.com/blogs/paul/post/sql-server-2008-does-my-database-contain-enterprise-only-features.aspx
 [7]: http://en.wikipedia.org/wiki/There_ain't_no_such_thing_as_a_free_lunch
 [8]: http://msdn.microsoft.com/en-us/library/dd894051(v=sql.100).aspx