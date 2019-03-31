---
title: The OLE DB Source and the Oracle Date Literal
author: Koen Verbeeck
type: post
date: 2013-01-21T11:36:00+00:00
excerpt: Recently I had some issues using ANSI date literals in an SSIS OLE DB source query to an Oracle database.
url: /index.php/datamgmt/dbprogramming/mssqlserver/ole-db-source-date-literal/
views:
  - 8873
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - Oracle
  - SSIS
tags:
  - ansi
  - date literal
  - ole db source
  - oracle ole db provider
  - pl/sql
  - ssis

---
<p style="text-align: justify;">
  Recently I was developing an SSIS package in BIDS 2008R2 which was part of a Oracle to SQL Server migration; my favorite kind of migration. This package had a very simple SQL select statement in the OLE DB source using the Oracle OLE DB provider (slightly altered to protect the innocent):
</p>

<pre>SELECT
	 columnA
	,columnB
	,42 AS Code
	,DATE'1900-01-01'
FROM user.myTable mt
WHERE mt.Status IN (10,20,30) AND mt.TransactionDate &gt; DATE'2012-01-01';</pre>

<p style="text-align: justify;">
  The preview in the OLE DB worked flawlessly and I finished constructing my data flow. However, when I ran the package, I got all sorts of weird metadata errors indicating problems at the source. Basically the errors told me new columns needed to be added to the external columns collection (see the advanced editor of the source and check out the <em>Input and Output properties</em> tab) and that existing columns should be removed. Weird because the source and the SQL query hadn’t changed.
</p>

<p style="text-align: justify;">
  Time to investigate the issue. To reproduce the issue, I created a very simple package with one data flow. In the data flow, I have this select statement in an OLE DB source retrieving data from the Oracle database:
</p>

<pre>SELECT
	 DATE'2013-01-14' AS TestDate
	,'This is a test' AS TestString
	,42 AS TestInt
FROM DUAL;</pre>

<p style="text-align: justify;">
  The output of the source component is connected to a multicast component, which serves as a trash destination.
</p>

<p style="text-align: justify;">
  As you can see in the following screenshot, the preview works without a hitch:
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/Oracle_DateLiteral/sourceconnection.PNG?mtime=1358753514"><img src="/wp-content/uploads/users/koenverbeeck/Oracle_DateLiteral/sourceconnection.PNG?mtime=1358753514" alt="" width="556" height="307" /></a>
</div>

<span style="text-align: justify;">However, when I run the package, I get the following warnings and errors during validation:</span>

> _<span style="font-size: x-small;">SSIS package &#8220;TestOracle.dtsx&#8221; starting. Information: 0x4004300A at (DFT) Read from Oracle, SSIS.Pipeline: Validation phase is beginning.<br /></span>__<span style="font-size: x-small;">Warning: 0x800470C8 at (DFT) Read from Oracle, (OLE_SRC) Read from DUAL [1]: The external columns for component &#8220;(OLE_SRC) Read from DUAL&#8221; (1) are out of synchronization with the data source columns. The <strong>column &#8220;TEST&#8221; needs to be added</strong> to the external columns.<br /></span>__<span style="font-size: x-small;">The <strong>external column &#8220;TESTDATE&#8221; (191) needs to be removed</strong> from the external columns.<br /></span>__<span style="font-size: x-small;">Error: 0xC004706B at (DFT) Read from Oracle, SSIS.Pipeline: &#8220;component &#8220;(OLE_SRC) Read from DUAL&#8221; (1)&#8221; failed validation and returned validation status &#8220;VS_NEEDSNEWMETADATA&#8221;.<br /></span>__<span style="font-size: x-small;">Error: 0xC004700C at (DFT) Read from Oracle, SSIS.Pipeline: One or more component failed validation.<br /></span>__ <span style="font-size: x-small;">Error: 0xC0024107 at (DFT) Read from Oracle: There were errors during task validation.<br /></span>__<span style="font-size: x-small;">SSIS package &#8221; TestOracle.dtsx &#8221; finished: Failure.</span>_

<p style="text-align: justify;">
  I marked the issues in bold. Apparently, SSIS insists there’s a new column called Test which isn’t included in the SQL statement but should be added to the metadata anyway. The TestDate column, which is real, should be removed. Somehow, SSIS interpretes the SQL statement wrong and truncates the column name TestDate to Test.
</p>

