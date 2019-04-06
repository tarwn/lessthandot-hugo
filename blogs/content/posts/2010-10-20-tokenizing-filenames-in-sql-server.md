---
title: Tokenizing filenames in SQL Server
author: SQLDenis
type: post
date: 2010-10-20T15:10:34+00:00
excerpt: |
  Someone at my job needed to grab a filepath, take out the filename and then split all the pieces into their own columns which were separated by either a dot or an underscore. If you had the following table
  
  CREATE TABLE #test (id INT primary key, PATH&hellip;
url: /index.php/datamgmt/dbprogramming/tokenizing-filenames-in-sql-server/
views:
  - 6813
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - function
  - pivot
  - sql server 2005
  - sql server 2008
  - transpose

---
Someone at my job needed to grab a filepath, take out the filename and then split all the pieces into their own columns which were separated by either a dot or an underscore. If you had the following table

<pre>CREATE TABLE #test (id INT primary key, PATH VARCHAR(1000))
INSERT #test VALUES(1,'Y:dgfdgdgd_Databla_2010_10_14_Pre_Calc.BAK')
INSERT #test VALUES(2,'Y:dgfdbla_2010_10_15_Pre_Calc.BAK')
INSERT #test VALUES(4,'Y:dgfdgdgdgdgdgdgdSome_Databla_2010_16_Pre_Calc.BAK')
INSERT #test VALUES(3,'Y:dgfdgdgdgdgdgdgdSome_Databla_2010_11_12_Pre_Calc.BAK')</pre>

Then this was the required output.

<div class="tables">
  <table>
    <tr>
      <th>
        id
      </th>
      
      <th>
        PATH
      </th>
      
      <th>
        1
      </th>
      
      <th>
        2
      </th>
      
      <th>
        3
      </th>
      
      <th>
        4
      </th>
      
      <th>
        5
      </th>
      
      <th>
        6
      </th>
      
      <th>
        7
      </th>
      
      <th>
        8
      </th>
      
      <th>
        9
      </th>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        10
      </td>
      
      <td>
        14
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        NULL
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
        bla_2010_10_15_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        10
      </td>
      
      <td>
        15
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        NULL
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        bla_2010_11_12_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        11
      </td>
      
      <td>
        12
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        NULL
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        bla_2010_16_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        16
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        NULL
      </td>
    </tr>
  </table>
</div>

I thought that this was an interesting exercise, the person who asked me about this wrote some procedures and user defined functions but it was too slow because he was using a lot of charindex and substring functions and storing results in intermediate variables, then continued parsing until the end of the string.

In order to attack this problem you have to divide and conquer. Here are the four steps that are needed
  

** 

  1. Take the dot out
  2. Get just the filename
  3. Split the string
  4. Pivot the results

</strong>

## 1 Take the dot out

This is pretty easy, all you have to do is use the replace function and replace &#8216;.&#8217; with &#8216;_&#8217;

<pre>declare @FilePath varchar(100)
SELECT @FilePath = 'Y:dgfdgdgd_Databla_2010_10_14_Pre_Calc.BAK'
SELECT REPLACE(@FilePath,'.','_')</pre>

Result
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
Y:dgfdgdgd\_Databla\_2010\_10\_14\_Pre\_Calc_BAK

## 2 Get just the filename

To get just the filename, you use the reverse function, then you look for the position of the first backslash, you will then use the right function and use the position of the first backslash as the number of characters to keep. You add -1 to the length because you do not want the backslash included.

<pre>declare @FilePath varchar(100)
SELECT @FilePath = 'Y:dgfdgdgd_Databla_2010_10_14_Pre_Calc.BAK'
SELECT RIGHT(@FilePath,PATINDEX('%%',REVERSE(@FilePath))-1) AS PATH</pre>

Result
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
bla\_2010\_10\_14\_Pre_Calc.BAK

## 2A combine 1 and 2

This is just a combination of 1 and 2, take the dot out and get just the file name

<pre>declare @FilePath varchar(100)
SELECT @FilePath = 'Y:dgfdgdgd_Databla_2010_10_14_Pre_Calc.BAK'
SELECT RIGHT(REPLACE(@FilePath,'.','_'),PATINDEX('%%',REVERSE(@FilePath))-1) AS PATH</pre>

Result
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
bla\_2010\_10\_14\_Pre\_Calc\_BAK

## 3 Split the string

To split the string we can use the built in table of numbers master..spt_values. Running the code below

<pre>declare @FilePath varchar(100)
SELECT @FilePath = 'Y:dgfdgdgd_Databla_2010_10_14_Pre_Calc.BAK'
SELECT @FilePath = RIGHT(REPLACE(@FilePath,'.','_'),PATINDEX('%%',REVERSE(@FilePath))-1) 

SELECT @FilePath as FilePath, SUBSTRING('_' + @FilePath + '_', Number + 1,
    CHARINDEX('_', '_' + @FilePath + '_', Number + 1) - Number -1)AS VALUE
    FROM master..spt_values v
    WHERE TYPE = 'P'
    AND Number &lt;= LEN('_' + @FilePath  + '_') - 1
    AND SUBSTRING('_' + @FilePath  + '_', Number, 1) = '_'</pre>

This is the output

<div class="tables">
  <table>
    <tr>
      <th>
        FilePath
      </th>
      
      <th>
        VALUE
      </th>
    </tr>
    
    <tr>
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
    </tr>
    
    <tr>
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        2010
      </td>
    </tr>
    
    <tr>
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        10
      </td>
    </tr>
    
    <tr>
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        14
      </td>
    </tr>
    
    <tr>
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        Pre
      </td>
    </tr>
    
    <tr>
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        Calc
      </td>
    </tr>
    
    <tr>
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        BAK
      </td>
    </tr>
  </table>
