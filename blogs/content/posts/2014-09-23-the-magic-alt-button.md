---
title: The Magic Alt Button
author: Koen Verbeeck
type: post
date: 2014-09-23T13:39:54+00:00
ID: 2967
url: /index.php/datamgmt/dbprogramming/mssqlserver/the-magic-alt-button/
views:
  - 12521
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - SSIS
tags:
  - block
  - selecting text
  - sql
  - sql server
  - ssis
  - syndicated
  - tsql

---
<p style="text-align: justify">
  Lately I have been using more and more the awesomeness of the Alt-button in SQL Server Management Studio (SSMS). What do I mean with this? While holding the Alt-button on your keyboard, you can select a block of text instead of lines of text.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/09/blockselected.jpg"><img class="alignnone wp-image-2976" src="/wp-content/uploads/2014/09/blockselected-243x300.jpg" alt="blockselected" width="308" height="381" srcset="/wp-content/uploads/2014/09/blockselected-243x300.jpg 243w, /wp-content/uploads/2014/09/blockselected.jpg 571w" sizes="(max-width: 308px) 100vw, 308px" /></a>
</p>

<p style="text-align: justify">
  Note that this is not an exclusive feature, you can do this in some other applications as well (like Word and Notepad++). An alternative is holding Alt+Shift and moving the cursor with the arrow keys. This can be useful for example when working on a laptop without a mouse present and you don't really trust yourself holding Alt, the select button and moving the touchpad at the same time. This alternative doesn't work in every application though. In Word this keyboard shortcut is used to move the current paragraph.
</p>

<p style="text-align: justify">
  Anyway, what makes selecting blocks of code so magic and awesome? Because I can for example create a SQL update statement for a dimension using a staging table super quickly. Allow me to illustrate.
</p>

<p style="text-align: justify">
  Suppose I want to create an update statement to update the Employee dimension of the AdventureWorks database. The source data is located in a staging table with almost the exact schema of the target table. Typically this staging table is populated with update data coming from an incremental load SSIS package. Using a staging table and an update statement in an Execute SQL Task allows us to get rid of the OLE DB Command, which issues updates row by row in SSIS. Which we do not want. Anyway, I digress. Let's assume the staging table has the following schema:
</p>

