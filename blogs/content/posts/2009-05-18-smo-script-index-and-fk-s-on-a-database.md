---
title: SMO Script Index and FK's on a database
author: Ted Krueger (onpnt)
type: post
date: 2009-05-18T18:26:17+00:00
ID: 435
url: /index.php/datamgmt/dbprogramming/smo-script-index-and-fk-s-on-a-database/
views:
  - 12272
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server Admin

---
This is a quick one and I see one issue already in the script generating a useless Go, use, go but here we go.

Using SMO to script some indexes and FK's found on tables.

Create a new c# app project in VS.NET (mine is named object scripter). Add the GUI objects so it looks like this

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//smo_1.gif" alt="" title="" width="770" height="603" />
</div>

Paste the code (not error handled and very quickly written..you've been warned!!!) below in the code view

```csharp
using System;
using System.Collections;
using System.Collections.Specialized;
using System.Collections.Generic;
using System.Text;
using System.Windows.Forms;
using System.Data;
using System.Data.SqlClient;
using Microsoft.SqlServer.Management.Smo;

namespace object_scripter
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            
        }
        public static string scripter(string scriptAction,string server, string dbname)
        {
            StringCollection sc = new StringCollection();
            ScriptingOptions so = new ScriptingOptions();
            so.IncludeDatabaseContext = true;
            so.DriForeignKeys = true;

            StoredProcedure sp = new StoredProcedure();
            Server serv = new Server(server);
            Database dbase = serv.Databases[dbname];

            string script = "";

            foreach (Table t in dbase.Tables)
            {
                foreach (ForeignKey key in t.ForeignKeys) 
                {
                        sc = key.Script(so);
                        foreach (string s in sc)
                        {
                            script += "nrGonr" + s;
                        }
                }
            }

            sc.Clear();

            foreach (Table t in dbase.Tables)
            {
                foreach (Index index in t.Indexes)
                {
                    sc = index.Script(so);
                    foreach (string s in sc)
                    {
                        script += "nrGonr" + s;
                    }
                }
            }

            return script;
        }

        private void button1_Click(object sender, EventArgs e)
        {
            richTextBox1.AppendText(scripter("CREATE", textBox1.Text, textBox2.Text));
        }
    }
}
```
Save and run. Enter an instance (dev one!!!), DB name and hit Run Me. Should appear as

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//smo_2.gif" alt="" title="" width="770" height="603" />
</div>