---
title: 'DATEFROMPARTS  and DATETIMEFROMPARTS functions in SQL Server Denali CTP3'
author: SQLDenis
type: post
date: 2011-07-13T17:22:00+00:00
excerpt: |
  Function 
           
           
            Syntax 
           
           
            Return value 
           
           
            Return data type 
           
          
         
         
           
             
              DATEFROMPARTS&hellip;
url: /index.php/datamgmt/datadesign/datefromparts-and-datetimefromparts-functions-in/
views:
  - 10060
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
tags:
  - dates
  - denali
  - functions

---
SQL Server Denali CTP3 has added a bunch of date/time functions.

In the [A Quick look at the new EOMONTH function in SQL Server Denali CTP3][1] post I have already looked at the EOMONTH function. In this post I want to take a look at the DATEFROMPARTS and DATETIMEFROMPARTS functions

## DATEFROMPARTS

If you are a .NET programmer then you probably know that you can construct a date by passing a bunch of integers to the DateTime constructor. To create a date of July, 13, 2011 you would do something like this

<pre>DateTime date1 = new DateTime(2011, 7, 13);
Console.WriteLine(date1.ToString());</pre>

7/13/2011 12:00:00 AM

In SQL Server, you can do something similar now with the DATEFROMPARTS function. Here is what the syntax looks like

_DATEFROMPARTS ( year, month, day )_

Here is an example

<pre>SELECT DATEFROMPARTS ( 2011, 7, 13 ) AS Result;</pre>

2011-07-13

If you pass in all ones, you will get year 1, month 1 and day 1

<pre>SELECT DATEFROMPARTS(1,1,1)</pre>

0001-01-01

You can of course also pass in functions, so to get the first day of the current year and month, you would do this

<pre>SELECT DATEFROMPARTS(year(getdate()),month(getdate()),1)</pre>

2011-07-01

Here is what BOL has to say about DATEFROMPARTS:

_DATEFROMPARTS returns a date value with the date portion set to the specified year, month and day, and the time portion set to the default. If the arguments are not valid, then an error is raised. If required arguments are null, then null is returned._

## DATETIMEFROMPARTS 

The syntax for DATETIMEFROMPARTS looks like this

_DATETIMEFROMPARTS ( year, month, day, hour, minute, seconds, milliseconds )_

If you were to pass in the same values as for date into the DATETIMEFROMPARTS function you will get an error

<pre>SELECT DATETIMEFROMPARTS(1,1,1)</pre>

Msg 174, Level 15, State 1, Line 1
  
The datetimefromparts function requires 7 argument(s).

It would have been nice that the function returned you the datetime with every thing else as 0 instead of giving an error. 

So if you have this in .NET

<pre>DateTime date1 = new DateTime(2011, 7, 13, 16, 32, 18, 500);
Console.WriteLine(date1.ToString("M/dd/yyyy h:mm:ss.fff tt"));</pre>

7/13/2011 4:32:18.500 PM

You can do this in SQL

<pre>SELECT DATETIMEFROMPARTS ( 2011, 7, 13, 16, 32, 18, 500 ) AS Result;</pre>

2011-07-13 16:32:18.500

There are a couple of things to be aware of
  
You have to be within the valid datetime range (January 1, 1753, through December 31, 9999)

<pre>SELECT DATETIMEFROMPARTS(1600,1,1,1,1,1,500)</pre>

Msg 289, Level 16, State 3, Line 1
  
Cannot construct data type datetime, some of the arguments have values which are not valid.
  
&#12288;

If you use datetime2, which goes back to year 1 then you are fine, however if you just change DATETIMEFROMPARTS to DATETIME2FROMPARTS you will have a problem, DATETIME2FROMPARTS needs also precision 

<pre>SELECT DATETIME2FROMPARTS(1600,1,1,1,1,1,500)</pre>

Msg 174, Level 15, State 1, Line 1
  
The datetime2fromparts function requires 8 argument(s).
  
&#12288;

<pre>SELECT DATETIME2FROMPARTS(1600,1,1,1,1,1,500,3)</pre>

1600-01-01 01:01:01.500

Here is what BOL has to say about DATETIMEFROMPARTS:

