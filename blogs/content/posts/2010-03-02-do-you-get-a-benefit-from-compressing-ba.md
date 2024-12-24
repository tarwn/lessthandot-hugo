---
title: Do You Get A Benefit From Compressing Backups If You Already Have Compressed Data?
author: SQLDenis
type: post
date: 2010-03-02T16:07:27+00:00
ID: 709
url: /index.php/datamgmt/datadesign/do-you-get-a-benefit-from-compressing-ba/
views:
  - 26900
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - backups
  - compression
  - restores

---
Do you benefit from compressed backups if your data is compressed already? This was a question that was asked recently; I thought that the question was interesting and decided to do some testing.

Here is what we will do; we will create two databases with only one table. In one database we will use page level compression and in the other database we won't use compression. After that we will backup each database twice, once with backup compression and once without backup compression. After that, we will do restore from these backups. After doing all these, we will find out if data compression makes backup compression faster. We will also find out if using backup compression on a database that uses data compression will result in a smaller backup file. At the bottom of this post you will see a table that has all these numbers in one spot.

# Setup scripts

Let's get started, first we will create the database that will not use compression

```sql
USE [master]
GO

CREATE DATABASE [TestUncompressed] ON  PRIMARY 
( NAME = N'TestUncompressed_Data', FILENAME = N'C:DbTestTestUncompressed_Data.mdf'   )
 LOG ON 
( NAME = N'TestUncompressed_Log', FILENAME = N'C:DbTestTestUncompressed_Log.ldf'  )
GO

ALTER DATABASE [TestUncompressed] SET RECOVERY  SIMPLE
GO
```

Now we will create the database that will use data compression

```sql
USE [master]
GO
CREATE DATABASE [TestCompressed] ON  PRIMARY 
( NAME = N'TestCompressed_Data', FILENAME = N'C:DbTestTestCompressed_Data.mdf'   )
 LOG ON 
( NAME = N'TestCompressed_Log', FILENAME = N'C:DbTestTestCompressed_Log.ldf'  )
GO

ALTER DATABASE [TestCompressed] SET RECOVERY  SIMPLE
GO
```

The following block of code will create a table with 3 million rows in the database that will not use compression

```sql
USE [TestUncompressed]
GO


CREATE TABLE Test (SomeCol INT NOT NULL,
					SomeDate DATETIME NOT NULL,
					SomeChar CHAR(30) NOT NULL,
					SomeGuid UNIQUEIDENTIFIER NOT NULL)
					
					
GO

CREATE CLUSTERED INDEX ci_test ON Test(SomeCol, SomeDate, SomeChar)
GO

INSERT Test (SomeCol, SomeDate, SomeChar, SomeGuid)
SELECT x.Number, 
	CONVERT(DATE,DATEADD(mi,Number,'20100101')),
	DATENAME(MONTH,(CONVERT(DATE,DATEADD(hh,Number,'20100101')))) + '2010',
	NEWID()
	
	 FROM (					
SELECT TOP 3000000 ROW_NUMBER() OVER (ORDER BY s.id) AS Number
 FROM master..sysobjects s 
CROSS JOIN master..sysobjects s2) x
```

The following block of code will create a table with 3 million rows in the database that will use data compression

```sql
USE [TestCompressed]
GO


CREATE TABLE Test (SomeCol INT NOT NULL,
					SomeDate DATETIME NOT NULL,
					SomeChar CHAR(30) NOT NULL,
					SomeGuid UNIQUEIDENTIFIER NOT NULL)
GO
					
CREATE CLUSTERED INDEX ci_test ON Test(SomeCol, SomeDate, SomeChar)
WITH (DATA_COMPRESSION = PAGE)
GO
					
					

INSERT Test (SomeCol, SomeDate, SomeChar, SomeGuid)
SELECT * FROM [TestUncompressed]..Test
```

# Backups

Now it is time to do the backups, we will do 4 backups. Each database will be backed up compressed and uncompressed. Here is the code that will do that. I use the _C:DbTest_ folder, if you want to run this, code make sure that you create that folder.

