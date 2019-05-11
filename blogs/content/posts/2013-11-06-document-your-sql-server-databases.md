---
title: Document Your SQL Server Databases with Extended Properties
author: Jes Borland
type: post
date: 2013-11-06T15:08:00+00:00
ID: 2193
excerpt: 'How important is database documentation to you? I know you have a hundred "more important" things to do. But consider this: how much time is it going to take you to document the purpose of a constraint right now? How much time will you spend trying to r&hellip;'
url: /index.php/datamgmt/datadesign/document-your-sql-server-databases/
views:
  - 61132
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
How important is database documentation to you? I know you have a hundred "more important" things to do. But consider this: how much time is it going to take you to document the purpose of a constraint right now? How much time will you spend trying to remember that purpose in seven months when a developer has a question about it? Documenting a database and its objects when they are created can save you hours of work in the long run.

There are many ways to create and store documentation. You could create a spreadsheet for each database, with information about each table, view, and stored procedure – and update it manually. You could buy a third party product and use it – but then you're paying for a method to track this information. Or you could use a built-in tool in SQL Server to add the information, and use Reporting Services to display it.

### **Extended Properties** 

Many objects in SQL Server – tables, filegroups, triggers, and more – have a mechanism for you to add properties to them beyond the built-in information such as names. **Extended properties** allow you to customize the information, storing the data within the database itself. When you need to retrieve the information, you simply query it.

Extended properties have been available since at least SQL Server 2000. They're available in all editions – this isn't an Enterprise Edition only feature. You can use them on databases you've created, and in third party databases – they don't modify the data structure at all.

How cool is that? All you need is a little knowledge on how to get the data in and how to get it out, and you'll be all set.

### **To What Can You Add Extended Properties?** 

You can add extended properties to many – but not all – objects in SQL Server. Common items with the functionality are the database, schemas, tables, views, columns, constraints, functions, parameters, and triggers. Less-used items such as service broker queues, partition functions and schemes, and plan guides can also be documented! The list of items you can add properties to is shown in Figure 1.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/xp hierarchy.JPG?mtime=1383748570" alt="" />
</p>

<p style="text-align: center;">
  <em>Figure 1: Extended Properties hierarchy</em>
</p>

### **Use Cases** 

Let's start with the database itself – you could add an "Application" property with the name of the application it's tied into.  For each schema in the database, you can document the purpose of it – was it to support application functionality, for readability, or for security? In functions and procedures, comments should explain the "why", but properties could be added to document the data types and default values for parameters – just in case they accidentally get changed. If you have a column with a default constraint or a default value, document what it is and why if appropriate. If there is a trigger, document why that's necessary.

These are just a few examples of the "why". Let's look at the "how".

### Security

Not just anyone can add or modify extended properties. Any users in the db\_owner role can add and make any changes. Users in the ddl\_admin role can add or update properties on objects, but not the database, users, or roles. Also, if a user owns an object or has ALTER or CONTROL permissions on an object, they can add or update properties. Be careful with permissions – even db_owner can be a higher level of permission than you wish to assign to some users.

### **Creating Extended Properties** 

To add extended properties to an object, you'll use the stored procedure [sp_addextendedproperty][1]. There are four major parameters to this procedure – name, value, level type, and level name. Name is just that – a name to describe the property. The value is the description – what is this property for, and why are you adding it? The level type is a _pre-defined value_ – refer back to Figure 1 for the values available. The level name is the _specific object_ you are adding the property to – a plan guide name, a table name, an index name.

Let's review the concept of levels – 0, 1, and 2. The highest-level property is the database. All other properties fall under this. Some properties are at level 0 – they are the top of the food chain, and aren't tied to anything except the database. Schemas, certificates, triggers, and plan guides are just a few examples. Then properties become nested. Tables and views are level 1 properties, under schema. A few properties even are at level 2 – column is level 2, under table and view, for example. Again,  reference the hierarchy in Figure 1 for complete details.

The syntax from [Books Online][1] is:

```sql
sp_addextendedproperty
    [ @name = ] { 'property_name' }
    [ , [ @value = ] { 'value' } 
        [ , [ @level0type = ] { 'level0_object_type' } 
          , [ @level0name = ] { 'level0_object_name' } 
                [ , [ @level1type = ] { 'level1_object_type' } 
                  , [ @level1name = ] { 'level1_object_name' } 
                        [ , [ @level2type = ] { 'level2_object_type' } 
                          , [ @level2name = ] { 'level2_object_name' } 
                        ] 
                ]
        ] 
    ] 
[;]
```
 

That's ugly, but let's walk through it. First are the name and value. You'll add these for every property. Then, you see a type and name for each level – 0, 1, and 2. If you add a level 0 property, you don't add any information for 1 or 2. If you add a level 1 property, you add the level 0 and level 1 information. If you want a level 2 property, you enter information for all three levels.

Let's add a property to the highest level, the database.

```sql
EXEC sp_addextendedproperty 
@name = N'Purpose', 
@value = 'Holds employee, customer, supplier, and order details for the company.';
```

 

