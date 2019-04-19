---
title: CTE and hierarchical queries
author: Naomi Nosonovsky
type: post
date: 2009-09-13T14:42:07+00:00
ID: 557
excerpt: |
  One of my favorite additions to T-SQL language in SQL Server 2005 is Common Table Expressions, CTE for short. Primarily I use them instead of derived tables to improve maintenance and code readability.
  
  See also this interesting article on recursive C&hellip;
url: /index.php/datamgmt/datadesign/cte-and-hierarchical-queries/
views:
  - 42080
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
One of my favorite additions to T-SQL language in SQL Server 2005 is Common Table Expressions, CTE for short. Primarily I use them instead of derived tables to improve maintenance and code readability.

 ![T-SQL Tuesday logo][1]

See also this interesting article on recursive CTE implementation [T-SQL: Using common table expressions (CTE) to generate sequences][2] and this interesting [discussion][3] about commonly-known problem of splitting delimited values.

You may find this [article][4] by SQLUSA (Kalman Toth) very interesting.

I think you will find these blogs by Brad Schulz [Viva la Famiglia!][5] and [This Article On Recurson Is Entitled “This Article On Recursion Is Entitled “This Article...][6] interesting and useful and enjoyable reading.

See also [Challenge 17][7] and its solutions at [Challenge 17 – Winners][8]

For instance, this code is much easier to read now after moving some logic into CTE

```sql
;WITH

-- -----------------------------------------------

-- Calcualtes sum of active taxes

-- for each of Tax Groups

-- -----------------------------------------------

Taxes as

(

  SELECT tg.nTaxGroupID             as nTaxGroupID,

         tg.cName                   as TaxGroupName,

         Sum(isNull(ti.nPercent,0)) as TaxPercent

    FROM TaxGroups                tg

    LEFT JOIN TaxGroupsToTaxItems tgi on tg.nTaxGroupID = tgi.nTaxGroupID

    LEFT JOIN TaxItems            ti  on tgi.nTaxItemID = ti.nTaxItemID

   WHERE tg.lActive = 1

   GROUP BY tg.nTaxGroupID,

            tg.cName

),

-- -----------------------------------------------

-- calculates total cost per Job

-- -----------------------------------------------

JDS as

(

  SELECT nJobHeaderID,

         Sum(nAmount * nQuantity) as TotalAmount

    FROM JobDetail

   GROUP BY nJobHeaderID

),

-- -----------------------------------------------

-- concatenates all Customer's phone numbers

-- into one string (LF delimited)

-- -----------------------------------------------

P as

(

  SELECT nCustomerID,

         Stuff(PhoneInfo, 1, 1, '') AS PhoneInfo      

    FROM ( SELECT InnerData.nCustomerID,

                  ( SELECT Char(10) + P.cPhoneNumber + ' - ' + rTrim(PT.cType)
                           
                      FROM PhoneNumbers   P

                     INNER JOIN PhoneType PT on P.nPhoneTypeID = PT.nPhoneTypeID

                     WHERE P.nCustomerID = InnerData.nCustomerID

                       FOR XML PATH('') -- this does string concatenation

                  ) as PhoneInfo

             FROM ( SELECT DISTINCT nCustomerID

                      FROM PhoneNumbers

                  ) as InnerData

         ) as OuterData

)          

SELECT J.nRouteHeaderID   as nRouteHeaderID, 

       JH.nCustomerID     as nCustomerID,

       JH.nJobHeaderID    as JobHeaderID,

       J.ScheduleTime     as ScheduleTime,

       JH.cContact        as Contact,

       J.SplitAmount      as SplitAmount,

       vJ.cAddr1          as Address1,

       vJ.cAddr2          as Address2,

       vJ.cCity           as City,

       vJ.cState          as State,

       vJ.cZip            as Zip,

       vJ.JobDescription  as JobDescription,

       JH.nDiscountPercent/100 * JDS.TotalAmount

                          as DiscountAmount,

       JH.nAmount         as JobTotal,

       T.TaxGroupName     as TaxType,

       T.TaxPercent       as TaxPercent,

       J.PermanentNote    as PermanentNote,

       P.PhoneInfo        as PhoneInfo

  FROM @Jobs               J

 INNER JOIN dbo.JobHeader JH  on J.nJobHeaderID   = JH.nJobHeaderID

  LEFT JOIN v_JobAddress  vJ  on J.nJobHeaderID   = vJ.nJobHeaderID

  LEFT JOIN Taxes         T   on JH.nTaxGroupID   = T.nTaxGroupID

  LEFT JOIN JDS           JDS on JH.nJobHeaderID  = JDS.nJobHeaderID

  LEFT JOIN P             P   on JH.[nCustomerID] = P.[nCustomerID]

 ORDER BY J.IdField -- Used for order but not retrieved

```
One of the most interesting applications of CTE is in solving hierarchical queries by using recursive CTEs. In SQL Server 2000 you would need to use temporary table and looping to solve the problem of hierarchical query, which can now be solved with 1 select in SQL Server 2005 and up.

