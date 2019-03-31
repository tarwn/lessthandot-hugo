---
title: Don’t put a return statement in a stored procedure inside a transaction
author: SQLDenis
type: post
date: 2012-05-18T00:57:00+00:00
excerpt: |
  I answered the following question earlier this evening: Return an output parameter from SQL Server via a stored procedure and c#
  
  Here is the proc in question, take a good look at it, do you see the problem?
  
  ALTER PROCEDURE [dbo].[Insert_UnknownCus&hellip;
url: /index.php/datamgmt/datadesign/don-t-put-a-return/
views:
  - 22275
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - how to
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - tip

---
I answered the following question earlier this evening: [Return an output parameter from SQL Server via a stored procedure and c#][1]

Here is the proc in question, take a good look at it, do you see the problem?

<pre>ALTER PROCEDURE [dbo].[Insert_UnknownCustomer_Quote_Document]
-- Add the parameters for the stored procedure here
@NewDocumentFileName nvarchar(100),
@NewDocumentWordCount int,
@NewQuoteAmount money,
@NewQuoteNumber int OUTPUT = 0

AS

DECLARE @Today datetime
SELECT @Today = GETDATE()

BEGIN TRANSACTION
BEGIN TRY

BEGIN
-- SET NOCOUNT ON added to prevent extra result sets from interfering with SELECT statements.
SET NOCOUNT ON;


-- Insert statements for procedure here
INSERT INTO dbo.Customers(DateAdded)
VALUES (@Today)

INSERT INTO dbo.Quotes(CustomerID, QuoteAmount, QuoteDate)
VALUES (@@IDENTITY, @NewQuoteAmount, @Today)

SELECT @NewQuoteNumber = @@IDENTITY
INSERT INTO dbo.DocumentFiles(QuoteNumber, DocumentFileName, DocumentFileWordCount)
VALUES (@NewQuoteNumber, @NewDocumentFileName, @NewDocumentWordCount)

-- Return quote number
RETURN @NewQuoteNumber

END
COMMIT TRANSACTION
END TRY

BEGIN CATCH
ROLLBACK TRANSACTION
PRINT 'Transaction rolled back.'
END CATCH</pre>

Here is a simplified version

<pre>CREATE proc prTest
as
DECLARE @id int

BEGIN TRAN
	INSERT TestID DEFAULT VALUES

	SELECT @id  =SCOPE_IDENTITY()

	RETURN @id
COMMIT TRAN
GO</pre>

Do you see it?

Let&#8217;s see what happens when you try running it

First create this table

<pre>CREATE TABLE TestID (id int identity)
go</pre>

Here is the proc again

<pre>CREATE proc prTest
as
DECLARE @id int

BEGIN TRAN
	INSERT TestID DEFAULT VALUES

	SELECT @id  =SCOPE_IDENTITY()

	RETURN @id
COMMIT TRAN
GO</pre>

Go ahead and execute it

<pre>DECLARE @id int
EXEC @id = prTest
SELECT @id</pre>

_Msg 266, Level 16, State 2, Procedure prTest, Line 0
  
Transaction count after EXECUTE indicates a mismatching number of BEGIN and COMMIT statements. Previous count = 0, current count = 1._

So the stored procedure blew up, no big deal right?
  
Open a new query window, run this

<pre>SELECT * FROM TestID</pre>

Take a note of the SPID in the status bar next to the username

As you can see the query is stuck
  
Now open yet another window and execute this

<pre>SELECT blocking_session_id,* 
FROM sys.dm_exec_requests
WHERE blocking_session_id &lt;&gt; 0</pre>

You will see that the SPID of the session where the select is running is returned by this query

As you can see, the transaction is still active and not committed, this is why the select statement is blocked

Go back to the first query window and either run COMMIT or ROLLBACK, the query will finish now

In the stored procedure, the return statement should come after the commit, it should look like this

<pre>ALTER proc prTest
as
DECLARE @id int

BEGIN TRAN
	INSERT TestID DEFAULT VALUES

	SELECT @id  =SCOPE_IDENTITY()


COMMIT TRAN
RETURN @id
GO</pre>

Now, you won&#8217;t get an error or a hanging transaction. Run it again

<pre>DECLARE @id int
EXEC @id = prTest
SELECT @id</pre>

It works now

In general, I don&#8217;t like to use return statements to return IDs, I like to use OUTPUT parameters instead, return statements in my opinion are to be used to return a status code

Here is what the proc would look like with an OUTPUT parameter

<pre>ALTER proc prTest @id int OUTPUT
AS

BEGIN TRAN
	INSERT TestID DEFAULT VALUES

	SELECT @id  =SCOPE_IDENTITY()
COMMIT TRAN
GO</pre>

Here is how you call it

<pre>DECLARE @id int
EXEC  prTest @id output
SELECT @id</pre>

Do you use a return statement to return identity values or do you use output parameters or do you use a plain vanilla SELECT statement? Leave me a comment and let me know

 [1]: http://stackoverflow.com/questions/10645730/return-an-output-parameter-from-sql-server-via-a-stored-procedure-and-c-sharp/10645751#10645751