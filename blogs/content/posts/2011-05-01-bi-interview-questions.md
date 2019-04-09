---
title: BI Interview Questions….
author: SQLology ~ Kim Tessereau
type: post
date: 2011-05-01T14:44:00+00:00
ID: 1153
excerpt: 'Recently I had to find a BI resource to do some ETL work for me.  I wanted to make sure I had the right person for the job so I came up with a list of interview questions that proved out to be spot on.  It really helped me weed out the folks that knew a&hellip;'
url: /index.php/datamgmt/datadesign/bi-interview-questions/
views:
  - 16605
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - SSIS

---
Recently I had to find a BI resource to do some ETL work for me.  I wanted to make sure I had the right person for the job so I came up with a list of interview questions that proved out to be spot on.  It really helped me weed out the folks that knew a lot of buzz words versus the folks that had actually worked with the different aspects of the BI ETL process.  I've provided some of the answers to a few of the questions.  You would be surprised at what little people really know when they claim to be an expert at BI.  I did find the perfect person using these questions and the project is well under way and on target! 

**Can you explain normalization?**

**First Normal Form (1NF)**

First normal form (1NF) sets the very basic rules for an organized database:

  * Eliminate duplicative columns from the same table. 
  * Create separate tables for each group of related data and identify each row with a unique column or set of columns (the primary key). 

**Second Normal Form (2NF)**

Second normal form (2NF) further addresses the concept of removing duplicative data:

  * Meet all the requirements of the first normal form. 
  * Remove subsets of data that apply to multiple rows of a table and place them in separate tables. 
  * Create relationships between these new tables and their predecessors through the use of foreign keys. 

**Third Normal Form (3NF)**

Third normal form (3NF) goes one large step further:

  * Meet all the requirements of the second normal form. 
  * Remove columns that are not dependent upon the primary key. 

 

**What is de-normalization?**

**What is a surrogate key?**

**Explain what a star schema is?**

**Explain snow flaking?**

**What features in SQL Server do you use to tune queries?**

**Are clustered index scans beneficial to an execution plan?**

**Can you explain the MERGE command and how it is used?**

Performs insert, update, or delete operations on a target table based on the results of a join with a source table. For example, you can synchronize two tables by inserting, updating, or deleting rows in one table based on differences found in the other table.

 

**Are there any performance issues with having a lot of indexes on a table?**

**What’s the difference in using a table variable and a temp table?**

**The first difference** is that transaction logs are not recorded for the table variables.  **The second** major difference is that any procedure with a temporary table cannot be pre-compiled, while an execution plan of procedures with table variables can be statically compiled in advance. Pre-compiling a script gives a major advantage to its speed of execution.  **Finally**, table variables exist only in the same scope as variables. Contrary to the temporary tables, they are not visible in inner stored procedures and in exec(string) statements.

**What are the benefits or detriments to using cursors?**

**What is foreach-loop container?** Give an example of where it can be used.

**What is a sequence container?** Give an example of where it can be used.

**What task do you use to send an email?**  Send Mail task.

**Mention a few mapping operations that the Character Map transformation supports? **

The Character Map transformation applies string functions, such as conversion from lowercase to uppercase, to character data. This transformation operates only on column data with a string data type. The Character Map transformation can convert column data in place or add a column to the transformation output and put the converted data in the new column. You can apply different sets of mapping operations to the same input column and put the results in different columns. For example, you can convert the same column to uppercase and lowercase and put the results in two different columns.

 

**Explain the functionality of: SCD transformation.**

The Slowly Changing Dimension transformation provides the following functionality for managing slowly changing dimensions:

  * Matching incoming rows with rows in the lookup table to identify new and existing rows. 
  * Identifying incoming rows that contain changes when changes are not permitted.
  * Identifying inferred member records that require updating.
  * Identifying incoming rows that contain historical changes that require insertion of new records and the updating of expired records.
  * Detecting incoming rows that contain changes that require the updating of existing records, including expired ones. 

**Explain the functionality of: Union All transformation.**

**What is the “Lookup” transformation used for?**

**What is the use of “package configurations” in SSIS?**

**What are the different ways in which configuration details can be stored?**

**How to deploy a package from development server to production server?**

**How to deploy packages to file system?**

**How to deploy packages to SQL Server? What database will packages be stored?**

 

 

 

 </p>