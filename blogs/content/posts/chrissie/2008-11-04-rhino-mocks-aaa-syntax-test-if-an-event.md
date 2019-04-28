---
title: Rhino mocks AAA syntax test if an event was raised
author: Christiaan Baes (chrissie1)
type: post
date: 2008-11-04T13:29:13+00:00
ID: 193
url: /index.php/desktopdev/mstech/rhino-mocks-aaa-syntax-test-if-an-event/
views:
  - 2888
categories:
  - Microsoft Technologies

---
Well, here is the code I came up with to test this. Trying it out seems like the best way. And good code is always self explanatory.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Rhino.Mocks;

namespace ConsoleApplication2
{
    [NUnit.Framework.TestFixture]
    public class Program
    {
        static void Main(string[] args)
        {
            var toThrow = new ToThrow();
            ICatchThrow catchThrow = new CatchThrow();
            toThrow.throwevent += catchThrow.catchthrow;
            toThrow.voidthrowingevent("");
        }

        public delegate void mydelegate(string param);

        public class ToThrow
        {
            public event mydelegate throwevent;

            public void voidthrowingevent(string param)
            {
                throwevent(param);
            }
        }

        public class CatchThrow : ICatchThrow
        {
            public void catchthrow(string param)
            {
                Console.WriteLine("Thrown" + param);
                Console.ReadLine();
            }
        }

        public interface ICatchThrow
        {
            void catchthrow(string param);
        }

        [NUnit.Framework.Test]
        public void Test_If_Event_Gets_Thrown()
        {
            var toThrow = new ToThrow();
            var catchThrow = MockRepository.GenerateStub&lt;ICatchThrow&gt;();
            toThrow.throwevent += catchThrow.catchthrow;
            toThrow.voidthrowingevent("");
            catchThrow.AssertWasCalled(e =&gt; e.catchthrow(""));
        }
    }
}
```