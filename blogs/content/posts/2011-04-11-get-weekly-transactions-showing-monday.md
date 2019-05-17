---
title: Get weekly transactions showing Monday's date
author: Naomi Nosonovsky
type: post
date: 2011-04-11T11:25:00+00:00
ID: 1110
excerpt: |
  This recent MSDN thread presented an interesting, as I believe, question:
  
  For a predefined set of the Application IDs show the weekly transactions and display Monday's day for each week. The interesting twist in this common PIVOT query is to display&hellip;
url: /index.php/datamgmt/datadesign/get-weekly-transactions-showing-monday/
views:
  - 6510
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
This [recent MSDN thread][1] presented an interesting, I believe, question:

For a predefined set of the Application IDs show the weekly transactions and display Monday's day for each week. The interesting twist in this common PIVOT query is to display a Monday's date for a week.

Rather than showing a solution for that particular thread, I show an AdventureWorks sample query. This query will return top 5 total amounts for each week by Customer (using SalesOrderHeader table).

Here is the query 

```sql
DECLARE  @First_Bow      DATETIME, 
         @Week_Start_Day TINYINT 

SET @Week_Start_Day = 2 

SELECT @First_Bow = CONVERT(DATETIME,-53690 + ((@Week_Start_Day + 5)%7))

WITH cte 
     AS (SELECT SH.CustomerID, 
                TotalDue            AS Total, 
                DATEADD(DAY,(DATEDIFF(DAY,@FIRST_BOW,OrderDate) / 7) * 7, 
                        @FIRST_BOW) AS [MondayDate] 
         FROM   Sales.SalesOrderHeader SH), 
     cte2 
     AS (SELECT Total, 
                MondayDate, 
                ROW_NUMBER() 
                  OVER(PARTITION BY MondayDate ORDER BY Total DESC, CustomerID) AS Row 
         FROM   cte) 
SELECT * 
FROM   cte2 
       PIVOT 
       (SUM(Total) 
        FOR Row IN ( [1],[2],[3],[4],[5],[6] ) ) pvt
```
The interesting part here is in determining the Monday's day for each week. I decided to use a solution presented in SQL Server Forums [topic][2].

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/5442bbd7-ef89-47f8-8ba9-eef04b6b5fe4/#aa2f0695-501b-43b2-a559-290f6c50b0c7
 [2]: http://www.sqlteam.com/forums/topic.asp?TOPIC_ID=47307
 [3]: http://forum.lessthandot.com/viewforum.php?f=17
 [4]: http://forum.lessthandot.com/viewforum.php?f=22