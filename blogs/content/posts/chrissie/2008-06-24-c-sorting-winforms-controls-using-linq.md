---
title: 'C# Sorting winforms controls using linq'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-24T10:48:56+00:00
ID: 71
url: /index.php/desktopdev/mstech/c-sorting-winforms-controls-using-linq/
views:
  - 8963
categories:
  - 'C#'
  - Microsoft Technologies

---
Use the power of linq

Create a form with some controls on it and a textbox called textbox1.

then but this in the load event of the form

```csharp
textBox1.Clear();
            IEnumerable&lt;Control&gt; query = from p in this.Controls.OfType&lt;Control&gt;() orderby p.TabIndex select p;
            foreach (Control c in query)
            {
                textBox1.AppendText(c.TabIndex + " " + c.Name + Environment.NewLine);    
            }```
and you probably need these usings.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Windows.Forms;```
At first I couldn&#8217;t get it to work until I found this [LINQ &#8211; Query Windows Forms Controls
  
by Sam Allen][1] and then the light came. I was missing the TypeOf method. This is really cool. No more reflection needed to get that control you realy need.

 [1]: http://dotnetperls.com/Content/LINQ-Windows-Forms.aspx