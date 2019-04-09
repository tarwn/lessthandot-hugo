---
title: Decimal and Numeric problems when you don’t specify precision and scale
author: George Mastros (gmmastros)
type: post
date: 2009-10-09T11:24:21+00:00
ID: 581
url: /index.php/datamgmt/datadesign/decimal-and-numeric-problems-when-you-do/
views:
  - 15496
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
When you don't specify the precision and scale for your decimal data types, SQL Server will use its own default values, which is probably NOT what you want. In fact, the default precision and scale values are 18,0. If you want a whole number data type, use bigint, int, smallint, or int. If you want fractional numbers use DECIMAL, but ALWAYS specify the precision and scale.

For example:

sql
Declare @Blah Decimal  

Set @Blah = 65.00  
  
SELECT SQL_VARIANT_PROPERTY(@Blah, 'BaseType') AS [Base Type],  
	SQL_VARIANT_PROPERTY(@Blah, 'Precision') AS [PRECISION],  
	SQL_VARIANT_PROPERTY(@Blah, 'Scale') AS [Scale]
```

When you run the above code, you will see:
  
DECIMAL, 18, 0

As you can see, the default precision is 18 and the scale is 0. Most developers will choose to use an Integer (or bigint, smallint, tinyint) if they are working with whole numbers, and a decimal when working with fractional numbers. Unfortunately, the default precision and scale for the decimal data type results in the equivalent of an integer. In fact, the only difference between an integer and a Decimal(18,0) is the range of numbers that can be stored and the size (memory) used to hold the value.

<pre>Storage Size            Minimum Value               Maximum Value
               ------------    --------------------------  -------------------------
Decimal(18,0)       9            -999,999,999,999,999,999    999,999,999,999,999,999
bigint              8          –9,223,372,036,854,775,808  9,223,372,036,854,775,807
int                 4                      –2,147,483,648              2,147,483,647
</pre>