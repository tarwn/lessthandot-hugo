---
title: Find Out If You Are Using Deprecated Features In SQL Server 2008
author: SQLDenis
type: post
date: 2010-01-14T16:58:41+00:00
ID: 676
url: /index.php/datamgmt/datadesign/find-out-if-you-are-using-deprecated-fea-2008/
views:
  - 32014
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dmv
  - dynamic management views
  - sql server 2005
  - sql server 2008

---
Yesterday we used the sys.dm\_os\_performance_counters dynamic management view in the post [Find Out What Percent Of The Log Is Being Used For Each Database In SQL Server 2005 and 2008][1] to find out the log space used, today we will use this dynamic management view to find out if we are using any deprecated features.

So you have upgraded your old server to SQL Server 2008 and you wonder if you have any deprecated features in your code. Well, there is a query for that, if I run this query below on one of my test servers where I have some databases that I just restored from a 2000 instance I get some results back

sql
select instance_name,cntr_value 
from sys.dm_os_performance_counters
where Object_name = 'SQLServer:Deprecated Features'
and cntr_value > 0
```

Here is the output from that query

<pre>instance_name   					cntr_value
Macedonian                                                    	2
Lithuanian_Classic                                            	2
Korean_Wansung_Unicode                                        	2
Hindi                                                         	2
Azeri_Latin_90                                                	2
Azeri_Cyrillic_90                                             	2
'@' and names that start with '@@' as Transact-SQL identifiers	20
String literals as column aliases                             	646
ROWGUIDCOL                                                    	104
PERMISSIONS                                                   	2
'::' function calling syntax                                  	5
Oldstyle RAISERROR                                            	34
CREATE_DROP_DEFAULT                                           	15
Numbered stored procedures                                    	2
fulltext_catalogs.data_space_id                               	9
fulltext_catalogs.path                                        	9
sql_dependencies                                              	5
sysprocesses                                                  	4
numbered_procedures                                           	17
syscurconfigs                                                 	1
sysdatabases                                                  	73
sysaltfiles                                                   	9
syslogins                                                     	21
sysservers                                                    	2
sysindexkeys                                                  	24
syscolumns                                                    	29
sysindexes                                                    	14
sysreferences                                                 	17
sysfilegroups                                                 	1
sysfiles                                                      	392
sysobjects                                                    	59
sysusers                                                      	7
sysdepends                                                    	3
SET ANSI_PADDING OFF                                          	68
SET CONCAT_NULL_YIELDS_NULL OFF                               	68
SET ANSI_NULLS OFF                                            	68
SET ROWCOUNT                                                  	1485
Database compatibility level 80                               	21
ALTER DATABASE WITH TORN_PAGE_DETECTION                       	2
sp_dboption                                                   	1
sp_addlogin                                                   	1
DATABASEPROPERTYEX('IsFullTextEnabled')                       	369
DATABASEPROPERTY                                              	22576
INDEX_OPTION                                                  	25
XP_API                                                        	28
USER_ID                                                       	48
DBCC SHOWCONTIG                                               	4
Table hint without WITH                                       	603
Data types: text ntext or image                               	546
More than two-part column name                                	2
NOLOCK or READUNCOMMITTED in UPDATE or DELETE                 	1566                                                                                   
</pre>

Keep in mind that I have some throw-away databases that I use to answer questions on newsgroups so some of these things that show up might be because of that.

So how does this query help you? Well, you are at least aware that you are using these deprecated features inside your code somewhere. It is time to call the object_definition function to find exactly where this happens 🙂

It could also be that internal code uses these deprecated features....wouldn't that be ironic?

You can also take a look at these blog posts by [George Mastros][2] that show you how you can find out where some of this stuff is called from

[Identify procedures that call SQL Server undocumented procedures][3]
  
[Don't use text datatype for SQL 2005 and up][4]

In case you don't have access to this dynamic management view, here is the whole list of deprcated features that this query returns

sql
select instance_name 
from sys.dm_os_performance_counters
where Object_name = 'SQLServer:Deprecated Features'
```

ALTER LOGIN WITH SET CREDENTIAL
  
SQL\_AltDiction\_CP1253\_CS\_AS
  
Macedonian
  
Lithuanian_Classic
  
Korean\_Wansung\_Unicode
  
Hindi
  
Azeri\_Latin\_90
  
Azeri\_Cyrillic\_90
  
