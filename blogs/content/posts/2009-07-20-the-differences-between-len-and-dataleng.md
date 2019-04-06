---
title: The differences between LEN and DATALENGTH in SQL Server
author: SQLDenis
type: post
date: 2009-07-20T13:01:36+00:00
url: /index.php/datamgmt/dbprogramming/the-differences-between-len-and-dataleng/
views:
  - 35181
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - datalength
  - gotcha
  - len
  - t-sql

---
The differences between LEN and DATALENGTH in SQL Server!
  
I have seen a bunch of questions about this recently and decided to do a little post to clear up the confusion.
  
First let&#8217;s take a look what Books On Line has to say about these two functions

**LEN**
  
Returns the number of characters, rather than the number of bytes, of the given string expression, excluding trailing blanks.

**DATALENGTH**
  
Returns the number of bytes used to represent any expression.

So what does that mean? It means that the LEN function will first right trim the value and then give you a count of the charaters, the DATALENGTH function on the other hand does not right trim the value and gives you the storage space required for the characters.

Take a look at this example

<pre>declare @v nchar(5)
select @v ='ABC  '


select len(@v),datalength(@v)</pre>

The output for len is 3 while the output for datalength =10. The reason that datalength returns the value 10 is because nvarchar uses 2 bytes to store 1 character by using unicode while varchar is using ascii which requires 1 byte per charaters

Let&#8217;s take a look at some more data, first create this table

<pre>create table #TeslLen (	CharCol char(5), 
			VarCharCol varchar(5),
			NCharCol nchar(5), 
			NVarCharCol nvarchar(5))


insert #TeslLen values('A','A','A','A')
insert #TeslLen values('AB','AB','AB','AB')
insert #TeslLen values('ABC','ABC','ABC','ABC')
insert #TeslLen values('ABCD','ABCD','ABCD','ABCD')
insert #TeslLen values('ABCDE','ABCDE','ABCDE','ABCDE')
insert #TeslLen values(' ',' ',' ',' ')</pre>

Now run the following query

<pre>select CharCol as Value,len(CharCol) as LenChar,DATALENGTH(CharCol) as DLenChar,
	len(VarCharCol) as LenVarChar,DATALENGTH(VarCharCol)as DLenVarChar,
	len(NCharCol) as LenNChar,DATALENGTH(NCharCol) as DLenNChar,
	len(NVarCharCol) as LenNVarChar,DATALENGTH(NVarCharCol) as DLenNVarChar
from #TeslLen</pre>

Here is the output for all the columns with LEN and DATALENGTH

<table border="1" cellspacing ="1" cellpadding ="1">
  <tr>
    <td>
      Value
    </td>
    
    <td>
      LenChar
    </td>
    
    <td>
      DatalengthChar
    </td>
    
    <td>
      LenVarChar
    </td>
    
    <td>
      DatalengthVarChar
    </td>
    
    <td>
      LenNChar
    </td>
    
    <td>
      DatalengthNChar
    </td>
    
    <td>
      LenNVarChar
    </td>
    
    <td>
      DatalengthNVarChar
    </td>
  </tr>
  
  <tr>
    <td>
      A
    </td>
    
    <td>
      1
    </td>
    
    <td>
      5
    </td>
    
    <td>
      1
    </td>
    
    <td>
      1
    </td>
    
    <td>
      1
    </td>
    
    <td>
      10
    </td>
    
    <td>
      1
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      AB
    </td>
    
    <td>
      2
    </td>
    
    <td>
      5
    </td>
    
    <td>
      2
    </td>
    
    <td>
      2
    </td>
    
    <td>
      2
    </td>
    
    <td>
      10
    </td>
    
    <td>
      2
    </td>
    
    <td>
      4
    </td>
  </tr>
  
  <tr>
    <td>
      ABC
    </td>
    
    <td>
      3
    </td>
    
    <td>
      5
    </td>
    
    <td>
      3
    </td>
    
    <td>
      3
    </td>
    
    <td>
      3
    </td>
    
    <td>
      10
    </td>
    
    <td>
      3
    </td>
    
    <td>
      6
    </td>
  </tr>
  
  <tr>
    <td>
      ABCD
    </td>
    
    <td>
      4
    </td>
    
    <td>
      5
    </td>
    
    <td>
      4
    </td>
    
    <td>
      4
    </td>
    
    <td>
      4
    </td>
    
    <td>
      10
    </td>
    
    <td>
      4
    </td>
    
    <td>
      8
    </td>
  </tr>
  
  <tr>
    <td>
      ABCDE
    </td>
    
    <td>
      5
    </td>
    
    <td>
      5
    </td>
    
    <td>
      5
    </td>
    
    <td>
      5
    </td>
    
    <td>
      5
    </td>
    
    <td>
      10
    </td>
    
    <td>
      5
    </td>
    
    <td>
      10
    </td>
  </tr>
  
  <tr>
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
      5
    </td>
    
    <td>
    </td>
    
    <td>
      1
    </td>
    
    <td>
    </td>
    
    <td>
      10
    </td>
    
    <td>
    </td>
    
    <td>
      2
    </td>
  </tr>
</table>



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22