```sql
USE [AdventureWorksDW2012]
GO

CREATE TABLE [dbo].[Upd_DimEmployee](
	[EmployeeKey] [int] NOT NULL,
	[SalesTerritoryKey] [int] NULL,
	[FirstName] [nvarchar](50) NOT NULL,
	[LastName] [nvarchar](50) NOT NULL,
	[MiddleName] [nvarchar](50) NULL,
	[NameStyle] [bit] NOT NULL,
	[Title] [nvarchar](50) NULL,
	[HireDate] [date] NULL,
	[BirthDate] [date] NULL,
	[LoginID] [nvarchar](256) NULL,
	[EmailAddress] [nvarchar](50) NULL,
	[Phone] [nvarchar](25) NULL,
	[MaritalStatus] [nchar](1) NULL,
	[EmergencyContactName] [nvarchar](50) NULL,
	[EmergencyContactPhone] [nvarchar](25) NULL,
	[SalariedFlag] [bit] NULL,
	[Gender] [nchar](1) NULL,
	[PayFrequency] [tinyint] NULL,
	[BaseRate] [money] NULL,
	[VacationHours] [smallint] NULL,
	[SickLeaveHours] [smallint] NULL,
	[CurrentFlag] [bit] NOT NULL,
	[SalesPersonFlag] [bit] NOT NULL,
	[DepartmentName] [nvarchar](50) NULL,
	[StartDate] [date] NULL,
	[EndDate] [date] NULL,
	[Status] [nvarchar](50) NULL,
	[EmployeePhoto] [varbinary](max) NULL);
```<p style="text-align: justify">
  This update table has all the updateable columns of the Employee dimension and the surrogate key, but not the business key (since it won't change anyway). The surrogate key does not have the IDENTITY property, as it will be retrieved from the Employee dimension with a Lookup component in the SSIS package.
</p>

<p style="text-align: justify">
  OK, now let's create the update statement. We start with the list of updateable columns from the dimension, the UPDATE clause and the FROM and JOIN clauses.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/09/update_statement_01.jpg"><img class="alignnone size-medium wp-image-2977" src="/wp-content/uploads/2014/09/update_statement_01-268x300.jpg" alt="update_statement_01" width="268" height="300" srcset="/wp-content/uploads/2014/09/update_statement_01-268x300.jpg 268w, /wp-content/uploads/2014/09/update_statement_01.jpg 581w" sizes="(max-width: 268px) 100vw, 268px" /></a>
</p>

<p style="text-align: justify">
  Thanks to the surrogate key <em>EmployeeKey</em> we can easily join the Employee dimension and its staging table. First select a line after the columns while holding the Alt button.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/09/update_statement_02.jpg"><img class="alignnone size-medium wp-image-2978" src="/wp-content/uploads/2014/09/update_statement_02-273x300.jpg" alt="update_statement_02" width="273" height="300" srcset="/wp-content/uploads/2014/09/update_statement_02-273x300.jpg 273w, /wp-content/uploads/2014/09/update_statement_02.jpg 589w" sizes="(max-width: 273px) 100vw, 273px" /></a>
</p>

<p style="text-align: justify">
  This is easy with the mouse and you don't even need a cursor position to start from. You can select literally every block you want. If you want to do this with the keyboard only, you need to tab to the position you want and then start selecting.
</p>

<p style="text-align: justify">
  And now comes the awesome part. With the single line still selected, you can start typing at all the lines at once.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/09/update_statement_03.jpg"><img class="alignnone size-medium wp-image-2979" src="/wp-content/uploads/2014/09/update_statement_03-300x237.jpg" alt="update_statement_03" width="300" height="237" srcset="/wp-content/uploads/2014/09/update_statement_03-300x237.jpg 300w, /wp-content/uploads/2014/09/update_statement_03.jpg 827w" sizes="(max-width: 300px) 100vw, 300px" /></a>
</p>

<p style="text-align: justify">
  Great isn't it? Now select all of the columns with the Alt button and copy them.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/09/update_statement_04.jpg"><img class="alignnone size-medium wp-image-2980" src="/wp-content/uploads/2014/09/update_statement_04-276x300.jpg" alt="update_statement_04" width="276" height="300" srcset="/wp-content/uploads/2014/09/update_statement_04-276x300.jpg 276w, /wp-content/uploads/2014/09/update_statement_04.jpg 590w" sizes="(max-width: 276px) 100vw, 276px" /></a>
</p>

<p style="text-align: justify">
  Move your cursor to the first line, right after “= u.” and simply paste the columns.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/09/update_statement_05.jpg"><img class="alignnone size-medium wp-image-2981" src="/wp-content/uploads/2014/09/update_statement_05-268x300.jpg" alt="update_statement_05" width="268" height="300" srcset="/wp-content/uploads/2014/09/update_statement_05-268x300.jpg 268w, /wp-content/uploads/2014/09/update_statement_05.jpg 571w" sizes="(max-width: 268px) 100vw, 268px" /></a>
</p>

<p style="text-align: justify">
  That was created pretty fast, right? A lot faster than typing everything, even with Intellisense. Of course, we want an efficient update statement, so we'll add a WHERE clause with the same techniques that will check if anything has changed at all.
</p>

<p style="text-align: justify">
  Add the WHERE clause and paste the list of columns again.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/09/update_statement_06.jpg"><img class="alignnone size-medium wp-image-2982" src="/wp-content/uploads/2014/09/update_statement_06-286x300.jpg" alt="update_statement_06" width="286" height="300" srcset="/wp-content/uploads/2014/09/update_statement_06-286x300.jpg 286w, /wp-content/uploads/2014/09/update_statement_06.jpg 584w" sizes="(max-width: 286px) 100vw, 286px" /></a>
</p>

<p style="text-align: justify">
  Move the column list to the right and add the keyword OR and the table alias before every line (except the first) with the <em>type-on-every-line</em> trick.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/09/update_statement_07.jpg"><img class="alignnone size-medium wp-image-2973" src="/wp-content/uploads/2014/09/update_statement_07-280x300.jpg" alt="update_statement_07" width="280" height="300" srcset="/wp-content/uploads/2014/09/update_statement_07-280x300.jpg 280w, /wp-content/uploads/2014/09/update_statement_07.jpg 573w" sizes="(max-width: 280px) 100vw, 280px" /></a>
</p>

<p style="text-align: justify">
  Now select a line after the columns and add inequality operator and the other table alias.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/09/update_statement_08.jpg"><img class="alignnone size-medium wp-image-2974" src="/wp-content/uploads/2014/09/update_statement_08-273x300.jpg" alt="update_statement_08" width="273" height="300" srcset="/wp-content/uploads/2014/09/update_statement_08-273x300.jpg 273w, /wp-content/uploads/2014/09/update_statement_08.jpg 565w" sizes="(max-width: 273px) 100vw, 273px" /></a>
</p>

<p style="text-align: justify">
  Simply paste the column list again at the end.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/09/update_statement_09.jpg"><img class="alignnone size-medium wp-image-2975" src="/wp-content/uploads/2014/09/update_statement_09-296x300.jpg" alt="update_statement_09" width="296" height="300" srcset="/wp-content/uploads/2014/09/update_statement_09-296x300.jpg 296w, /wp-content/uploads/2014/09/update_statement_09.jpg 640w" sizes="(max-width: 296px) 100vw, 296px" /></a>
</p>

<p style="text-align: justify">
  Don't forget the semicolon at the end! To really top it off, we should include ISNULL functions everywhere to account for NULL values, but I'll leave that as an exercise for the reader 😉
</p>