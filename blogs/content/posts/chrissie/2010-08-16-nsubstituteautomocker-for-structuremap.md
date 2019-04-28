---
title: NSubstituteAutoMocker for StructureMap
author: Christiaan Baes (chrissie1)
type: post
date: 2010-08-16T05:15:25+00:00
ID: 872
url: /index.php/desktopdev/mstech/nsubstituteautomocker-for-structuremap/
views:
  - 4532
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - automocking
  - nsubstitute
  - structuremap

---
## Introduction

I use the [Automocking][1] feature of [StructureMap][2] quite a lot. But they only have support for [Rhino Mocks][3] and [Moq][4]. Now there is a new mocking framework, [NSubstitute][5], and I would like to use that. But for that I needed an AutoMocker to take some of the work out of my hands. The StructureMap AutoMocker makes it easy for you to extend it.
  
There are 2 ways you can extend the framework.

1. You can fork the StructureMap code and add the Automocker to it and then work with you compiled fork. Perhaps even giving back to the StructureMap team what you made. Or you can just reference the binaries and go from there. I choose to do the first.

## The Tests

First the test. This is what I want it to do when it works.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using NUnit.Framework;
using StructureMap.AutoMocking;
using NSubstitute;

namespace StructureMap.Testing.NSubstitute
{
    [TestFixture]
    public class NSubstituteAutoMockerTester
    {
        [Test]
        public void IfClassUnderTestReturnsAnObjectOfTypeTestClass()
        {
            var mocks = new NSubstituteAutoMocker&lt;TestClass&gt;();
            var test = mocks.ClassUnderTest;
            Assert.IsInstanceOfType(typeof(TestClass),test);
        }

        [Test]
        public void IfClassUnderTestIsNotNull()
        {
            var mocks = new NSubstituteAutoMocker&lt;TestClass&gt;();
            var test = mocks.ClassUnderTest;
            Assert.IsNotNull(test);
        }

        [Test]
        public void IfGetIClassUnderTestIsNotNull()
        {
            var mocks = new NSubstituteAutoMocker&lt;TestClass&gt;();
            var test = mocks.ClassUnderTest;
            Assert.IsNotNull(mocks.Get&lt;ITestClass&gt;());
        }

        [Test]
        public void IfGetITestClassIsSameAsClassUnderTestITestClass()
        {
            var mocks = new NSubstituteAutoMocker&lt;TestClass&gt;();
            var test = mocks.ClassUnderTest;
            Assert.AreSame(mocks.Get&lt;ITestClass&gt;(), test.TestClass1);
        }

        [Test]
        public void IfDependecyReturnsValueWhenSet()
        {
            var mocks = new NSubstituteAutoMocker&lt;TestClass&gt;();
            var test = mocks.ClassUnderTest;
            var itest = mocks.Get&lt;ITestClass&gt;();
            itest.returnInt().Returns(3);
            Assert.AreEqual(3, test.returnInt());
        }
        
    }

    public class TestClass
    {
        private ITestClass _test;

        public ITestClass TestClass1
        {
            get { return _test; }
        }

        public TestClass(ITestClass test)
        {
            _test = test;
        }

        public int returnInt()
        {
            return _test.returnInt();
        }
    }

    public interface ITestClass
    {
        int returnInt();
    }
}
```
## The implementation

You just need to add 3 files. The first inherits from AutoMocker<>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace StructureMap.AutoMocking
{
    public class NSubstituteAutoMocker&lt;TARGETCLASS&gt; : AutoMocker&lt;TARGETCLASS&gt; where TARGETCLASS : class
    {
        public NSubstituteAutoMocker()
        {
            _serviceLocator = new NSubstituteServiceLocator();
            _container = new AutoMockedContainer(_serviceLocator);
        }
    }
}
```
And that was very easy ;-).

The second implements ServiceLocator.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace StructureMap.AutoMocking
{
    class NSubstituteServiceLocator: ServiceLocator
    {

        #region ServiceLocator Members

        private readonly NSubstituteFactory _substitutes = new NSubstituteFactory();

        public T Service&lt;T&gt;() where T : class
        {
            return (T)_substitutes.CreateMock(typeof(T));
        }

        public object Service(Type serviceType)
        {
            return _substitutes.CreateMock(serviceType);
        }

        public T PartialMock&lt;T&gt;(params object[] args) where T : class
        {
            return (T)_substitutes.CreateMock(typeof(T));
        }

        #endregion
    }
}
```
I&#8217;m not sure about PartialMock since I don&#8217;t know if NSubstitute supports that.

And the third one was the most difficult one.

```csharp
using System;
using System.Reflection;

namespace StructureMap.AutoMocking
{
    public class NSubstituteFactory
    {
        private readonly Type mockOpenType;
       
        public NSubstituteFactory()
        {
            Assembly NSubstitute = Assembly.Load("NSubstitute");
            mockOpenType = NSubstitute.GetType("NSubstitute.Substitute");
            if (mockOpenType == null)
                throw new InvalidOperationException("Unable to find Type NSubstitute.Substitute in assembly " + NSubstitute.Location);
        }

        public object CreateMock(Type type)
        {
            MethodInfo[] methods = mockOpenType.GetMethods();
            foreach (MethodInfo method in methods)
            {
                if (method.ContainsGenericParameters && method.GetGenericArguments().Length == 1)
                {
                    Type[] genericArguments = new Type[] { type };
                    MethodInfo generic = method.MakeGenericMethod(type);
                    if (generic == null)
                        throw new InvalidOperationException(
                            "Unable to find method For&lt;&gt;() on MockRepository.");
                    return generic.Invoke(null, new object[1] {null});
                }
            }
            return null;
        }

    }
}```
The automocked assembly holds no reference to NSubstitute but loads it dynamically so the user that implements the assembly must have a reference to NSubstitute. The big advantage of this approach is that you will not get versioning conflicts. The big disadvantage is that if they decide to rename methods it will no longer work.

## Conclusion

It was fairly easy to make a new NSubstituteAutoMocker for StructureMap. Because it was Open source it was easy for me to look at what was already there and learn from it. I could have done the same when it was closed source but I would not have had the example and thus had to work out a lot of it myself.
  
This probably needs some more work. But it does what I need it to do for now.

 [1]: http://www.lostechies.com/blogs/joshuaflanagan/archive/2009/02/03/auto-mocking-explained.aspx
 [2]: http://structuremap.github.com/structuremap/index.html
 [3]: http://www.ayende.com/projects/rhino-mocks.aspx
 [4]: http://code.google.com/p/moq/
 [5]: http://nsubstitute.github.com/