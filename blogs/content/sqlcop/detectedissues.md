---
title: SQLCop Detected Issues
type: sqlcop
date: 2008-02-08T02:04:36+00:00
url: /sqlcop/detectedissues.php
---


<div class="bp">
    <h1>Detected issues</h1>
    <hr/>
    <h2>Code</h2>
    <ul>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/MSSQLServer/don-t-start-your-procedures-with-sp_">Procedures with SP_</a></li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/MSSQLServer/always-include-size-when-using-varchar-n">VarChar Size Problems</a></li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/always-include-precision-and-scale-with">Decimal Size Problem</a></li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DataDesign/identify-procedures-that-call-sql-server">Undocumented Procedures</a></li>
        <li>Procedures without SET NOCOUNT ON</li>
        <li>Procedures with SET ROWCOUNT</li>
        <li><a href="http://wiki.lessthandot.com/index.php/6_Different_Ways_To_Get_The_Current_Identity_Value">Procedures with @@Identity</a></li>
        <li>Procedures with dynamic sql</li>
        <li>Procedures using dynamic sql without sp_executesql</li>
    </ul>

    <h2>Column</h2>
    <ul>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/do-not-use-spaces-or-other-invalid-chara">Column Name Problems</a></li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/do-not-use-the-float-data-type">Columns with float data type</a></li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/don-t-use-text-datatype-for-sql-2005-and">Columns with image data type</a></li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/don-t-use-text-datatype-for-sql-2005-and">Tables with text/ntext</a></li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/sql-server-collation-conflicts">Collation Mismatch</a></li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/best-practice-don-t-not-cluster-on-uniqu">UniqueIdentifier with NewId</a></li>
    </ul>

    <h2>Table/Views</h2>
    <ul>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/MSSQLServer/don-t-prefix-your-table-names-with-tbl">Table Prefix</a></li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/do-not-use-spaces-or-other-invalid-chara">Table Name Problems</a></li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DataDesign/missing-foreign-key-constraints">Missing Foreign Keys</a></li>
        <li>Wide Tables</li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/best-practice-every-table-should-have-a">Tables without a primary key</a></li>
        <li>Empty Tables</li>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DataDesign/create-a-sorted-view-in-sql-server-2005--2008">Views with order by</a></li>
        <li>Unnamed Constraints</li>
    </ul>

    <h2>Indexes</h2>
    <ul>
        <li><a href="http://wiki.lessthandot.com/index.php/Finding_Fragmentation_Of_An_Index_And_Fixing_It">Fragmented indexes</a></li>
        <li><a href="http://www.jasonstrate.com/index.php/2010/06/index-those-foreign-keys/">Missing Foreign Key Indexes</a></li>
        <li>Forwarded Records</li>
    </ul>

    <h2>Configuration</h2>
    <ul>
        <li><a href="http://blogs.lessthandot.com/index.php/DataMgmt/DBProgramming/collation-conflicts-with-temp-tables-and">Database Collation</a></li>
        <li>Auto Close</li>
        <li>Auto Create</li>
        <li>Auto Shrink</li>
        <li>Auto Update</li>
        <li><a href="http://wiki.lessthandot.com/index.php/Database_compatibilty_level">Compatibility Level</a></li>
        <li>Login Language</li>
        <li>Old Backups</li>
        <li><a href="http://wiki.lessthandot.com/index.php/Fix_Orphaned_Database_Users">Orphaned Users</a></li>
        <li><a href="http://www.mssqltips.com/tip.asp?tip=1675">User Aliases</a></li>
        <li>Ad Hoc Distributed Queries</li>
        <li>CLR</li>
        <li>Database and log files on the same physical disk</li>
        <li>Database Mail</li>
        <li>Deprecated Features</li>
        <li>Instant File Initialization</li>
        <li>Max Degree of Parallelism</li>
        <li>OLE Automation Procedures</li>
        <li>Service Account</li>
        <li>SMO and DMO</li>
        <li>SQL Server Agent Service</li>
        <li>xp cmdshell</li>
    </ul>
    
    <h2>Health</h2>
    <ul>
        <li>Buffer cache hit ratio</li>
        <li>Page life expectancy</li>
    </ul>
</div>