The example of it which I always refer when I need to solve such a problem could be found in BOL: [Recursive Queries Using Common Table Expressions][9]

```sql
USE AdventureWorks;

WITH DirectReports (ManagerID, EmployeeID, Title, DeptID, Level)
AS
(
-- Anchor member definition
    SELECT e.ManagerID, e.EmployeeID, e.Title, edh.DepartmentID, 
        0 AS Level
    FROM HumanResources.Employee AS e
    INNER JOIN HumanResources.EmployeeDepartmentHistory AS edh
        ON e.EmployeeID = edh.EmployeeID AND edh.EndDate IS NULL
    WHERE ManagerID IS NULL
    UNION ALL
-- Recursive member definition
    SELECT e.ManagerID, e.EmployeeID, e.Title, edh.DepartmentID,
        Level + 1
    FROM HumanResources.Employee AS e
    INNER JOIN HumanResources.EmployeeDepartmentHistory AS edh
        ON e.EmployeeID = edh.EmployeeID AND edh.EndDate IS NULL
    INNER JOIN DirectReports AS d
        ON e.ManagerID = d.EmployeeID
)
-- Statement that executes the CTE
SELECT ManagerID, EmployeeID, Title, Level
FROM DirectReports
INNER JOIN HumanResources.Department AS dp
    ON DirectReports.DeptID = dp.DepartmentID
WHERE dp.GroupName = N'Research and Development' OR Level = 0;
```
Another slight variation of this same theme can be found in this [MSDN thread][10]
  
———————
  
Below I describe few recent problems I solved using recursive CTE technique:

Problem definition:

<code class="codespan"><br />
I use SQL Server 2005 and I have a list of appr. 7700 items. In the list there is the complete stock quantity for each item and I also have the typical amount for each pallet. I need a list with all individual pallets and its stock</code>

<div class="tables">
<table border="1" cellpadding ="2" cellspacing = "2"> 

<tr>
<th>
  Item
</th>

<th>
  stock
</th>

<th>
  amount/pallet
</th>
</tr>

<tr>
<td>
  100-001
</td>

<td>
  2500
</td>

<td>
  1000
</td>
</tr></table> 

<p>
<code class="codespan">I need the list to look like this</code>
</p>

<div class="tables">
<table border="1" cellpadding ="2" cellspacing = "2"> 

<tr>
  <td>
    100-001
  </td>
  
  <td>
    1000
  </td>
</tr>

<tr>
  <td>
    100-001
  </td>
  
  <td>
    1000
  </td>
</tr>

<tr>
  <td>
    100-001
  </td>
  
  <td>
    500
  </td>
</tr></table> 

<p>
  <code class="codespan">Is this possible to do?</code>
</p>

<p>
  It took me at least 20 minutes to come up with the following solution:
</p>

<pre lang="tsql">declare @t table (item Varchar(10), Stock money, Pallet money)

insert into @t values('100-001',   2500,    1000)
insert into @t values('100-002',   3000,    400)
insert into @t values('100-003',   2000,    400)


;with cte_toInsert as 
(select Item, Pallet, Amount, Original_Stock, Stock - Amount as Stock 
from (select Item, Pallet, Stock as Original_Stock, Stock, case when Stock-Pallet > 0 then Pallet else Stock end as Amount from @t) X 
where Stock = 0 or (Stock >=0 and Amount > 0) 
union all select Item, Pallet, Amount, Original_Stock, Stock - Amount as Stock 
from (select Item, Pallet, Original_Stock, Stock, 
case when Stock-Pallet > 0 then Pallet else Stock end as Amount from cte_toInsert) X 
where (Stock = 0 and Amount > 0) OR Stock > 0 )

select * from cte_toInsert order by Item, Amount DESC OPTION (MAXRECURSION 10) </pre>

<p>
  Here is another interesting problem where I had to use Nikola's help to come up to the final solution:
</p>

<p>
  Given the initial list like this
</p>

<pre lang="">
pk_rights   fk_rights   RightsName                                         RightsOrder Description
----------- ----------- -------------------------------------------------- ----------- ------------------------------

1           NULL        Open Category Library                              0           Open Category Library
18          NULL        Open Portfolio Footnote Library                    1           Open Portfolio Footnote Library
2           1           Add Category Library                               0           Add Category Library
3           1           Delete Category Library                            1           Delete Category Library
11          2           Edit Category Library                              0           Edit Category Library
7           2           Add Category                                       2           Add Category
12          2           Add Category Language                              4           Add Category Language
14          2           Add Category Caption                               6           Add Category Caption
9           2           Delete Category                                    3           Delete Category
16          2           Delete Category Caption                            7           Delete Category Caption
17          2           Delete Category Language                           5           Delete Category Language
8           7           Edit Category                                      0           Edit Category
13          12          Edit Category Lanaguage                            0           Edit Category Lanaguage
15          14          EditCategory Caption                               0           EditCategory Caption</pre>

<p>
  I need the output to look like this.... the indents are for readability only but the rows must be ordered in this sequence.
</p>

<pre lang="">
pk_rights   fk_rights   RightsName                                         RightsOrder Description
----------- ----------- -------------------------------------------------- ----------- ------------------------------

