---
title: Why avoiding multiple code blocks in a Stored Procedure
author: Axel Achten (axel8s)
type: post
date: 2013-01-21T11:45:00+00:00
excerpt: |
  It was a little remark from Bob Beauchemin (B|T) during the Belgian SQL Server Days that started me writing this post.
  Showing cached Execution Plans
  In this post I’m going to use information from the following Dynamic Management Views and Functions t&hellip;
url: /index.php/datamgmt/dbprogramming/why-avoiding-multiple-code-blocks/
views:
  - 15388
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - case
  - if else
  - procedure cache
  - t-sql

---
It was a little remark from Bob Beauchemin ([B][1]|[T][2]) during the [Belgian SQL Server Days][3] that started me writing this post.

**Showing cached Execution Plans**
  
In this post I’m going to use information from the following Dynamic Management Views and Functions to show some information about the cached Execution Plans of the queries used in this post:

  * sys.dm\_exec\_cached_plans shows types, usage, size… of the Execution Plans;
  * sys.dm\_exec\_sql_text shows the actual code of the query;
  * sys.dm\_exec\_query_plan is used to get the XML Execution Plan itself.

In fact I use following query to get the results:

<pre>SELECT cp.objtype, cp.cacheobjtype, cp.usecounts, cp.size_in_bytes, st.[text], qp.query_plan
	FROM sys.dm_exec_cached_plans cp
	CROSS APPLY sys.dm_exec_sql_text(cp.plan_handle) st
	CROSS APPLY sys.dm_exec_query_plan (cp.plan_handle)qp;</pre>

The result set of this query looks like this:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB1.JPG?mtime=1358775444"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB1.JPG?mtime=1358775444" width="933" height="427" /></a>
</div>

The result set can contain thousands of rows, depending on the uptime of the server, the number of queries and their corresponding execution plans, the available buffer memory…

**IF using 2 code blocks**
  
So let’s create a simple Stored Procedure that executes against the AdventureWorks2008R2 database:

<pre>USE AdventureWorks2008R2;
GO

CREATE PROCEDURE TwoPlans
	@IfParameter int
AS
	SET NOCOUNT ON;
	IF @IfParameter = 1
		SELECT a.City, COUNT(bea.AddressID) EmployeeCount
		FROM Person.BusinessEntityAddress bea 
			INNER JOIN Person.Address a
				ON bea.AddressID = a.AddressID
		GROUP BY a.City
		ORDER BY a.City
	ELSE
		SELECT DATEPART(yyyy,OrderDate) AS N'Year'
			,SUM(TotalDue) AS N'Total Order Amount'
		FROM Sales.SalesOrderHeader
		GROUP BY DATEPART(yyyy,OrderDate)
		ORDER BY DATEPART(yyyy,OrderDate);
GO</pre>

The results of the query aren’t important what is important is to see what happens in the Procedure Cache. To be able to see what’s happening we are going to empty the procedure cache:

<span class="MT_red">WARNING: executing the following code deletes all cached plans from the Procedure Cache. All Execution Plans need to be recompiled. This can result in a slow or unresponsive server.<br /> DON’T EXECUTE THE FOLLOWING CODE ON A PRODUCTION SERVER!!!<br /> </span>

<pre>DBCC FREEPROCCACHE;
GO</pre>

When you execute our first query again:

<pre>SELECT cp.objtype, cp.cacheobjtype, cp.usecounts, cp.size_in_bytes, st.[text], qp.query_plan
	FROM sys.dm_exec_cached_plans cp
	CROSS APPLY sys.dm_exec_sql_text(cp.plan_handle) st
	CROSS APPLY sys.dm_exec_query_plan (cp.plan_handle)qp;</pre>

You can see in the result set that only the Execution Plan from the above query is stored in the Procedure Cache.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB2.JPG?mtime=1358775444"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB2.JPG?mtime=1358775444" width="871" height="67" /></a>
</div>

Now execute the stored procedure:

<pre>EXEC TwoPlans 1;
GO</pre>

Execute the query against the Procedure Cache again:

