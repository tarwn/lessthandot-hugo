---
title: Delete all data in database (when you have FKs)
author: niikola
type: post
date: 2010-05-21T10:27:37+00:00
url: /index.php/datamgmt/dbprogramming/mssqlserver/delete-all-data-in-database-when-you-hav/
views:
  - 54144
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
Sometimes you have to &#8220;empty&#8221; the database, meaning you have to keep everything except data.

One way is to script &#8220;everything&#8221;, drop DB and create it again.

Another way is to delete data table by table, taking care of FK constraints, or to drop all FKs, then to remove data and at the end to restore FKs.

Here are 2 scripts for creating SQL code to:

1. a) Drop all existing FKs
     
b) Truncate tables
     
c) Create all FKs

2. Delete data from all tables in proper order (kill children first parents later method :D)

**Script 1. TRUNCATE with drop/create FKs**

<pre>SET NOCOUNT ON
GO

Select 'USE [' + db_name() +']';

Select 'ALTER TABLE ' + 
       '[' + s.name + '].[' + t.name + ']' +
       ' DROP CONSTRAINT [' + f.name +']'
  From sys.foreign_keys f
 Inner Join sys.tables t on f.parent_object_id=t.object_id
 Inner Join sys.schemas s on t.schema_id=s.schema_id
 Where t.is_ms_shipped=0;


Select 'TRUNCATE TABLE ' + '[' + s.name + '].[' + t.name + ']'      
  From sys.tables t
 Inner Join sys.schemas s on t.schema_id=s.schema_id
 Where t.is_ms_shipped=0;


Select 'ALTER TABLE ' + 
       '[' + s.name + '].[' + t.name + ']' +
       ' ADD CONSTRAINT [' + f.name + ']' +
       ' FOREIGN KEY (' +        
       Stuff( (Select ', ['+col_name(fk.parent_object_id, fk.parent_column_id) +']'
                 From sys.foreign_key_columns fk
                Where constraint_object_id = f.object_id 
                Order by constraint_column_id
                  FOR XML Path('')
            ), 1,2,'') + ')' +
       ' REFERENCES [' + 
       object_schema_name(f.referenced_object_id)+'].['+object_name(f.referenced_object_id) + '] (' +
       Stuff((Select ', ['+col_name(fc.referenced_object_id, fc.referenced_column_id)+']' 
                From sys.foreign_key_columns fc
               Where constraint_object_id = f.object_id 
               Order by constraint_column_id
                 FOR XML Path('')),
              1,2,'') +
        ')' + 
        ' ON DELETE ' + Replace(f.delete_referential_action_desc, '_', ' ')  +
        ' ON UPDATE ' + Replace(f.update_referential_action_desc , '_', ' ') collate database_default 
  From sys.foreign_keys f
 Inner Join sys.tables t on f.parent_object_id=t.object_id
 Inner Join sys.schemas s on t.schema_id=s.schema_id
 Where t.is_ms_shipped=0;</pre>

**Script 2. DELETE in proper order**

<pre>SET NOCOUNT ON
GO

Select 'USE [' + db_name() +']';
;With a as 
(
   Select 0 as lvl, 
          t.object_id as tblID 
     from sys.Tables t
    Where t.is_ms_shipped=0
      and t.object_id not in (Select f.referenced_object_id from sys.foreign_keys f)
   UNION ALL
   Select a.lvl + 1 as lvl, 
          f.referenced_object_id as tblId
     from a
    inner join sys.foreign_keys f 
       on a.tblId=f.parent_object_id 
      and a.tblID<>f.referenced_object_id
)
Select 'Delete from ['+ object_schema_name(tblID) + '].[' + object_name(tblId) + ']' 
  from a
 Group by tblId 
Order by Max(lvl),1</pre>

Note. Don&#8217;t forget to modify max column display size parameter in SSMS and preferably execute it in &#8220;result in text&#8221; mode (Ctrl-T)