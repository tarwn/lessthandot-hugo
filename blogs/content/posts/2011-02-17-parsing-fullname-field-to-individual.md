---
title: Parsing the FullName field to individual components
author: Naomi Nosonovsky
type: post
date: 2011-02-17T21:50:00+00:00
excerpt: |
  This post expands on my previous post on the similar topic
  Parsing the Address field to its individual components
  
  This time I will be solving a similar problem of parsing names to individual components. It is based on the following MSDN thread
  
  Gi&hellip;
url: /index.php/datamgmt/datadesign/parsing-fullname-field-to-individual/
views:
  - 18668
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
This post expands on my previous post on the similar topic
  
[Parsing the Address field to its individual components][1]

I encourage the readers who are unfamiliar with the CROSS APPLY technique used in this blog to read this very interesting blog by Brad Schulz
  
[T-SQL Tuesday #017: APPLY: It Slices! It Dices! It Does It All!][2]

This time I will be solving a similar problem of parsing names to individual components. It is based on the following [MSDN thread][3].

Given the names in the format of LastName (Suffix), FirstName (MiddleInitial) (where I used parenthesis to show optional parts), split the name into individual parts.

Here is the script that does it using my favorite CROSS APPLY technique and step by step approach:

<pre>/*create table #temp 
( 
    FULLNAME        VARCHAR(100), 
    ID              INT 
) 
  
INSERT INTO #TEMP VALUES ('TUCKER, KEVIN G', 1) 
INSERT INTO #TEMP VALUES ('SCOTT, JOHN', 2) 
INSERT INTO #TEMP VALUES ('ERIC, T W', 3) 
INSERT INTO #TEMP VALUES ('MUNICH, SMITH D', 4) 
INSERT INTO #TEMP VALUES ('LYOD SR, CLIVE G', 5) 
INSERT INTO #TEMP VALUES ('HANSEN JR, CHARLES S', 6) 
INSERT INTO #TEMP VALUES ('BROWN,SHERMAN', 7) 
INSERT INTO #TEMP VALUES ('ANDREWS III, CLARK A', 8) 
INSERT INTO #TEMP VALUES ('MAMMTAN, MARY LOU', 9) 
*/ 
DECLARE  @Suffixes  TABLE( 
                          Suffix VARCHAR(5) 
                          ) 

INSERT INTO @Suffixes 
VALUES     ('I'), 
           ('II'), 
           ('III'), 
           ('IV'), 
           ('V'), 
           ('SR'), 
           ('JR'), 
           ('1st'), 
           ('2nd'), 
           ('3rd') 

SELECT T.id, 
       T.Fullname, 
       F7.*, 
       F4.[LAST Name], 
       F4.Suffix 
FROM   #temp T 
       CROSS APPLY (SELECT LEFT(T.FullName,CHARINDEX(',',T.FULLNAME + ',') - 1) AS cLastName,
                           LTRIM(SUBSTRING(T.FullName,CHARINDEX(',',T.FULLNAME + ',') + 1, 
                                           LEN(T.FullName))) AS cFirstName) F1 
       CROSS APPLY (SELECT LEFT(F1.cLastName,CHARINDEX(' ',F1.cLastName + ' ') - 1) AS LName,
                           SUBSTRING(F1.cLastName,CHARINDEX(' ',F1.cLastName + ' ') + 1, 
                                     LEN(F1.cLastName)) AS pSuffix) F2 
       CROSS APPLY (SELECT CASE 
                             WHEN LEN(pSuffix) &gt; 0 
                                  AND EXISTS (SELECT 1 
                                              FROM   @Suffixes S 
                                              WHERE  S.Suffix = pSuffix) THEN 'Y' 
                             ELSE 'N' 
                           END AS SuffixExists) F3 
       CROSS APPLY (SELECT CASE 
                             WHEN F3.SuffixExists = 'Y' THEN F2.LName 
                             ELSE RTRIM(F2.LName + ' ' + F2.pSuffix) 
                           END AS [LAST Name], 
                           CASE 
                             WHEN F3.SuffixExists = 'Y' THEN F2.pSuffix 
                             ELSE '' 
                           END AS [Suffix]) F4 
       CROSS APPLY (SELECT LEFT(F1.cFirstName,CHARINDEX(' ',F1.cFirstName + ' ') - 1) AS FName,
                           SUBSTRING(F1.cFirstName,CHARINDEX(' ',F1.cFirstName + ' ') + 1, 
                                     LEN(F1.cFirstName)) AS MInitial) F5 
       CROSS APPLY (SELECT CASE 
                             WHEN LEN(MInitial) = 1 THEN 'Y' 
                             ELSE 'N' 
                           END AS MIExists) F6 
       CROSS APPLY (SELECT CASE 
                             WHEN F6.MIExists = 'Y' THEN F5.FName 
                             ELSE RTRIM(F5.FName + ' ' + F5.MInitial) 
                           END AS [FIRST Name], 
                           CASE 
                             WHEN F6.MIExists = 'Y' THEN F5.MInitial 
                             ELSE '' 
                           END AS [Middle Initial]) F7 </pre>

This code does not consider complex cases of 2 last names following by a suffix or first name consisting of two names following by the middle initial.

Hopefully this short blog is useful.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][4] forum or our [Microsoft SQL Server Admin][5] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/parsing-the-address-field-to-its-individ
 [2]: http://bradsruminations.blogspot.com/2011/04/t-sql-tuesday-017-it-slices-it-dices-it.html
 [3]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/c31fe71b-2c51-497d-a322-0e7e17a861cd
 [4]: http://forum.lessthandot.com/viewforum.php?f=17
 [5]: http://forum.lessthandot.com/viewforum.php?f=22