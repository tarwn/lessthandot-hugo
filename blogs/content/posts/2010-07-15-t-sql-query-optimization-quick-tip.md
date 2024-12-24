---
title: T-SQL Query Optimization Quick Tip
author: Erik
type: post
date: 2000-11-30T00:00:00+00:00
ID: 848
excerpt: "A query with low CPU and reads was taking over 3 minutes to complete, and an experienced SQL developer couldn't optimize it. Finally, the query was reduced to half a second not by changing the query but by changing the table ..."
draft: true
url: /?p=848
categories:
  - Microsoft SQL Server
tags:
  - optimization
  - sql server
  - table variable
  - temp table

---
A query in a stored procedures I'm developing had low CPU and reads, but was unexplainedly taking a very long time to complete. The execution plan was also really great, even after exerting all my query-tuning might and playing with many different join orders and doing a CROSS APPLY to force a pseudo skip-scan.

After wrestling with it over several days I finally discovered that the problem was having used a table variable! After switching to a temp table, I got a crazy performance improvement and everything settled back to normal. Here are the numbers:

<div class="tables">
  <table>
    <tr>
      <th>
        Table
      </th>
      
      <th>
        CPU
      </th>
      
      <th>
        Reads
      </th>
      
      <th>
        Duration
      </th>
    </tr>
    
    <tr>
      <td>
        variable
      </td>
      
      <td>
        734
      </td>
      
      <td>
        74,330
      </td>
      
      <td>
        130,619
      </td>
    </tr>
    
    <tr>
      <td>
        variable
      </td>
      
      <td>
        1,188
      </td>
      
      <td>
        98,658
      </td>
      
      <td>
        133,063
      </td>
    </tr>
    
    <tr>
      <td>
        temp
      </td>
      
      <td>
        359
      </td>
      
      <td>
        86,086
      </td>
      
      <td>
        416
      </td>
    </tr>
    
    <tr>
      <td>
        temp
      </td>
      
      <td>
        344
      </td>
      
      <td>
        85,854
      </td>
      
      <td>
        417
      </td>
    </tr>
  </table>
</div>

The weird thing is that while trying all my variations and different JOIN strategies, worse execution plans would result in significantly better duration even though the CPU and reads were worse:

<div class="tables">
  <table>
    <tr>
      <th>
        Table
      </th>
      
      <th>
        CPU
      </th>
      
      <th>
        Reads
      </th>
      
      <th>
        Duration
      </th>
    </tr>
    
    <tr>
      <td>
        variable
      </td>
      
      <td>
        1171
      </td>
      
      <td>
        414,772
      </td>
      
      <td>
        20,612
      </td>
    </tr>
  </table>
</div>

But 20 seconds was still not acceptable.

I'm so glad I figured this out as it was not going to be nice for users to sit and wait for 20 seconds just to pull in a few hundred orders.

So put a little note in your mental toolbox on the "table variable" slot: Note: this tool can occasionally cause terrible performance. Try a regular temp table if you have problems.