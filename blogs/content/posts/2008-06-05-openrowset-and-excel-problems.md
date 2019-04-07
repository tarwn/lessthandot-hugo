---
title: OPENROWSET And Excel Problems
author: SQLDenis
type: post
date: 2008-06-05T23:11:31+00:00
ID: 44
url: /index.php/datamgmt/datadesign/openrowset-and-excel-problems/
views:
  - 13356
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - error
  - excel
  - openrowset
  - sql server

---
Create an excel sheet
  
In the first 2 rows put some data, save the excel sheet as testing.xls on the c drive
  
Execute the command below

```sql
SELECT * FROM OPENROWSET( 'Microsoft.Jet.OLEDB.4.0',
'Excel 8.0;Database=C:testing.xls','SELECT * FROM [Sheet1$]')
```

You will see 1 row since the first row will be used for header names
  
If you want 2 rows you need to add HDR=No like this

```sql
SELECT * FROM OPENROWSET( 'Microsoft.Jet.OLEDB.4.0',
'Excel 8.0;Database=C:testing.xls;HDR=NO','SELECT * FROM [Sheet1$]')
```

Run the following OPENROWSET command

```sql
SELECT * FROM OPENROWSET( 'Microsoft.Jet.OLEDB.4.0',
'Excel 8.0;Database=SS:testing.xls','SELECT * FROM [Sheet1$]')
```

The path can&#8217;t be found (SS) you will get this error
  
Server: Msg 7399, Level 16, State 1, Line 1
  
OLE DB provider &#8216;Microsoft.Jet.OLEDB.4.0&#8217; reported an error. The provider did not give any information about the error.
  
OLE DB error trace [OLE/DB Provider &#8216;Microsoft.Jet.OLEDB.4.0&#8217; IDBInitialize::Initialize returned 0x80004005: The provider did not give any information about the error.].

Run the following OPENROWSET command

```sql
SELECT * FROM OPENROWSET( 'Microsoft.Jet.OLEDB.4.0',
'Excel 8.0;Database=C:testing2.xls','SELECT * FROM [Sheet1$]')
```

When you spell the filename (testing2) wrong you get this error
  
Server: Msg 7399, Level 16, State 1, Line 1
  
OLE DB provider &#8216;Microsoft.Jet.OLEDB.4.0&#8217; reported an error.
  
[OLE/DB provider returned message: The Microsoft Jet database engine could not find the object &#8216;Sheet1$&#8217;. Make sure the object exists and that you spell its name and the path name correctly.]
  
OLE DB error trace [OLE/DB Provider &#8216;Microsoft.Jet.OLEDB.4.0&#8217; IColumnsInfo::GetColumnsInfo returned 0x80004005: ].

Run the following OPENROWSET command

```sql
SELECT * FROM OPENROWSET( 'Microsoft.Jet.OLEDB.4.0',
'Excel 8.0;Database=C:testing.xls','SELECT * FROM [Sheet11$]')
```

When you spell the sheet name (Sheet11) wrong you get this error
  
Server: Msg 7357, Level 16, State 2, Line 1
  
Could not process object &#8216;Select * from [Sheet11$]&#8217;. The OLE DB provider &#8216;Microsoft.Jet.OLEDB.4.0&#8242; indicates that the object has no columns.
  
OLE DB error trace [Non-interface error: OLE DB provider unable to process object, since the object has no columnsProviderName=&#8217;Microsoft.Jet.OLEDB.4.0&#8242;, Query=Select * from [P2 2003 DJIA updates$]&#8217;].

Run the following OPENROWSET command

```sql
SELECT * FROM OPENROWSET( 'Microsoft.Jet.OLEDB.4.0',
'Excel 8.0;Database=D:testing.xls','SELECT * FROM [Sheet1$]')
```

When you type the wrong path (D) but the path exists, then you get this error
  
Server: Msg 7399, Level 16, State 1, Line 1
  
OLE DB provider &#8216;Microsoft.Jet.OLEDB.4.0&#8217; reported an error. The provider did not give any information about the error.
  
OLE DB error trace [OLE/DB Provider &#8216;Microsoft.Jet.OLEDB.4.0&#8217; IDBInitialize::Initialize returned 0x80004005: The provider did not give any information about the error.].

When you have an excel file open and you are trying to open it with OPENROWSET you will get this error

Server: Msg 7399, Level 16, State 1, Line 1
  
OLE DB provider &#8216;Microsoft.Jet.OLEDB.4.0&#8217; reported an error. The provider did not give any information about the error.
  
OLE DB error trace [OLE/DB Provider &#8216;Microsoft.Jet.OLEDB.4.0&#8217; IDBInitialize::Initialize returned 0x80004005: The provider did not give any information about the error.].

So hopefully the next time you get an error you can quickly figure out if it&#8217;s the file name, sheet name or path name that is wrong