sp\_detach\_db @keepfulltextindexfile
  
DESX algorithm
  
Vardecimal storage format
  
SET DISABLE\_DEF\_CNST_CHK
  
DEFAULT keyword as a default value
  
'@' and names that start with '@@' as Transact-SQL identifiers
  
'#' and '##' as the name of temporary tables and stored procedures
  
String literals as column aliases
  
IDENTITYCOL
  
ROWGUIDCOL
  
XMLDATA
  
COMPUTE [BY]
  
INSERT NULL into TIMESTAMP columns
  
Non-ANSI \*= or =\* outer join operators
  
FASTFIRSTROW
  
sp_configure 'ft notify bandwidth (min)'
  
sp_configure 'ft notify bandwidth (max)'
  
sp_configure 'ft crawl bandwidth (min)'
  
sp_configure 'ft crawl bandwidth (max)'
  
sp_configure 'priority boost'
  
sp_configure 'set working set size'
  
sp_configure 'open objects'
  
sp_configure 'locks'
  
sp_configure 'allow updates'
  
sp_configure 'disallow results from triggers'
  
CREATE TRIGGER WITH APPEND
  
PERMISSIONS
  
GROUP BY ALL
  
Multiple table hints without comma
  
HOLDLOCK table hint without parenthesis
  
'::' function calling syntax
  
SETUSER
  
Oldstyle RAISERROR
  
DROP INDEX with two-part name
  
Create/alter SOAP endpoint
  
CREATE\_DROP\_DEFAULT
  
CREATE\_DROP\_RULE
  
Numbered stored procedures
  
TIMESTAMP
  
dm\_fts\_memory\_buffers.row\_count
  
dm\_fts\_active\_catalogs.row\_count\_in\_thousands
  
dm\_fts\_active\_catalogs.worker\_count
  
dm\_fts\_active\_catalogs.previous\_status_description
  
dm\_fts\_active\_catalogs.previous\_status
  
dm\_fts\_active\_catalogs.status\_description
  
dm\_fts\_active_catalogs.status
  
dm\_fts\_active\_catalogs.is\_paused
  
fulltext\_catalogs.file\_id
  
fulltext\_catalogs.data\_space_id
  
fulltext_catalogs.path
  
dm\_fts\_memory_buffers
  
dm\_fts\_active_catalogs
  
fulltext_catalogs
  
endpoint_webmethods
  
soap_endpoints
  
sql_dependencies
  
sysperfinfo
  
fn_servershareddrives
  
fn_virtualservernodes
  
sysprocesses
  
syscacheobjects
  
fn\_get\_sql
  
database\_principal\_aliases
  
numbered\_procedure\_parameters
  
numbered_procedures
  
syscurconfigs
  
sysconfigures
  
sysopentapes
  
sysdevices
  
syslockinfo
  
sysdatabases
  
sysaltfiles
  
syslogins
  
sysoledbusers
  
sysremotelogins
  
sysmessages
  
sysservers
  
systypes
  
sysindexkeys
  
syscolumns
  
sysindexes
  
sysconstraints
  
sysforeignkeys
  
sysreferences
  
sysfilegroups
  
sysfiles
  
syscomments
  
sysobjects
  
sysusers
  
sysdepends
  
sysfulltextcatalogs
  
syspermissions
  
sysprotects
  
sysmembers
  
sp\_fulltext\_service @action=resource_usage
  
sp\_fulltext\_service @action=data_timeout
  
sp\_fulltext\_service @action=connect_timeout
  
sp\_fulltext\_service @action=clean_up
  
MODIFY FILEGROUP READWRITE
  
MODIFY FILEGROUP READONLY
  
UPDATETEXT or WRITETEXT
  
READTEXT
  
SET ANSI_PADDING OFF
  
SET CONCAT\_NULL\_YIELDS_NULL OFF
  
SET ANSI_NULLS OFF
  
SET REMOTE\_PROC\_TRANSACTIONS
  
SET ROWCOUNT
  
Database compatibility level 90
  
Database compatibility level 80
  
RESTORE DATABASE or LOG WITH PASSWORD
  
RESTORE DATABASE or LOG WITH MEDIAPASSWORD
  
ADDING TAPE DEVICE
  
BACKUP DATABASE or LOG TO TAPE
  
BACKUP DATABASE or LOG WITH PASSWORD
  
BACKUP DATABASE or LOG WITH MEDIAPASSWORD
  