Note that I only used the @name and @value parameters. There are no level types or names. I'm documenting the purpose of the database.

Let's add a property to a level 0 – a schema.

```sql
EXECUTE sys.sp_addextendedproperty 
@name = N'Purpose',
@value = N'Holds info for Jes.',
	@level0type = N'SCHEMA', 
	@level0name = 'Jes';
```
 

I find it easiest to start at the bottom to read this. The property is for a Schema, and the schema is Jes.  This property documents the purpose of the schema – it holds information for one user, Jes.

Let's add to a level 1 – a table.

```sql
EXECUTE sys.sp_addextendedproperty 
@name = N'Purpose',
@value = N'Holds running data.',
	@level0type = N'SCHEMA', 
	@level0name = 'Jes', 
		@level1type = N'Table', 
		@level1name = 'Running';
```
 

Let's start at the bottom. I'm documenting a Table, which is named Running. This is under the type Schema, and the schema name is Jes. I'm documenting the purpose of the table – to hold running data for this user, Jes.

Now let's add to a level 2, column.

```sql
EXECUTE sys.sp_addextendedproperty 
@name = 'Measurement', 
@value = 'Distance run measured in miles.', 
	@level0type = 'SCHEMA', 
	@level0name = 'Jes', 
		@level1type = 'Table', 
		@level1name = 'Running', 
			@level2type = 'Column', 
			@level2name = 'RunDistance';
```
 

Reading up from the bottom, again: this property is for a Column named RunDistance. It's in the Table Running, which is in the Schema Jes. I'm documenting the measurement, since there are different options – in my case the distance is in miles.

You should now understand the hierarchy of sp_addextendedproperty. Once you've added properties, though, how do you updated, remove, or extract the information?

### **Updating Extended Properties** 

What if something changes? Maybe a default value changes, or you want to add more information. You can execute [sp_updateextendedproperty][2] to update the information. This is very similar to adding – pass in the property name and the updated value, then the appropriate levels.

Let's say I want to be more specific about the purpose of the schema Jes. I can update the 'Purpose' property easily.

```sql
exec sp_updateextendedproperty 
@name = N'Purpose',
@value = N'Holds tables created by user Jes.',
	@level0type = N'SCHEMA', 
	@level0name = 'Jes';
```
 

### **Deleting Extended Properties** 

What if you decide an extended event has outlived its purpose or for some reason need to remove it? You can execute [sp_dropextendedproperty][3], passing in the same parameters as before.

In this example, I've added a property, Purpose, to Jes.Running.RunDistance that I want to remove.

```sql
exec sp_dropextendedproperty 
	@name = 'Purpose', 
		@level0type = 'SCHEMA', 
		@level0name = 'Jes', 
			@level1type = 'Table', 
			@level1name = 'Running', 
				@level2type = 'Column', 
				@level2name = 'RunDistance';
```
 

Executing this removes the property from the database.

### **Viewing Extended Properties** 

Once you've decided what you're going to document and you add the information, you'll want a way to look it up! There are two main methods – executing [fn_listextendedproperty][4] and querying [sys.extended_properties][5]. I prefer the latter method, as it allows you to join the results with other catalog views to get more information, and  we  will focus on this method.

The view returns a class, class description, major ID, minor ID, name, and value. Name and value are the easiest to remember – they are the same values you entered in sp_addextendedproperty.

Then things start getting a little different. Most of the level 0 properties tie into a specific class. Rather than querying levels to find objects – the way we added the properties – we query for classes. To view database-level extended properties, we look for class 0. To view plan guide properties, we look for class 27. (A full list can be found [here][5].) Then details about level 1 and level 2 objects are found by using the major ID and minor ID.

For example, I want to view the extended properties for an index on a table in a specific schema. In my WHERE clause, I would set the class = 7. To find the table name, I'd join sys.extended\_properties.major\_id to sys.tables.object\_id. To find the index name, I'd join sys.extended\_properties.major\_id to sys.indexes.object\_id and sys.extended\_properties.minorid to sys.indexes.index\_id.

Let's look at this by using only the information available in sys.extended_properties.

```sql
SELECT EP.class_desc AS PropertyOn, 
	DB_NAME() AS DatabaseName, 
	EP.name AS ExtendedPropertyDescription, 
	EP.value AS ExtendedPropertyValue 
FROM sys.extended_properties AS EP 
WHERE EP.name <> 'MS_Description' 
	AND EP.class = 7; 
```
<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/xp 1.JPG?mtime=1383748570" alt="" />
</p>

<p style="text-align: center;">
  <em>Figure 2: sys.extended_properties results</em>
</p>

The information returned is very basic. I can tell this property is for an index, but I can't tell for which table or index. I can join this view to others to get more information.