</div>

We are almost there, instead of just one string we want to grab the whole table. We do a cross join with the numbers table and also use the ROW_NUMBER function to assign a column number which we will use as the column name later
  
Run this to see what we have so far

<pre>SELECT id,PATH, SUBSTRING('_' + PATH + '_', Number + 1,
    CHARINDEX('_', '_' + PATH + '_', Number + 1) - Number -1)AS VALUE,
    ROW_NUMBER() OVER (PARTITION BY id ORDER BY id,number ) AS ROW
    FROM master..spt_values v
    CROSS JOIN (SELECT id,RIGHT(REPLACE(PATH,'.','_'),PATINDEX('%%',REVERSE(PATH))-1) AS PATH  
    FROM #test ) AS #test
    WHERE TYPE = 'P'
    AND Number &lt;= LEN('_' + PATH  + '_') - 1
    AND SUBSTRING('_' + PATH  + '_', Number, 1) = '_'
    </pre>

Here is the output

<div class="tables">
  <table>
    <tr>
      <th>
        id
      </th>
      
      <th>
        PATH
      </th>
      
      <th>
        VALUE
      </th>
      
      <th>
        ROW
      </th>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        1
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        2010
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
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        10
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
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        14
      </td>
      
      <td>
        4
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        5
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        6
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        7
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        bla_2010_10_15_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        1
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        bla_2010_10_15_Pre_Calc_BAK
      </td>
      
      <td>
        2010
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
        bla_2010_10_15_Pre_Calc_BAK
      </td>
      
      <td>
        10
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
        bla_2010_10_15_Pre_Calc_BAK
      </td>
      
      <td>
        15
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
        bla_2010_10_15_Pre_Calc_BAK
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        5
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        bla_2010_10_15_Pre_Calc_BAK
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        6
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        bla_2010_10_15_Pre_Calc_BAK
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        7
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        bla_2010_11_12_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        1
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        bla_2010_11_12_Pre_Calc_BAK
      </td>
      
      <td>
        2010
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
        bla_2010_11_12_Pre_Calc_BAK
      </td>
      
      <td>
        11
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
        bla_2010_11_12_Pre_Calc_BAK
      </td>
      
      <td>
        12
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
        bla_2010_11_12_Pre_Calc_BAK
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        5
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        bla_2010_11_12_Pre_Calc_BAK
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        6
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        bla_2010_11_12_Pre_Calc_BAK
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        7
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        bla_2010_16_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        1
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        bla_2010_16_Pre_Calc_BAK
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        bla_2010_16_Pre_Calc_BAK
      </td>
      
      <td>
        16
      </td>
      
      <td>
        3
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        bla_2010_16_Pre_Calc_BAK
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        4
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        bla_2010_16_Pre_Calc_BAK
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        5
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        bla_2010_16_Pre_Calc_BAK
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        6
      </td>
    </tr>
  </table>
</div>

The missing piece is transposing the columns, this is easily accomplished by using the PIVOT operator. As you can see we use the ROW_NUMBER function to create a number for us, this number is then used as a column name by the Pivot operator. Here is the final query which produced the desired output

<pre>SELECT * FROM
   ( 
    SELECT id,PATH, SUBSTRING('_' + PATH + '_', Number + 1,
    CHARINDEX('_', '_' + PATH + '_', Number + 1) - Number -1)AS VALUE,
    ROW_NUMBER() OVER (PARTITION BY id ORDER BY id,number ) AS ROW
    FROM master..spt_values v
    CROSS JOIN (SELECT id,RIGHT(REPLACE(PATH,'.','_'),PATINDEX('%%',REVERSE(PATH))-1) AS PATH  
    FROM #test ) AS #test
    WHERE TYPE = 'P'
    AND Number &lt;= LEN('_' + PATH  + '_') - 1
    AND SUBSTRING('_' + PATH  + '_', Number, 1) = '_') AS pivTemp
    PIVOT
(   MAX(VALUE)
    FOR ROW IN ([1],[2],[3],[4],[5],[6],[7],[8],[9])
  ) AS pivTable</pre>

The query produces the desired output.

<div class="tables">
  <table>
    <tr>
      <th>
        id
      </th>
      
      <th>
        PATH
      </th>
      
      <th>
        1
      </th>
      
      <th>
        2
      </th>
      
      <th>
        3
      </th>
      
      <th>
        4
      </th>
      
      <th>
        5
      </th>
      
      <th>
        6
      </th>
      
      <th>
        7
      </th>
      
      <th>
        8
      </th>
      
      <th>
        9
      </th>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        bla_2010_10_14_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        10
      </td>
      
      <td>
        14
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        NULL
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
        bla_2010_10_15_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        10
      </td>
      
      <td>
        15
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        NULL
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        bla_2010_11_12_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        11
      </td>
      
      <td>
        12
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        NULL
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        bla_2010_16_Pre_Calc_BAK
      </td>
      
      <td>
        bla
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        16
      </td>
      
      <td>
        Pre
      </td>
      
      <td>
        Calc
      </td>
      
      <td>
        BAK
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        NULL
      </td>
    </tr>
  </table>
</div>



## Conclusion

As you can see, if you apply small steps and divide the problem, you can easily solve it even if it looks very difficult at first glance. Remember most likely you can already use something which is built into the product, try not to reinvent the wheel.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22