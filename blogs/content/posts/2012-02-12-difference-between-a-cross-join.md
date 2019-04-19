---
title: Difference between a cross join and a full outer join
author: SQLDenis
type: post
date: 2012-02-12T12:36:00+00:00
ID: 1522
excerpt: |
  We are interviewing for some SQL developer positions and it seems that most people that I interviewed do not know the difference between a full outer join and a cross join. 
  
  Cross join
  A cross join produces the Cartesian product of the tables involv&hellip;
url: /index.php/datamgmt/datadesign/difference-between-a-cross-join/
views:
  - 31281
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - joins
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - t-sql

---
We are interviewing for some SQL developer positions and it seems that most people that I interviewed the past 3 weeks do not know the difference between a full outer join and a cross join. 

## Cross join

A cross join produces the Cartesian product of the tables involved in the join. The size of a Cartesian product result set is the number of rows in the first table multiplied by the number of rows in the second table

Here what Wikipedia has to say about it: http://en.wikipedia.org/wiki/Cartesian_product

> For example, the Cartesian product of the 13-element set of standard playing card ranks {Ace, King, Queen, Jack, 10, 9, 8, 7, 6, 5, 4, 3, 2} and the four-element set of card suits {&#9824;, &#9829;, &#9830;, &#9827;} is the 52-element set of all possible playing cards: ranks × suits = {(Ace, &#9824;), (King, &#9824;), ..., (2, &#9824;), (Ace, &#9829;), ..., (3, &#9827;), (2, &#9827;)}. The corresponding Cartesian product has 52 = 13 × 4 elements. The Cartesian product of the suits × ranks would still be the 52 pairings, but in the opposite order {(&#9824;, Ace), (&#9824;, King), ...}. Ordered pairs (a kind of tuple) have order, but sets are unordered. The order in which the elements of a set are listed is irrelevant; the deck can be shuffled and it is still the same set of cards.

## Full outer join

A full outer join includes all rows from both tables, regardless of whether or not the other table has a matching value.

Here is what wikipedia has on full outer join: http://en.wikipedia.org/wiki/Join\_(SQL)#Full\_outer_join

> Conceptually, a full outer join combines the effect of applying both left and right outer joins. Where records in the FULL OUTER JOINed tables do not match, the result set will have NULL values for every column of the table that lacks a matching row. For those records that do match, a single row will be produced in the result set (containing fields populated from both tables).
  
> For example, this allows us to see each employee who is in a department and each department that has an employee, but also see each employee who is not part of a department and each department which doesn't have an employee.

## Examples

Time to look at some code. First create these two really simple tables

```sql
CREATE TABLE TableA (IDA int)
GO

INSERT TableA VALUES(1)
INSERT TableA VALUES(2)
INSERT TableA VALUES(3)
GO


CREATE TABLE TableB (IDB int)
GO
INSERT TableB VALUES(2)
INSERT TableB VALUES(3)
INSERT TableB VALUES(4)
GO
```

As you can see both table have 3 rows but only 2 rows are common to both tables. If we do a full outer join now, we will get back a result set that has 4 rows. We get 2 rows that are common to both tables, then we get 1 row from TableA which does not exist in TableB, we also get 1 row from TableB which does not exist in TableA

```sql
SELECT * FROM TableA a
FULL OUTER JOIN TableB b on a.IDA = b.IDB
```

Here is the output

<div class="tables">
  <table>
    <tr>
      <th>
        IDA
      </th>
      
      <th>
        IDB
      </th>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        NULL
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        3
      </td>
    </tr>
    
    <tr>
      <td>
        NULL
      </td>
      
      <td>
        4
      </td>
    </tr>
  </table>
</div>

As you can see there are two rows that have NULL in them, these are the ones that don't exist in both tables

Now, let's look at the cross join. We will get back 9 rows since we have 3 rows in both tables, output will be 3 x 3 rows

```sql
SELECT * FROM TableA a
CROSS JOIN TableB b 
ORDER BY a.IDA,b.IDB
```

Here is the output

<div class="tables">
  <table>
    <tr>
      <th>
        IDA
      </th>
      
      <th>
        IDB
      </th>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        3
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        4
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        3
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        4
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        3
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        4
      </td>
    </tr>
  </table>
</div>

As you can see for each row in TableA, you will get back all 3 rows from TableB

I got all kind of creative answer in responds to this question, some people were describing the cross join as an inner join or describing both join as being the same

If you want to read a little more about joins and a different way of doing a cross join, check out these two posts [SQL Advent 2011 Day 15: Joins][1] and [SQL Advent 2011 Day 16: CROSS APPLY and OUTER APPLY][2]

 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-15
 [2]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-16