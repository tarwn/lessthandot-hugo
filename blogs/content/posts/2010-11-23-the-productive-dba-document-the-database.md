---
title: 'The Productive DBA: Part 2 Document the Database'
author: ptheriault
type: post
date: 2010-11-23T11:30:08+00:00
ID: 957
excerpt: How many times have you started at a new company or been given a new database that you have to code for and you have no clue how the schema relates?  How much time is wasted trying to figure out that schema?  If you're lucky you will be given a database...
url: /index.php/datamgmt/datadesign/the-productive-dba-document-the-database/
views:
  - 4668
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration

---
How many times have you started at a new company or been given a new database that you have to code for and you have no clue how the schema relates? How much time is wasted trying to figure out that schema? If you're lucky you will be given a database that has at least been developed correctly with foreign keys and a document to accompany it. If you're not lucky, you've just been handed a database with 50,000 objects that you need to document. Don't be that DBA that hands off a database to someone else without an accompanying diagram and document that defines each table and object in plain English. 

There are a few tools and methods to get this done. I'll start with the best way to document your database. First, write your definitions for each object in plain English. You must write for a non-technical audience. You never know who will end of up with your document. The best location to store each object definition is right within the database. There are multiple reasons for this, the first is that you don't have to worry about maintaining documentation in multiple locations, the second is there are some good tools that can be used to auto generate your documentation. For example SQL Doc by Red Gate can create a document in html, chm or doc format.

There are two ways to add the definitions to the database. The first way is through the SSMS GUI. For example if you want to add the description for a column you right click on the table name and select Modify. This will open the table definition in the GUI. Select the column that you are going to add the description for. In the lower window, (column properties) there will be an area to enter the description. Add you description and save your changes. I'm not really a fan of this method for two reasons, first, it's very easy to make an error and say change a datatype without meaning to. The second reason is I believe is every DBA should know the T-SQL behind the GUI. It would also be faster to create one large script and run it all in at once. In order to accomplish that you would use _sp_addextendedproperty_, see BOL for the syntax and options for this procedure. As you may have guessed, when you add a description to the database it stores them in the system table _sysproperties_. If you work at a company like mine where your database is in a constant state of development, then ask your developers to include _sp_addextendedproperty_ scripts with their DDL scripts. This will cut down on the leg work you might have to do trying to figure out the description for each column. 

Once you've completed the discovery phase and clearly documented the database objects then it's time to create a diagram. There are many tools on the market that can auto generate your diagrams for you. I think one of the easier tools to use is Visio. These tools will include table relationships IF you have created FKs in your database. If you don't have those relationships defined then the process becomes a bit more involved as you will have to go back and identify those relationships. (That would be a whole new blog on the importance of using FKs) Again, be as detailed as you can here. Make sure you include the type of relationship, is it 1 to 1, 1 to many, many to 1 or many to many. Without this information the developer(s) is at a serious disadvantage and must spend time to figure it out. Depending on the number of tables in your database it may be too large to put it all in one diagram. You may choose to diagram your db by module. For example if you work in the Insurance Industry you may have some like Quotes, Policies, Claims, Documents and WorkFlow. I would create diagram for each module with notation on how Quotes link to Policies, or Policies to Claims.

The importance of this level of documentation speaks for itself. Think how easy it would be to just hand a developer or new DBA a document and diagram to your Sales or Insurance database and say "go to work." The time saved in trying to figure everything out could add up to weeks. And that is weeks of more production from them!