1           NULL        Open Category Library                              0           Open Category Library
2           1              Add Category Library                               0        Add Category Library
11          2                 Edit Category Library                              0     Edit Category Library
3           1              Delete Category Library                            1        Delete Category Library
7           2              Add Category                                       2        Add Category
8           7                 Edit Category                                      0     Edit Category
9           2              Delete Category                                    3        Delete Category
12          2              Add Category Language                              4        Add Category Language
13          12                Edit Category Lanaguage                            0     Edit Category Lanaguage
17          2              Delete Cateogry Language                           5        Delete Cateogry Language
14          2              Add Category Caption                               6        Add Category Caption
15          14                EditCategory Caption                               0     EditCategory Caption
16          2              Delete Category Caption                            7        Delete Category Caption
18          NULL        Open Portfolio Footnote Library                    1           Open Portfolio Footnote Library</pre>

<p>
  Here is a solution by Nikola:
</p>

<pre lang="tsql">
;with cte_t AS
(
SELECT T1.pk_rights,
    t1.fk_rights,
    T1.RightsName,
    0 AS LEVEL,
    T1.RightsOrder,
    T1.Description AS ChildDescr,
    CAST(RIGHT('000'+CAST(t1.RightsOrder AS VARCHAR(3)),2)  AS VARCHAR(50)) AS ro
FROM @Test T1
WHERE T1.fk_rights IS NULL
UNION all
SELECT T1.pk_rights,
    T1.fk_rights,
    T1.RightsName,
    LEVEL + 1 AS LEVEL,
    T1.RightsOrder,
    T1.Description AS ChildDescr,
    CAST(D.ro + '-' + RIGHT('000'+CAST(t1.RightsOrder AS VARCHAR(3)),2) AS VARCHAR(50)) AS ro
FROM @Test T1
INNER join cte_t D ON T1.fk_rights = D.pk_rights
)
SELECT cte.pk_rights,
    cte.fk_rights,
    LEFT(REPLICATE('   ',cte.LEVEL)+cte.RightsName,50) AS RightsName,
    LEFT(REPLICATE('   ',cte.LEVEL)+CAST(RightsOrder AS VARCHAR(5)),15) AS RightsOrder,
    LEFT(cte.ChildDescr,50) AS [Description]
FROM cte_t cte
ORDER BY ro
</pre>

<p>
  Another simple problem was presented in this <a href="http://forums.asp.net/p/1476562/3434155.aspx#3434155">thread</a>
</p>

<p>
  Find sum of products which have multiple level of categories and subcategories:
</p>

<pre lang="tsql">
-- creating test data
declare @Test table (ID int identity(1,1), Valore int, IDRef int NULL)
insert into @Test (Valore, IDRef) values (100,null), (20,1), (15,2),(-10,3), (200,null),(20,5),(335,6)

-- now creating recursive CTE
select * from @Test
; with cte as (select ID as Root, ID as IDRef, Valore, ID from @Test where IdRef IS NULL 
union all
select C.Root, T.IDRef, T.Valore, T.ID from cte C inner join @Test T on T.IDRef = C.ID)

-- Our final result
select C.Root, SUM(Valore) as Total from cte C group by C.Root option (MaxRecursion 10)</pre>

<p>
  There is a similar problem discussed in this <a href="http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/cc672149-0318-475b-b8fa-3ac986a571c1">thread</a>:
</p>

<p>
  and you can view very interesting solution in this <a href="http://www.sqlmag.com/Articles/ArticleID/92997/92997.html?Ad=1">blog</a> by Itzik Ben-Gan.
</p>

<p>
  *** <strong>Remember, if you have a SQL related question, try our <a href="http://forum.ltd.local/viewforum.php?f=17">Microsoft SQL Server Programming</a> forum or our <a href="http://forum.ltd.local/viewforum.php?f=22">Microsoft SQL Server Admin</a> forum</strong><ins></ins></div> </div>

 [1]: /wp-content/uploads/blogs/DataMgmt/olap_1.gif "T-SQL Tuesday logo"
 [2]: http://smehrozalam.wordpress.com/2009/06/09/t-sql-using-common-table-expressions-cte-to-generate-sequences/
 [3]: http://forum.ltd.local/viewtopic.php?f=17&t=7566&st=0&sk=t&sd=a
 [4]: http://www.sqlusa.com/bestpractices2005/organizationtree/
 [5]: http://bradsruminations.blogspot.com/2009/10/viva-la-famiglia.html
 [6]: http://bradsruminations.blogspot.com/2010/03/this-article-on-recurson-is-entitled.html
 [7]: http://beyondrelational.com/blogs/tc/archive/2009/11/16/tsql-challenge-17-creating-cross-rows-references-with-inline-hyperlinks.aspx
 [8]: http://beyondrelational.com/blogs/tc/archive/2010/03/16/tsql-challenge-17-winners.aspx
 [9]: http://msdn.microsoft.com/en-us/library/ms186243.aspx
 [10]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/6dfd0140-d018-481f-9363-c0ab49d9517f