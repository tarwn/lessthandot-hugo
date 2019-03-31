---
title: Use the GROUPING function to determine whether a value is NULL because of ROLLUP
author: SQLDenis
type: post
date: 2011-03-23T13:58:00+00:00
excerpt: 'If you have been writing queries that use ROLLUP, you are probably aware that the aggregated rows return NULL for the column that you are grouping by. What if you already have a NULL value in that column, how can you know which row is the aggregated row&hellip;'
url: /index.php/datamgmt/datadesign/use-the-grouping-function-to/
views:
  - 5736
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - aggregations
  - functions
  - group by
  - grouping
  - rollup
  - sql server 2008

---
If you have been writing queries that use ROLLUP, you are probably aware that the aggregated rows return NULL for the column that you are grouping by. What if you already have a NULL value in that column, how can you know which row is the aggregated row? Let&#8217;s take a look, first create this table

<pre>CREATE TABLE TestRollup(Country VARCHAR(20),Col1 INT, col2 INT)
INSERT TestRollup VALUES('United States',20,10)
INSERT TestRollup VALUES('United States',30,90)

INSERT TestRollup VALUES('Denmark',20,10)
INSERT TestRollup VALUES('Denmark',44,33)

INSERT TestRollup VALUES('Zimbabwe',20,10)
INSERT TestRollup VALUES('Zimbabwe',20,10)
INSERT TestRollup VALUES('Zimbabwe',20,1000)
INSERT TestRollup VALUES('Zimbabwe',2000,10)</pre>

Now let&#8217;s do our simple ROLLUP query

<pre>SELECT Country, SUM(Col1) Col1Sum, SUM(col2) AS Col2Sum
FROM TestRollup
GROUP BY Country WITH ROLLUP</pre>

Here is the results

<div class="tables">
  <table>
    <tr>
      <th>
        Country
      </th>
      
      <th>
        Col1Sum
      </th>
      
      <th>
        Col2Sum
      </th>
    </tr>
    
    <tr>
      <td>
        Denmark
      </td>
      
      <td>
        64
      </td>
      
      <td>
        43
      </td>
    </tr>
    
    <tr>
      <td>
        United States
      </td>
      
      <td>
        50
      </td>
      
      <td>
        100
      </td>
    </tr>
    
    <tr>
      <td>
        Zimbabwe
      </td>
      
      <td>
        2060
      </td>
      
      <td>
        1030
      </td>
    </tr>
    
    <tr>
      <td>
        NULL
      </td>
      
      <td>
        2174
      </td>
      
      <td>
        1173
      </td>
    </tr>
  </table>
</div>

We can easily determine that the Country column that has the value NULL is the total. What happens when we add the following row

<pre>INSERT TestRollup VALUES(NULL,2000,3000)</pre>

Now when we run the same query again, we have two rows where Country is NULL

<pre>SELECT Country, SUM(Col1) Col1Sum, SUM(col2) AS Col2Sum
FROM TestRollup
GROUP BY Country WITH ROLLUP</pre>

<div class="tables">
  <p>
    Here are the results
  </p>
  
  <table>
    <tr>
      <th>
        Country
      </th>
      
      <th>
        Col1Sum
      </th>
      
      <th>
        Col2Sum
      </th>
    </tr>
    
    <tr>
      <td>
        NULL
      </td>
      
      <td>
        2000
      </td>
      
      <td>
        3000
      </td>
    </tr>
    
    <tr>
      <td>
        Denmark
      </td>
      
      <td>
        64
      </td>
      
      <td>
        43
      </td>
    </tr>
    
    <tr>
      <td>
        United States
      </td>
      
      <td>
        50
      </td>
      
      <td>
        100
      </td>
    </tr>
    
    <tr>
      <td>
        Zimbabwe
      </td>
      
      <td>
        2060
      </td>
      
      <td>
        1030
      </td>
    </tr>
    
    <tr>
      <td>
        NULL
      </td>
      
      <td>
        4174
      </td>
      
      <td>
        4173
      </td>
    </tr>
  </table>
</div>

To figure out which of the two is the one that is caused by ROLLUP, you can use the GROUPING function, the function will return 1 if it is aggregated and 0 otherwise.

Here is what Books On Line has to say about [GROUPING][1]

_**GROUPING**
  
Indicates whether a specified column expression in a GROUP BY list is aggregated or not. GROUPING returns 1 for aggregated or 0 for not aggregated in the result set. GROUPING can be used only in the SELECT <select> list, HAVING, and ORDER BY clauses when GROUP BY is specified.</p> 

GROUPING is used to distinguish the null values that are returned by ROLLUP, CUBE or GROUPING SETS from standard null values. The NULL returned as the result of a ROLLUP, CUBE or GROUPING SETS operation is a special use of NULL. This acts as a column placeholder in the result set and means all.</em>

Now, let&#8217;s add GROUPING(Country) to our query

<pre>SELECT Country, SUM(Col1) Col1Sum, SUM(col2) AS Col2Sum, GROUPING(Country) AS GroupingCountry
FROM TestRollup
GROUP BY Country WITH ROLLUP</pre>

Here are the results, as you can see the function returns 1 for the aggregated row

<div class="tables">
  <table>
    <tr>
      <th>
        Country
      </th>
      
      <th>
        Col1Sum
      </th>
      
      <th>
        Col2Sum
      </th>
      
      <th>
        GroupingCountry
      </th>
    </tr>
    
    <tr>
      <td>
        NULL
      </td>
      
      <td>
        2000
      </td>
      
      <td>
        3000
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        Denmark
      </td>
      
      <td>
        64
      </td>
      
      <td>
        43
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        United States
      </td>
      
      <td>
        50
      </td>
      
      <td>
        100
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        Zimbabwe
      </td>
      
      <td>
        2060
      </td>
      
      <td>
        1030
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        NULL
      </td>
      
      <td>
        4174
      </td>
      
      <td>
        4173
      </td>
      
      <td>
        1
      </td>
    </tr>
  </table>
</div>

Now we can simply add a CASE expression to display Total for the aggregated column

<pre>SELECT CASE  GROUPING(Country) WHEN 1 THEN 'Total' ELSE Country END Country, SUM(Col1) Col1Sum, SUM(col2) AS Col2Sum
FROM TestRollup
GROUP BY Country WITH ROLLUP</pre>

Here is what the results look like 

<div class="tables">
  <table>
    <tr>
      <th>
        Country
      </th>
      
      <th>
        Col1Sum
      </th>
      
      <th>
        Col2Sum
      </th>
    </tr>
    
    <tr>
      <td>
        NULL
      </td>
      
      <td>
        2000
      </td>
      
      <td>
        3000
      </td>
    </tr>
    
    <tr>
      <td>
        Denmark
      </td>
      
      <td>
        64
      </td>
      
      <td>
        43
      </td>
    </tr>
    
    <tr>
      <td>
        United States
      </td>
      
      <td>
        50
      </td>
      
      <td>
        100
      </td>
    </tr>
    
    <tr>
      <td>
        Zimbabwe
      </td>
      
      <td>
        2060
      </td>
      
      <td>
        1030
      </td>
    </tr>
    
    <tr>
      <td>
        Total
      </td>
      
      <td>
        4174
      </td>
      
      <td>
        4173
      </td>
    </tr>
  </table>
</div>

That is it for this post, hopefully it will help someone

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://msdn.microsoft.com/en-us/library/ms178544.aspx
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22