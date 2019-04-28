---
title: Making the simplest of domainmodels can be harder than you think
author: Christiaan Baes (chrissie1)
type: post
date: 2009-02-02T08:42:02+00:00
ID: 310
excerpt: |
  Lets start with the requirements.
  
  
  
    User enters data of Person
    User is only allowed to enter LastName and FirstName.
    User is only allowed 30 characters per field.
    Fields are not allowed to be empty
  
  
  
  Seems simple enough.
  
  Lets sta&hellip;
url: /index.php/architect/introductionarchitecturedesign/making-the-simplest-of-domainmodels-can/
views:
  - 2843
categories:
  - Introduction to Architecture and Design
tags:
  - 'c#'
  - ddd

---
Lets start with the requirements.

>   * User enters data of Person
>   * User is only allowed to enter LastName and FirstName.
>   * User is only allowed 30 characters per field.
>   * Fields are not allowed to be empty

Seems simple enough.

Lets start by creating some unittests to verify the requirements.

```csharp
[Test]
        public void If_person_LastName_property_sets_and_returns_correct_value()
        {
            Person person = new Person();
            person.LastName = "Test";
            Assert.AreEqual("Test", person.LastName);
        }

[Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The LastName can not be more than 30 characters long")]
        public void If_person_LastName_property_cannot_be_more_than_30_characters()
        {
            Person person = new Person();
            person.LastName = "TestTestTestTestTestTestTestTest";
        }

        [Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The LastName can not be empty")]
        public void If_person_LastName_property_cannot_be_empty()
        {
            Person person = new Person();
            person.LastName = "";
        }

        [Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The LastName can not be empty")]
        public void If_person_LastName_property_cannot_be_null()
        {
            Person person = new Person();
            person.LastName = null;
        }

        [Test]
        public void If_person_FirstName_property_sets_and_returns_correct_value()
        {
            Person person = new Person();
            person.FirstName = "Test";
            Assert.AreEqual("Test", person.FirstName);
        }

        [Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The FirstName can not be more than 30 characters long")]
        public void If_person_FirstName_property_cannot_be_more_than_30_characters()
        {
            Person person = new Person();
            person.FirstName = "TestTestTestTestTestTestTestTest";
        }

        [Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The FirstName can not be empty")]
        public void If_person_FirstName_property_cannot_be_empty()
        {
            Person person = new Person();
            person.FirstName = "";
        }

[Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The FirstName can not be empty")]
        public void If_person_FirstName_property_cannot_be_null()
        {
            Person person = new Person();
            person.FirstName = null;
        }
```
And hey presto we have a bunch of test that won&#8217;t go anywhere ;-). So lets make them red shall we meaning we want the thing to at least compile.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace RaisingEvents.DomainModel
{
    public class Person
    {
        private string lastName;
        private string firstName;

        public Person() 
        {
        }

        public string LastName
        {
            get { }
            set { }
        }

        public string FirstName
        {
            get { }
            set { }
        }
    }
}
```
Nice red all the way.

Now lets skip a few (more than a few actually but I&#8217;m not writing a book about TDD) steps and make them all green.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace RaisingEvents.DomainModel
{
    public class Person
    {
        private string lastName;
        private string firstName;

        public Person() 
        {
        }

        public int Id
        {
            get { return id; }
            set { id = value; }
        }

        public string LastName
        {
            get { return lastName; }
            set 
            {
                if (String.IsNullOrEmpty(value)) { throw new Exception("The LastName can not be empty"); }
                if (value.Length &gt; 30) { throw new Exception("The LastName can not be more than 30 characters long"); }
                lastName = value; 
            }
        }

        public string FirstName
        {
            get { return firstName; }
            set 
            {
                if (String.IsNullOrEmpty(value)) { throw new Exception("The FirstName can not be empty"); }
                if (value.Length &gt; 30) { throw new Exception("The FirstName can not be more than 30 characters long"); }
                firstName = value; 
            
            }
        }
    }
}
```
Every test goes green. SO we are all happy and exited because we followed the requirements and our domain object is correct&#8230; or so we think.

And we thought wrong, we didn&#8217;t read the requirements correctly. Or&#8230; the above unittests don&#8217;t reflet the requirements correctly. Why? I hear you think. Because we allowed invalid state to kreep into our little model. LastName nor FirstName should ever be empty, I repeat ever.

Lets add this test

```csharp
[Test]
        public void If_person_FirstName_property_cannot_be_null_when_LastName_is_filled()
        {
            Person person = new Person();
            person.LastName = "test";
            Assert.IsNotNull(person.FirstName);
            Assert.Less( person.FirstName.Length,30);
            Assert.AreNotEqual("", person.FirstName);
        }

        [Test]
        public void If_person_LastName_property_cannot_be_null_when_firstname_is_filled()
        {
            Person person = new Person();
            person.FirstName = "test";
            Assert.IsNotNull(person.LastName);
            Assert.Less(person.LastName.Length,30);
            Assert.AreNotEqual("", person.LastName);
        }```