```sql
BACKUP DATABASE TestCompressed TO DISK='C:DbTestTestCompressedBackupCompressed.bak' 
WITH  COMPRESSION
GO


BACKUP DATABASE TestCompressed TO DISK='C:DbTestTestCompressedBackupUnCompressed.bak' 
GO


BACKUP DATABASE TestUncompressed TO DISK='C:DbTestTestUncompressedBackupCompressed.bak' 
WITH  COMPRESSION
GO


BACKUP DATABASE TestUncompressed TO DISK='C:DbTestTestUncompressedBackupUnCompressed.bak' 
GO
```

Here are the times that each backup took.
  
<span class="MT_smaller"><em>Processed 11640 pages FOR DATABASE 'TestCompressed', FILE 'TestCompressed_Data' ON FILE 1.<br /> Processed 2 pages FOR DATABASE 'TestCompressed', FILE 'TestCompressed_Log' ON FILE 1.<br /> <strong>BACKUP DATABASE successfully processed 11642 pages IN 5.483 seconds (16.587 MB/sec).</strong></p> 

<p>
  Processed 11640 pages FOR DATABASE 'TestCompressed', FILE 'TestCompressed_Data' ON FILE 1.<br /> Processed 1 pages FOR DATABASE 'TestCompressed', FILE 'TestCompressed_Log' ON FILE 1.<br /> <strong>BACKUP DATABASE successfully processed 11641 pages IN 6.461 seconds (14.076 MB/sec).</strong>
</p>

<p>
  Processed 25496 pages FOR DATABASE 'TestUncompressed', FILE 'TestUncompressed_Data' ON FILE 1.<br /> Processed 2 pages FOR DATABASE 'TestUncompressed', FILE 'TestUncompressed_Log' ON FILE 1.<br /> <strong>BACKUP DATABASE successfully processed 25498 pages IN 9.167 seconds (21.729 MB/sec).</strong>
</p>

<p>
  Processed 25496 pages FOR DATABASE 'TestUncompressed', FILE 'TestUncompressed_Data' ON FILE 1.<br /> Processed 1 pages FOR DATABASE 'TestUncompressed', FILE 'TestUncompressed_Log' ON FILE 1.<br /> <strong>BACKUP DATABASE successfully processed 25497 pages IN 14.243 seconds (13.985 MB/sec).</strong><br /> </em></span>
</p>

<p>
  Here is also a chart showing you the backup times in seconds.<br /> <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//BackupChart4.PNG" alt="" title="" width="554" height="409" /><br /> <em>1 = TestCompressedBackupCompressed.bak<br /> 2 = TestCompressedBackupUnCompressed.bak<br /> 3 = TestUncompressedBackupCompressed.bak<br /> 4 = TestUncompressedBackupUnCompressed.bak</em>
</p>

<p>
  Below is an image showing the sizes of the backups. As you can see, the compressed backup is only a third of the size of the uncompressed backup from the database that had the uncompressed table. When using a compressed backup against a table that was already using data compression the size of the backup drops from 90MB to 60MB. in this case the size is 66% of the uncompressed size.
</p>

<p>
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//CompressedSizes.PNG" alt="" title="" width="445" height="152" />
</p>

<p>
  <h1>
    Restores
  </h1>
  
  <p>
    Now let's do the restores.
  </p>
  
  <pre lang="tsql">USE master 
GO

RESTORE DATABASE [TestCompressed] 
FROM  DISK = N'C:DbTestTestCompressedBackupCompressed.bak' 
WITH  FILE = 1,  NOUNLOAD,  REPLACE,  STATS = 10
GO

--RESTORE DATABASE successfully processed 11642 pages in 22.601 seconds (4.024 MB/sec).

RESTORE DATABASE [TestCompressed] 
FROM  DISK = N'C:DbTestTestCompressedBackupUnCompressed.bak' 
WITH  FILE = 1,  NOUNLOAD,  REPLACE,  STATS = 10
GO

--RESTORE DATABASE successfully processed 11641 pages in 23.593 seconds (3.854 MB/sec).


