---
title: Do not use the float data type
author: George Mastros (gmmastros)
type: post
date: 2009-11-16T13:25:36+00:00
url: /index.php/datamgmt/dbprogramming/do-not-use-the-float-data-type/
views:
  - 34122
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
That may seem a little harsh, and it&#8217;s not always true. However, most of the time, the float data type should be avoided. Unfortunately, the float (and real) data types are approximate data types that can lead to significant rounding errors.

**How to detect this problem:**

<pre>Select Table_Name + '.' + Column_Name As Name, 'Table' As ObjectType
From   Information_Schema.Columns
Where  Data_Type in ('Float', 'Real')

UNION ALL

SELECT  Name, Types.Description
FROM    (
        SELECT S.Name, S.XType, C.TEXT
        FROM   sysobjects S
               INNER Join syscomments C
                 ON  S.id = C.id
                 And S.xtype in ('P', 'v', 'TF', 'FN')
        WHERE   OBJECTPROPERTY(S.ID, N'IsMSShipped') = 0
 
        UNION All
 
        SELECT OBJECT_NAME(A.id), s.XType, LeftText + RightText
        FROM   sysobjects s
               INNER Join (
                 SELECT Id, RIGHT(TEXT, 10) AS LeftText, ColId
                 FROM   syscomments
         ) AS A
                   ON  S.id = A.id
                   And OBJECTPROPERTY(S.ID, N'IsMSShipped') = 0
                   And S.xtype in ('P', 'v', 'TF', 'FN')
               INNER Join (
                 SELECT Id, LEFT(TEXT, 10) AS RightText, ColId
         FROM   syscomments
             ) AS B
                   ON  A.id = B.id
                   and A.ColId = B.ColId - 1
        ) AS A
		Inner join (
			Select 'FN' As XType, 'Function' As Description
			Union All
			Select 'P' As XType, 'Procedure' As Description
			Union All
			Select 'V' As XType, 'View' As Description
			Union All
			Select 'TF' As XType, 'Table Values Function' As Description
			) As Types
			On A.XType = Types.XType
WHERE   TEXT Like '%float[^(]%'
ORDER BY Name</pre>

**How to correct it:** Examine the data you are using and identify the precision and scale required. Change the data type (or code) to use a decimal with the precision and scale you require.

**Level of severity:** Moderate

**Level of difficulty:** Easy