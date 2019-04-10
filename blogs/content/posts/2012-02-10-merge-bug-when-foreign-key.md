---
title: MERGE bug when Foreign Key constraint is created on non-clustered index (2008RTM)
author: niikola
type: post
date: 2012-02-10T12:21:00+00:00
ID: 1517
excerpt: |
  Few days ago my colleague showed me strange behavior of SQL Server's 2008  RTM MERGE statement. Although this is corrected in SP1 1 or more probably in CU1 (there is bug solved in CU1 connected to this - imo), I do believe it's worth to be mentioned.
  I&hellip;
url: /index.php/datamgmt/dbprogramming/mssqlserver/merge-bug-when-foreign-key/
views:
  - 15838
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - merge
  - sql server
  - sql server 2008

---
Few days ago my colleague showed me strange behavior of SQL Server's 2008 RTM MERGE statement. Although this is corrected in SP1 1 or more probably in CU1 (there is [bug solved in CU1][1] connected to this – imo), I do believe its worth to be mentioned.

If you try to update parent column (column referenced by foreign key) even with the same value it fails with message “<span style="font-family: courier new,courier;">The MERGE statement conflicted with the REFERENCE constraint</span>” when index used for FK relationship is NONCLUSTERED. It does not matter if this index is primary key, unique constraint or non-filtered unique key (those are options you can use for FK). Moreover, it does not matter if there is (or there's not) some other clustered index. It also does not matter if child table has indexes, clustered or non-clustered.

How to reproduce:

  * Create parent table with nonclustered index (using either primary key, unique constraint or unique index syntax) over some column.
  * Create child table referencing above mentioned column.
  * Insert values in both tables. One row per table is enough (child table should have not null reverencing column)
  * Execute Merge statement on parent table updating referenced column to the same value.

 

Example:

<span style="color: #888888;">— Create tables</span>  
`<strong>Create Table</strong> tparent ( cCode <strong><span style="color: #888888;">varchar(2) not null</span></strong>);<br /><strong>Create table</strong> tchild  ( cCode <span style="color: #888888;"><strong>varchar(2) not null</strong></span>);  </p>
<p>``<span style="color: #888888;">-- Create non-clustered PK on parent table<br /></span>``<span><strong>Alter Table</strong> tparent <br /> <strong>add Constraint</strong> PK_tparent<br /> <strong>Primary Key NONCLUSTERED</strong> (cCode <strong><span style="color: #888888;">asc</span></strong>);  </p>
<p>-</span>``<span><span style="color: #888888;">- Create Foreign key</span></span>`  
`<span><strong>Alter Table </strong>tchild                    <br /><strong> add Constraint</strong> fk_tchild_tparent<br /> <strong>Foreign Key</strong> (cCode) <br /> <strong>References</strong> tparent(cCode);</p>
<p></span>``<span>-</span>``<span><span style="color: #888888;">- Create Foreign key</span></span>`  
`<span><strong>Insert Into</strong> tparent(cCode) <strong>values </strong>('AA');<br /><strong>Insert Into </strong>tchild(cCode)  <strong>values </strong>('AA');</p>
<p><span style="color: #888888;">-- MERGE</span><br /><strong>Merge </strong>tparent <span style="color: #888888;"><strong>as</strong></span> trg<br /><strong>Using </strong>(<strong>Select</strong> 'AA') <span style="color: #888888;"><strong>as</strong></span> src(cCode)<br /> <strong>on</strong> src.cCode=trg.cCode<br /><strong> When</strong> <strong>Matched </strong><br /> <strong>Then Update Set</strong> cCode=trg.cCode;<br /> </span>`

`<span><span style="color: #888888;">-- RESULT</span></span>`  
`Msg 547, Level 16, State 0, Line 21<br /><span class="MT_red">The MERGE statement conflicted with the REFERENCE constraint "fk_tchild_tparent". <br />The conflict occurred in database "tempdb", table "dbo.tchild", column 'cCode'.</span><br />The statement has been terminated.`

UPDATE:

Simmilar issue was found by Alexander Kuznetsov in [“Trusted” Foreign Keys Allow Orphans, Reject Valid Child Rows][2] for SQL Server 2008 R2.

 [1]: http://support.microsoft.com/kb/956718
 [2]: http://sqlblog.com/blogs/alexander_kuznetsov/archive/2011/10/17/trusted-foreign-keys-allow-orphans-reject-valid-child-rows.aspx