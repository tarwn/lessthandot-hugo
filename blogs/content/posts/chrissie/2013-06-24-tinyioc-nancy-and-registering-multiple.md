---
title: TinyIoC/Nancy and registering multiple implementations of the same interface the easy way.
author: Christiaan Baes (chrissie1)
type: post
date: 2013-06-24T18:30:00+00:00
ID: 2116
excerpt: Registering multiple implementations of an interface in TinyIoC.
url: /index.php/webdev/serverprogramming/tinyioc-nancy-and-registering-multiple/
views:
  - 9715
categories:
  - ASP.NET
  - Server Programming
tags:
  - 'c#'
  - nancy
  - tinyioc

---
[TinyIoC][1], the default IoC container for [Nancy][2], does not autoregister multiple instances for the same interface. 

So the following code will give you two 0&#8217;s. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            var container = new TinyIoC.TinyIoCContainer();
            container.AutoRegister();
            Console.WriteLine(container.Resolve&lt;C3&gt;().NumberOfElements);
            Console.WriteLine(container.Resolve&lt;IEnumerable&lt;I1&gt;&gt;().Count());
            Console.ReadLine();
        }
    }

    public interface I1
    {
        
    }
    public class C1:I1
    {
        
    }
    public class C2:I1
    {
        
    }

    public class C3
    {
        private int _count;

        public C3(IEnumerable&lt;I1&gt; list)
        {
            _count = list.Count();
        }

        public int NumberOfElements { get { return _count; }
        }
    }

}
```
There is one way of registering these instances and that is by using container.RegisterMultiple

```csharp
container.RegisterMultiple&lt;I1&gt;(new[] {typeof (C1), typeof (C2)});
            ```
But when I add implementations I have to update that and I could forget to do that. So I need a way to automate that. 

With a little help of [StackOverflow][3], because it is easier to copy/paste then to think myself, I came to this. 

```csharp
var container = new TinyIoC.TinyIoCContainer();
            var type = typeof(I1);
            var types = AppDomain.CurrentDomain.GetAssemblies().ToList()
                .SelectMany(s =&gt; s.GetTypes())
                .Where(x =&gt; type.IsAssignableFrom(x) && x.IsClass);
            container.RegisterMultiple&lt;I1&gt;(types.ToArray());
            ```
And now I can add implementations and the container will pick it up.

GrumpyDev azures me this will be included in the next version of TinyIoC and Nancy.

So the above is for TinyIoC 1.2 and Nancy 0.17.1

 [1]: https://github.com/grumpydev/TinyIoC
 [2]: http://nancyfx.org/
 [3]: http://stackoverflow.com/questions/26733/getting-all-types-that-implement-an-interface-with-c-sharp-3-0