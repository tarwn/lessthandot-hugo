---
title: 'SQL Server: When 8000 Characters Is Not Enough'
author: Erik
type: post
date: 2009-04-06T21:24:51+00:00
ID: 375
excerpt: "MS SQL Server 2000 has a limitation of 8000 characters in a varchar variable. The historical reason for this limitation, I believe, is related to the 8k size of data pages, where in-row data can't exceed something like 8060 bytes (actual numbers vary a&hellip;"
url: /index.php/datamgmt/dbprogramming/mssqlserver/sql-2000-when-8000-characters-is-not-eno/
views:
  - 62097
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
MS SQL Server 2000 has a limitation of 8000 characters in a varchar variable. The historical reason for this limitation, I believe, is related to the 8k size of data pages, where in-row data can't exceed something like 8060 bytes (actual numbers vary a little).

But what if you need to work with data that is longer than 8000 characters? When storing data in a table, you can use the text datatype which is stored out-of-row (though there are options about storing strings shorter than 8k characters in-row and then moving them out-of-row if they grow). But you can't use the text datatype as a variable. Look:

```sql
DECLARE @longdata text
```

> <span class="MT_red">Msg 2739, Level 16, State 1, Line 1<br /> The text, ntext, and image data types are invalid for local variables.</span>

But while that seems definitive, it is not the whole story, because you **can** use the text data type as a parameter in a stored procedure:

```sql
CREATE PROCEDURE DisplayText @longdata text
AS
SELECT @longdata
```

This works fine, but there are limitations on what can be done with that @longdata variable just like there are with text columns, such as the inability to use the Len() function (use DataLength instead).

If you truly must have variables with more than 8000 characters in a stored procedure, but they can't be passed in as parameters, then you are now in the unfortunate position of having to use tables and text pointers. You'll need the functions TEXTPTR, WRITETEXT, UPDATETEXT, and READTEXT. With these, you can work with pieces of the long data types (no longer than 8000 characters at a time in a variable).

Here's an example of putting more than 8000 characters into a column in an SP:

```sql
CREATE TABLE #Table (
   id int identity(1,1) primary key clustered,
   longdata text
)

INSERT #Table (longdata) VALUES ('data to be added to->')
INSERT #Table (longdata) VALUES ('more data->')

DECLARE
   @id int,
   @chunknum int,
   @ptr varbinary(16),
   @textchunk varchar(8000)
SET @id = 0
WHILE 1 = 1 BEGIN
   SELECT TOP 1 @id = id, @ptr = TextPtr(longdata) FROM #Table WHERE id > @id ORDER BY id
   IF @@RowCount = 0 BREAK
   SET @chunknum = 1
   WHILE @chunknum <= 5 BEGIN
      SET @textchunk = Replicate(Char(96 + @chunknum), 8000)
      UPDATETEXT #Table.longdata @ptr NULL NULL @textchunk
      SET @chunknum = @chunknum + 1
   END
END

SELECT * FROM #Table
DROP TABLE #Table
```

You can see that you have to loop over each row, and then loop over each chunk of data you want to read from or write to the column.

Now you know the basics of handling more than 8000 characters in SQL 2000. But there are a few subtle things you should know:

• The 8000-character limitation does not apply to literal strings, just variables and rowsets. This means that with the DisplayText stored procedure above you can put in more than 8000 characters like so:

```sql
EXEC DisplayText '<more than 8000 characters here>'
```

This does not mean you can somehow exceed 8000 characters with a commonly-tried but mistaken method such as

```sql
SELECT Left(longdata, 8000) + Substring(longdata, 8001, 8000) + Substring(longdata, 16001, 8000)
```

