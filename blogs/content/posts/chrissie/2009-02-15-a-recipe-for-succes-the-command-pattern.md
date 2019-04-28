---
title: 'A Recipe for Success: The Command Pattern, StructureMap and a dash of Generics'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-02-15T15:47:45+00:00
ID: 327
url: /index.php/desktopdev/mstech/a-recipe-for-succes-the-command-pattern/
views:
  - 11973
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'
  - command patter
  - structuremap
  - vb.net

---
So you have lots of forms in your winforms application and you have to open them from several places/forms. But you don&#8217;t want to repeat yourself all the time. And you like to keep things centralized. You also want it to be flexible, if possible and easily extendable/extensible/extendingable, uhm, be able to extend it in an easy manner. I think this is a typical case for the command pattern. So let&#8217;s start with that.

Warning! what follows is a lot of code. So this will be a long post. But you can skip all the parts you don&#8217;t like. Not that you will.

Let&#8217;s start by creating a form.

```csharp
namespace Structuremaptrial.View.Forms
{
    public class Form1 : System.Windows.Forms.Form
    {
       public Form1()
       {
           this.Text = "Form1";
       }
    }
}```
Now let&#8217;s create an other form that has a button that Opens Form1.

```csharp
namespace Structuremaptrial.View.Forms
{
    public class MenuForm : System.Windows.Forms.Form
    {
       public MenuForm()
       {
           InitializeComponent();
           this.Text = "MenuForm";
       }

        private void OpenForm1_Click(object sender, EventArgs e)
        {
            var form = new Form1();
            form.Show();
        }
    }
}```
That looks simple enough.

But hey, What if we have the need for a third form and that needs to be opened in Form1 and MenuForm? What if we need Form1 to get dependencies like a service or a controller or a viewmodel?

Let&#8217;s change Form1, shall we, to take a controller?

```csharp
namespace Structuremaptrial.View.Forms
{
    public class Form1 : System.Windows.Forms.Form
    {
       private form1Controller controller
       public Form1(Form1Controller Controller)
       {
           this.Text = "Form1";
           this.controller = Controller;
       }
    }
}```
Now we have to change MenuForm.

```csharp
namespace Structuremaptrial.View.Forms
{
    public class MenuForm : System.Windows.Forms.Form
    {
       public MenuForm()
       {
           InitializeComponent();
           this.Text = "MenuForm";
       }

        private void OpenForm1_Click(object sender, EventArgs e)
        {
            var Form1Controller form1Controller = new Form1Controller();
            var form = new Form1(form1Controller);
            form.Show();
        }
    }
}```
Can you imagine having to do that all over the place?? And having to do that everytime a dependency on form changes?? I think not.

Well StructureMap is very good at resolving dependecies. And the commandpattern is very good for avoiding the repitition.

What structuremap also likes is abstraction. So let&#8217;s Abstract a little here.

First let&#8217;s abstract Form1

```csharp
namespace Structuremaptrial.View.Forms
{
    public interface IForm1
    {
        void DoShow();
    }
}```
Now let&#8217;s have form1 implement that interface.

```csharp
namespace Structuremaptrial.View.Forms
{
    public class Form1 : System.Windows.Forms.Form: IForm1
    {
       private form1Controller controller
       public Form1(IForm1Controller Controller)
       {
           this.Text = "Form1";
           this.controller = Controller;
       }
       public void DoShow()
       {
           this.Show();
       }
    }
}```
Ok. And now we configure StructureMap so that we can easily make this stuff.

You can do this in your main.

```csharp
StructureMap.ObjectFactory.Initialize(x =&gt;
            {
                x.ForRequestedType&lt;View.Forms.IForm1&gt;().TheDefault.Is.OfConcreteType&lt;View.Forms.Form1&gt;();
                x.ForRequestedType&lt;View.Forms.IForm1Controller&gt;().TheDefault.Is.OfConcreteType&lt;View.Forms.Form1Controller&gt;();
               
            }```
And now we can change our menuform to this.