RESTORE DATABASE [TestUncompressed] 
FROM  DISK = N'C:DbTestTestUncompressedBackupCompressed.bak' 
WITH  FILE = 1,  NOUNLOAD,  REPLACE,  STATS = 10
GO
--RESTORE DATABASE successfully processed 25498 pages in 29.337 seconds (6.789 MB/sec)

RESTORE DATABASE [TestUncompressed] 
FROM  DISK = N'C:DbTestTestUncompressedBackupUnCompressed.bak' 
WITH  FILE = 1,  NOUNLOAD,  REPLACE,  STATS = 10
GO
--RESTORE DATABASE successfully processed 25497 pages in 32.358 seconds (6.155 MB/sec).</pre>
  
  <p>
    As you can see from the times in the comments in the code block above, the restores are faster when you use compression.
  </p>
  
  <p>
    Here is a chart with the restore times in seconds.
  </p>
  
  <p>
    <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//BackupChart3.PNG" alt="" title="" width="554" height="409" />
  </p>
  
  <p>
    <em>1 = TestCompressedBackupCompressed.bak<br /> 2 = TestCompressedBackupUnCompressed.bak<br /> 3 = TestUncompressedBackupCompressed.bak<br /> 4 = TestUncompressedBackupUnCompressed.bak</em>
  </p>
  
  <p>
    <h2>
      Conclusion
    </h2>
    
    <p>
      To finish up, here is a table that shows all restore and backup times for the 4 different backups
    </p>
    
    <div style="border:5px dotted red;">
      <table cellpadding="5" cellspacing="2">
        <tr>
          <td>
            <strong>Name&nbsp;</strong>
          </td>
          
          <td>
            <strong>Backup time&nbsp;</strong>
          </td>
          
          <td>
            <strong>Backup MB/sec&nbsp;</strong>
          </td>
          
          <td>
            <strong>Restore time</strong>
          </td>
          
          <td>
            <strong>Restore MB/sec&nbsp;</strong>
          </td>
          
          <td>
            <strong>File size&nbsp;</strong>
          </td>
        </tr>
        
        <tr>
          <td>
            <strong>TestCompressedBackupCompressed</strong>
          </td>
          
          <td>
            5.483
          </td>
          
          <td>
            16.587 MB/sec
          </td>
          
          <td>
            22.601
          </td>
          
          <td>
            4.024 MB/sec
          </td>
          
          <td>
            65,646 kb
          </td>
        </tr>
        
        <tr>
          <td>
            <strong>TestCompressedBackupUnCompressed</strong>
          </td>
          
          <td>
            6.461
          </td>
          
          <td>
            14.076 MB/sec
          </td>
          
          <td>
            23.593
          </td>
          
          <td>
            3.854 MB/sec
          </td>
          
          <td>
            93,484 kb
          </td>
        </tr>
        
        <tr>
          <td>
            <strong>TestUncompressedBackupCompressed</strong>
          </td>
          
          <td>
            9.167
          </td>
          
          <td>
            21.729 MB/sec
          </td>
          
          <td>
            29.337
          </td>
          
          <td>
            6.789 MB/sec
          </td>
          
          <td>
            65,679 kb
          </td>
        </tr>
        
        <tr>
          <td>
            <strong>TestUncompressedBackupUnCompressed</strong>
          </td>
          
          <td>
            14.243
          </td>
          
          <td>
            13.985 MB/sec
          </td>
          
          <td>
            32.358
          </td>
          
          <td>
            6.155 MB/sec
          </td>
          
          <td>
            205,108 kb
          </td>
        </tr>
      </table>
    </div>
    
    <p>
      As you can see compression is a great way to keep your backups small and it also makes your backup and restore process finish faster as well.
    </p>
    
    <p>
      *** <strong>Remember, if you have a SQL related question, try our <a href="http://forum.lessthandot.com/viewforum.php?f=17">Microsoft SQL Server Programming</a> forum or our <a href="http://forum.lessthandot.com/viewforum.php?f=22">Microsoft SQL Server Admin</a> forum</strong><ins></ins>
    </p>