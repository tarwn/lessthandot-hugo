---
title: Sometimes I don’t want to be DRY
author: Christiaan Baes (chrissie1)
type: post
date: 2010-09-29T08:25:01+00:00
ID: 911
url: /index.php/desktopdev/mstech/sometimes-i-don-t-want-to-be-dry/
views:
  - 2406
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - vb.net

---
## Introduction

We all know that DRY (Don&#8217;t repeat yourself) is a very important mantra for a developer. But like all things there are some exceptions. In the following case I knew I was kinda repeating myself but I can&#8217;t care less.

## The database side

Time to go look in the database (yuck, I know). There is no reason why you should not normalize your data when you work with an RDBMS (of course there are plenty of reasons why you would not do it, but bear with me). Perhaps in this day and age this a bit of over optimization but the smaller the pages the less IO you would get (I learned that from my fellow LTD people).

So I have this table 

```tsql
CREATE TABLE Season
( Id TinyInt PRIMARY KEY
, Name varchar(10))```
And I have 5 (yes 5) rows in that table.

<code class="codespan">Id  Name&lt;/p>
&lt;p>0   None&lt;br />
1   Spring&lt;br />
2   Summer&lt;br />
3   Autumn&lt;br />
4   Winter</code>

The important thing is that this will be used in a many to many relationship.

## The Code

In my aggregate root I would always have an ISet of Season objects since the rules are.

  * No season
  * 1 or a combination of seasons
  * Each season will only appear once in the list

Now on the code side I have several options. My Season Domain Object could look like this. 

```vbnet
Public Class Season
  Public ReadOnly Property Id as Int16
  Public ReadOnly Property Name as String
End Class```
That would just be a straight translation of what is in the database.

Or like this

```vbnet
Public Class Season
  Public ReadOnly Property Id as Int16
  Public ReadOnly Property Name as Season
  
  Public Enum SeasonEnum
    None = 0
    Spring = 1
    Summer = 2
    Autumn = 3
    Winter = 4
  End Enum
  
End Class```
That would already violate DRY since the enum is already repeating what I have in the database.

Or I could just have an ISet of those enums and not have a separate object either way.

## Conclusion

I think that the ISet of enums is the way to go here.
  
Because I&#8217;m pretty sure the seasons aren&#8217;t going to change any time soon.
  
But some might even say I just need to stop using the overnormalized version of that database and just use the string version and get rid of the extra table.

What do you think?

<span class="MT_smaller">Only 2 more posts until the 250th and final post. Actually only 1 more since post 250 is already written 😉</span>