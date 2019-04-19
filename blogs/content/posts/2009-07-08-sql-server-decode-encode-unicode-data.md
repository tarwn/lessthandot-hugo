---
title: SQL Server Decode/Encode unicode data
author: George Mastros (gmmastros)
type: post
date: 2009-07-08T10:43:10+00:00
ID: 496
url: /index.php/datamgmt/datadesign/sql-server-decode-encode-unicode-data/
views:
  - 27271
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
tags:
  - unicode

---
Not all systems can correctly accommodate Unicode data. Therefore, it's not uncommon to receive data that is encoded in such a way that it can be stored in an ASCII format. Of course, SQL Server can store Unicode data easily if you use the nvarchar data type. Converting to and from Unicode data can be time consuming. When you receive data that is Unicode encoded, I suggest you decode it and store it in an nvarchar column. This will result in better performance.

Usually, when Unicode is encoded in an ASCII format, the Unicode value is somehow embedded within it. For example, the Greek letter Omega &#937;, has a Unicode value of 937. It may be encoded like this: &#937;

Usually, non Unicode data will not be encoded. So you may see a string like this: “&#937;mega”

Converting from an encoded string to a real Unicode string is more difficult than it may seem. There are 65,535 possible Unicode characters (the first 255 match ASCII characters). The example I showed has 3 digit Unicode values, but we should also be able to accommodate 4 and 5 digit Unicode characters too.

The basis for the function below is the CharIndex SQL Server functions. CharIndex has an optional 3rd parameter that allows you to specify where (within the string) to start searching. What we do is first find where the encoded Unicode data starts, and then where it ends. Once we know this, we do a simple replace. Since there can be multiple encoded Unicode characters, we put the whole thing in to a while loop.

```sql
Create Function DecodeUnicodeData(@Data nVarChar(4000), @Prefix VarChar(100), @Suffix VarChar(100))
Returns nvarchar(4000)
As
Begin
  Declare @Start Int
  Declare @End Int

  While CharIndex(@Prefix, @Data) > 0
    Begin
      Set @Start = CharIndex(@Prefix, @Data)
      Set @End = CharIndex(@Suffix, @Data, @Start)

      Set @Data = Replace(@Data, SubString(@Data, @Start, @End -@Start + Len(@Suffix)),NCHAR(SubString(@Data, @Start+ Len(@Prefix),@End -@Start - Len(@Prefix))))
    End
  
  Return @Data

End
```

You can call it like this:

```sql
Select dbo.DecodeUnicodeData('&#937;mega', '&#', ';')
```

This blog wouldn't be complete if I didn't also give you a function for encoding data, too. What I mean is, suppose you need to supply data in this format. How would you do it?

```sql
Create Function EncodeUnicodeData(@Data NVarChar(4000), @Prefix VarChar(20), @Suffix VarChar(20))
Returns VarChar(8000)
As 
Begin
  Declare @i Int
  Declare @Output VarChar(8000)

  Set @i = 1
  Set @Output = ''

  While @i <= Len(@Data)	
    Begin
      If Unicode(SubString(@Data, @i, 1)) > 255
        Set @Output = @Output + @Prefix + Convert(VarChar(5),Unicode(SubString(@Data, @i, 1))) + @Suffix
      Else
        Set @Output = @Output + SubString(@Data, @i, 1)

      Set @i = @i + 1
    End

  Return @Output
End
```

You can call it like this:

```sql
Select dbo.EncodeUnicodeData(N'&#937;mega', '&#', ';')
```
