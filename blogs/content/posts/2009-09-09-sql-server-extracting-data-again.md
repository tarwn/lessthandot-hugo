---
title: SQL Server Extracting Data (again)
author: George Mastros (gmmastros)
type: post
date: 2009-09-09T13:06:00+00:00
ID: 554
excerpt: 'In a recent article, I explained how to extract numbers from a string.  This blog post is slightly different.  In the previous article, I show how to extract single numeric values (consecutive numbers).  In this article, I will show how to extract all v&hellip;'
url: /index.php/datamgmt/datadesign/sql-server-extracting-data-again/
views:
  - 16715
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server

---
In a recent article, I explained how to [extract numbers from a string][1]. This blog post is slightly different. In the previous article, I show how to extract single numeric values (consecutive numbers). In this article, I will show how to extract all values (non-consecutive). 

This functionality is particularly useful for formatting data (when you need to export in a consistent way). For example, phone numbers (in the U.S.) are usually 10 digits long, but some people like to use (111) 222-3333 or 111-222-3333 or even 111.222.3333. 

The idea here is to remove all characters that we don't want so that we can apply any formatting we do want.

Most of the time, when you see code like this, it will step through the data character by character. This code is different. It uses PatIndex to find the first occurrence of an invalid character. It removes that character, and then continues on until all instances of invalid characters are removed.

Please note that I am NOT claiming that this code is the fastest, or the most efficient way to accomplish this. I think the efficiency of this code depends on the nature of your data. If there are a lot of invalid characters in relatively long strings, this is probably not the right code to use. If there are few invalid characters in relatively long strings, then this code should be more efficient than looping over every character in the string.

The following code will remove non-alpha characters.

```sql
CREATE Function [dbo].[RemoveNonAlphaCharacters](@Temp VarChar(1000))
Returns VarChar(1000)
AS
Begin

	While PatIndex('%[^a-z]%', @Temp) > 0
		Set @Temp = Stuff(@Temp, PatIndex('%[^a-z]%', @Temp), 1, '')

	Return @TEmp
End
```
The following code will remove non-alpha numeric characters.
  
<span class="MT_red"><strong>** Note:</strong></span> The floowing code has changed recently based on a comment from one of the readers Andrei. Originally I had a search pattern of <span class="MT_red">[^a-z^0-9]</span>, which was incorrect. This search pattern incorrectly allowed the ^ to stay in the data when it should have been removed. The correct search pattern should be: <span class="MT_red">[^a-z0-9]</span>

```sql
CREATE Function [dbo].[RemoveNonAlphaNumericCharacters](@Temp VarChar(1000))
Returns VarChar(1000)
AS
Begin

	While PatIndex('%[^a-z0-9]%', @Temp) > 0
		Set @Temp = Stuff(@Temp, PatIndex('%[^a-z0-9]%', @Temp), 1, '')

	Return @Temp
End
```
The following code will remove non-numeric characters.

```sql
CREATE Function [dbo].[RemoveNonNumericCharacters](@Temp VarChar(1000))
Returns VarChar(1000)
AS
Begin

	While PatIndex('%[^0-9]%', @Temp) > 0
		Set @Temp = Stuff(@Temp, PatIndex('%[^0-9]%', @Temp), 1, '')

	Return @TEmp
End
```
As you can see, the real work is done by using the like comparison functionality of the PatIndex function.

'%[^a-z]%'
  
'%[^a-z0-9]%'
  
'%[^0-9]%'

It should be relatively simple to modify the previous functions to suit any sort of comparisons. You can use it to remove ranges of characters, or even for single characters (similar to the replace function).

For example, if you wanted to remove certain punctuation characters, but leave everything else, you could use '%[^.^,^-]%' (to remove dot, comma, and dash).

You can use the following code to test this functionality. It creates a table variable with some sample data. The query at the end returns the original data, and another column showing the output from the three functions presented here.

```sql
Declare @Test Table(Data VarChar(100))

Insert Into @Test Values('(111) 222-3333')
Insert Into @Test Values('111-222-3333')
Insert Into @Test Values('111.222.3333')
Insert Into @Test Values('(800) XXX-3333')
Insert Into @Test Values('')
Insert Into @Test Values(NULL)

Select	Data,
		dbo.RemoveNonAlphaCharacters(Data) As AlphaOnly,
		dbo.RemoveNonAlphaNumericCharacters(Data) As AlphaNumericOnly,
		dbo.RemoveNonNumericCharacters(Data) As NumericOnly
From	@Test
```
<pre>Data           AlphaOnly   AlphaNumericOnly NumericOnly
---------      ----------- ---------------- -----------
(111) 222-3333             1112223333       1112223333
111-222-3333               1112223333       1112223333
111.222.3333               1112223333       1112223333
(800) XXX-3333 XXX         800XXX3333       8003333

NULL           NULL        NULL             NULL
</pre>

 [1]: /index.php/DataMgmt/DataDesign/extracting-numbers-with-sql-server