```csharp
namespace Structuremaptrial.View.Forms
{
    public class MenuForm : System.Windows.Forms.Form
    {
       public MenuForm()
       {
           InitializeComponent();
           this.Text = "MenuForm";
       }

        private void OpenForm1_Click(object sender, EventArgs e)
        {
            var form = StructurMap.ObjectFactory.GetInstance&lt;View.Forms.IForm1&gt;;
            form.DoShow();
        }
    }
}```
This seems nice but we don&#8217;t want to have to throw around the GetInstance everywhere. To be honest, if you have more than one GetInstance in your application then you have a code smell. First thing we could do is have form1 injected as a dependency. Like this.

```csharp
namespace Structuremaptrial.View.Forms
{
    public class MenuForm : System.Windows.Forms.Form
    {
       private IForm1 form1;

       public MenuForm(IForm1 Form1)
       {
           InitializeComponent();
           this.Text = "MenuForm";
           form1 = Form1;
       }

        private void OpenForm1_Click(object sender, EventArgs e)
        {
            form1.DoShow();
        }
    }
}```
Looks good but it gets boring if you have to do this.

```csharp
namespace Structuremaptrial.View.Forms
{
    public class MenuForm : System.Windows.Forms.Form
    {
       private IForm1 form1;
       private IForm2 form2;
       private IForm3 form3;
       private IForm4 form4;
       private IForm5 form5;
       private IForm6 form6;
       private IForm7 form7;

       public MenuForm(IForm1 Form1,IForm2 Form2,IForm3 Form3,IForm4 Form4,IForm5 Form5,IForm6 Form6,IForm7 Form7)
       {
           InitializeComponent();
           this.Text = "MenuForm";
           form1 = Form1;
           form2 = Form2;
           form3 = Form3;
           form4 = Form4;
           form5 = Form5;
           form6 = Form6;
           form7 = Form7;
       }

        private void OpenForm1_Click(object sender, EventArgs e)
        {
            form1.DoShow();
        }
        private void OpenForm2_Click(object sender, EventArgs e)
        {
            form2.DoShow();
        }
        private void OpenForm3_Click(object sender, EventArgs e)
        {
            form3.DoShow();
        }
        private void OpenForm4_Click(object sender, EventArgs e)
        {
            form4.DoShow();
        }
        private void OpenForm5_Click(object sender, EventArgs e)
        {
            form5.DoShow();
        }
        private void OpenForm6_Click(object sender, EventArgs e)
        {
            form6.DoShow();
        }
        private void OpenForm7_Click(object sender, EventArgs e)
        {
            form7.DoShow();
        }
    }
}```
Can you see it getting out of hand when you have dozens of these forms? If not hundreds? I can.

So let&#8217;s improve on that a little and let&#8217;s get the command pattern to help. Let&#8217;s create a command invoker first.

Let&#8217;s call it Navigation and let&#8217;s make a nice interface for it too, so that we can inject it with StructureMap. 

```csharp
namespace Structuremaptrial.Controller
{
    public interface INavigation
    {
        void Execute&lt;INavigationCommand&gt;() where INavigationCommand : NavigationCommands.INavigationCommand, new();
    }
}```
```csharp
namespace Structuremaptrial.Controller
{
    public class Navigation : INavigation
    {
        public void Execute&lt;INavigationCommand&gt;() where INavigationCommand : NavigationCommands.INavigationCommand, new()
        {
            var command = new INavigationCommand();
            command.Execute();
        }
    }
}```
I made it generic to start with because this post was getting a bit long ;-). But of course we also need a Command class to deal with the command.

INavigationCommand is a real simple interface.

```csharp
public interface INavigationCommand
    {
        void Execute();
    }```
```csharp
public class OpenForm1 : INavigationCommand
    {
        private Form1 form1

        public void Execute()
        {
            var form = StructurMap.ObjectFactory.GetInstance&lt;View.Forms.IForm1&gt;;
            form.DoShow();
        }
    }```
Now we can have a new MenuForm too.

