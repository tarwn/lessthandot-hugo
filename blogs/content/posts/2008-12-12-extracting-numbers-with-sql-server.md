---
title: Extracting numbers with SQL Server
author: George Mastros (gmmastros)
type: post
date: 2008-12-12T17:50:48+00:00
ID: 251
excerpt: "We all have perfectly normalized tables, with perfectly scrubbed data, right?  I wish!  Sometimes we are stuck with dirty data in legacy applications.  What's worse is that we are sometimes expected to do interesting things with dirty data.  In this blo&hellip;"
url: /index.php/datamgmt/datadesign/extracting-numbers-with-sql-server/
views:
  - 82552
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
We all have perfectly normalized tables, with perfectly scrubbed data, right? I wish! Sometimes we are stuck with dirty data in legacy applications. What's worse is that we are sometimes expected to do interesting things with dirty data. In this blog, I will show you how to extract a number from a varchar column that contains letters and numbers.

First, let's take a look at what some of that data might look like:

<span style="color:blue;">2.1 miles<br /> 4 miles<br /> Approximately 6.5 miles<br /> 3.9<br /> 7.2miles</span>

Suppose we wanted to extract just the numeric portion of the text. How would we do this? My first reaction was to use PatIndex to find the first non-numeric character. Unfortunately, this won't work because of the 3rd row (Approximately 6.5 miles). Then, I thought about CharIndex, knowing there is an optional 3rd parameter that allows us to pick the starting location for the search. Unfortunately, CharIndex doesn't allow pattern matching, and PatIndex doesn't accommodate starting somewhere other than the beginning. 

How do we do this? Well... it get's tricky.

The first thing we need to do is to find the character that is a number. For this, we can use PatIndex:

**Select PatIndex('%[0-9.-]%', Data)**

Next, Let's remove any characters from the beginning of the string, like this:

**Select SubString(Data, PatIndex('%[0-9.-]%', Data), 8000)**

This allows us to accommodate any characters that appear before the numbers. The substring result forces the numbers to the beginning of the string. Next step is to determine where the numbers end. For that, we can use PatIndex again.

**Select PatIndex('%[^0-9.-]%', SubString(Data, PatIndex('%[0-9.-]%', Data), 8000))**

PatIndex returns an integer for the first character that matches. We'll want to use this in a LEFT function, there there is a potential problem. If there's no match, PatIndex will return 0. So, we should make sure that PatIndex will always return at least one. We can trick the system by appending a character before running the PatIndex function. Like this:

**Select PatIndex('%[^0-9.-]%', SubString(Data, PatIndex('%[0-9.-]%', Data), 8000) + 'X')**

By including something we know will match the pattern, we guarantee that we get a number greater than zero from the PatIndex function.

Next step is to get the left part of the substring, which will return just the numbers. Like this:

**Select Left(SubString(Data, PatIndex('%[0-9.-]%', Data), 8000), PatIndex('%[^0-9.-]%', SubString(Data, PatIndex('%[0-9.-]%', Data), 8000) + 'X')-1)**

Now, I will admit that this is a very ugly formula to use. However, there is a high probability that it is re-usable. Because of this, it would make a nice little function to have in our SQL Arsenal.

```sql
Create Function dbo.GetNumbers(@Data VarChar(8000))
Returns VarChar(8000)
AS
Begin	
    Return Left(
             SubString(@Data, PatIndex('%[0-9.-]%', @Data), 8000), 
             PatIndex('%[^0-9.-]%', SubString(@Data, PatIndex('%[0-9.-]%', @Data), 8000) + 'X')-1)
End
```

Now, we can use this function wherever we need it. As a final step, let's test it on our sample data. Before doing this, let's think about other data that could potentially cause us problems. We should test an empty string, NULL, a string without any numbers, and a string that contains multiple numbers separated by characters.

```sql
Declare @Temp Table(Data VarChar(60))

Insert Into @Temp Values('2.1 miles')
Insert Into @Temp Values('4 miles')
Insert Into @Temp Values('Approximately 6.5 miles')
Insert Into @Temp Values('3.9')
Insert Into @Temp Values('7.2miles')
Insert Into @Temp Values('')
Insert Into @Temp Values(NULL)
Insert Into @Temp Values('No Numbers Here')
Insert Into @Temp Values('approximately 2.5 miles, but less than 3')

Select Data, dbo.GetNumbers(Data)
From   @Temp
```

The results are:

<pre><span style="color:blue;">
Data                                     Numbers
---------------------------------------- -------
2.1 miles                                2.1
4 miles                                  4
Approximately 6.5 miles                  6.5
3.9                                      3.9
7.2miles                                 7.2
                                         
NULL                                     NULL
No Numbers Here                          
approximately 2.5 miles, but less than 3 2.5
</span></pre>

I wish we could count on our data being perfect, but we really can't. The best we can do is to use the data we are given in a way that works.
