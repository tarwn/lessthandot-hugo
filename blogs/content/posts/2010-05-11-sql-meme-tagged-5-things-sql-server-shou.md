---
title: 'SQL Meme: Tagged: 5 things SQL Server should drop'
author: SQLDenis
type: post
date: 2010-05-11T13:42:56+00:00
url: /index.php/datamgmt/datadesign/sql-meme-tagged-5-things-sql-server-shou/
views:
  - 8781
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - meme
  - sql server
  - tagged

---
I have been tagged by [Aaron Bertrand][1] in the latest SQL meme: [Tagged: 5 things SQL Server should drop][2]. This meme is about five thing that you wished that were dropped from SQL Server. Actually I prefer that SQL Server adds stuff instead of removes stuff, I can always ingore the things I don&#8217;t like. Anyhow since this is about things that should be removed, here is my list.

### Isnumeric

The isnumeric function is something that can bring you into trouble without you even knowing it, for example run these queries

<pre>select	ISNUMERIC(CHAR(9)),
		ISNUMERIC('1D2'),
		ISNUMERIC('.'),
		ISNUMERIC('-'),
		ISNUMERIC('+')</pre>

Really a tab (Char(9) is numeric? I would be much better if SQL Server had IsInteger, IsFloat,IsTinyInteger functions or perhaps the ability to pass in the the type to isnumeric
  
Something like this

<pre>SELECT ISNUMERIC('123', 'TinyInt')</pre>

### Restrictions on Indexed Views

Lookup indexed views and you will see more restrictions than you can fathom, you can&#8217;t memorize these things either, there are too many. First you need a bunch of setting

<pre>SET NUMERIC_ROUNDABORT OFF;
SET ANSI_PADDING, ANSI_WARNINGS, CONCAT_NULL_YIELDS_NULL, ARITHABORT,
    QUOTED_IDENTIFIER, ANSI_NULLS ON;</pre>

Why not replace that with

<pre>SET CREATE_INDEXED_VIEW_SETTINGS ON</pre>

This would be similar to ANSI_DEFAULTS, that sets 7 other settings to ON or OFF

After you are done with all these settings you have a whole laundry list of restrictions. Some of these restriction seems a little restricting to me, I have heard that other RDBMS vendors don&#8217;t have some of these restrictions in their materialized views.

### Ordered views

Take away the ability to create an ordered view since it is ignored anyway.
  
So for example if you create this view, which was working nicely in SQL Server 2000

<pre>CREATE VIEW vTestSort
AS
SELECT TOP 100 PERCENT id FROM TestSort
ORDER BY id</pre>

This doesn&#8217;t work in 2005 or 2008

OF course you can do this and it will &#8216;work&#8217; and I wrote about it here: [Create a sorted view in SQL Server 2005 and SQL Server 2008][3]

<pre>CREATE VIEW vTestSort2
AS
SELECT TOP 99.99 PERCENT  id FROM TestSort
ORDER BY id</pre>

I say get rid of that, if people want the view to be in a specific order then specify ORDER BY&#8230;how lazy can you be?

### Unique constraints with one NULL value

This is a great interview question and that is the only usefulness of this constraint. Either allow multiple NULL values or disallow NULL values.
  
Of course you can now use a Filtered Index in 2008 to accomplish this

<pre>create unique nonclustered index IX_LookMaMultipleNullValues on dbo.SomeTable(SomeColumn)
where SomeColumn is not null;</pre>

But not everyone is on 2008

### Functions that are very similar

Aaron already touched upon this with timestamp and rowversion in his post. SQL Server has a bunch of function that are very similar, I think we should retire some of those. Here is a list.

**ISNULL and COALESCE**
  
Those are not really the same, COALESCE allows for more than 1 expressions, preserves the datatype and is ANSI standard. So why do we need ISNULL? I know, I know..someone will post that ISNULL performs better&#8230;&#8230;..

**CURRENT_TIMESTAMP and GETDATE()**
  
Banish GETDATE() and use CURRENT\_TIMESTAMP instead, the only reason people (me included) use GETDATE() is because we are lazy and it is shorter. As a matter of fact use CURRENT\_TIMESTAMP when you create a table and then script the table out&#8230;what do you see? It will be GETDATE()

**User functions**

<pre>SELECT	SYSTEM_USER,
		SUSER_SNAME(), 
		USER,
		USER_NAME(), 
		CURRENT_USER, 
		SESSION_USER
		</pre>

When I tell you to capture a user which of these six will you use?

That is it for me, I am tagging the following people
  
Ted Krueger [Blog][4] / twitter: http://twitter.com/onpnt
  
Michelle Ufford [Blog][5] / twitter: http://twitter.com/sqlfool
  
Denny Cherry [Blog][6] / twitter: http://twitter.com/mrdenny
  
Adam Haines [Blog][7] / twitter: http://twitter.com/Adam_Haines

 [1]: http://twitter.com/AaronBertrand
 [2]: http://sqlblog.com/blogs/aaron_bertrand/archive/2010/05/11/tagged-5-things-sql-server-should-drop.aspx
 [3]: /index.php/DataMgmt/DataDesign/create-a-sorted-view-in-sql-server-2005--2008
 [4]: /index.php/All/?disp=authdir&author=68
 [5]: http://sqlfool.com/
 [6]: http://itknowledgeexchange.techtarget.com/sql-server/
 [7]: http://jahaines.blogspot.com/