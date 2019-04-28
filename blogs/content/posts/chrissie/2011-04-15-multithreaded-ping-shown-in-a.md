---
title: 'Multithreaded ping shown in a grid C# version'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-04-15T07:53:00+00:00
ID: 1115
excerpt: |
  Since I don't want my dear friend Ted to do this himself I converted the code in my previous post into C#.
  
  I also wrapped the ping in a using statement and now only swallow InvalidOperationExption because cactching Exception is not good. 
  
  using Sy&hellip;
url: /index.php/desktopdev/mstech/multithreaded-ping-shown-in-a/
views:
  - 6901
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'
  - multithreaded
  - ping

---
Since I don&#8217;t want my dear friend [Ted][1] to do this himself I converted the code in [my previous post][2] into C#.

I also wrapped the ping in a using statement and now only swallow InvalidOperationException because catching Exception is not good. 

```csharp
using System;
using System.Collections.Generic;
using System.Drawing;
using System.Net.NetworkInformation;
using System.Threading;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace mutlithreadedping
{
    public partial class Form1 : Form
    {

        private List&lt;int&gt; _success = new List&lt;int&gt;();
        private List&lt;int&gt; _done = new List&lt;int&gt;();

        private Thread _fThread;
        private Thread _fThread2;

        private delegate void AddRowDelegate(int column);
        private delegate void SetOnlineDelegate(int rowindex);

        private void Form1_Load(object sender, EventArgs e)
        {
            fGrid.Columns.Add("Ip", "Ip");
            fGrid.Columns.Add("Ping", "Ping");
            fGrid.Columns[0].Width = 100;
            fGrid.Columns[1].AutoSizeMode = DataGridViewAutoSizeColumnMode.Fill;
            for (var i = 0; i &lt;= 4; i++)
            {
                var myRowIndex = fGrid.Rows.Add();
                fGrid.Rows[myRowIndex].Cells[0].Value = "10.216.110." + (11 + myRowIndex);
            }
        }

        private void ThreadProc()
        {
            Parallel.For(0, 5,
                         b =&gt;
                         {
                             while (!_done.Contains(b))
                             {
                                 fGrid.Invoke(new AddRowDelegate(AddRow), new Object[] { b });
                                 Thread.Sleep(300);
                             }
                             fGrid.Invoke(new SetOnlineDelegate(SetOnline), new Object[] { b });
                         });
        }

        private void AddRow(int rowindex)
        {
            if (fGrid.Rows[rowindex].Cells[1].Value == null || fGrid.Rows[rowindex].Cells[1].Value.ToString().Contains(".....") || !fGrid.Rows[rowindex].Cells[1].Value.ToString().Contains("Pinging"))
            {
                fGrid.Rows[rowindex].Cells[1].Value = "Pinging 10.216.110." + (11 + rowindex) + " ";
            }
            else
            {
                fGrid.Rows[rowindex].Cells[1].Value = fGrid.Rows[rowindex].Cells[1].Value + ".";
            }
            fGrid.Rows[rowindex].Cells[1].Style.BackColor = Color.White;
        }

        private void ThreadProc2()
        {
            Parallel.For(0, 5, CheckOnline);
        }

        private void CheckOnline(int rowindex)
        {
            using (var ping = new Ping())
            {
                try
                {
                    var pingreply = ping.Send("10.216.110." + (11 + rowindex), 2000);
                    if (pingreply == null || pingreply.Status == IPStatus.Success)
                    {
                        lock (_success) { _success.Add(rowindex); }
                    }
                }
                catch (InvalidOperationException)
                {
                    // consider it done but no success
                }
                lock(_done) {_done.Add(rowindex);}
            }
        }

        private void SetOnline(int rowindex)
        {
            if (!_success.Contains(rowindex))
            {
                fGrid.Rows[rowindex].Cells[1].Value = "Offline";
                fGrid.Rows[rowindex].Cells[1].Style.BackColor = Color.Red;
            }
            else
            {
                fGrid.Rows[rowindex].Cells[1].Value = "Online";
                fGrid.Rows[rowindex].Cells[1].Style.BackColor = Color.Green;
            }
        }

        private void pingToolStripMenuItem_Click(object sender, EventArgs e)
        {
            _done = new List&lt;int&gt;();
            _success = new List&lt;int&gt;();
            _fThread = new Thread(ThreadProc) {IsBackground = true};
            _fThread.Start();
            _fThread2 = new Thread(ThreadProc2) {IsBackground = true};
            _fThread2.Start();
        }

    }
}
```

 [1]: /index.php/All/?disp=authdir&author=68
 [2]: /index.php/DesktopDev/MSTech/multithreading-pings-and-showing-them