```sql
SELECT EP.class_desc AS PropertyOn, 
	DB_NAME() AS DatabaseName, 
	SCH.name AS SchemaName , 
	TBL.name AS TableName, 
	IND.name AS IndexName, 
	EP.name AS ExtendedPropertyDescription, 
	EP.value AS ExtendedPropertyValue 
FROM sys.extended_properties AS EP 
	LEFT JOIN sys.tables TBL ON TBL.object_id = EP.major_id 
	LEFT JOIN sys.schemas SCH ON SCH.schema_id = TBL.schema_id 
	LEFT JOIN sys.indexes IND ON IND.object_id = TBL.object_id 
		AND IND.index_id = EP.minor_id
WHERE EP.name <> 'MS_Description' 
	AND EP.class = 7;  
```
 

The results are much more comprehensive now.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/xp 2.JPG?mtime=1383748570" alt="" />
</p>

<p style="text-align: center;">
  <em>Figure 3: Joining sys.extended_properties to other views for better results</em>
</p>

Let's look at a few other examples. (Note that our end goal is to have "one query to rule them all" – I want to be able to run one general query to get the results into an SSRS report. Thus, there will be some NULL columns.)

I added some database-level properties. To query those, I look for class 0. Note that I'm not joining it to any other views – all the information is contained in sys.extended_properties.

```sql
SELECT EP.class_desc AS PropertyOn, 
	DB_NAME() AS DatabaseName, 
	NULL AS SchemaName , 
	NULL AS TableName, 
	NULL AS ColumnName, 
	NULL AS IndexName, 
	NULL AS ProcedureName, 
	NULL AS ParameterName,
	EP.name AS ExtendedPropertyDescription, 
	EP.value AS ExtendedPropertyValue
FROM sys.extended_properties AS EP 
WHERE EP.name <> 'MS_Description' 
	AND EP.class = 0; 
```<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/xp 3.JPG?mtime=1383748570" alt="" />
</p>

<p style="text-align: center;">
  <em>Figure 4: database results</em>
</p>

I've documented the schemas in the database – remember, these were level 0. To get the properties, I'll query class 3. Here, I want to join to sys.schemas to get the schema name.

```sql
SELECT EP.class_desc AS PropertyOn, 
	DB_NAME() AS DatabaseName, 
	SCH.name AS SchemaName , 
	NULL AS TableName, 
	NULL AS ColumnName, 
	NULL AS IndexName, 
	NULL AS ProcedureName, 
	NULL AS ParameterName, 
	EP.name AS ExtendedPropertyDescription, 
	EP.value AS ExtendedPropertyValue
FROM sys.extended_properties AS EP 
	LEFT JOIN sys.schemas SCH ON SCH.schema_id = EP.major_id 
WHERE EP.name <> 'MS_Description' 
	AND EP.class = 3; 
```<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/xp 4.JPG?mtime=1383748570" alt="" />
</p>

<p style="text-align: center;">
  <em>Figure 5: schema results</em>
</p>

I also documented table information – this was a level 1 property, and is a class 1 – and column information, which is a level 2. Much like my index query above, I join to the sys.tables, sys.schemas, and sys.columns view to get additional information about the table and columns.

```sql
SELECT EP.class_desc AS PropertyOn, 
	DB_NAME() AS DatabaseName, 
	SCH.name AS SchemaName , 
	TBL.name AS TableName, 
	COL.name AS ColumnName, 
	NULL AS IndexName, 
	NULL AS ProcedureName, 
	NULL AS ParameterName, 
	EP.name AS ExtendedPropertyDescription, 
	EP.value AS ExtendedPropertyValue 
FROM sys.extended_properties AS EP 
	LEFT JOIN sys.tables TBL ON TBL.object_id = EP.major_id 
	LEFT JOIN sys.schemas SCH ON SCH.schema_id = TBL.schema_id 
	LEFT JOIN sys.columns COL ON COL.object_id = TBL.object_id 
		AND COL.column_id = EP.minor_id
WHERE EP.name <> 'MS_Description' 
	AND EP.class = 1;  
```<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/xp 5.JPG?mtime=1383748571" alt="" />
</p>

<p style="text-align: center;">
  <em>Figure 6: table and column information</em>
</p>

The hardest part of using extended properties is understanding what class, major ID, and minor ID stand for. As  you use them more, it will become much easier to understand, and I hope that these examples help as you document your databases.

### **What's Next?** 

Having the information in your database is great – but it's only the start. You'll want to be able to easily pull the information when needed. You could write a series of queries, save the .sql files, and run them over and over again. But that isn't going to help other people in your organization – the junior DBA who isn't sure which table to modify, the developer who needs to understand why a certain index was added, or the analyst who is trying to understand the database layout to write reports.

But what if you could write these queries and add them to an SSRS report?

 [1]: http://technet.microsoft.com/en-us/library/ms180047.aspx
 [2]: http://technet.microsoft.com/en-us/library/ms186989(v=sql.105).aspx
 [3]: http://technet.microsoft.com/en-us/library/ms178595.aspx
 [4]: http://technet.microsoft.com/en-us/library/ms179853(v=sql.105).aspx
 [5]: http://technet.microsoft.com/en-us/library/ms177541.aspx