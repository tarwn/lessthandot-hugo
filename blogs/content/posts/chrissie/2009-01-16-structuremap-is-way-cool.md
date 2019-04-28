---
title: StructureMap is way cool.
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-16T18:35:48+00:00
ID: 287
url: /index.php/desktopdev/mstech/structuremap-is-way-cool/
views:
  - 6143
categories:
  - Microsoft Technologies
tags:
  - buildup
  - 'c#'
  - structuremap

---
And it just got cooler. After I read this [&#8220;BuildUp&#8221; Existing Objects with StructureMap][1] I got an aha moment. The BuildUp function that was recently added just solved my very ugle winforms usercontrol problem. I use to have to add objectfactory.getinstance code in the constructors to get my dependencies just to keep the designer happy. I hate to design winforms without the designer but the designer hates me. But now this love hate relationship has bcome a little more livable. And all this because of [Jeremy Miller][2]. Thank you [Jeremy][2] once again.

What you are about to see is called C#. I know you&#8217;re not used to it from me but I will translate it to VB.Net this weekend. I promise.

I did not harm any real forms for this little example but I hope this proofs my point just as well.

Let&#8217;s take this case. A form has 2 usercontrols. Each usercontrol has a dependecy on a certain service. 

Lets start with the services. These are pretty simple. I also made some interfaces to go with them.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Structuremaptrial.Services
{
    public interface IService1
    {
        String Test { get; }
    }
}
```
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Structuremaptrial.Services
{
    public interface IService2
    {
        String Test { get; }
    }
}
```
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Structuremaptrial.Services
{
    public class Service1 : Services.IService1
    {
        public string Test
        {
            get { return "service1"; }
        }
    }
}
```
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Structuremaptrial.Services
{
    public class Service2 : Services.IService2
    {
        public string Test
        {
            get { return "service2"; }
        }
    }
}
```
As you can see when service1 is initialized it will return service1, service2 will return service2.

So then I made a usercontrol baseclass.

```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Drawing;
using System.Data;
using System.Linq;
using System.Text;
using System.Windows.Forms;

namespace Structuremaptrial.View.Controls
{
    public partial class Basecontrol : UserControl
    {
        public Basecontrol()
        {
            InitializeComponent();

            StructureMap.ObjectFactory.BuildUp(this);
        }
    }
}
```
This one, and only this one ;-). contains the buidlup method that does all the magic.

And then I made two very simple usercontrols.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Structuremaptrial.View.Controls
{
    public class UserControl1 : Basecontrol
    {
        public Services.IService1 Service{ get; set; }
    }
}
```
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Structuremaptrial.View.Controls
{
    public class UserControl2 : Basecontrol
    {
        public Services.IService2 Service { get; set; }
    }
}
```
As you can see Usercontrol1 needs service1 and Usercontrol2 needs service2.

As you can see there is no initialisation code for the services. Nowhere.

And then it&#8217;s time for the form.

I also interfaced that.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Structuremaptrial.View.Forms
{
    public interface IForm1
    {
    }
}
```
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Structuremaptrial.View.Forms
{
    public class Form1 : IForm1
    {
        public Form1()
        {
            View.Controls.UserControl1 UserControl1 = new View.Controls.UserControl1();
            View.Controls.UserControl2 UserControl2 = new View.Controls.UserControl2();
            Console.WriteLine(UserControl1.Service.Test);
            Console.WriteLine(UserControl2.Service.Test); 
        }
    }
}
```
As you can see no initialization for the service here either. But I do call them to print the text. So all logic tell us that a terrible nullreferenceexception will fall upon us. Shame on us.

But not to worry.

First we do some structuremap magic and initialize it.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Structuremaptrial.IoC
{
    public class Configure
    {
        public void Setup()
        {
            StructureMap.ObjectFactory.Initialize(x =&gt;
            {

                x.ForRequestedType&lt;Services.IService1&gt;().TheDefault.Is.OfConcreteType&lt;Services.Service1&gt;();
                x.ForRequestedType&lt;Services.IService2&gt;().TheDefault.Is.OfConcreteType&lt;Services.Service2&gt;();
                x.ForRequestedType&lt;View.Forms.IForm1&gt;().TheDefault.Is.OfConcreteType&lt;View.Forms.Form1&gt;();
                x.SetAllProperties(y =&gt;
                {
                    y.OfType&lt;Services.IService1&gt;();
                    y.OfType&lt;Services.IService2&gt;();
                });
            }
            );
        }
    }
}
```
As you can see I use the SetAllProperties so that structuremap knows that when it encounters this properties it needs to fill them. And that is what BuildUp does.

And now I want to spike what I have there.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Structuremaptrial
{
    class Program
    {
        static void Main(string[] args)
        {
            var conf = new IoC.Configure();
            conf.Setup();
            var form1 = StructureMap.ObjectFactory.GetInstance&lt;View.Forms.IForm1&gt;();
            Console.ReadLine();
        }
    }
}
```
Which as you will see if you run this will give you this.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Structuremapresult.jpg" alt="" title="" width="254" height="183" />
</div>

Ain&#8217;t it purdy? I think that says it all. 

This was fun ;-).

 [1]: http://codebetter.com/blogs/jeremy.miller/archive/2009/01/16/quot-buildup-quot-existing-objects-with-structuremap.aspx
 [2]: http://codebetter.com/blogs/jeremy.miller/default.aspx