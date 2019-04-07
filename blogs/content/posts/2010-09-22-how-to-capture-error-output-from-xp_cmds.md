---
title: How to capture the error output from xp_cmdshell in SQL Server
author: SQLDenis
type: post
date: 2010-09-22T13:37:55+00:00
ID: 903
url: /index.php/datamgmt/dbprogramming/how-to-capture-error-output-from-xp_cmds/
views:
  - 28628
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - error
  - how to
  - sql server 2005
  - sql server 2008
  - tip
  - trick
  - xp_cmdshell

---
A person asked the following question:

> I am running the following command:
> 
> EXEC @ReturnCode = master.dbo.xp_cmdshell @cmdline
> 
> On the Results tab I get 2 lines Could not find a part of the path &#8216;serverdirectoryfilename&#8217;. NULL
> 
> How do I capture the first line in an error message? I tried using a Try Catch block with &#8220;SELECT @ErrorMessage = ERROR_MESSAGE()&#8221; and it doesn&#8217;t grab it.
> 
> The message is not coming from sys.messages. Where is this error message coming from then?

First of all that message comes from the Command Shell/DOS, not from SQL Server. There is a way to grab the message if you store the output in a table. The xp_cmdshell proc will return 1 if there is a failure and 0 if it executed succesfully
  
So if you were to execute the bogus command _bla bla c:_ you would get the following output

_&#8216;bla&#8217; is not recognized as an internal or external command,operable program or batch file._

If you did something like _dir z:_ when you don&#8217;t have a z drive you would see the following

_The system cannot find the path specified._

Now let&#8217;s look at some code by running a dir command on a drive that doesn&#8217;t exist, if you do have a z drive then change it to something that you don&#8217;t have

sql
DECLARE @cmdline VARCHAR(500),
		@ReturnCode INT, 
		@ErrorMessage varchar(2000)

--Command to execute
SELECT @cmdline = 'dir z:'

-- Initialize variable
SELECT @ErrorMessage = ''

--Create temp table to hold result
CREATE TABLE #temp (SomeCol VARCHAR(500))

--dump result into temp table
INSERT #temp
EXEC @ReturnCode = master.dbo.xp_cmdshell @cmdline

-- If we have an error populate variable
IF @ReturnCode <> 0
BEGIN
	SELECT @ErrorMessage = @ErrorMessage + SomeCol   
	FROM #temp
	WHERE SomeCol IS NOT NULL

	--Display error message and return code
	SELECT @ErrorMessage as ErrorMessage  ,@ReturnCode as ReturnCode

END
-- Look how 'green' we are
DROP TABLE #temp
```

After you run that you should see the following
  


<div class="tables">
  <table cellpadding="1" cellspacing="1" border="1">
    <tr>
      <th>
        ErrorMessage
      </th>
      
      <th>
        ReturnCode
      </th>
    </tr>
    
    <tr>
      <td>
        The system cannot find the path specified.
      </td>
      
      <td>
        1
      </td>
    </tr>
  </table>
</div>

Change 

sql
SELECT @cmdline = 'dir z:'
```

to 

sql
SELECT @cmdline = 'bla bla z:'
```

Run the code again and now you should see the following output.
  


<div class="tables">
  <table cellpadding="1" cellspacing="1" border="1">
    <tr>
      <th>
        ErrorMessage
      </th>
      
      <th>
        ReturnCode
      </th>
    </tr>
    
    <tr>
      <td>
        &#8216;bla&#8217; is not recognized as an internal or external command,operable program or batch file.
      </td>
      
      <td>
        1
      </td>
    </tr>
  </table>
</div>

That is it for this post; as you can see there is a way to grab the error message from xp_cmdshell

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22