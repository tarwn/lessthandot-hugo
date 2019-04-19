---
title: Hex Editoring
author: Jes Borland
type: post
date: 2012-10-30T13:22:00+00:00
ID: 1774
excerpt: It's time to try something new! I was recently challenged to show something in database page with a hex editor for another blog post. I thought, “Hex what? Are we casting spells?” Let's start with, “What is a hex editor?”
url: /index.php/datamgmt/dbprogramming/hex-editoring/
views:
  - 9488
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
It's time to try something new! I was recently challenged to show something in database page with a hex editor for another blog post. I thought, “Hex what? Are we casting spells?”

Let's start with, “What is a hex editor?” It's a program that lets you view the binary of a computer file – the 0's and 1's that it's made of. There are many available.

I started with my coworker Kendra Little's ([blog][1] | [twitter][2]) blog[, Corrupting Databases for Dummies- Hex Editor Edition][3]. I downloaded the hex editor [XVI32][4], as recommended. She gives instructions on how to install the software, create a database, create some objects, and then open the hex editor to break a page.

<p style="text-align: center;">
  <a href="http://www.flickr.com/photos/12739382@N04/3607882912/lightbox/"><img src="/wp-content/uploads/users/grrlgeek/skull crossbones.jpg?mtime=1351608410" alt="" /></a>
</p>

<p style="text-align: center;">
  <em><strong>Note</strong>: this should not be done in, near, or even in the same state as a production database.</em>
</p>

Beyond the basics of her blog post, here are the steps I went through to view a database page, break it, and fix it.

### **Breaking the Page** 

I issue DBCC IND to view a list of pages that belong to a table or index. The syntax for the command is:

```sql
DBCC IND ('DBName', 'TableName', <indexnumber>);
```

I run the command:

```sql
DBCC IND ('CorruptMe', 'DeadBirdies', 2);
```

Make note of a file and page number to work with. I issue a DBCC PAGE command to view page 1116. The syntax for the command is:

```sql
DBCC PAGE( {'dbname' | dbid}, filenum, pagenum [, printopt={0|1|2|3} ]);
```

I run the command:

```sql
DBCC PAGE('CorruptMe', 1, 1116, 3);
```

Following the steps in Kendra's blog, I take the database offline, copy the physical file location, and figure out my page offset. I'll need that information going forward.

### **The Hex Editor**

Next, I open XVI32 and open the file. Well, that's kind of fun-looking!

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Hex 1.JPG?mtime=1351608565" alt="" />
</p>

Quite frankly, I have no idea what the heck I'm looking at.

To find the page I had selected, I go to Address > Go To and enter my offset value. I'm now seeing the page on the right side. I can tell this because I can see the word “Tweetie” – the data I inserted in the table.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Hex 2.JPG?mtime=1351608653" alt="" />
</p>

I click on the first T and type X. Apparently I've just changed the data in my data file. Oops! I've corrupted a data file!

I save the file and go back to SSMS. I bring the database online. That works without a problem. I run a query to view data in that table, which should access the nonclustered index I mangled.

```sql
USE CorruptMe;
GO
SELECT birdName
FROM dbo.DeadBirdies;
```

I get a terrible error: “SQL Server detected a logical consistency-based I/O error: incorrect checksum”.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Hex 3.JPG?mtime=1351608719" alt="" />
</p>

What happens if I run a DBCC CHECKDB? More errors!

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Hex 4.JPG?mtime=1351608783" alt="" />
</p>

Yes, I broke a database. On purpose.

But this is good! It gives me the opportunity to play with a hex editor, see what corruption errors look like, and fix them.

Fix them, you say? Yes, fix them!

### **Fixing the Corrupted File** 

This is an easy fix. A corrupted nonclustered index can be fixed by dropping and rebuilding.

First, I go to SSMS and expand the database, table, and indexes. I right-click the index and select Script Index As > Drop and Script Index As > Create To > New Query Editor window.

I run the query to drop the index:

```sql
DROP INDEX dbo.DeadBirdies.ncBirds;
```

When I run the query again, data is returned! This time, there is no error.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Hex%205.jpg?mtime=1351609676" alt="" />
</p>

Now, I rebuild the nonclustered index using the Create statement I scripted, and I can still run the query without errors.

```sql
CREATE NONCLUSTERED INDEX ncBirds ON dbo.DeadBirdies(BirdName);
```

**What I Learned** 

Someday, I may need to manually edit a database (or other) file. This is one of those tasks it's nice to know I've done in practice before I ever need to actually do it – I know what to look for and what to do. I won't need to panic at a critical moment.

Magical! Where is [@SQLUnicorn][5] when I need him?

 [1]: http://www.brentozar.com/archive/author/kendra-little/
 [2]: https://twitter.com/Kendra_Little
 [3]: http://www.littlekendra.com/2011/01/24/corrupthexeditor/
 [4]: http://www.chmaas.handshake.de/delphi/freeware/xvi32/xvi32.htm
 [5]: https://twitter.com/SQLUnicorn