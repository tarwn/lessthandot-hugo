---
title: .Net has a Set implementation since the 3.5 framework. Use it.
author: Christiaan Baes (chrissie1)
type: post
date: 2008-08-20T17:32:43+00:00
ID: 111
url: /index.php/desktopdev/mstech/net-has-a-set-implementation-since-the-3/
views:
  - 2438
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET

---
In Java, they have had a set implementation for ages. Well, .Net has it too. So no more need to import Iesi.set. The <code class="codespan">HashSet&lt;T&gt;</code> or <code class="codespan">HashSet(Of T)</code> is one of the collections you need most. For this to work, your objects need to override the Equals and GetHashCode functions, so that they reflect the business rules.

I have an example here of a List:

```vbnet
Module Module1

    Sub Main()
        Dim personlist As List(Of Person) = New List(Of Person)()
        Dim person1 As Person = New Person("Chris", "Baes")
        Dim person2 As Person = New Person("Chris", "Baes")
        Console.WriteLine(person1.Equals(person2))
        Dim person3 As Person = New Person("Chris1", "Baes")
        Console.WriteLine(person3.Equals(person2))
        personlist.Add(person1)
        Console.WriteLine(personlist.Count)
        personlist.Add(person2)
        Console.WriteLine(personlist.Count)
        personlist.Add(person3)
        Console.WriteLine(personlist.Count)
        Console.ReadLine()
    End Sub

    Public Class Person
        Implements IComparable

        Private _firstName As String
        Private _lastName As String

        Public Sub New(ByVal FirstName As String, ByVal LastName As String)
            _firstName = FirstName
            _lastName = LastName
        End Sub

        Public Property FirstName() As String
            Get
                Return _firstName
            End Get
            Set(ByVal value As String)
                _firstName = value
            End Set
        End Property

        Public Property LastName() As String
            Get
                Return _lastName
            End Get
            Set(ByVal value As String)
                _lastName = value
            End Set
        End Property

        Public Overrides Function Equals(ByVal obj As Object) As Boolean
            Return obj.FirstName.Equals(Me.FirstName)
        End Function

        Public Overrides Function GetHashCode() As Integer
            Return Me.FirstName.GetHashCode()
        End Function

        Public Function CompareTo(ByVal obj As Object) As Integer Implements System.IComparable.CompareTo
            obj.FirstName.compareto(Me.FirstName)
        End Function
    End Class
End Module
```
And the C# implementation:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Testing_list_C
{
    class Program
    {
        static void Main(string[] args)
        {
            List&lt;Person&gt; personlist = new List&lt;Person&gt;();
            Person person1 = new Person("Chris", "Baes");
            Person person2 = new Person("Chris", "Baes");
            Console.WriteLine(person1.Equals(person2));
            Person person3 = new Person("Chris1", "Baes");
            Console.WriteLine(person3.Equals(person2));
            personlist.Add(person1);
            Console.WriteLine(personlist.Count);
            personlist.Add(person2);
            Console.WriteLine(personlist.Count);
            personlist.Add(person3);
            Console.WriteLine(personlist.Count);
            Console.ReadLine();
        }
    }

    class Person:IEquatable&lt;Person&gt;
    {
        String firstName;
        String lastName;

        public Person(String FirstName, String LastName)
        {
            firstName = FirstName;
            lastName = LastName;
        }

        public String FirstName
        {
            get
            {
                return firstName;
            }
            set
            {
                firstName = value;
            }
        }

        public String LastName
        {
            get
            {
                return lastName;
            }
            set
            {
                lastName = value;
            }
        }

        public bool Equals(Person obj)
        {
            return obj.firstName.Equals(this.firstName);
        }

        public bool Equals(Object obj)
        {
            return ((Person)obj).firstName.Equals(this.firstName);
        }
    }
}
```
The Result will be:

> True
  
> False
  
> 1
  
> 2
  
> 3

If you replace the List with HashSet then the result will be:

> True
  
> False
  
> 1
  
> 1
  
> 2

The only problem is that you will not get any sort of exceptions. It will just not add it to the HashSet and go on with its business as usual. So I wouldn&#8217;t call this very user friendly because the user doesn&#8217;t understand, you have to explain, but I guess that is our job and it is easily solved with contains.