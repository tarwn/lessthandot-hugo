---
title: Boost your datatransport with XML, VB 11 and denormalisation techniques !
author: Bert Michielsen
type: post
date: 2013-10-09T22:08:00+00:00
ID: 2174
excerpt: |
  Xml, as we all know, has a drawback, being the potential growth in size, characteristical to the format , especially when dealing with complex types and collections of complex types. Let's take a look at a classic example, taken from Wikipedia.
  
  
  &lt&hellip;
url: /index.php/desktopdev/mstech/vbnet/boost-your-datatransport-with-xml/
views:
  - 9831
rp4wp_auto_linked:
  - 1
categories:
  - VB.NET

---
Xml, as we all know, has a drawback, being the potential growth in size, characteristical to the format , especially when dealing with complex types and collections of complex types. Let's take a look at a classic example, taken from Wikipedia. 

```xml
<person>
  <firstName>John</firstName>
  <lastName>Smith</lastName>
  <age>25</age>
  <address>
    <streetAddress>21 2nd Street</streetAddress>
    <city>New York</city>
    <state>NY</state>
    <postalCode>10021</postalCode>
  </address>
  <phoneNumbers>
    <phoneNumber type="home">212 555-1234</phoneNumber>
    <phoneNumber type="fax">646 555-4567</phoneNumber>
  </phoneNumbers>
</person>
```
Try to imagine a collection of 1000 persons, each with 4 possible addresses and up to 10 telephonenumbers and having to transport this over a wire, and it all becomes clear instantly. 

But is XML really less performant sizewise ? We could use XML-attributes and easily serialize an object like this : 

```xml
<person firstName="John" lastName="Smith" age="25" />
```
Based on this example it would seem that attribute-only XML outperforms about anything. But does it realy ? The minute complex types and certainly collections are involved, maintaining that advantage becomes difficult, if not impossible, as you can see here: 

```xml
<person firstName="John" lastName="Smith" age="25">
  <address streetAddress="21 2nd Street" city="New York" state="NY" postalCode="10021" />
  <phoneNumbers>
    <phoneNumber type="home" number="212 555-1234"/>
    <phoneNumber type="fax"  number="646 555-4567"/>
  </phoneNumbers>
</person>
```
This is quite an improvement if we still imagine those same 1000 persons. Nevertheless, as JSON demonstrates : complex types and collections can be handled in a more intelligent way when it comes to limiting growth in size where serialization is involved. 

That said, we can still tweak serialization from within our code, of course. By using attributes in our classes we can limit the length of the tagnames or prevent properties we don't want to be serialized from appearing within the resulting XML, and thus adding to the total size. 

```vbnet
Public Class Address
    <XmlIgnore>
    Public Property ID As Integer
    Public Property Street As String
    Public Property City As String
    Public Property Code As String
    Public Property State As String
```
This gives us the means to trim resulting XML in such a way that the final result may feel like being "acceptable". Our classes we could then expand with functionality to serialize any instance at any moment as well as deserialize back into them. Typically we use XmlSerializer objects for this. 

```vbnet
Public Class Address
    <XmlIgnore>
    Public Property ID As Integer
    Public Property Street As String
    Public Property City As String
    Public Property Code As String
    Public Property State As String
    Public Property SequenceID As Integer

    Public ReadOnly Property AsXML As XElement
        Get
            ' serialize instance with the help of an XML serializer, XML writer and memorystream and return the generated XML
        End Get
    End Property

    Public Shared ReadOnly Property FromXML(xml As XElement) As Address
        Get
            Return CType(New XmlSerializer(GetType(Address)).Deserialize(xml.CreateReader), Address)
        End Get
    End Property

End Class
```
There's a drawback though. If we want to serialize our instances into XML purely in memory, this turns out to be quite a mess. You'll get there eventually, but the more you want to customise the XML to be generated the messier it gets. Serializing to file(s) on hard disk is a tad more straightforward, but do we want to make a roundtrip to the hard disk at each serialization at all times ? Obviously no. 

Luckily this can be done in a much easier way, courtesy of the .NET framework builtin Linq To XML. Linq To XML turns both reading and writing XML into a breeze. 

Now, there's a reason I have used VB as PL here, instead of C# or C++. From VB 9 onward it has this cool feature on board, named XML litterals. No, C# does not have this feature on board. As for the why, you have to address the C# compiler development team for that. Anyway, as you can see, this feature is nothing less than awesome. 

