---
title: Scripts to Detect Data Warehouse Issues
author: Sam Vanga
type: post
date: 2013-01-15T12:31:00+00:00
excerpt: "Standards and best practices are like flu shots you take before you're infected; Database best practices protect your databases from bad things. But, we all make mistakes. It could be because we're on a time crunch, or we're lazy (which I'm guilty of by&hellip;"
url: /index.php/webdev/business-intelligence/scripts-to-detect-data-warehouse/
views:
  - 8943
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
tags:
  - best practices
  - data warehouse
  - t-sql

---
Standards and best practices are like flu shots you take before you&#8217;re infected; Database best practices protect your databases from bad things. But, we all make mistakes. It could be because we&#8217;re on a time crunch, or we&#8217;re lazy (which I&#8217;m guilty of by the way), or maybe it&#8217;s part of being a developer.

Common mistakes include: tables without a primary key, column name problems, missing foreign keys, etc., This is where I love LTD&#8217;s very own [SQLCop][1]. I can quickly go on with my database development and rely on SQLCop to [detect the issues][2]. It saves time and ensures that database standards are met.

However, there are some issues explicit to data warehouses that SQLCop doesn&#8217;t look for. I list those issues below and provide scripts to detect them. I use these scripts in conjunction with SQLCop.

#### Detect tables in a data warehouse that aren&#8217;t prefixed with either Dim or Fact:

Tables in a warehouse are generally prefixed with Dim and Fact for dimensions and fact respectively, to easily distinguish them.

<pre>SELECT  [schema_name] = s.name ,
        table_name = t.name
FROM    sys.tables t
        INNER JOIN sys.schemas s ON s.schema_id = t.schema_id
WHERE   t.name NOT LIKE 'Dim%'
        AND t.name NOT LIKE 'Fact%'
        AND t.TYPE = 'U';</pre></p> 

#### Find tables in a data warehouse that don&#8217;t have a primary key:

Like in OLTP databases, all tables in a data warehouse also should have a primary key defined.

<pre>SELECT  schema_name = SCHEMA_NAME(schema_id) ,
        table_name = name
FROM    sys.tables
WHERE   OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0
ORDER BY SCHEMA_NAME(schema_id) ,
        name;</pre>

 

#### Detect dimension tables with a composite primary key:

A composite primary key on a dimension table causes degraded performance. It is best to create a single column primary key.

<pre>SELECT  c.TABLE_NAME ,
        COUNT(*)
FROM    INFORMATION_SCHEMA.TABLE_CONSTRAINTS pk ,
        INFORMATION_SCHEMA.KEY_COLUMN_USAGE c
WHERE   CONSTRAINT_TYPE = 'PRIMARY KEY'
        AND c.TABLE_NAME = pk.TABLE_NAME
        AND c.CONSTRAINT_NAME = pk.CONSTRAINT_NAME
        AND c.TABLE_NAME LIKE 'Dim%'
GROUP BY c.TABLE_NAME
HAVING  COUNT(*) &gt; 1</pre>

 

#### Detect dimension tables that don&#8217;t have Identity column as a primary key:

Usually, surrogate key is made the primary key of the dimension table. Surrogate key is an auto generated Identity value.

<pre>SELECT  dim_table = t.name ,
        primary_key = c.name ,
        c.is_identity
FROM    sys.tables t
        INNER JOIN sys.key_constraints kc ON t.OBJECT_ID = kc.parent_object_id
        INNER JOIN sys.indexes i ON i.OBJECT_ID = kc.parent_object_id
                                    AND i.type_desc = 'CLUSTERED'
        INNER JOIN sys.index_columns ic ON ic.OBJECT_ID = kc.parent_object_id
                                           AND ic.index_id = 1
        INNER JOIN sys.columns c ON c.OBJECT_ID = t.OBJECT_ID
                                    AND c.column_id = ic.column_id
WHERE   t.TYPE = 'U'
        AND t.name LIKE 'Dim%'
        AND kc.type_desc = 'PRIMARY_KEY_CONSTRAINT'
        AND c.is_identity = 0</pre>

 

