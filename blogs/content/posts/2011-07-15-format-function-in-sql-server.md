---
title: Format function in SQL Server Denali CTP3
author: SQLDenis
type: post
date: 2011-07-15T08:30:00+00:00
ID: 1255
excerpt: |
  SQL Server Denali CTP3 brings a couple of new functions, one of these is the FORMAT  function
  
  The syntax of the format function looks like this
  
  FORMAT ( value, format [, culture ] )
  
  Here is what Books On Line has to say about the arguments that&hellip;
url: /index.php/datamgmt/datadesign/format-function-in-sql-server/
views:
  - 5215
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - denali
  - format
  - functions
  - localization
  - sql server

---
SQL Server Denali CTP3 brings a couple of new functions, one of these is the FORMAT function

The syntax of the format function looks like this

FORMAT ( value, format [, culture ] )

Here is what Books On Line has to say about the arguments that you can pass in

_**value**
  
Expression of a supported data type to format.</p> 

**format**
  
nvarchar format pattern.

The format argument must contain a valid .NET Framework format string, either as a standard format string (for example, &#8220;C&#8221; or &#8220;D&#8221;), or as a pattern of custom characters for dates and numeric values (for example, &#8220;MMMM dd, yyyy (dddd)&#8221;). Composite formatting is not supported. For a full explanation of these formatting patterns, please consult the.NET Framework documentation on string formatting in general, custom date and time formats, and custom number formats. A good starting point is the topic, &#8220;[Formatting Types.][1]&#8221;

**culture**
  
Optional nvarchar argument specifying a culture.

If the culture argument is not provided, then the language of the current session is used. This language is set either implicitly, or explicitly by using the SET LANGUAGE statement. culture accepts any culture supported by the .NET Framework as an argument; it is not limited to the languages explicitly supported by SQL Server . If the culture argument is not valid, FORMAT raises an error.</em>

Before we continue, I recommend that you visit the [National Language Support (NLS) API Reference][2] page to see all the locales that are available

If you are a .NET programmer then this function should look very familiar to you

Let&#8217;s take a look at how it all works, first let&#8217;s create a table and inserts some locales info so that it will be easier to show the different output later

sql
CREATE TABLE Locales(locale varchar(100))
insert Locales
select 'en-US'   --USA
union
select 'nl' --Netherlands
union
select 'fr'  --France
union
select 'de' --Germany
union
select 'no'  --Norway
union
select 'ru' --Russia
```

Now, let&#8217;s format some dates

sql
DECLARE @d DATETIME = '01/01/2011';

select locale,FORMAT ( @d, 'd', locale ) AS Result,
              FORMAT( @d, 'yyyy-MM-dd', locale ) Result2
from Locales
```

Here is what the output looks like, as you can see if you use specific formatting, the output is the same no matter what the locale is

<div class="tables">
  <table>
    <tr>
      <th>
        locale
      </th>
      
      <th>
        Result
      </th>
      
      <th>
        Result2
      </th>
    </tr>
    
    <tr>
      <td>
        de
      </td>
      
      <td>
        01.01.2011
      </td>
      
      <td>
        2011-01-01
      </td>
    </tr>
    
    <tr>
      <td>
        en-US
      </td>
      
      <td>
        1/1/2011
      </td>
      
      <td>
        2011-01-01
      </td>
    </tr>
    
    <tr>
      <td>
        fr
      </td>
      
      <td>
        01/01/2011
      </td>
      
      <td>
        2011-01-01
      </td>
    </tr>
    
    <tr>
      <td>
        nl
      </td>
      
      <td>
        1-1-2011
      </td>
      
      <td>
        2011-01-01
      </td>
    </tr>
    
    <tr>
      <td>
        no
      </td>
      
      <td>
        01.01.2011
      </td>
      
      <td>
        2011-01-01
      </td>
    </tr>
    
    <tr>
      <td>
        ru
      </td>
      
      <td>
        01.01.2011
      </td>
      
      <td>
        2011-01-01
      </td>
    </tr>
  </table>
</div>

Let&#8217;s look at another example, this one will format currency

sql
select locale,FORMAT ( 100, 'c', locale ) AS Result
from Locales
```



<div class="tables">
  <table>
    <tr>
      <th>
        locale
      </th>
      
      <th>
        Result
      </th>
    </tr>
    
    <tr>
      <td>
        de
      </td>
      
      <td>
        100,00 €
      </td>
    </tr>
    
    <tr>
      <td>
        en-US
      </td>
      
      <td>
        $100.00
      </td>
    </tr>
    
    <tr>
      <td>
        fr
      </td>
      
      <td>
        100,00 €
      </td>
    </tr>
    
    <tr>
      <td>
        nl
      </td>
      
      <td>
        € 100,00
      </td>
    </tr>
    
    <tr>
      <td>
        no
      </td>
      
      <td>
        kr 100,00
      </td>
    </tr>
    
    <tr>
      <td>
        ru
      </td>
      
      <td>
        100,00&#1088;.
      </td>
    </tr>
  </table>
</div>

As you can see the currency symbol is different depending on what locale has been used, the symbol will also alternate between the end or the start of the output depending again on the locale

You can also specify the number of characters after the decimal point

sql
select locale,FORMAT ( 100.34, 'C1', locale ) AS Result1,
			  FORMAT ( 100.34, 'C2', locale ) AS Result2,
			  FORMAT ( 100.34, 'C3', locale ) AS Result3,
			  FORMAT ( 100.34, 'C4', locale ) AS Result4
from Locales
```



<div class="tables">
  <table>
    <tr>
      <th>
        locale
      </th>
      
      <th>
        Result1
      </th>
      
      <th>
        Result2
      </th>
      
      <th>
        Result3
      </th>
      
      <th>
        Result4
      </th>
    </tr>
    
    <tr>
      <td>
        de
      </td>
      
      <td>
        100,3 €
      </td>
      
      <td>
        100,34 €
      </td>
      
      <td>
        100,340 €
      </td>
      
      <td>
        100,3400 €
      </td>
    </tr>
    
    <tr>
      <td>
        en-US
      </td>
      
      <td>
        $100.3
      </td>
      
      <td>
        $100.34
      </td>
      
      <td>
        $100.340
      </td>
      
      <td>
        $100.3400
      </td>
    </tr>
    
    <tr>
      <td>
        fr
      </td>
      
      <td>
        100,3 €
      </td>
      
      <td>
        100,34 €
      </td>
      
      <td>
        100,340 €
      </td>
      
      <td>
        100,3400 €
      </td>
    </tr>
    
    <tr>
      <td>
        nl
      </td>
      
      <td>
        € 100,3
      </td>
      
      <td>
        € 100,34
      </td>
      
      <td>
        € 100,340
      </td>
      
      <td>
        € 100,3400
      </td>
    </tr>
    
    <tr>
      <td>
        no
      </td>
      
      <td>
        kr 100,3
      </td>
      
      <td>
        kr 100,34
      </td>
      
      <td>
        kr 100,340
      </td>
      
      <td>
        kr 100,3400
      </td>
    </tr>
    
    <tr>
      <td>
        ru
      </td>
      
      <td>
        100,3&#1088;.
      </td>
      
      <td>
        100,34&#1088;.
      </td>
      
      <td>
        100,340&#1088;.
      </td>
      
      <td>
        100,3400&#1088;.
      </td>
    </tr>
  </table>
</div>

There you have it, formatted exactly like you want. I think the FORMAT function is a welcome addition, it will make formatting much easier than before when we have to mess around with CAST or CONVERT and style arguments

 [1]: http://msdn.microsoft.com/en-us/library/26etazsy.aspx
 [2]: http://msdn.microsoft.com/en-us/goglobal/bb896001.aspx