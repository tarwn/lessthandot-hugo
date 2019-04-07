---
title: SQL Server Proper Case Function
author: George Mastros (gmmastros)
type: post
date: 2010-02-24T11:34:43+00:00
ID: 710
excerpt: "SQL Server (T-SQL specifically) is not usually the best place to write a function for modifying the case of your data. String functions are generally slow and often a bit cumbersome to implement.  That being said, it's not uncommon to have data in your&hellip;"
url: /index.php/datamgmt/dbprogramming/sql-server-proper-case-function/
views:
  - 25049
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
SQL Server (T-SQL specifically) is not usually the best place to write a function for modifying the case of your data. String functions are generally slow and often a bit cumbersome to implement. That being said, it&#8217;s not uncommon to have data in your tables that needs to be &#8220;cleaned up a bit&#8221;. In situations like this, it is acceptable to write and use a function like this.

With SQL, it is easy to convert strings to upper case or lower case, but what about making it mixed case/title case. This functionality is useful in many different situations. There are many examples on the internet that show how this can be done. Some work better than others, and perform better than others. The simplest versions of this will loop through a string, character by character, and capitalize anything following a space. Unfortunately, this is not always adequate.

I have developed a method that actively seeks out any character that is not a letter. Under certain circumstances, this is more appropriate because it satisfies more situations. This method also reduces the number of loops that need to be executed by using the PatIndex function and looking for non-alpha characters followed by lower case alpha characters.

**General theory:**

Like I stated earlier, this code actively seeks out data from within the string that needs to be changed. The first thing we do is convert the string to lower case except for the first character, which is converted to upper case. For example, suppose you have this: THIS IS-ALL &#8220;Caps&#8221;

At the beginning of the function, we convert to lower case except for the first character, so it becomes:

This is-all &#8220;caps&#8221;

Next, we actively seek out positions where one character is non-alphabetic followed by a lower case alphabetic character. In this case, we would return character position 5 (space followed by lower case i). We then use the stuff function to convert this to upper case. This would result in:

This Is-all &#8220;caps&#8221;

We then repeat this process until there are no more occurrences of non-alpha followed by lower case alpha characters.

This code uses several functions, some that you may not be familiar with. Upper converts characters to upper case. Lower converts characters to lower case. SubString allows you to retrieve data from the middle of string. The less common functions here are PatIndex and Stuff.

PatIndex searches through a string and returns the position where a search pattern is found. In our case, we want to find a &#8220;non-alpha character followed by a lower case alpha character&#8221;. By default, searches are not case sensitive. This is actually controlled by the collation of your database. You can get case sensitive searches by using a binary collation. For this function, I use:

PatIndex(&#8216;%\[^a-zA-Z\]\[a-z\]%&#8217;, @Data COLLATE Latin1\_General\_Bin)

The other function I use here is stuff. Stuff can be used to insert text in to the middle of a string. It can also be used similar to a Replace function. With replace, you essentially replace all occurrences of one string with another. Stuff is different in that you can replace data based on its position within a string without regard to what is at that position. For example:

Select Stuff(&#8216;lower case&#8217;, 7, 1, &#8216;C&#8217;)

Notice how the 7th character (the lower case c) is replaced with an upper case C.

sql
Create Function dbo.Proper(@Data VarChar(8000))
Returns VarChar(8000)
As
Begin
  Declare @Position Int

  Select @Data = Stuff(Lower(@Data), 1, 1, Upper(Left(@Data, 1))),
         @Position = PatIndex('%[^a-zA-Z][a-z]%', @Data COLLATE Latin1_General_Bin)

  While @Position > 0
    Select @Data = Stuff(@Data, @Position, 2, Upper(SubString(@Data, @Position, 2))),
           @Position = PatIndex('%[^a-zA-Z][a-z]%', @Data COLLATE Latin1_General_Bin)

  Return @Data
End
```
In my opinion, there are several things that make this function better. It will correctly capitalize all words and it minimizes the number of loops. In fact, it will loop just once for each word that needs to be capitalized.