<pre>SELECT cp.objtype, cp.cacheobjtype, cp.usecounts, cp.size_in_bytes, st.[text], qp.query_plan
	FROM sys.dm_exec_cached_plans cp
	CROSS APPLY sys.dm_exec_sql_text(cp.plan_handle) st
	CROSS APPLY sys.dm_exec_query_plan (cp.plan_handle)qp;</pre>

The result set shows us the execution of the above query with a usecount of 2 and the Execution Plan of our Stored Procedure:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB3.JPG?mtime=1358775444"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB3.JPG?mtime=1358775444" width="871" height="85" /></a>
</div>

Now click the XML link in the query_plan column. A new tab will open in SSMS showing you the XML Execution Plan:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB4.JPG?mtime=1358775444"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB4.JPG?mtime=1358775444" width="1434" height="865" /></a>
</div>

You can now read the XML plan and you will find both the statements in the query plan:

<pre>ShowPlanXML xmlns="http://schemas.microsoft.com/sqlserver/2004/07/showplan" Version="1.1" Build="10.50.2500.0"&gt;
  <BatchSequence&gt;
    <Batch&gt;
      <Statements&gt;
        <StmtSimple StatementText="CREATE procedure TwoPlans&#xD;&#xA;&#x9;@IfParameter int&#xD;&#xA;as&#xD;&#xA;&#x9;SET NOCOUNT ON;&#xD;&#xA;" StatementId="1" StatementCompId="3" StatementType="SET ON/OFF" /&gt;
        <StmtCond StatementText="&#x9;If @IfParameter = 1&#xD;&#xA;&#x9;" StatementId="2" StatementCompId="4" StatementType="COND"&gt;
          <Condition /&gt;
          <Then&gt;
            <Statements&gt;
              <StmtSimple StatementText="&#x9;SELECT a.City, COUNT(bea.AddressID) EmployeeCount&#xD;&#xA;&#x9;&#x9;FROM Person.BusinessEntityAddress bea &#xD;&#xA;&#x9;&#x9;&#x9;INNER JOIN Person.Address a&#xD;&#xA;&#x9;&#x9;&#x9;&#x9;ON bea.AddressID = a.AddressID&#xD;&#xA;&#x9;&#x9;GROUP BY a.City&#xD;&#xA;&#x9;&#x9;ORDER BY a.City&#xD;&#xA;" StatementId="3" StatementCompId="5" StatementType="SELECT" StatementSubTreeCost="0.604708" StatementEstRows="574.696" StatementOptmLevel="FULL" QueryHash="0x03E92D79FC617C86" QueryPlanHash="0xED13B89036D1A5E6" StatementOptmEarlyAbortReason="GoodEnoughPlanFound"&gt;
                <StatementSetOptions QUOTED_IDENTIFIER="true" ARITHABORT="true" CONCAT_NULL_YIELDS_NULL="true" ANSI_NULLS="true" ANSI_PADDING="true" ANSI_WARNINGS="true" NUMERIC_ROUNDABORT="false" /&gt;
                <QueryPlan CachedPlanSize="32" CompileTime="7" CompileCPU="7" CompileMemory="344"&gt;…
             </StmtSimple&gt;
            </Statements&gt;
          </Then&gt;
          <Else&gt;
            <Statements&gt;
              <StmtSimple StatementText="&#x9;Else&#xD;&#xA;&#x9;&#x9;SELECT DATEPART(yyyy,OrderDate) AS N'Year'&#xD;&#xA;&#x9;&#x9;&#x9;,SUM(TotalDue) AS N'Total Order Amount'&#xD;&#xA;&#x9;&#x9;FROM Sales.SalesOrderHeader&#xD;&#xA;&#x9;&#x9;GROUP BY DATEPART(yyyy,OrderDate)&#xD;&#xA;&#x9;&#x9;ORDER BY DATEPART(yyyy,OrderDate);&#xD;" StatementId="4" StatementCompId="8" StatementType="SELECT" StatementSubTreeCost="0.780189" StatementEstRows="4" StatementOptmLevel="FULL" QueryHash="0xA73814A2D4649412" QueryPlanHash="0x7A5BDE1102728DAA" StatementOptmEarlyAbortReason="GoodEnoughPlanFound"&gt;…
            </Statements&gt;
          </Else&gt;
        </StmtCond&gt;
      </Statements&gt;
    </Batch&gt;
  </BatchSequence&gt;