```vbnet
Public Class Address
    Public Property ID As Integer
    Public Property Street As String
    Public Property City As String
    Public Property Code As String
    Public Property State As String
    Public Property SequenceID As Integer

    Public ReadOnly Property AsXML As XElement
        Get
            Return <Address Street=<%= Street %>
                       City=<%= City %>
                       Code=<%= Code %>
                       State=<%= State %>
                   />
        End Get
    End Property

    Public Shared ReadOnly Property FromXML(xml As XElement) As Address
        Get
            Return New Address With {
                                        .Street = xml.@Street,
                                        .City = xml.@City,
                                        .Code = xml.@Code,
                                        .State = xml.@State
                                    }
        End Get
    End Property

End Class
```
We don't have to use serializer objects anymore, as you can see. As a matter of fact, even the use of Attributes in our code has become obsolete because we serialize only what we want and only how we want it, without relying on external objects. And what's more, it is compiled, not interpreted as you might think. 

Consuming this functionality is just as easy. 

```vbnet
Dim someAddress As New Address With {
                                                .Street = "21 2nd Street",
                                                .City = "New York",
                                                .Code = "10021",
                                                .State = "NY"
                                            }

        Console.WriteLine(someAddress.AsXML.ToString)

        Dim anAddress As Address = Address.FromXML(<Address Street="21 2nd Street"
                                                       City="New York"
                                                       State="NY"
                                                       Code="10021"
                                                   />)

        Console.WriteLine("Street : " & anAddress.Street)
        Console.WriteLine("City : " & anAddress.City)
        Console.WriteLine("State : " & anAddress.State)
        Console.WriteLine("Code : " & anAddress.Code)
```
Now, at least, we are somewhere. With a relative small overhead we can use extra functionality within objects in our code to generate XML wich leaves all in all a small fingerprint where our data-transport is concerned. Yet, the fingerprint is still there, and when we have to deal with various nested complex types combined with very large collections, there still is the risk that the size of the generated XML may become unacceptable. 

We haven't gone to the bottom yet though, where XML is concerned. There still is one option whe haven't explored. What we can do is **_denormalising_** our XML. So instead of composing elements we place all their attributes in 1 big root tag. Sort of like denormalising a table model of a database. The **_<Person><Address /><Phonenumbers><Phonenumber /><Phonenumber /></Phonenumbers></Person>_** example above would look in a denormalised form like this : 

```xml
<person firstName="John"
        lastName="Smith"
        age="25"
        A.Street="21 2nd Street"
        A.City="New York"
        A.State="NY"
        A.Code="10021"
        P1.Type="home"
        P1.Value="212 555-1234"
        P2.Type="fax"
        P2.Value="646 555-4567" />
```
As you can see, this is XML in it's very most compact form. JSON can hardly do better than this ( in fact, it can't). Notice that I used a dot within the attributes to have some sort of indication that we are dealing with a composition of elements really, which has to be taken into account when deserialising back into objects. In fact, the dot serves as a degenerate dimension, in database speak. The dot is arbitrarily chosen from my part ( as are the composition identifiers A for Address, P1 for Phonenumber1, P2 for Phonenumber2 ). It might as well have been an underscore or whatever. But a dot is fine and it's use within attribute names does seem to pass inspection on validity. 

