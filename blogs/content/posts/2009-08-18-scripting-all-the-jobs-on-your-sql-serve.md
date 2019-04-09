---
title: Scripting All The Jobs On Your SQL Server Instance Into Separate Files By Using SMO
author: SQLDenis
type: post
date: 2009-08-18T14:02:38+00:00
ID: 540
url: /index.php/datamgmt/dbprogramming/scripting-all-the-jobs-on-your-sql-serve/
views:
  - 22912
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - jobs
  - msdb
  - scripting
  - smo

---
You inherited a brand new SQL server, one of the first questions you ask is of course where is the source control for this servers I want to see all the procedures and jobs. The answer might be that they use daily backups as source control or just don't do any source control at all.
  
If you want all jobs into one file then you don't need to use SMO since there is an easier solution, take a look here: [Scripting all jobs on SQL Server 2005/2008 into one file][1]

If you want all the jobs into their own separate file then keep on reading here. What you do NOT want to do is script all these jobs one by one. I wrote a little C# console app that will scripts out all the jobs on a server and save them into separate files with a sql extension, this way you can dump them into a source control folder.

Before I show the code I want to point out the code you need to change depending if you use SQL or windows authentication.

If you want to do windows authentication then use these 4 lines

```csharp
ServerConnection conn = new ServerConnection();
conn.LoginSecure = true;
conn.ServerInstance = "localhost";
Server srv = new Server(conn);
```

If you need to use SQL authentication then use the following 6 lines

```csharp
ServerConnection conn = new ServerConnection();
conn.LoginSecure = false;
conn.Login = "YourUserName";
conn.Password = "YourPassword";
conn.ServerInstance = "localhost";
Server srv = new Server(conn);
```

Below is the complete program, it is nothing fancy and you probably want to refactor some stuff out into their own methods.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.SqlServer.Management.Smo;
using Microsoft.SqlServer.Management.Smo.Agent;
using Microsoft.SqlServer.Management.Common;
using System.Collections.Specialized;
using System.IO;


namespace ConsoleApplicationJobs
{
    class Program
    {
        
        static void Main(string[] args)
        
        {
            StringCollection sc = new StringCollection();
            ScriptingOptions so = new ScriptingOptions();
            so.IncludeDatabaseContext = true;
            

            //Setup connection, this is windows authentication
            ServerConnection conn = new ServerConnection();
            conn.LoginSecure = true;
            conn.ServerInstance = "localhost";
            Server srv = new Server(conn);
            
            
            
            string script = "";
            
            string JobName;
            //Loop over all the jobs
            foreach (Job J in srv.JobServer.Jobs) {

                //Output name in the console
                Console.WriteLine(J.Name.ToString());
                
                JobName = J.Name.ToString();
                sc = J.Script(so);
                
                //Get all the text for the job
                foreach (string s in sc)
                {
                    script += s;
                }
                
                //Generate the file
                TextWriter tw = new StreamWriter("C:\" + JobName + ".sql");
                tw.Write(script);
                tw.Close();

                //Make the string blank again
                script = "";
            }
        }

    }
}
```
That is all, you could also use PowerShell combined with SMO to accomplish the same



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DBProgramming/scripting-all-jobs-on-sql-server-2005-20
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22