#### Detect primary keys that don&#8217;t follow the naming convention:

<pre>SELECT  dim_table = t.name ,
        primary_key = c.name
FROM    sys.tables t
        INNER JOIN sys.key_constraints kc ON t.OBJECT_ID = kc.parent_object_id
        INNER JOIN sys.indexes i ON i.OBJECT_ID = kc.parent_object_id
                                    AND i.type_desc = 'CLUSTERED'
        INNER JOIN sys.index_columns ic ON ic.OBJECT_ID = kc.parent_object_id
                                           AND ic.index_id = 1
        INNER JOIN sys.columns c ON c.OBJECT_ID = t.OBJECT_ID
                                    AND c.column_id = ic.column_id
WHERE   t.TYPE = 'U'
        AND t.name LIKE 'Dim%'
        AND kc.type_desc = 'PRIMARY_KEY_CONSTRAINT'
        AND c.name <&gt; REPLACE(t.name, 'Dim', '') + 'Key'</pre>

 

#### Detect fact tables that have no foreign keys:

Without a foreign key, a fact table isn&#8217;t really a fact table.

<pre>SELECT table_name = t.name
		, fk_count = COUNT(*)
    FROM sys.tables t
    INNER JOIN
    sys.foreign_keys fk ON t.OBJECT_ID = fk.parent_object_id
    WHERE  t.name LIKE 'fact%'
    GROUP BY t.name
    HAVING COUNT(*) < 1  </pre>

 

#### Detect fact tables that have foreign key(s) to another fact table:

It&#8217;s unlikely to have a fact table related to another fact table.

<pre>SELECT  foreign_key = fk.name ,
        child_table = t.name ,
        parent_name = rt.name
FROM    sys.foreign_keys fk
        INNER JOIN sys.tables rt ON rt.object_id = fk.referenced_object_id
        INNER JOIN sys.tables t ON t.object_id = fk.parent_object_id
WHERE   rt.name LIKE 'Fact%'</pre>

 

#### Detect missing foreign key(s) in fact tables &#8211; Columns suffixed with Key, but don&#8217;t have foreign key constraint:

I stole the following query from [here][3] posted by [George Mastros][4], and replaced ID with Key to use it for data warehouse scenario.

<pre>SELECT  C.TABLE_SCHEMA,C.TABLE_NAME,C.COLUMN_NAME
    FROM    INFORMATION_SCHEMA.COLUMNS C          
            INNER Join INFORMATION_SCHEMA.TABLES T            
              ON C.TABLE_NAME = T.TABLE_NAME    
              And T.TABLE_TYPE = 'Base Table'
              AND T.TABLE_SCHEMA = C.TABLE_SCHEMA        
            LEFT Join INFORMATION_SCHEMA.CONSTRAINT_COLUMN_USAGE U            
              ON C.TABLE_NAME = U.TABLE_NAME            
              And C.COLUMN_NAME = U.COLUMN_NAME
              And U.TABLE_SCHEMA = C.TABLE_SCHEMA
    WHERE   U.COLUMN_NAME IS Null          
            And C.COLUMN_NAME Like '%Key'
    ORDER BY C.TABLE_SCHEMA, C.TABLE_NAME, C.COLUMN_NAME</pre>

 

Results of above queries aren&#8217;t always issues. They are just rare, you&#8217;ve to look at them closely and make sure there is a reason for each choice. Also, you may use different naming conventions that make these queries void. In that case, I hope you&#8217;re able to alter them to your needs.

Follow me on Twitter! @[SamuelVanga][5]

 [1]: http://sqlcop.ltd.local/ "SQLCop"
 [2]: http://sqlcop.ltd.local/detectedissues.php "SQLCop detects these issues"
 [3]: /index.php/DataMgmt/DataDesign/missing-foreign-key-constraints "missing foreign keys sql cop"
 [4]: /index.php/All/?disp=authdir&author=10 "George M"
 [5]: /twitter.com/SamuelVanga "SamuelVanga Twitter"