ALTER DATABASE WITH TORN\_PAGE\_DETECTION
  
RESTORE DATABASE or LOG WITH DBO_ONLY
  
sp\_estimated\_rowsize\_reduction\_for_vardecimal
  
sp_helpdevice
  
sp_lock
  
sp_getbindtoken
  
sp_bindsession
  
sp_helpextendedproc
  
sp_dropextendedproc
  
sp_addextendedproc
  
sp\_help\_fulltext\_catalog\_components
  
sp\_help\_fulltext\_tables\_cursor
  
sp\_help\_fulltext_tables
  
sp\_help\_fulltext\_columns\_cursor
  
sp\_help\_fulltext_columns
  
sp\_help\_fulltext\_catalogs\_cursor
  
sp\_help\_fulltext_catalogs
  
sp\_fulltext\_column
  
sp\_fulltext\_table
  
sp\_fulltext\_database
  
sp\_fulltext\_catalog
  
sp\_db\_vardecimal\_storage\_format
  
sp_resetstatus
  
sp\_attach\_single\_file\_db
  
sp\_attach\_db
  
sp_dbcmptlevel
  
sp_renamedb
  
sp_indexoption
  
sp_dboption
  
sp_dbremove
  
sp\_create\_removable
  
sp\_certify\_removable
  
sp_remoteoption
  
sp_helpremotelogin
  
sp_dropremotelogin
  
sp_addremotelogin
  
sp_addserver
  
sp_depends
  
sp_dropalias
  
sp_unbindrule
  
sp_bindrule
  
sp_unbindefault
  
sp_bindefault
  
sp_droptype
  
sp_addtype
  
sp\_change\_users_login
  
sp_srvrolepermission
  
sp_dbfixedrolepermission
  
xp_loginconfig
  
sp_changeobjectowner
  
sp_droprole
  
sp_addrole
  
sp_approlepassword
  
sp_dropapprole
  
sp_addapprole
  
sp_revokedbaccess
  
sp_grantdbaccess
  
sp_dropuser
  
sp_adduser
  
sp_defaultlanguage
  
sp_defaultdb
  
sp_password
  
xp_revokelogin
  
xp_grantlogin
  
sp_revokelogin
  
sp_denylogin
  
sp_grantlogin
  
sp_droplogin
  
sp_addlogin
  
IN PATH
  
FULLTEXTSERVICEPROPERTY('ConnectTimeout')
  
FULLTEXTSERVICEPROPERTY('DataTimeout')
  
FULLTEXTSERVICEPROPERTY('ResourceUsage')
  
DATABASEPROPERTYEX('IsFullTextEnabled')
  
FULLTEXTCATALOGPROPERTY('LogSize')
  
FULLTEXTCATALOGPROPERTY('PopulateStatus')
  
DATABASEPROPERTY
  
sp_configure 'remote proc trans'
  
SET OFFSETS
  
ALL Permission
  
INSERT_HINTS
  
INDEX_OPTION
  
OLEDB for ad hoc connections
  
XP_API
  
Using OLEDB for linked servers
  
DBCC INDEXDEFRAG
  
REMSERVER
  
INDEXKEY_PROPERTY
  
USER_ID
  
FILE_ID
  
EXTPROP_LEVEL0USER
  
EXTPROP_LEVEL0TYPE
  
Returning results from trigger
  
DBCC [UN]PINTABLE
  
DBCC DBREINDEX
  
DBCC SHOWCONTIG
  
Text in row table option
  
Table hint without WITH
  
Indirect TVF hints
  
TEXTVALID
  
TEXTPTR
  
Data types: text ntext or image
  
More than two-part column name
  
Index view select list without COUNT_BIG(*)
  
NOLOCK or READUNCOMMITTED in UPDATE or DELETE 

\*** **Remember, if you have a SQL related question try our [Microsoft SQL Server Programming][5] forum or our [Microsoft SQL Server Admin][6] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/find-out-what-percent-of-the-log-is-bein-2008
 [2]: /index.php/All/?disp=authdir&author=10
 [3]: /index.php/DataMgmt/DataDesign/identify-procedures-that-call-sql-server
 [4]: /index.php/DataMgmt/DBProgramming/don-t-use-text-datatype-for-sql-2005-and
 [5]: http://forum.ltd.local/viewforum.php?f=17
 [6]: http://forum.ltd.local/viewforum.php?f=22