---
title: SQL Server Fibonacci Sequence
author: George Mastros (gmmastros)
type: post
date: 2008-12-02T19:44:43+00:00
url: /index.php/datamgmt/dbprogramming/mssqlserver/sql-server-fibonacci-sequence/
views:
  - 8511
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
This probably falls under the category of &#8216;Why bother&#8217;. However, there has been some recent interest in calculating arbitrarily large fibonacci numbers. There is nothing particularly difficult about calculating these numbers until you reach the maximum number that a bigint (or decimal) can hold. With Fibonacci numbers, you reach this limit pretty fast. There&#8217;s nothing particularly difficult about calculating this sequence, it merely involves adding 2 numbers together. But, since we run in to the data type limit, we need to start thinking outside the box. 

Obviously, we cannot store the numbers in any sort of number data type, so we need to use strings instead. Then, we need to create a way to add strings together as though they were numbers. This is the real challenge with calculating large numbers, but it&#8217;s really not that difficult either. We&#8217;ll simply write a function that adds 2 strings together as though they were numbers.

Please understand that this is not production quality code. I&#8217;m not validating the inputs and I&#8217;m being careless about the data type conversions. Also note that I use the varchar(max) data type. This implies SQL2005 code. If you want to test this on SQL2000, then you&#8217;ll need to replace varchar(max) with VarChar(8000). You won&#8217;t be able to calculate as many numbers, but you&#8217;ll still get quite a few.

<pre>Create Function dbo.AddString(@String1 VarChar(8000), @String2 VarChar(8000))
Returns VarChar(max)
As
Begin
	Declare @CarryTheOne int
	Declare @i Int
	Declare @Output VarChar(max)
	Declare @Digit1 Int
	Declare @Digit2 int

    If Len(@String1) < Len(@String2) 
        select @String1 = Replace(Right(Space(Len(@String2)) + @String1, Len(@String2)), ' ', '0')
    Else
        select @String2 = Replace(Right(Space(Len(@String1)) + @String2, Len(@String1)), ' ', '0')

    set @CarryTheOne = 0
	Set @i = 0
	Set @Output = ''

	While @i < Len(@String1)
		Begin
			Set @Digit1 = SubString(@String1, Len(@String1) - @i, 1)
			Set @Digit2 = SubString(@String2, Len(@String1) - @i, 1)
	       
			Select @Output = Convert(VarChar(max), (@Digit1 + @Digit2 + @CarryTheOne) % 10) + @Output
			If @Digit1 + @Digit2 + @CarryTheOne > 9
				Set @CarryTheOne = 1
			Else
				Set @CarryTheOne = 0
		
			Set @i = @i + 1
	
		End
   
    If @CarryTheOne = 1 
        Set @Output = '1' + @Output
    
    Return @Output
End</pre>

The user defined function shown above allows us to add strings as though they were numbers. Now, how do we calculate the fibonacci series? Like this:

<pre>Declare @Temp Table(Id Int Identity(1,1), FIB VarChar(max))

Insert Into @Temp(FIB) Values('0')
Insert Into @Temp(FIB) Values('1')

Declare @i Int
Select @i = Max(Id) From @Temp

While @i < 1000
	Begin
	
		Insert Into @Temp(FIB)
		Select dbo.AddString((Select Fib From @Temp Where Id = @i), (Select Fib From @Temp Where Id = @i-1))

		Set @i = @i + 1

	End

Select Fib From @Temp Order By Id</pre>

That&#8217;s all there is to it!