Sure, this may actually temporarily create a longer string (I don't know for sure) but the final value in the rowset will not exceed 8000 characters.

You can concatenate strings when submitting dynamic SQL statements:

```sql
DECLARE
   @SQL1 varchar(8000),
   @SQL2 varchar(8000),
   @SQL3 varchar(8000),
   @SQL4 varchar(8000)

SET @SQl1 = '8000 characters here'
SET @SQl2 = '8000 characters here'
SET @SQl3 = '8000 characters here'
SET @SQl4 = '8000 characters here'

EXEC (@SQL1 + @SQL2 + @SQL3 + @SQL4)
```

While I can't really recommend this method, if you are desperate and this is the only way to get the job done, it's a possibility. I've personally only used it in two narrow situations: creating pre-processed SQL objects where the dynamic SQL is executed rarely only when something changes, and the code it creates is static until the next change. One creates history-keeping triggers, and the other builds a standard set of pivoted views in response to changes of the metadata definitions of some web objects. Both required this method to be able to handle the number of columns possible. 

• SQL 2005 has a new 'max' keyword for the length of the (n)var/char data types that allows variables to be as big as the (n)text datatype can be. You can do anything to them that you could with regular varchar types, but behind the scenes they function like the text data type with values less than 8000 characters in-row and values greater than 8000 characters stored in out-of-row pages.

```sql
DECLARE @longdata varchar(max)
SET @longdata = '<more than 8000 characters here>'
```

Max here simply means that the data type can go up to the maximum storage size allowed, which is 2^31-1 bytes (minus 2 more bytes for presumably a length value). Note that you can't specify varchar(16000) or some value over 8000. You only get between 1 and 8000, or the special identifier _max_.

As a practical example of using a text datatype to defeat the 8000-character limitation in MS SQL Server 2000, here is a SendMail stored procedure that will use the CDO.Message object to send email with a body longer than 8000 characters. Note: put in your mail server name or make it a parameter or perhaps make it read from a table!

```sql
CREATE PROCEDURE SendMail
   @From varchar(1000),
   @To varchar(1000),
   @Subject varchar(1000) = 'No Subject',
   @TextBody text = NULL,
   @HtmlBody text = NULL
AS
-- Written by Erik E
-- Sends an email using the CDO.Message object and the SP_OA procedures.
-- Notably, accepts text input for emails longer than 8000 characters.
DECLARE
   @CDOMessage int,
   @ReturnCode int

SET @ReturnCode = 0 

EXEC @ReturnCode = SP_OACREATE 'CDO.Message', @CDOMessage OUT
IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'CDO.Message', NULL, NULL) GOTO Err END

EXEC @ReturnCode = SP_OASETPROPERTY @CDOMessage, 'From', @From
IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'From', 'CDO.Message', @From) GOTO Err END

EXEC @ReturnCode = SP_OASETPROPERTY @CDOMessage, 'To', @To
IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'To', 'CDO.Message', @To) GOTO Err END

EXEC @ReturnCode = SP_OASETPROPERTY @CDOMessage, 'Subject', @Subject
IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'Subject', 'CDO.Message', @Subject) GOTO Err END

IF @TextBody IS NOT NULL BEGIN
   EXEC @ReturnCode = SP_OASETPROPERTY @CDOMessage, 'TextBody', @TextBody
   IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'TextBody', 'CDO.Message', Convert(varchar(8000), @TextBody)) GOTO Err END
END

IF @HtmlBody IS NOT NULL BEGIN
   EXEC @ReturnCode = SP_OASETPROPERTY @CDOMessage, 'HtmlBody', @HtmlBody
   IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'HtmlBody', 'CDO.Message', Convert(varchar(8000), @HtmlBody)) GOTO Err END
END

EXEC @ReturnCode = SP_OASETPROPERTY @CDOMessage, 'Configuration.Fields("http://schemas.microsoft.com/cdo/configuration/sendusing").Value', '2'
IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'Configuration.Fields("http://schemas.microsoft.com/cdo/configuration/sendusing").Value', 'CDO.Message', '2') GOTO Err END

EXEC @ReturnCode = SP_OASETPROPERTY @CDOMessage, 'Configuration.Fields("http://schemas.microsoft.com/cdo/configuration/smtpserver").Value', 'YourMailServerName'
IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'Configuration.Fields("http://schemas.microsoft.com/cdo/configuration/smtpserver").Value', 'CDO.Message', 'YourMailServerName') GOTO Err END

EXEC @ReturnCode = SP_OASETPROPERTY @CDOMessage, 'Configuration.Fields("http://schemas.microsoft.com/cdo/configuration/smtpserverport").Value', '25'
IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'Configuration.Fields("http://schemas.microsoft.com/cdo/configuration/smtpserverport").Value', 'CDO.Message', '25') GOTO Err END

EXEC @ReturnCode = SP_OAMETHOD @CDOMessage, 'Configuration.Fields.Update'
IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'Configuration.Fields.Update', 'CDO.Message', NULL) GOTO Err END

EXEC @ReturnCode = SP_OAMETHOD @CDOMessage, 'Send'
IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'Send', 'CDO.Message', NULL) GOTO Err END

Err:
SET @ReturnCode = 0
IF @CDOMessage <> 0 EXEC @ReturnCode = SP_OADESTROY @CDOMessage
IF @ReturnCode <> 0 BEGIN PRINT dbo.ObjectErrorFunc(@CDOMessage, @ReturnCode, 'CDO.Message', NULL, NULL) END
```

The error handling here after each SP\_OA SP call uses a custom function I wrote. I found it to be invaluable when debugging problems with OLE calls to various objects from SQL Server. You may either tear out this error handling, call the sp\_OAGetErrorInfo procedure yourself, or use mine, which is below (plus a couple of dependent functions that you may want to rewrite/modify/reimplement/stop using).

```sql
CREATE FUNCTION [dbo].[ObjectErrorFunc] (
   @Object int,
   @ReturnCode int,
   @Context varchar(1000), -- Property or Method
   @ObjectType varchar(8000), -- Object Name, when known
   @Value sql_variant -- Parameter value, if known
)
RETURNS varchar(8000)
AS
-- Written by Erik E
-- Makes (more) sense out of error codes when using the OLE sp_OA procedures.
-- Sometimes the message returned by sp_OAGetErrorInfo is cryptic, so this
--    function is a place where researched information on errors can be saved
--    for future reference.
BEGIN
   DECLARE
      @ErrorMessage varchar(8000),
      @ErrorCode binary(4),
      @Source varchar(8000),
      @Description varchar(8000)

   SET @ErrorCode = convert(binary(4), @ReturnCode)

   EXEC sp_OAGetErrorInfo
      @Object,
      @Source OUTPUT,
      @Description OUTPUT

      SET @Context = Coalesce(NullIf(@Context, ''), @Source, 'OLE Unknown Source')
      SELECT
         @ErrorMessage = 
            IsNull(@Source, 'OLE Unknown Source')
            + ' Error ' + IsNull('0x' + dbo.BinaryToHex(@ErrorCode), '0x???????')
            + ' - ' + Coalesce(Descr, LTrim(@Description), 'Unknown Error')
      FROM
         (
            SELECT Error = @ErrorCode
         ) E LEFT JOIN (
            SELECT Error = convert(binary(4), 0x80004005), Descr = convert(varchar(8000), 'Invalid OLE object handle: The specified handle value (' + convert(varchar(8000), @Object) + ') does not refer to a valid OLE object.')
            UNION ALL SELECT 0x80010108, 'The object invoked has disconnected from its clients: The previously valid referenced object has closed or stopped running since the last reference.'
            UNION ALL SELECT 0x80020003, 'Invalid property or method: property or method ''' + @Context + ''' was not found' + IsNull(' on OLE object of type ''' + @ObjectType + '.''', '.')
            UNION ALL SELECT 0x80020005, 'Type mismatch: data type of a Transact-SQL local variable used to store a return value of property or method ''' + @Context + ''' did not match the Visual Basic data type of the property or method return value. Or, the return value of property or method ''' + @Context + ''' was requested, but it does not return a value.'
            UNION ALL SELECT 0x80020006, 'Unknown name: property or method ''' + @Context + ''' was not found for the specified object' + IsNull(' ''' + @ObjectType + '.''', + '.')
             UNION ALL SELECT 0x80020008, 'Bad variable type: data type of a Transact-SQL value passed as a method parameter was NULL or did not match the Microsoft® Visual Basic® method parameter data type.'
            UNION ALL SELECT 0x8002000B, '''Subscript out of range'' on property or method ''' + @Context + IsNull(''' of object type ''' + @ObjectType, '') + '.'''
            UNION ALL SELECT 0x8002000E, 'Invalid number of parameters on property or method ''' + @Context + IsNull(''' of object type ''' + @ObjectType, '') + '.'''
            UNION ALL SELECT 0x80020011, '''Does not support a collection'' on property or method ''' + @Context + IsNull(''' of object type ''' + @ObjectType, '') + '.'''
            UNION ALL SELECT 0x80040220, 'The "SendUsing" configuration value is invalid.'
            UNION ALL SELECT 0x80080005, 'Server execution failed: OLE object ''' + @Context + ''' is registered as a local OLE server (.exe file) but the .exe file could not be found or started.'
            UNION ALL SELECT 0x80040154, 'Class ''' + @Context + ''' not registered.'
            UNION ALL SELECT 0x800401F3, 'Invalid class string: OLE object ProgID/CLSID ''' + @Context + ''' is not registered on Server ''' + @@ServerName + '''. OLE automation servers need to be registered before they can be instantiated using sp_OACreate. This can be done using regsvr32.exe for inprocess (.dll) servers, or the /REGSERVER command-line switch for local (.exe) servers.'
            UNION ALL SELECT 0x8004275B, 'sp_OACreate - data type (' + @Context + ') or value ' + IsNull('(' + Convert(varchar(8000), @Value) + ')', '') + ' of parameter ''context'' is invalid. Valid context parameter values are 1, 4, and 5.'
            UNION ALL SELECT 0x80042727, 'sp_OASetProperty - improper usage: parameters are ObjPointer int IN, PropertyName varchar IN, @setval <any> IN [, additional indexing IN params].'
            UNION ALL SELECT 0x80042731, 'Output values of type Object require output parameters of type int.'
            UNION ALL SELECT 0x80042732, 'Output values of type Object are not allowed in result sets. Use a variable for parameter ''returnvalue'' with the OUTPUT keyword in order to return a handle to an object.'
            UNION ALL SELECT 0x80042742, 'Traversal string:  Bad whitespace: Error parsing method or property specification string ''' + @Context + '.'''
            UNION ALL SELECT 0x8007007E, 'Module could not be found: OLE object ''' + @Context + ''' is registered as an in-process OLE server (.dll file), but the .dll file could not be found or loaded.'
         )  X ON E.Error = X.Error
   RETURN @ErrorMessage
END
```

```sql
CREATE FUNCTION [dbo].[NumberToHex] (@Number sql_variant)
RETURNS varchar(72)
AS
-- Written by Erik E
-- Converts a value to its hex string representation
BEGIN
   DECLARE
      @Hex varchar(72),
      @Pos int,
      @Num decimal(38, 0),
      @HexChar tinyint
   SET @Pos = DataLength(@Number) * 2
   SET @Num = convert(decimal(38,0), @Number)
   WHILE @Pos > 0 BEGIN
      SET @HexChar = @Num - (Floor(@Num / Convert(decimal(38, 0), 16)) * Convert(decimal(38, 0), 16)) + 48
      SET @Hex = Char(@HexChar + CASE WHEN @HexChar >= 58 THEN 7 ELSE 0 END) + IsNull(@Hex, '')
      SET @Num = Floor(@Num / 16)
      SET @Pos = @Pos - 1
   END
   RETURN @Hex
END
```

```sql
CREATE FUNCTION [dbo].[BinaryToHex] (@Number varbinary(32))
RETURNS varchar(72)
AS
-- Written by Erik E
-- Converts varbinary to its hex string representation
BEGIN
   RETURN dbo.NumberToHex(Convert(int, @Number))
END
```

Here's a sample execution of SendMail for you:

```sql
EXEC SendMail 'emtucifor@example.com', 'emtucifor@example.com', 'This is a test email', @HTMLBody = '<8000 characters here>'
```

If you are sending email to someone who uses Outlook, it helps to provide both TextBody and HTMLBody, because the TextBody is used in the pop-up preview window of the content, so if the person is present when the email arrives, the preview will work correctly.

I hope that you benefit from this brief but intended-to-be-thorough write-up on exceeding the 8000-character limitation in SQL 2000.

Erik