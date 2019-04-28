---
title: Changing the app.config on install
author: Christiaan Baes (chrissie1)
type: post
date: 2010-01-14T11:26:11+00:00
ID: 675
url: /index.php/desktopdev/mstech/changing-the-app-config-on-install/
views:
  - 5434
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - .net
  - app.config
  - 'c#'
  - install
  - setup

---
I had the need to create a setup that changed my app.config file. In actual fact I wanted to create several setup files which I could then send to the user so that it would install the same program but with a small difference in the app.config file. And I also did not want any user input for this else I could have easily done it with just one setupproject. I don&#8217;t trust users to enter the correct value.

So I created 2 setup projects. One that doesn&#8217;t change anything and the other that changes the app.config file.

I found [Build a Custom Action InstallHelper Configuration Editor][1] article on Eggheadcafe and found it extremely usefull. 

First thing to do is to create yoru setup project. Nothing difficult about that. Just give it the primary output you want and it will work.

Now create the second one that will change the app.config file. This one will also have primary output of your application but it also needs a second executable, the executable that will change the app.config file. What it will actualy do is. First install your primary output for your application then install the primary output of the helper exe, run the helper exe. Seems simple enough.

So we also need to create a helper project. I used a windows application for this which makes it easy to show any errors that might popup along the way.

See here the structure of the solution.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/setup/setup.png" alt="" title="" width="440" height="487" />
</div>

DownloadDatabase is the setup project that does not change the app.config. and DownloadDatabaseKits does change it.

I will first guide you through the installhelper project. It is simple. 

It has one form frmUI and that does everything apart from some small thing in Program.cs.

```csharp
using System;
using System.Windows.Forms;

namespace InstallHelper2
{
    public class Program
    {
        [STAThread]
        static void Main(string[] args)
        {
            var path = "";
            if (args.Length &gt; 0)
            {
                foreach (var s in args)
                {
                    path += s;
                }
                path = path.Replace("ProgramFiles", "Program Files");
            }
            Application.Run(new FrmUi(path));
        }
    }
}
```
Not sure if the path needs adjusting but I left that piece of code in there.

```csharp
using System;
using System.Linq;
using System.Windows.Forms;
using System.Xml.Linq;

namespace InstallHelper2
{
    public partial class FrmUi : Form
    {
        private String path;

        public FrmUi(String path)
        {
            InitializeComponent();
            this.path = path;
        }
        
        public String Text1{ get{ return textBox1.Text;} set { textBox1.Text = value; } }

        private void FrmUi_Load(object sender, EventArgs e)
        {
            textBox1.Text = "Path: " + path + ".config" + Environment.NewLine;
            textBox1.AppendText("Version" + Environment.NewLine);
            if (System.IO.File.Exists(path))
            {
                try
                {
                    var file = XElement.Load(path + ".config");
                    var xNamespace = file.GetDefaultNamespace();
                    var element = from c in file.Elements("userSettings").Elements("WebUpdateClient.Properties.Settings").Elements("setting")
                                  where c.Attribute("name").Value == "Version"
                                  select c;
                    if (element.Count() &gt; 0)
                    {
                        foreach (var key in element)
                        {
                            textBox1.AppendText("value: " + key.Value + Environment.NewLine);
                            key.ReplaceNodes(new XElement("value", "TexDatabases2007Kits"));
                            file.Save(path + ".config");
                            textBox1.AppendText("File saved 2");
                        }
                        this.Close();
                    }
                    else
                    {
                        textBox1.AppendText("No elements found");
                    }
                }
                catch (Exception ex)
                {
                    textBox1.AppendText("error was thrown: " + ex.Message);
                }
            }
            else
            {
                textBox1.AppendText("file not found");
            }

        }
    }
}
```
The above opens the config file, changes the value named Version, saves the file back and then closes the form if everything is successfull. If not it will remain open and show the error message until the user closes the form. Simple.

Now comes the difficult thing. 

You have to add a Custom action to your setup project and this in the Commit category. You add the install helper primary output and you give you change its properties. See Image for more details.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/setup/setup2.png" alt="" title="" width="598" height="390" />
</div>

The important thing is the 

> \[ProgramFilesFolder\]\[Manufacturer\][ProductName]WebUpdateClient.exe

You will have to change the WebUpdateClient.exe to the name of your exe, you have to do this for and arguments and CustomActionData. And you have to set InstallerClass to False.

And that&#8217;s it. Very easy but a real timewaster if you don&#8217;t get it right the first time ;-).

 [1]: http://www.eggheadcafe.com/tutorials/aspnet/ec13b205-9a90-496f-9d7b-ccebdf1d3ca1/build-a-custom-action-ins.aspx