Now everything is in place for generating very compact XML and an easy deserialisation into complex types . But... ( oh yes, there's a but )... each attribute within an XML tag must be unique. There's always a way to work around this of course, but in the end the easiest solution is to introduce an extra property ( which will not be serialized of course ) within classes that will participate in data-transport. We will name this property **_SequenceID_** (but you can pick another name if you like to). 

On a note: I for my self allways add a property SequenceID to my domain classes. On another level, it allows me for example to use this property for binding in XAML to a SortDescription. In that way I only have to manage the SequenceID of my classes in code behind, when sorting is involved. Binding will do the rest. 

OK, so far the theory. But will this all work ? When I tried this out, I found it surprisingly easy in combination with XML litterals in VB. You have noticed the lack of underscores amongst other things. The code is written in VB 11 ( Visual Studio 2012 ). 

```vbnet
Public Class Person
    Public Property ID As String
    Public Property FirstName As String
    Public Property LastName As String
    Public Property Age As Integer
    Public Address As Address
    Public PhoneNumbers As IEnumerable(Of PhoneNumber)
    Public Property SequenceID As Integer

    Public ReadOnly Property AsXML As XElement
        Get
            Return <Person FirstName=<%= FirstName %>
                       LastName=<%= LastName %>
                       Age=<%= Age.ToString %>
                       A.Street=<%= Address.Street %>
                       A.City=<%= Address.City %>
                       A.Code=<%= Address.Code %>
                       A.State=<%= Address.State %>
                       <%=
                           From nbr In PhoneNumbers
                           Select {
                           New XAttribute("P" & nbr.SequenceID & ".Type", nbr.Type),
                           New XAttribute("P" & nbr.SequenceID & ".Value", nbr.Value)
                           }
                       %>
                   />
        End Get
    End Property
```
As for the FromXML method, here we do pay the price for denormalization. We have to get the XML content back into normalized form in order to deserialize back into an object. We can do this in LINQ by means of a self join or a groupby. Moreover, we will have to do this on an element of the array resulting from a String.Split function. It will take a second self join though to be able to fill the newly created object. But that's not all: we need the sequenceid to be able to serialize. Easy enough with a direct invoke of a lambda. But we cannot do that on the second self join, because it will not get the same result as what a ROW_NUMBER function would do in TSQL. So we have to iterate through the collection again to create that sequenceid. OK, this maybe is 1 step too much. We could go for the serialization of this SequenceID property also. Then again, that would add to the size of the generated XML, and it would not add to the content, since this property only serves to create unique attributenames. Again : denormalization also has drawbacks. 

```vbnet
Public Shared ReadOnly Property FromXML(xml As XElement) As Person
   Get
      Dim phonenumberdata = (From att In xml.Attributes.Where(
                                     Function(attr) attr.Name.LocalName.Split("."c)(0).Contains("P")
                                     ).GroupBy(Function(a) {a.Name.LocalName.Split("."c)(0),
                                                            a.Name.LocalName.Split("."c)(1),
                                                            a.Value
                                                                             })
                             Select att.Key).ToList
      Dim phonenumbers = (From item In phonenumberdata
                          Join item2 In phonenumberdata
                          On item(0) Equals item2(0)
                          Where item(1) <> item2(2)
                          Select {item(1), item(2), item2(1), item2(2)}
                         ).Where(
                                 Function(o) o(0) = "Type" And
                                             o(0) <> o(2)).ToList
      Dim addressprops = xml.Attributes.Where(
                                Function(a) a.Name.LocalName.Split("."c)(0) = "A"
                                ).Select(
                                      Function(addr) addr.Value
                                         ).ToArray
            Dim ix = 0
            Dim p As New Person With {
                                   .FirstName = xml.@FirstName,
                                   .LastName = xml.@LastName,
                                   .Age = CInt(xml.@Age),
                                   .Address = New Address With {
                                                  .Street = addressprops(0),
                                                  .City = addressprops(1),
                                                  .Code = addressprops(2),
                                                  .State = addressprops(3)
                                                               },
                                   .PhoneNumbers = (From item In phonenumbers
                                                    Let i = Function()
                                                               ix += 1
                                                               Return ix
                                                            End Function.Invoke
                                                    Select New PhoneNumber With {
.SequenceID = i,
.Type = item(1), 
.Value = item(3)
}).ToList
                                    }
            Return p
        End Get
    End Property
```
There's one extra which is not related to XML serialization but which I do want to mention here. We have seen the power of XML literals in VB. Well, this feature can very handily be used for overrides of ToString functions. Which we will do here. 

```vbnet
Public Overrides Function ToString() As String
        Return <String>
                   FirstName : <%= FirstName %>
                   LastName  : <%= LastName %>
                   Age       : <%= Age %>
                   Address     
                   Street    : <%= Address.Street %>
                   City      : <%= Address.City %>
                   Code      : <%= Address.Code %>
                   State     : <%= Address.State %>
                   Phonenumbers<%= Environment.NewLine %>
                   <%= From nbr In PhoneNumbers
                       Select "                   " & nbr.Type & " : " & nbr.Value & Environment.NewLine
                   %>
               </String>.Value
    End Function

End Class

```
Now we can consume this class to serialize and deserialize instances into and from denormalized XML, which is as compact as XML can possibly get. 

```vbnet
Dim aPerson As New Person _
                       With {
                                .FirstName = "John",
                                .LastName = "Smith",
                                .Age = 25,
                                .ID = "106",
                                .SequenceID = 1,
                                .Address = New Address With {
                                                                .Street = "21 2nd Street",
                                                                .City = "New York",
                                                                .State = "NY",
                                                                .Code = "10021",
                                                                .SequenceID = 1},
                                .PhoneNumbers = {
                                           New PhoneNumber With {
                                                          .Type = "home", 
                                                          .Value = "212 555-1234", 
                                                          .SequenceID = 1
                                                          },
                                           New PhoneNumber With {
                                                          .Type = "fax", 
                                                          .Value = "646 555-4567", 
                                                          .SequenceID = 2
                                                          }
                                                }.ToList
                            }

            Console.WriteLine(aPerson.AsXML.ToString)

            aPerson = Person.FromXML(<person FirstName="John"
                                         LastName="Smith"
                                         Age="25"
                                         A.Street="21 2nd Street"
                                         A.City="New York"
                                         A.Code="10021"
                                         A.State="NY"
                                         P1.Type="home"
                                         P1.Value="212 555-1234"
                                         P2.Type="fax"
                                         P2.Value="646 555-4567"
                                         P3.Type="work"
                                         P3.Value="646555-4566"/>)

            Console.WriteLine(aPerson.ToString)
```
And you know what's the nicest thing of it all? Excel can handle this kind of XML without any problem at all. Of course, you will have a worksheet with 1 row and a hell of lot of columns.