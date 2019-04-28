---
title: A case for PEVerify and a bug in the VB.Net compiler
author: Christiaan Baes (chrissie1)
type: post
date: 2011-10-30T05:56:00+00:00
ID: 1365
excerpt: 'So this is going to be a long blogpost. I got this code from Katrien De Graeve last Friday. She got a mail from someone in the community where something would compile in VS2008 but not in VS2010. In essence I could reproduce the behavior&hellip;'
url: /index.php/desktopdev/mstech/a-case-for-peverify-and/
views:
  - 4932
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - bug
  - compiler
  - peverify
  - vb.net

---
## Introduction

So this is going to be a long blogpost. I got this code from [Katrien De Graeve][1] last Friday. She got a mail from someone in the community where something would compile in VS2008 but not in VS2010. In essence I could reproduce the behavior and I contacted [Lucian Wischik][2] (Spec Lead for Visual Basic) because I found the problem intriguing. I did not find a connect item on this problem.

## The problem

This is the code you need to reproduce the problem.

```vbnet
Module Module1

    Public Delegate Sub Callback(Of T)(ByVal t As T)
    
    Public Class FunctionArgs

    End Class

    Public Class BaseType

    End Class

    Public Class DerivedType
        Inherits BaseType

    End Class
    
    Public Class BaseClass(Of T)

        Public Overridable Sub Go()

        End Sub

    End Class

    Public MustInherit Class MiddleClass(Of T As BaseType)
        Inherits BaseClass(Of T)
        
        &lt;Obsolete("Please use Go(ByVal aArgs As FunctionArgs)")&gt; _
        Public Overrides Sub Go()
            MyBase.Go()
        End Sub

        Public Overridable Overloads Sub Go(ByVal aArgs As FunctionArgs)
            Me.Go(aArgs, False)
        End Sub

        Public Overridable Overloads Sub Go(ByVal aArgs As FunctionArgs, ByVal aCheckObjectAndTaskTogether As Boolean)

        End Sub

    End Class

    Public Class InterestingClass
        Inherits MiddleClass(Of DerivedType)

        Public Overrides Sub Go(ByVal aArgs As FunctionArgs, ByVal aCheckObjectAndTaskTogether As Boolean)

        End Sub

    End Class

    Sub Main()
        Dim task As New InterestingClass
        Dim c As New Callback(Of FunctionArgs)(AddressOf task.Go)
    End Sub

End Module
```
This will compile just fine in VB9, VB10 and even VB11. The problem is when you run this it will give you an exception.

> System.TypeLoadException was unhandled
    
> Message=Could not load type &#8216;MiddleClass\`1&#8217; from assembly &#8216;ConsoleApplication6, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null&#8217;.
    
> Source=ConsoleApplication6
    
> TypeName=MiddleClass\`1
    
> StackTrace:
         
> at ConsoleApplication6.Module1.Main()
         
> at System.AppDomain._nExecuteAssembly(RuntimeAssembly assembly, String[] args)
         
> at System.AppDomain.ExecuteAssembly(String assemblyFile, Evidence assemblySecurity, String[] args)
         
> at Microsoft.VisualStudio.HostingProcess.HostProc.RunUsersAssembly()
         
> at System.Threading.ThreadHelper.ThreadStart_Context(Object state)
         
> at System.Threading.ExecutionContext.Run(ExecutionContext executionContext, ContextCallback callback, Object state, Boolean ignoreSyncCtx)
         
> at System.Threading.ExecutionContext.Run(ExecutionContext executionContext, ContextCallback callback, Object state)
         
> at System.Threading.ThreadHelper.ThreadStart()
    
> InnerException: 

It should not give you this exception.

In essence this means that it can&#8217;t find the method <code class="codespan">Public Overridable Overloads Sub Go(ByVal aArgs As FunctionArgs)</code> which is in the superclass. And there is no good reason why it should not take the method in the superclass, after all marking a method as Overridable means that we can override it but we don&#8217;t have to, and if we don&#8217;t override it in our subclass than the compiler should just take the method from the superclass, which it clearly doesn&#8217;t do in this instance.

I even tried to do the same thing in C# and that just passed.

```csharp
using System;
 
namespace ConsoleApplication1
{

    internal class Program
    {
        private static void Main(string[] args)
        {
            var task = new InterestingClass();
            var c = new Callback&lt;FunctionArgs&gt;(task.Go);
        }
    }

    public delegate void Callback&lt;T&gt;(T t);

    public class FunctionArgs
    {
    }

    public class BaseType
    {
    }

    public class DerivedType : BaseType
    {
    }

    public class BaseClass&lt;T&gt;
    {

        public virtual void Go()
        {
        }
    }

    public class MiddleClass&lt;T&gt; : BaseClass&lt;T&gt;
    {

        [Obsolete("Please use Go(ByVal aArgs As FunctionArgs)")]
        public override void Go()
        {
            base.Go();
        }

        public virtual void Go(FunctionArgs aArgs)
        {
            this.Go(aArgs, false);
        }

        public virtual void Go(FunctionArgs aArgs, Boolean aCheckObjectAndTaskTogether)
        {
        }
    }

    public class InterestingClass : MiddleClass&lt;DerivedType&gt;
    {

        public override void Go(FunctionArgs aArgs, Boolean aCheckObjectAndTaskTogether)
        { }

    }
}```
## PEVerify

I then mailed Lucian, who confirmed this to be a bug. He also said this.

> VS2008SP1 &#8212; the generated exe fails PEVerify and crashes at runtime with an “Unable to load type” error
> 
> VS2010SP1 &#8212; the generated exe fails PEVerify and crashes at runtime with an “Unable to load type” error
> 
> VS.Next (Dev11) &#8212; the generated exe fails PEVerify, but succeeds at runtime

I never thought about using PEVerify. But it would have saved some time. 

This the error you will get when you do the PEVerify.

> Microsoft (R) .NET Framework PE Verifier. Version 4.0.30319.1
  
> Copyright (c) Microsoft Corporation. All rights reserved.
> 
> [IL]: Error: [F:MyDocumentsVisual Studio 2010ProjectsConsoleApplication6bin
  
> Debugconsoleapplication6.exe : ConsoleApplication6.Module1::Main] Type load fa
  
> iled.
  
> 1 Error(s) Verifying consoleapplication6.exe

So running PEVerify after a compile would have shown the error very quickly.

## Solutions

There are several ways to work around this bug.

From the original author.

First of all you can just add this to Interestingclass.

```
Public Overrides Sub Go(ByVal aArgs As FunctionArgs)

  MyBase.Go(aArgs)

End Sub```
That way the compiler doesn&#8217;t have to look for it.

From Lucian.

Another workaround: you could also fix it with “AddressOf CType(task, MiddleClass(Of DerivedType)).Go” to tell it explicitly which class declared the “Go” method.

And this workaround also from Lucian.

```vbnet
Dim c = Sub(x As FunctionArgs) task.Go(x)```
or

```vbnet
Dim c As New Callback(Of FunctionArgs)(Sub(x) task.Go(x))```
This makes avoids the faulty “AddressOf” by instead using a lambda.

## Conclusion

There is a small bug in the AddressOf algorithm that goes in search of a method. Running PEverify after the compile would have shown that. Lambdas don&#8217;t use the same algorithm. The solution is simple. 

Thanks to Katrien and the original author for finding this. Thanks to Lucian for responding my mails on a Saturday. The VB.Net community just rocks.

 [1]: http://blogs.msdn.com/b/katriend/
 [2]: http://www.wischik.com/lu/