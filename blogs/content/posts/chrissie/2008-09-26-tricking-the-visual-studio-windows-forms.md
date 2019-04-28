---
title: tricking the visual studio windows forms designer
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-26T16:04:35+00:00
ID: 152
url: /index.php/desktopdev/mstech/tricking-the-visual-studio-windows-forms/
views:
  - 7017
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - generics
  - hack
  - windows forms

---
No visual inheritance doesn&#8217;t work in Visual studio (whatever version). You need way to many trickery to get it to work. I&#8217;m working on a blogpost that says why VI doesn&#8217;t work but it is taking longer then excpected. There are so many things that don&#8217;t work. 

One thing is the fact that if you inherit from a form that uses generics the designer just gives up and refuses to draw the form at designtime. It has no problem whatsever to display the form when at runtime. But still, if you have a mediumcomplex form it is not desirable to write the code manually. 

But for this problem there is a workaround. Actualy it is a dirty filty hack but it works ;-). I found this code while looking at the code for [MVC#][1], I will post about this later too.

```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Text;
using System.Windows.Forms;
using MVCSharp.Winforms;
using MVCSharp.Winforms.Configuration;
using MVCSharp.Examples.WindowsFormsExample.ApplicationLogic;

namespace MVCSharp.Examples.WindowsFormsExample.Presentation
{
    [WinformsView(typeof(MainTask), MainTask.MainView, IsMdiParent = true)]
    public partial class MainForm : MainForm_Generic, IMainView
    {
        public MainForm()
        {
            InitializeComponent();
        }

        public void SetCurrViewName(string viewName)
        {
            currentViewStatusLbl.Text = viewName;
        }

        private void ToolStripMenuItem_Click(object sender, EventArgs e)
        {
           Controller.Navigate((sender as ToolStripMenuItem).Text);
        }
    }

    // This intermediate class is used as a workaround for the bug
    // in the VS form designer when inheriting a generic user control class.
    public class MainForm_Generic : WinFormView&lt;MainViewController&gt;
    { }
}```
Did you see the trick?

Yep you make an in between class that inherits from the genric form and then your form inherits from that. A dirty filty hack but it works ;-).

 [1]: http://www.mvcsharp.org/