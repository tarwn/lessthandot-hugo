---
title: How to search for data with underscores or brackets in SQL Server?
author: SQLDenis
type: post
date: 2012-05-02T14:07:00+00:00
excerpt: |
  You can search for data in your tables by using the LIKE operator. The LIKE operator determines whether a specific character string matches a specified pattern. A pattern can include regular characters and wildcard characters.
  
  Here is what Book On Li&hellip;
url: /index.php/datamgmt/datadesign/how-to-search-for-data/
views:
  - 29339
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - like
  - regular expressions
  - s ql server 2005
  - sql server 2000
  - sql server 2008
  - sql server 2012

---
You can search for data in your tables by using the LIKE operator. The LIKE operator determines whether a specific character string matches a specified pattern. A pattern can include regular characters and wildcard characters.

Here is what Books On Line has on the use of wild cards

<div class="tables">
  <table>
    <tr>
      <th>
        <p>
          Wildcard character
        </p>
      </th>
      
      <th>
        <p>
          Description
        </p>
      </th>
      
      <th>
        <p>
          Example
        </p>
      </th>
    </tr>
    
    <tr>
      <td>
        <p>
          %
        </p>
      </td>
      
      <td>
        <p>
          Any string of zero or more characters.
        </p>
      </td>
      
      <td>
        <p>
          WHERE title LIKE &#8216;%computer%&#8217; finds all book titles with the word &#8216;computer&#8217; anywhere in the book title.
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          _ (underscore)
        </p>
      </td>
      
      <td>
        <p>
          Any single character.
        </p>
      </td>
      
      <td>
        <p>
          WHERE au_fname LIKE &#8216;_ean&#8217; finds all four-letter first names that end with ean (Dean, Sean, and so on).
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          [ ]
        </p>
      </td>
      
      <td>
        <p>
          Any single character within the specified range ([a-f]) or set ([abcdef]).
        </p>
      </td>
      
      <td>
        <p>
          WHERE au_lname LIKE &#8216;[C-P]arsen&#8217; finds author last names ending with arsen and starting with any single character between C and P, for example Carsen, Larsen, Karsen, and so on. In range searches, the characters included in the range may vary depending on the sorting rules of the collation.
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          [^]
        </p>
      </td>
      
      <td>
        <p>
          Any single character not within the specified range ([^a-f]) or set ([^abcdef]).
        </p>
      </td>
      
      <td>
        <p>
          WHERE au_lname LIKE &#8216;de[^l]%&#8217; all author last names starting with de and where the following letter is not l.
        </p>
      </td>
    </tr>
  </table>
</div>

Let&#8217;s take a look at some examples, first create this table and insert some data.

<pre>CREATE TABLE TestLike (SomeCol VARCHAR(200))
GO
INSERT TestLike VALUES ('12345')
INSERT TestLike VALUES ('abd23bbb')
INSERT TestLike VALUES ('abc_bb')
INSERT TestLike VALUES ('abc______bb')
INSERT TestLike VALUES ('zzzz')
INSERT TestLike VALUES ('z0z0z0z0z0')
INSERT TestLike VALUES ('bla')</pre>

So what happens if you do the following

<pre>SELECT * FROM TestLike
where SomeCol LIKE '%_%'</pre>

output
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
12345
  
abd23bbb
  
abc_bb
  
abc\___\___bb
  
zzzz
  
z0z0z0z0z0
  
bla

You get back everything. This is because in a regular expression, the underscore means any single character. In order to search for an underscore, you need to put it in brackets

<pre>SELECT * FROM TestLike
where SomeCol LIKE '%[_]%'</pre>

output
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
abc_bb
  
abc\___\___bb

Let&#8217;s take a look at another example, what if you want to search for the left bracket [?

First insert the following row

<pre>INSERT TestLike VALUES ('2222[2222')</pre>

Now when you do something like this

<pre>SELECT * FROM TestLike
where SomeCol LIKE '%[%'</pre>

Nothing is returned. You can however put the left bracket in brackets

<pre>SELECT * FROM TestLike
where SomeCol LIKE '%[[]%'</pre>

output
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
2222[2222

Brackets are also required for ranges. If you want to return all the rows where any of characters is 2 or 3, you can&#8217;t do this

<pre>SELECT * FROM TestLike
where SomeCol LIKE '%2-3%'</pre>

You can surround that with brackets

<pre>SELECT * FROM TestLike
where SomeCol LIKE '%[2-3]%'</pre>

output
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
12345
  
abd23bbb
  
2222[2222

You can of course also use OR

<pre>SELECT * FROM TestLike
where SomeCol LIKE '%2%'
OR SomeCol LIKE '%3%'</pre>

But what if you want to expand that to check for a range between 2 and 6

<pre>SELECT * FROM TestLike
where SomeCol LIKE '%2%'
OR SomeCol LIKE '%3%'
OR SomeCol LIKE '%4%'
OR SomeCol LIKE '%5%'
OR SomeCol LIKE '%6%'</pre>

This way is much cleaner in my opinion

<pre>SELECT * FROM TestLike
where SomeCol LIKE '%[2-6]%'</pre>

That is it for this post, check out the related post [SQL Server does support regular expressions in check constraints, triggers are not always needed][1] which also shows some regular expressions

 [1]: /index.php/DataMgmt/DBProgramming/sql-server-does-support-regular-expressi