---
title: 'Does anyone use the  NTILE() windowing function?'
author: SQLDenis
type: post
date: 2013-01-16T12:22:00+00:00
ID: 1921
excerpt: 'A question was asked on StackOverflow about how NTILE() works: Want to learn more on NTILE() I answered that question and then started thinking about NTILE(), I realized that I have never used this function in production code. Everytime I used it was fo&hellip;'
url: /index.php/datamgmt/dbprogramming/does-anyone-use-the-ntile/
views:
  - 7050
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - ntile
  - ranking
  - windowing functions

---
A question was asked on StackOverflow about how NTILE() works: [Want to learn more on NTILE()][1] I answered that question and then started thinking about NTILE(), I realized that I have never used this function in production code. Everytime I used it was for demoware purposes. I did a quick check against a bunch of databases

```sql
SELECT * FROM sys.sql_modules
WHERE OBJECT_DEFINITION(object_id) LIKE '%ntile%'
```

——-
  
(0 row(s) affected)

Doesn't exist. I also have a hard time figuring out where I would use it, maybe if I want to put an equal number or rows in a bunch of buckets, maybe then this will be used to call another process asynchronously?

For you that don't know what NTILE() does it basically distributes the rows in a bunch of groups. If you specify 2 groups and you have 10 rows then each group will have 5 rows.

Let's look at some code to understand how it works, first create this simple table

```sql
CREATE TABLE  #temp(StudentID CHAR(2),  Score  INT) 

INSERT #temp  VALUES('S1',75 ) 
INSERT #temp  VALUES('S2',83)
INSERT #temp  VALUES('S3',91)
INSERT #temp  VALUES('S4',83)
INSERT #temp  VALUES('S5',93 ) 
```

Now if you use NTILE() to create 2 buckets, you will see 1 and 2 as NtileValue

```sql
SELECT NTILE(2) OVER(ORDER BY Score) AS NtileValue,*
FROM #temp
ORDER BY 1
```

Here are the results:

<pre>NtileValue	StudentID	Score
1		S1		75
1		S2		83
1		S4		83
2		S3		91
2		S5		93</pre>

Since the number of rows are not even, the first bucket will have three rows and the second bucket will have two rows

Let's add one more row to this table

```sql
INSERT #temp  VALUES('S6',92 ) 
```

Now let's run the same query as before

```sql
SELECT NTILE(2) OVER(ORDER BY Score) AS NtileValue,*
FROM #temp
ORDER BY 1
```

Here are the results:

<pre>NtileValue	StudentID	Score
1		S1		75
1		S2		83
1		S4		83
2		S3		91
2		S6		92
2		S5		93</pre>

As you can see both buckets now have three rows

What if we use NTILE(3)?

```sql
SELECT NTILE(3) OVER(ORDER BY Score) AS NtileValue,*
FROM #temp
ORDER BY 1
```

Here are the results:

<pre>NtileValue	StudentID	Score
1		S1		75
1		S2		83
2		S4		83
2		S3		91
3		S6		92
3		S5		93</pre>

As expected, you now get three buckets back.

So do you use NTILE() currently or do you have plans to use it in the future?

 [1]: http://stackoverflow.com/questions/14355324/want-to-learn-more-on-ntile