</ShowPlanXML&gt;</pre>

An easier trick to see the Graphical Execution Plans is to open [SQL Sentry Plan Explorer][4] and copy the XML into the Plan XML tab:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB5.JPG?mtime=1358775444"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB5.JPG?mtime=1358775444" width="652" height="298" /></a>
</div>

After doing this SQL Sentry Plan Explorer will give you all the details about the Execution Plan and you’ll see in the Plan Diagram that SQL Server created an Execution Plan for both the queries altough only the first one was used:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB6.JPG?mtime=1358775445"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB6.JPG?mtime=1358775445" width="1347" height="765" /></a>
</div>

**IF calling 2 Stored Procedures**
  
Now let’s create 2 Stored Procedures that each will execute 1 part of the code from the previous query:

<pre>CREATE PROCEDURE EmpCntCity
AS
	SET NOCOUNT ON;
	SELECT a.City, COUNT(bea.AddressID) EmployeeCount
	FROM Person.BusinessEntityAddress bea 
		INNER JOIN Person.Address a
			ON bea.AddressID = a.AddressID
	GROUP BY a.City
	ORDER BY a.City;
GO

CREATE PROCEDURE OrderAmountYear
AS
	SET NOCOUNT ON;
	SELECT DATEPART(yyyy,OrderDate) AS N'Year'
		,SUM(TotalDue) AS N'Total Order Amount'
	FROM Sales.SalesOrderHeader
	GROUP BY DATEPART(yyyy,OrderDate)
	ORDER BY DATEPART(yyyy,OrderDate);
GO</pre>

Next we create a Stored Procedure that will contain the IF…ELSE logic and based on the input parameter will execute one of the above Stored Procedures:

<pre>CREATE PROCEDURE TwoProcs
	@IfParameter int
AS
	SET NOCOUNT ON;
	IF @IfParameter = 1
		EXEC EmpCntCity
	ELSE
		EXEC OrderAmountYear;
GO</pre>

Now we can call our Stored Procedure and execute 1 of the Stored Procedures:

<pre>EXEC TwoProcs 1;
GO</pre>

Let’s again query the Procedure Cache:

<pre>SELECT cp.objtype, cp.cacheobjtype, cp.usecounts, cp.size_in_bytes, st.[text], qp.query_plan
	FROM sys.dm_exec_cached_plans cp
	CROSS APPLY sys.dm_exec_sql_text(cp.plan_handle) st
	CROSS APPLY sys.dm_exec_query_plan (cp.plan_handle)qp;</pre>

And have a look at the result:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB7.JPG?mtime=1358775445"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/IFCB7.JPG?mtime=1358775445" width="919" height="125" /></a>
</div>

Now we see that executing the second Stored Procedure also created 2 Executions Plans: 1 for the Stored Procedure “TwoProcs” and one for the Stored Procedure “EmpCntCity” that was effectively executed. But we see that there was no Execution Plan created for the Stored Procedure “OrderAmountYear”. And this Execution Plan was more complex than the Execution Plan of our “TwoProcs” Stored Procedure. We can also see that the sum of the sizes of the 2 Execution Plans (65536 + 16384 = 81920) is still smaller than the Execution Plan of the “TwoPlans” Stored Procedure (98304).

**Conclusion**
  
Avoid Stored Procedures that contain complete code blocks encapsulated in IF&#8230;ELSE or CASE blocks. It will result in SQL Server creating Execution Plans for all possibilities, consuming more Buffer Cache (memory) and in the end slow down the execution of the code.
  
As a bonus, troubleshooting the individual Stored Procedures will be much easier and there is a bigger chance that you can reuse the Stored Procedures.

 [1]: http://www.sqlskills.com/blogs/bobb
 [2]: https://twitter.com/bobbeauch
 [3]: http://www.sqlserverdays.be
 [4]: http://www.sqlsentry.com/plan-explorer/sql-server-query-view.asp