---
title: ServiceStack.Text has a nice extension method called Dump and it has a few friends
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-31T07:49:00+00:00
ID: 1889
excerpt: Using the ServiceStack.Text extension methods Dump(), PrintDump() and SerializeToString().
url: /index.php/desktopdev/mstech/servicestack-text-has-a-nice/
views:
  - 18861
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - csharp
  - dump
  - servicestack.text

---
## Introduction

Yesterday I was [adding a pdf feature to my Nancy service][1]. And I was outputting my objects to the pdf like this.

```csharp
foreach (var prop in model.GetType().GetProperties())
{
  oDoc.Add(new Paragraph(prop.Name + ":" + prop.GetValue(model).ToString()));    
}```
But we all know this is far from finished and will only work in the most simple of cases, once you add complex types to your type the results will be less than optimal.

Of course you are not alone in this world and other people have had to do this to. And Demis Bellot even made a nuget package out of his solution. So here is Servicestack.Text and the [Dump][2] extension method and friends.

## Dump

So go ahead and look for servicestack.text on nuget and add it to your project.

First of all I would like to change my method above to use the Dump() method.

And so my method now looks like this.

```chsarp
protected virtual Action&lt;Stream&gt; GetPdfContents(TModel model)
        {
            return stream =&gt;
                {
                    var oDoc = new Document(PageSize.A4);
                    PdfWriter.GetInstance(oDoc, stream);
                    oDoc.Open();
                    oDoc.Add(new Paragraph(model.Dump()));    
                    oDoc.Close();
                };
        }```
So you just need you object and call the Dump() extension method on it and the output will look like this.

> {
  
> propertyname: value,
  
> propertyname2: value
  
> }

Nicely formatted JSV.

Yep that&#8217;s it.

The output now looks like this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/servicestacktext/servicestacktext1.png?mtime=1356945714"><img alt="" src="/wp-content/uploads/users/chrissie1/servicestacktext/servicestacktext1.png?mtime=1356945714" width="532" height="670" /></a>
</div>

Or I could output a list of objects and that would look like this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/servicestacktext/servicestacktext2.png?mtime=1356945965"><img alt="" src="/wp-content/uploads/users/chrissie1/servicestacktext/servicestacktext2.png?mtime=1356945965" width="594" height="723" /></a>
</div>

And that is very convenient. This is the the same as doing SerializeAndFormat().

## PrintDump

And do you remember this code?

```csharp
var trees = http.Get("http://localhost:65367/trees");
            foreach (var t in trees.DynamicBody.Trees)
            {
                Console.WriteLine(t.Id);
                Console.WriteLine(t.Genus);
            }
            var result = http.Get("http://localhost:65367/trees/1");
            var tree = result.DynamicBody;
            Console.WriteLine(tree.Id);
            Console.WriteLine(tree.Genus);
            ```
Lots of Console.Writelines and not very robust.

```csharp
var trees = http.Get("http://localhost:65367/trees");
            trees.StaticBody&lt;Object&gt;().PrintDump();
            var result = http.Get("http://localhost:65367/trees/1");
            result.StaticBody&lt;Object&gt;().PrintDump();```
PrintDup won&#8217;t work on dynamic objects so I switched to a staticobject, but easyhttp doesn&#8217;t mind.

And I get this as the result.

> {
          
> Trees:
          
> [
                  
> {
                          
> Id: 1,
                          
> Genus: Fagus
                  
> },
                  
> {
                          
> Id: 2,
                          
> Genus: Quercus
                  
> },
                  
> {
                          
> Id: 3,
                          
> Genus: Betula
                  
> },
                  
> {
                          
> Id: 4,
                          
> Genus: Fagus
                  
> },
                  
> {
                          
> Id: 5,
                          
> Genus: Quercus
                  
> },
                  
> {
                          
> Id: 6,
                          
> Genus: Betula
                  
> },
                  
> {
                          
> Id: 7,
                          
> Genus: Fagus
                  
> },
                  
> {
                          
> Id: 8,
                          
> Genus: Quercus
                  
> },
                  
> {
                          
> Id: 9,
                          
> Genus: Betula
                  
> },
                  
> {
                          
> Id: 10,
                          
> Genus: Fagus
                  
> },
                  
> {
                          
> Id: 11,
                          
> Genus: Quercus
                  
> },
                  
> {
                          
> Id: 12,
                          
> Genus: Betula
                  
> },
                  
> {
                          
> Id: 13,
                          
> Genus: Fagus
                  
> },
                  
> {
                          
> Id: 14,
                          
> Genus: Quercus
                  
> },
                  
> {
                          
> Id: 15,
                          
> Genus: Betula
                  
> }
          
> ],
          
> NumberOfTrees: 15
  
> }
  
> {
          
> Id: 1,
          
> Genus: Fagus
  
> }

Genius.

## SerializeToString

And if you on&#8217;t like the formatting then you can use SerializeToString().

```csharp
var trees = http.Get("http://localhost:65367/trees");
Console.WriteLine(trees.StaticBody&lt;Object&gt;().SerializeToString());
var result = http.Get("http://localhost:65367/trees/1");
Console.WriteLine(result.StaticBody&lt;Object&gt;().SerializeToString());
```
Which will have this kind of output.

> {&#8220;Trees&#8221;:[{&#8220;Id&#8221;:1,&#8221;Genus&#8221;:&#8221;Fagus&#8221;},{&#8220;Id&#8221;:2,&#8221;Genus&#8221;:&#8221;Quercus&#8221;},{&#8220;Id&#8221;:3,&#8221;Genus&#8221;:&#8221;B
  
> etula&#8221;},{&#8220;Id&#8221;:4,&#8221;Genus&#8221;:&#8221;Fagus&#8221;},{&#8220;Id&#8221;:5,&#8221;Genus&#8221;:&#8221;Quercus&#8221;},{&#8220;Id&#8221;:6,&#8221;Genus&#8221;:&#8221;Bet
  
> ula&#8221;},{&#8220;Id&#8221;:7,&#8221;Genus&#8221;:&#8221;Fagus&#8221;},{&#8220;Id&#8221;:8,&#8221;Genus&#8221;:&#8221;Quercus&#8221;},{&#8220;Id&#8221;:9,&#8221;Genus&#8221;:&#8221;Betul
  
> a&#8221;},{&#8220;Id&#8221;:10,&#8221;Genus&#8221;:&#8221;Fagus&#8221;},{&#8220;Id&#8221;:11,&#8221;Genus&#8221;:&#8221;Quercus&#8221;},{&#8220;Id&#8221;:12,&#8221;Genus&#8221;:&#8221;Betu
  
> la&#8221;},{&#8220;Id&#8221;:13,&#8221;Genus&#8221;:&#8221;Fagus&#8221;},{&#8220;Id&#8221;:14,&#8221;Genus&#8221;:&#8221;Quercus&#8221;},{&#8220;Id&#8221;:15,&#8221;Genus&#8221;:&#8221;Bet
  
> ula&#8221;}],&#8221;NumberOfTrees&#8221;:15}
  
> {&#8220;Id&#8221;:1,&#8221;Genus&#8221;:&#8221;Fagus&#8221;}

## Conclusion

These are methods that come in very handy when you do a spike or they would even come in handy for debugging, sometimes even in production.

And there are even more extension methods in that little package. You can find them all [on their github page][3]. Just look at ToJson and ToXml.

 [1]: /index.php/WebDev/ServerProgramming/nancy-and-returning-a-pdf
 [2]: http://www.servicestack.net/mythz_blog/?p=202
 [3]: https://github.com/ServiceStack/ServiceStack.Text#readme