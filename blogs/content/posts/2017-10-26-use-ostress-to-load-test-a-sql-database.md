---
title: Use ostress to load test a SQL database
author: Jes Borland
type: post
date: 2017-10-26T14:49:36+00:00
ID: 8831
url: /index.php/datamgmt/dbprogramming/use-ostress-to-load-test-a-sql-database/
views:
  - 7240
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Have you ever wondered how you could take one or more .sql files you captured as a workload and run them against a SQL Server or Azure SQL Database to test performance or impact? Microsoft has a free tool to do just that – ostress.

Ostress.exe is part of the RML utilities. The first step is to download it from <https://www.microsoft.com/en-us/download/details.aspx?id=4511>. Along with the command-line tools, there is also a helpful help document!

Ostress allows you to specify one file, or a folder that contains multiple files, to run. You can also specify a number of connections to be made to the database, to simulate multiple users or applications running the same query. Each connection can then run the file one or more times.

The next thing you'll need is one or more .sql files that the tool will run.

To run a load test, you'll open RML cmd prompt and enter your command. A few common parameters, which you'll be using:

<p style="padding-left: 30px">
  -S – server name – this works for a SQL Server or an Azure SQL Database.<br /> -E – Windows authentication. The other option is -U and -P for SQL authentication.<br /> -d – database name.<br /> -i – Path to batch file(s), such as C:\ostress\ostress_batch_file.sql.<br /> -n – Number of connections to create.<br /> -r – Number of iterations through the file each connection will make.<br /> -q – Quiet mode – no result display.<br /> -o – Output file directory.
</p>

A sample command which will take one file I created (ostress\_batch\_file.sql), create 5 connections, and run it twice on each:
  
ostress -SJesSb -E -dAdventureWorks2016 -iC:\ostress\ostress\_batch\_file.sql -n5 -r2 -oC:\ostress

In the cmd window:

[<img class="aligncenter size-full wp-image-8832" src="/wp-content/uploads/2017/10/ostress-cmd.png" alt="ostress-cmd" width="793" height="434" srcset="/wp-content/uploads/2017/10/ostress-cmd.png 793w, /wp-content/uploads/2017/10/ostress-cmd-300x164.png 300w, /wp-content/uploads/2017/10/ostress-cmd-768x420.png 768w" sizes="(max-width: 793px) 100vw, 793px" />][1]
  
The output folder contains one log file, and one output file for each connection.

[<img class="aligncenter size-full wp-image-8833" src="/wp-content/uploads/2017/10/ostress-output.png" alt="ostress-output" width="487" height="199" srcset="/wp-content/uploads/2017/10/ostress-output.png 487w, /wp-content/uploads/2017/10/ostress-output-300x123.png 300w" sizes="(max-width: 487px) 100vw, 487px" />][2]

One question I've been asked was how to build delays into the batch. My suggestion is to edit the T-SQL script, using WAITFOR DELAY, to accomplish that.

Give ostress a try when you want a simple load testing tool for SQL Server!

 [1]: /wp-content/uploads/2017/10/ostress-cmd.png
 [2]: /wp-content/uploads/2017/10/ostress-output.png