<p style="text-align: justify;">
  Changing the <em>DelayValidation</em> property of the data flow to True didn’t have much effect. The package starts running but crashes at the source component:
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/Oracle_DateLiteral/error_delayvalidation.PNG?mtime=1358753556"><img src="/wp-content/uploads/users/koenverbeeck/Oracle_DateLiteral/error_delayvalidation.PNG?mtime=1358753556" alt="" width="296" height="229" /></a>
</div>

<span style="text-align: justify;">The output has also slightly changed:</span>

> _<span style="font-size: x-small;">SSIS package &#8221; TestOracle.dtsx &#8221; starting.<br /></span>__<span style="font-size: x-small;">Information: 0x4004300A at (DFT) Read from Oracle, SSIS.Pipeline: Validation phase is beginning.<br /></span>__<span style="font-size: x-small;">…<br /></span>__<span style="font-size: x-small;">Information: 0x40043006 at (DFT) Read from Oracle, SSIS.Pipeline: Prepare for Execute phase is beginning.<br /></span>__<span style="font-size: x-small;">Information: 0x40043007 at (DFT) Read from Oracle, SSIS.Pipeline: Pre-Execute phase is beginning.<br /></span>__<span style="font-size: x-small;">Error: 0xC0202005 at (DFT) Read from Oracle, (OLE_SRC) Read from DUAL [210]: <strong>Column &#8220;TESTSTRING&#8221; cannot be found at the datasource</strong>.<br /></span>__<span style="font-size: x-small;">Error: 0xC004701A at (DFT) Read from Oracle, SSIS.Pipeline: component &#8220;(OLE_SRC) Read from DUAL&#8221; (210) failed the pre-execute phase and returned error code 0xC0202005.<br /></span>__<span style="font-size: x-small;">Information: 0x40043008 at (DFT) Read from Oracle, SSIS.Pipeline: Post Execute phase is beginning.<br /></span>__<span style="font-size: x-small;">Information: 0x40043009 at (DFT) Read from Oracle, SSIS.Pipeline: Cleanup phase is beginning.<br /></span>__<span style="font-size: x-small;">Task failed: (DFT) Read from Oracle<br /></span>__<span style="font-size: x-small;">SSIS package &#8221; TestOracle.dtsx &#8221; finished: Failure.</span>_

<p style="text-align: justify;">
  I highlighted the important issue in bold and I removed some warnings about my input columns not being used in the data flow. Now, SSIS complains about another column suddenly missing at the datasource. However, the dual table in Oracle hasn’t changed of course and the SQL statement is also still the same. It’s also remarkable the preview works every time, but the data flow still fails.
</p>

<p style="text-align: justify;">
  So what’s causing the issue? After playing around with the original query, a colleague and I guessed it might had something to do with the date literals used in the query, because seemingly it affected columns around the column using the date literal.
</p>

<p style="text-align: justify;">
  I used the ANSI date literal to specify a hard-coded date value. For example: DATE’1900-01-01’. Check out the following Oracle documentation about date literals: <a href="http://docs.oracle.com/cd/B19306_01/server.102/b14200/sql_elements003.htm#BABGIGCJ">Literals</a>. Let’s replace this literal with the Oracle date value TO_DATE(‘1900-01-01’,’yyyy-mm-dd’):
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/Oracle_DateLiteral/OracleDateValue_query.PNG?mtime=1358753584"><img src="/wp-content/uploads/users/koenverbeeck/Oracle_DateLiteral/OracleDateValue_query.PNG?mtime=1358753584" alt="" width="397" height="283" /></a>
</div>

<span style="text-align: justify;">Suddenly, the package  runs without any problem:</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/Oracle_DateLiteral/success.PNG?mtime=1358753621"><img src="/wp-content/uploads/users/koenverbeeck/Oracle_DateLiteral/success.PNG?mtime=1358753621" alt="" width="295" height="230" /></a>
</div>

<span style="text-align: justify;">What’s more intriguing is that when I use the </span>_Microsoft OLE DB provider for Oracle_ <span style="text-align: justify;">using the original query, the package also runs succesfully. So it seems the Oracle OLE DB provider, downloaded from the Oracle website, has some sort of issue with the ANSI date literal. I haven’t found any documentation why the provider messes up the columns like that, but if someone has better Google skills, please drop a note in the comments.</span>

<p style="text-align: justify;">
  <strong>Conclusion</strong>
</p>

<p style="text-align: justify;">
  Avoid using the ANSI date literals in your Oracle queries, but rather use the TO_DATE function. If it is an option, use the Microsoft OLE DB provider for Oracle.
</p>