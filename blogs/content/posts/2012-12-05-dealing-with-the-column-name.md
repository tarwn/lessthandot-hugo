---
title: Dealing with the Column name 'TEXT()' contains an invalid XML identifier as required by FOR XML; '('(0x0028) is the first character at fault error
author: SQLDenis
type: post
date: 2012-12-05T11:42:00+00:00
ID: 1829
excerpt: |
  I had to deploy a user defined function I was given yesterday, when I tried to I got the following error
  
  Msg 6850, Level 16, State 1, Procedure fnGetBooks, Line 8
  Column name 'TEXT()' contains an invalid XML identifier as required by FOR XML; '('(0x&hellip;
url: /index.php/webdev/business-intelligence/dealing-with-the-column-name/
views:
  - 10191
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
tags:
  - concatenate
  - how to
  - rows to column
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - t-sql
  - xml

---
I had to deploy a user defined function I was given yesterday, when I tried to I got the following error

Msg 6850, Level 16, State 1, Procedure fnGetBooks, Line 8
  
Column name 'TEXT()' contains an invalid XML identifier as required by FOR XML; '('(0x0028) is the first character at fault.

The function looked a little like this one

```sql
CREATE FUNCTION fnGetBooks (@AuthorID INT)
 
RETURNS VARCHAR(8000)
AS
BEGIN
       
 DECLARE @BookList VARCHAR(8000)
 SELECT @BookList =(
 
     SELECT  BookName + ', ' AS [TEXT()]
 
     FROM    Books
     WHERE AuthorID = @AuthorID
     AND BookName IS NOT NULL
     ORDER BY BookName
 
     FOR XML PATH('') )
       
 
RETURN LEFT(@BookList,(LEN(@BookList) -1))
END
GO
```

Trying to run that will give you the same error. Do you see the problem? First I determined what 0x0028 was, you can easy do this by running the following query

```sql
SELECT CHAR(0x0028)
```

As you can see, that is the left parentheses (
  
Interesting but not really helpful, I know I wrote stuff that uses FOR XML PATH myself in the past. I ran the following query to give me a list of objects that use FOR XML PATH

```sql
SELECT * 
FROM sys.objects
WHERE OBJECT_DEFINITION(object_id) LIKE '%FOR%%XML%%PATH%'
```

Then I looked at some of those functions, the only thing I noticed is that TEXT was lowercase

Instead of

```sql
SELECT  BookName + ', ' AS [TEXT()]
```

It was

```sql
SELECT  BookName + ', ' AS [text()]
```

Once you make the change, the error will disappear. My suspicion is that some code formatting tool made it uppercase, perhaps the programmer copied and pasted it into some window and then packaged the output...I will have to follow up on that one

If you are interested what that function is used for, take a look at [Concatenate Values From Multiple Rows Into One Column][1]

 [1]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#10