That test fails because I am able to set a LastName and not have any firstname. Ofcourse I could wait for the domainobject to be persisted and then find out it was wrong but then that would defeat the purpose of making a Domainmodel. The model should be able to retain a valid state at all times.

So lets change our DomainModel to reflect the requirements a bit better.

And for me that would be this.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace RaisingEvents.DomainModel
{
    public class Person
    {
        private string lastName;
        private string firstName;

        public Person(string LastName, string FirstName)
        {
            this.LastName = LastName;
            this.FirstName = FirstName;
        }

        public string LastName
        {
            get { return lastName; }
            set 
            {
                if (String.IsNullOrEmpty(value)) { throw new Exception("The LastName can not be empty"); }
                if (value.Length &gt; 30) { throw new Exception("The LastName can not be more than 30 characters long"); }
                lastName = value; 
            }
        }

        public string FirstName
        {
            get { return firstName; }
            set 
            {
                if (String.IsNullOrEmpty(value)) { throw new Exception("The FirstName can not be empty"); }
                if (value.Length &gt; 30) { throw new Exception("The FirstName can not be more than 30 characters long"); }
                firstName = value; 
            
            }
        }
    }
}
```
No more default constructor.

So that means none of my tests will run anymore.

I have to change them to this.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using NUnit.Framework;

namespace RaisingEvents.DomainModel.Tests
{
    [TestFixture]
    public class TestPerson
    {
        [Test]
        public void If_person_LastName_property_sets_and_returns_correct_value()
        {
            Person person = new Person("LastName","FirstName");
            person.LastName = "Test";
            Assert.AreEqual("Test", person.LastName);
        }

        [Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The LastName can not be more than 30 characters long")]
        public void If_person_LastName_property_cannot_be_more_than_30_characters()
        {
            Person person = new Person("LastName", "FirstName");
            person.LastName = "TestTestTestTestTestTestTestTest";
        }

        [Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The LastName can not be empty")]
        public void If_person_LastName_property_cannot_be_empty()
        {
            Person person = new Person("LastName", "FirstName");
            person.LastName = "";
        }

        [Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The LastName can not be empty")]
        public void If_person_LastName_property_cannot_be_null()
        {
            Person person = new Person("LastName", "FirstName");
            person.LastName = null;
        }

        [Test]
        public void If_person_FirstName_property_sets_and_returns_correct_value()
        {
            Person person = new Person("LastName", "FirstName");
            person.FirstName = "Test";
            Assert.AreEqual("Test", person.FirstName);
        }

        [Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The FirstName can not be more than 30 characters long")]
        public void If_person_FirstName_property_cannot_be_more_than_30_characters()
        {
            Person person = new Person("LastName", "FirstName");
            person.FirstName = "TestTestTestTestTestTestTestTest";
        }

        [Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The FirstName can not be empty")]
        public void If_person_FirstName_property_cannot_be_empty()
        {
            Person person = new Person("LastName", "FirstName");
            person.FirstName = "";
        }

        [Test]
        [ExpectedException(ExceptionName = "System.Exception", ExpectedMessage = "The FirstName can not be empty")]
        public void If_person_FirstName_property_cannot_be_null()
        {
            Person person = new Person("LastName", "FirstName");
            person.FirstName = null;
        }

        [Test]
        public void If_person_FirstName_property_cannot_be_null_when_LastName_is_filled()
        {
            Person person = new Person("LastName", "FirstName");
            person.LastName = "test";
            Assert.IsNotNull(person.FirstName);
            Assert.Less( person.FirstName.Length,30);
            Assert.AreNotEqual("", person.FirstName);
        }

        [Test]
        public void If_person_LastName_property_cannot_be_null_when_firstname_is_filled()
        {
            Person person = new Person("LastName", "FirstName");
            person.FirstName = "test";
            Assert.IsNotNull(person.LastName);
            Assert.Less(person.LastName.Length,30);
            Assert.AreNotEqual("", person.LastName);
        }
    }
}
```
Now I&#8217;m more satisfied with my test meeting the requirements. 

So the lesson of the day. Always think carefuly and read carefuly what it says on the package.

Is this the best and only way of doing things? No way. I&#8217;m sure we can come up with beter ways. What if we need an empty constructor for serializing? Is this still POCO? No because of the lack of empty constructor. But sometimes we need to compromise a little to get work done or we use less rigid DTO objects in the rest of the application. I wasn&#8217;t looking for a compromise. Just a way to meet the requirements.