```csharp
namespace Structuremaptrial.View.Forms
{
    public class MenuForm : System.Windows.Forms.Form
    {
       private INavigation navigation;

       public MenuForm(INavigation Navigation)
       {
           InitializeComponent();
           this.Text = "MenuForm";
           navigation = Navigation;
       }

        private void OpenForm1_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm1&gt;();
        }
        private void OpenForm2_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm2&gt;();
        }
        private void OpenForm3_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm3&gt;();
        }
        private void OpenForm4_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm4&gt;();
        }
        private void OpenForm5_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm5&gt;();
        }
        private void OpenForm6_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm6&gt;();
        }
        private void OpenForm7_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm7&gt;();
        }
    }
}```
Of course, all the above works. But as you can see, you have to create a command for each form and we still have to use GetInstance. We could have, of course, injected all the commands in the Navigation claas or injected an array of them but that would not have been very handy, you will have to trust me on this. And with the above method we can&#8217;t get rid of the getinstance because the command has not been created by structuremap. But we have a solution and it&#8217;s called BuildUp.

Let&#8217;s create a generic class that uses BuildUp, shall we?

```csharp
public class OpenForm&lt;FormType&gt; : INavigationCommand
    {
        public FormType Form { get; set; }
        
        public OpenForm()
        {
           StructureMap.ObjectFactory.BuildUp(this);
        }

        public void Execute()
        {
            Form.DoShow();
        }
    }```
I don&#8217;t have to change anything to Navigation but I will have to change something to my Commands, or I could just delete them and have the MenuForm use the Generic instance instead.

If I want to change the MenuForm then it would be something like this.

```csharp
namespace Structuremaptrial.View.Forms
{
    public class MenuForm : System.Windows.Forms.Form
    {
       private INavigation navigation;

       public MenuForm(INavigation Navigation)
       {
           InitializeComponent();
           this.Text = "MenuForm";
           navigation = Navigation;
       }

        private void OpenForm1_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm&lt;IForm1&gt;&gt;();
        }
        private void OpenForm2_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm&lt;IForm2&gt;&gt;();
        }
        private void OpenForm3_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm&lt;IForm3&gt;&gt;();
        }
        private void OpenForm4_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm&lt;IForm4&gt;&gt;();
        }
        private void OpenForm5_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm&lt;IForm5&gt;&gt;();
        }
        private void OpenForm6_Click(object sender, EventArgs e)
        {
            navigation.Execute&lt;OpenForm&lt;IForm6&gt;&gt;();
        }
        private void OpenForm7_Click(object sender, EventArgs e)
        {
            navigation.ExecuteOpenForm&lt;IForm7&gt;&gt;();
        }
    }
}```
Or by changing the Commands

Of course we will also have to adapt our structuremap configuration to get it all to work.

```csharp
StructureMap.ObjectFactory.Initialize(x =&gt;
            {
                x.ForRequestedType&lt;View.Forms.IForm1&gt;().TheDefault.Is.OfConcreteType&lt;View.Forms.Form1&gt;();
                x.ForRequestedType&lt;View.Forms.IForm2&gt;().TheDefault.Is.OfConcreteType&lt;View.Forms.Form2&gt;();
                x.ForRequestedType&lt;View.Forms.IForm3&gt;().AsSingletons().TheDefault.Is.OfConcreteType&lt;View.Forms.Form3&gt;();
                x.ForRequestedType&lt;View.Forms.IForm&gt;().TheDefault.Is.OfConcreteType&lt;View.Forms.MainForm&gt;();
                x.ForRequestedType&lt;Services.IService1&gt;().TheDefault.Is.OfConcreteType&lt;Services.Service1&gt;();
                x.ForRequestedType&lt;Services.IService2&gt;().TheDefault.Is.OfConcreteType&lt;Services.Service2&gt;();
                x.ForRequestedType&lt;Controller.INavigation&gt;().TheDefault.Is.OfConcreteType&lt;Controller.Navigation&gt;();
                x.SetAllProperties(y =&gt;
                {
                    y.OfType&lt;View.Forms.IForm1&gt;();
                    y.OfType&lt;View.Forms.IForm2&gt;();
                    y.OfType&lt;View.Forms.IForm3&gt;();
                });
            }
            );```
I have made a solution for you all so that you can download and try it.

There is a VB.Net version of this code in there too. 

[StructureMapTrial.zip][1]

I hope this will help someone. I know it helped me.;-)

 [1]: /wp-content/uploads/blogs/DesktopDev//Structuremaptrial.zip ""