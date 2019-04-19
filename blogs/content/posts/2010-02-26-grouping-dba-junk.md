---
title: Using schemas to maintain order as a DBA
author: Ted Krueger (onpnt)
type: post
date: 2010-02-26T13:28:54+00:00
ID: 558
url: /index.php/datamgmt/datadesign/grouping-dba-junk/
views:
  - 8736
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - IBM DB2 Admin
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - MySQL Admin
  - Oracle Admin
tags:
  - dba
  - schema
  - sql server

---
## Chaos or order?

Managing objects in large and small installations of SQL Server can be a job in itself at times. In particular, for the DBA, objects we create on the instances we manage more often than not are found littered over the user and system databases. These objects more often are found in the master database in SQL Server. Really, why not put them there? We are the “masters” over the database server right? SSMS has this quality to it that when we connect to it, we get the master database glaring us in the face by default just like a booby. So of course that means we create our objects there. Right? 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/booby.gif" alt="" title="" width="460" height="504" /></p> 
  
  <h3>
    Who's really the booby?
  </h3>
</div>

After years managing the databases, we may find ourselves feeling just like that booby when it takes minutes, hours and sometimes even failing to find the scripts we previously created to maintain our database servers. 

Maintaining order as a DBA starts with our own messes. That's a pretty direct statement we can really dive into. SQL Server has for many versions given us the ability to manage our messes by grouping them into meaningful areas called schemas. Many times people set schemas aside and only think of them as a security method but they are much more. They take the booby out of us! 

In my own installations each instance contains a DBA database and everything I do as a DBA or Developer resides in there. To learn more about that first step in maintaining order check out, “[Instance design; Where to do your work as a DBA and DB Developer][1]“.

We can go much farther than that knowing schemas are available to us by grouping objects specific to other databases we maintain. 

Let's say we have two databases on an instance named, ERP and WMS. In our DBA database we can create schemas to match the databases such as WMS\_OBJ and ERP\_OBJ. Now when we create procedures, function, views and so on we can put them into the schema that represents the database they refer to.

```sql
CREATE PROCEDURE WMS_OBJ.GRABAWIDGET
AS
SELECT WIDGETS FROM WMS.WIDGET_TABLE
```

Without much thought we can quickly find all our objects 

```sql
SELECT * FROM INFORMATION_SCHEMA.ROUTINES 
WHERE SPECIFIC_SCHEMA = 'WMS_OBJ'
```



Quickly we see the grouping and maintenance benefits of doing this but it doesn't stop there. Once these objects are grouped in schemas, we can manage all of them as a single entity. They can be scripted to DR sites quickly, replicated, moved and a really cool point, we can authorize users to gain access to these schemas. If new team members come into your group you can quickly give them the schema rights they need to get started while maintaining the other schemas and security levels. We can transfer objects from schema to schema as well making migrations quicker and easier. 

## Take aways...

In all, schemas make us better DBAs by allowing us quicker responses to situations by having order on our database servers. Security is stronger and better managed as well. Upon connecting to SSMS, first check to see if you are in the master database. Get in the habit of working outside the master database for greater control and to leave a smaller footprint on the system side of SQL Server. Take a few minutes to look at the objects used daily, monthly or even yearly and see if they can be grouped into schemas. After the initial work of creating the schemas and moving objects to them, I think order just may be achieved.

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/instance-design-where-to-do-your-work-as