_DATETIMEFROMPARTS returns a fully initialized datetime value. If the arguments are not valid, then an error is raised. If required arguments are null, then a null is returned._

Here is what BOL has to say about DATETIME2FROMPARTS:
  
_DATETIME2FROMPARTS returns a fully initialized datetime2 value. If the arguments are not valid, an error is raised. If required arguments are null, then null is returned. However, if the precision argument is null, then an error is raised.</p> 

The fractions argument depends on the precision argument. For example, if precision is 7, then each fraction represents 100 nanoseconds; if precision is 3, then each fraction represents a millisecond. If the value of precision is zero, then the value of fractions must also be zero; otherwise, an error is raised.</em>

Here is a list of some of these new date/time functions

<div class="tables">
  <table>
    <tr>
      <th>
        <p>
          Function
        </p>
      </th>
      
      <th>
        <p>
          Syntax
        </p>
      </th>
      
      <th>
        <p>
          Return value
        </p>
      </th>
      
      <th>
        <p>
          Return data type
        </p>
      </th>
    </tr>
    
    <tr>
      <td>
        <p>
          <strong>DATEFROMPARTS </strong>
        </p>
      </td>
      
      <td>
        <p>
          DATEFROMPARTS ( year, month, day )
        </p>
      </td>
      
      <td>
        <p>
          Returns a date value for the specified year, month, and day.
        </p>
      </td>
      
      <td>
        <p>
          date
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          <strong>DATETIME2FROMPARTS </strong>
        </p>
      </td>
      
      <td>
        <p>
          DATETIME2FROMPARTS ( year, month, day, hour, minute, seconds, fractions, precision )
        </p>
      </td>
      
      <td>
        <p>
          Returns a datetime2 value for the specified date and time and with the specified precision.
        </p>
      </td>
      
      <td>
        <p>
          datetime2<br /> <strong>(</strong> precision <strong>)</strong>
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          <strong>DATETIMEFROMPARTS</strong>
        </p>
      </td>
      
      <td>
        <p>
          DATETIMEFROMPARTS ( year, month, day, hour, minute, seconds, milliseconds )
        </p>
      </td>
      
      <td>
        <p>
          Returns a datetime value for the specified date and time.
        </p>
      </td>
      
      <td>
        <p>
          datetime
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          <strong>DATETIMEOFFSETFROMPARTS </strong>
        </p>
      </td>
      
      <td>
        <p>
          DATETIMEOFFSETFROMPARTS ( year, month, day, hour, minute, seconds, fractions, hour_offset, minute_offset, precision )
        </p>
      </td>
      
      <td>
        <p>
          Returns a datetimeoffset value for the specified date and time and with the specified offsets and precision.
        </p>
      </td>
      
      <td>
        <p>
          datetime<br /> <strong>(</strong> precision <strong>)</strong>
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          <strong>SMALLDATETIMEFROMPARTS</strong>
        </p>
      </td>
      
      <td>
        <p>
          SMALLDATETIMEFROMPARTS ( year, month, day, hour, minute )
        </p>
      </td>
      
      <td>
        <p>
          Returns a smalldatetime value for the specified date and time.
        </p>
      </td>
      
      <td>
        <p>
          smalldatetime
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          <strong>TIMEFROMPARTS </strong>
        </p>
      </td>
      
      <td>
        <p>
          TIMEFROMPARTS ( hour, minute, seconds, fractions, precision )
        </p>
      </td>
      
      <td>
        <p>
          Returns a time value for the specified time and with the specified precision.
        </p>
      </td>
      
      <td>
        <p>
          time<br /> <strong>(</strong> precision <strong>)</strong>
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          <strong>EOMONTH </strong>
        </p>
      </td>
      
      <td>
        <p>
          EOMONTH ( start_date [, month_to_add ] )
        </p>
      </td>
      
      <td>
        <p>
          Returns the last day of the month that contains the specified date, with an optional offset.
        </p>
      </td>
      
      <td>
        <p>
          Return type is the type of start_date or datetime2(7).
        </p>
      </td>
    </tr>
  </table>
</div>

 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/a-quick-look-at-the-1