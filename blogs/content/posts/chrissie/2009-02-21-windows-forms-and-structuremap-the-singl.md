---
title: Windows forms and structuremap the single instance form
author: Christiaan Baes (chrissie1)
type: post
date: 2009-02-21T07:45:49+00:00
ID: 332
url: /index.php/desktopdev/mstech/windows-forms-and-structuremap-the-singl/
views:
  - 8638
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - structuremap

---
So we have a windows forms application and some of these forms can only be used once. Like for instance a dashboard. Everytime we show the form we just want it to either show a new one, if it&#8217;s the first time opened, or we want to show the one that already exists. In essence, this would be good case for a singleton pattern. But the singleton pattern is evil. Why is it evil? Because it is hard to unittest. And it makes us do something that we really don&#8217;t want to. 

So having established that we got to find a better solution for this poblem. And we have. It&#8217;s called StructureMap. StructureMap gives you the possibility to create singletons from normal classes. StructureMap know when to create a new one and when to give you the one that already exists.

So let&#8217;s test this. We first take the code from my [previous blogpost][1]. And then add this little test to it. 

```csharp
using NUnit.Framework;
using Structuremaptrial.View.Forms;

namespace Structuremaptrial.IoC.Tests
{
    [TestFixture]
    public class TestConfigure
    {
        private IoC.Configure configure;
 
        [SetUp]
        public void Setup()
        {
            configure = new IoC.Configure();
            configure.Setup();
        }

        [TearDown]
        public  void Teardown()
        {

        }

        [Test]
        public void Can_open_Iform1_twice()
        {
            IForm1 form1;
            form1 = StructureMap.ObjectFactory.GetInstance&lt;IForm1&gt;();
            form1.DoShow();
            form1.DoClose();
            form1 = StructureMap.ObjectFactory.GetInstance&lt;IForm1&gt;();
            form1.DoShow();
            form1.DoClose();
        }

    }
}
```
Form1 is **not** a singleton, as can be seen in this structuremap configuration file. And the test passes. 

```csharp
x.ForRequestedType&lt;View.Forms.IForm1&gt;().TheDefault.Is.OfConcreteType&lt;View.Forms.Form1&gt;();
                ```
However IForm3 is a singleton.

```csharp
x.ForRequestedType&lt;View.Forms.IForm3&gt;().AsSingletons().TheDefault.Is.OfConcreteType&lt;View.Forms.Form3&gt;();
                ```
And when we run a similar test against IForm3. Like this.

```csharp
[Test]
        public void Can_open_Iform3_twice()
        {
            IForm3 form3;
            form3 = StructureMap.ObjectFactory.GetInstance&lt;IForm3&gt;();
            form3.DoShow();
            form3.DoClose();
            form3 = StructureMap.ObjectFactory.GetInstance&lt;IForm3&gt;();
            form3.DoShow();
            form3.DoClose();
        }```
It to goes green. Great, I hear you think. StructureMap is great. It is, however, I cheated.

Lets make IForm1 a Singleton and see what happens.

```csharp
x.ForRequestedType&lt;View.Forms.IForm1&gt;().AsSingletons().TheDefault.Is.OfConcreteType&lt;View.Forms.Form1&gt;();
                ```
It doesn&#8217;t pass the test. I can only open the form once after that it doesn&#8217;t work anymore.

It fails with this shiny error message.

> **TestConfigure.Can\_open\_Iform1_twice : FailedSystem.ObjectDisposedException: Geen toegang tot een verwijderd object.
  
> Objectnaam: Form1.
  
> bij System.Windows.Forms.Control.CreateHandle()
  
> bij System.Windows.Forms.Form.CreateHandle()
  
> bij System.Windows.Forms.Control.get_Handle()
  
> bij System.Windows.Forms.Control.SetVisibleCore(Boolean value)
  
> bij System.Windows.Forms.Form.SetVisibleCore(Boolean value)
  
> bij System.Windows.Forms.Control.Show()
  
> bij Structuremaptrial.View.Forms.BaseForm.DoShow() in BaseForm.cs: line 7
  
> bij Structuremaptrial.IoC.Tests.TestConfigure.Can\_open\_Iform1_twice() in TestConfigure.cs: line 32** 

Sorry for the Dutch errormessage (I wish they didn&#8217;t do that). But it says No access to a disposed object. Fun. 

What is the problem? Well, a Windows form doesn&#8217;t really get disposed when a user clicks on the close button (which they tend to do quite often), it gets put in a disposed state but does not become null or nothing. The IsDisposed attribute of the form will show true but that is of little help. This is a problem for a singleton created with StructureMap because StructureMap checks if it has created the object and if it is null. It doesn&#8217;t check for IsDisposed because a Windows form is the only object that has this strange behaviour. 

So what to do? I could write an patch for StructureMap and get it over and done with. But I didn&#8217;t think that was a great idea. StructureMap is there to do the right thing, not the bizarre thing. So we have to find another solution. And there is one.

And it was in Form3 already. 

```csharp
private void Form3_FormClosing(object sender, System.Windows.Forms.FormClosingEventArgs e)
        {
            if (e.CloseReason != CloseReason.UserClosing) return;
            e.Cancel = true;
            this.Hide();
        }
```
This piece of code just hides the form and makes sure that it doesn&#8217;t get disposed. And this is what I really want the form to be. It is a singleton, after all. Once it is created, it should be there for me. The above code just makes sure of that. 

So as you can see, the above test are not natural. You are testing the container in, essence. But I also prevents bugs. 

<span class="MT_red">Little gotcha.</span>

```csharp
IForm1 form1;
            form1 = StructureMap.ObjectFactory.GetInstance&lt;IForm1&gt;();
            form1.DoClose();
            form1 = StructureMap.ObjectFactory.GetInstance&lt;IForm1&gt;();
            form1.DoClose();```
<span class="MT_red">The above test always passes. For some reason, the event form_closing doesn&#8217;t get called if the form hasn&#8217;t been shown, while the Addhandler routine has run no matter if the form was shown. They probably check if the form is visible. So if you call Close on a form that is not visible it won&#8217;t actually go in a disposed state, it will just remain invisible.</span>

 [1]: /index.php/All/?p=338