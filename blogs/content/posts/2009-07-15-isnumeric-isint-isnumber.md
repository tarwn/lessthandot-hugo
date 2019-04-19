---
title: IsNumeric, IsInt, IsNumber
author: George Mastros (gmmastros)
type: post
date: 2009-07-15T12:17:00+00:00
ID: 512
url: /index.php/datamgmt/datadesign/isnumeric-isint-isnumber/
views:
  - 28777
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
Occasionally, it's necessary to convert character data (char, nchar, varchar, nvarchar) to a number. Before doing so, it is best to make sure that the value can be converted to a number.

IsNumeric would be the obvious choice, but has some problems because it allows for unexpected characters during the conversion. For example, the following strings will return true from the IsNumeric function.

$12.09
  
1.4e3
  
2d4

Technically, all of these are numbers.

```sql
Select Convert(Money, '$12.09')
Select Convert(Float, '1.4e3')
Select Convert(Float, '2d4')
```

Often times, we don't want to allow the dollar sign or scientific notation. 

To make sure a value can be converted to an integer, you can append .0e0 to the end of the string before checking. If the original value had a decimal point, then adding another decimal point will cause the value to not be numeric. Similarly, adding e0 to a value that is already expressed in scientific notation will cause IsNumeric to return false.

Consider the following values:

<pre>Original   + '.0e0'    + 'e0'
--------   ----------  --------
$12.09     $12.09.0e0  $12.09e0
1.4e3      1.4e3.0e0   1.4e3e0
2d4        2d4.0e0     2d4e0
3.7        3.7.0e0     3.7e0
412        412.0e0     412e0</pre>

Notice how the second column will only evaluate to true for the value that can be converted to an integer. Also notice how the 3rd column will only evaluate to true for the last 2 values.

You can create your own User Defined Function to check for integers, like so:

```sql
CREATE Function dbo.IsInteger(@Value VarChar(18))
Returns Bit
As 
Begin
  
  Return IsNull(
     (Select Case When CharIndex('.', @Value) > 0 
                  Then Case When Convert(int, ParseName(@Value, 1)) <> 0
                            Then 0
                            Else 1
                            End
                  Else 1
                  End
      Where IsNumeric(@Value + 'e0') = 1), 0)

End
```

To use this new function:

```sql
Select Convert(int, field)
From   Table
Where  dbo.IsInteger(field) = 1
```

If you want to allow fractional numbers, then you can add e0 to the isnumeric test.

```sql
Select IsNumeric('$12.09' + 'e0')
Select IsNumeric('1.4e3' + 'e0')
Select IsNumeric('2d4' + 'e0')
Select IsNumeric('3.7' + 'e0')
```

Again, you can create a User Defined Function to test this.

```sql
Create Function IsNumber(@Value VarChar(18))
Returns Bit
As
Begin
  Return (Select IsNumeric(@Value + 'e0'))
End
```

After creating the User Defined Functions, you can test it with the following code.

```sql
Declare @Temp Table(Data VarChar(18))

Insert Into @Temp Values('$12.09')
Insert Into @Temp Values('1.4e3')
Insert Into @Temp Values('2d4')
Insert Into @Temp Values('3.7')
Insert Into @Temp Values('412')

Select Data, 
       IsNumeric(Data) As [IsNumeric], 
       dbo.IsInteger(Data) As IsInteger, 
       dbo.IsNumber(Data) As IsNumber
From   @Temp
```
