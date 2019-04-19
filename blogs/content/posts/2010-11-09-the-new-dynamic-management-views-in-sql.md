---
title: The new Dynamic Management Views in SQL Server Denali
author: SQLDenis
type: post
date: 2010-11-09T16:28:40+00:00
ID: 936
excerpt: |
  With SQL Server Denali CTP1 come 20 new dynamic management views, this brings the total number of dynamic management views to 155
  
  Here is a list of the new dynamic management views, included is also the type of the dynamic management view&hellip;
url: /index.php/datamgmt/datadesign/the-new-dynamic-management-views-in-sql/
views:
  - 4333
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - ctp
  - database
  - sql server denali

---
With SQL Server Denali CTP1 come 20 new dynamic management views, this brings the total number of dynamic management views to 155

Here is a list of the new dynamic management views, included is also the type of the dynamic management view

<div class="tables">
  <table>
    <tr>
      <th>
        DMV Name
      </th>
      
      <th>
        DMV Type
      </th>
    </tr>
    
    <tr>
      <td>
        dm_db_objects_disabled_on_compatibility_level_change
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
    
    <tr>
      <td>
        dm_db_uncontained_entities
      </td>
      
      <td>
        VIEW
      </td>
    </tr>
    
    <tr>
      <td>
        dm_exec_describe_first_result_set
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
    
    <tr>
      <td>
        dm_exec_describe_first_result_set_for_object
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
    
    <tr>
      <td>
        dm_fts_index_keywords_by_property
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
    
    <tr>
      <td>
        dm_hadr_availability_group_states
      </td>
      
      <td>
        VIEW
      </td>
    </tr>
    
    <tr>
      <td>
        dm_hadr_availability_replica_states
      </td>
      
      <td>
        VIEW
      </td>
    </tr>
    
    <tr>
      <td>
        dm_hadr_database_replica_states
      </td>
      
      <td>
        VIEW
      </td>
    </tr>
    
    <tr>
      <td>
        dm_hadr_database_synchronization_states
      </td>
      
      <td>
        VIEW
      </td>
    </tr>
    
    <tr>
      <td>
        dm_hadr_instance_node_map
      </td>
      
      <td>
        VIEW
      </td>
    </tr>
    
    <tr>
      <td>
        dm_hadr_name_id_map
      </td>
      
      <td>
        VIEW
      </td>
    </tr>
    
    <tr>
      <td>
        dm_logconsumer_cachebufferrefs
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
    
    <tr>
      <td>
        dm_logconsumer_privatecachebuffers
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
    
    <tr>
      <td>
        dm_logpool_consumers
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
    
    <tr>
      <td>
        dm_logpool_hashentries
      </td>
      
      <td>
        VIEW
      </td>
    </tr>
    
    <tr>
      <td>
        dm_logpool_sharedcachebuffers
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
    
    <tr>
      <td>
        dm_logpool_stats
      </td>
      
      <td>
        VIEW
      </td>
    </tr>
    
    <tr>
      <td>
        dm_logpoolmgr_freepools
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
    
    <tr>
      <td>
        dm_logpoolmgr_respoolsize
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
    
    <tr>
      <td>
        dm_logpoolmgr_stats
      </td>
      
      <td>
        SQL_INLINE_TABLE_VALUED_FUNCTION
      </td>
    </tr>
  </table>
</div>

Unfortunately, when I downloaded this there was no documentation available yet, once documentation is available I will take a closer look at the new dynamic management views in Denali

If you would like a list of all dynamic management views in SQL Server Denali then run this query

```sql
select * from sysobjects
where name like 'dm[_]%'
```

In this post [Playing around with sys.dm\_exec\_describe\_first\_result\_set And sys.dm\_exec\_describe\_first\_result\_set\_for\_object][1] I am taking a quick look at two of those views

Click on the [SQL Server Denali][2] tag to see all our SQL Server Denali related posts

 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/playing-around-with-sys-dm_exec_describe
 [2]: /index.php/All/sql+server+denali: