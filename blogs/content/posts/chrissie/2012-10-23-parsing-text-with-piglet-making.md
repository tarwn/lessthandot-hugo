---
title: Parsing text with Piglet making the first tests pass
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-23T12:45:00+00:00
ID: 1766
excerpt: Doing some parsing of Latin plant names using Piglet.
url: /index.php/desktopdev/mstech/parsing-text-with-piglet-making/
views:
  - 20319
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - piglet
  - vb.net

---
## Introduction

Yesterday [RandomPunter][1] mentioned [Piglet][2] on Twitter.

According to the site Piglet is:

> Piglet, the little friendly parser and lexer tool
> 
> Piglet is a library for lexing and parsing text, in the spirit of those big parser and lexer genererators such as bison, antlr and flex. While not as feature packed as those, it is also a whole lot leaner and much easier to understand.

Today I had some time and set out to see what this is all about. I never used a parser like this before so the learning curve would be high.

I set out to parse a list of Plant names (the Latin ones) so that I could get the Genus, species, subspecies of them.

## The Tests

First I set out to create some tests because that is the best way to see if the code I would be writing was correct.

```vbnet
Public Class Plant
    Public Property Genus As String
    Public Property Species As String
    Public Property SubSpecies As String
    Public Property IsHybrid As Boolean
End Class

Public Class ParserTests
    &lt;Test&gt;
    Public Sub IfGenusCanBeFoundWhenOnlyGenusAndSpiecesAreThere()
        Dim parser = New ParseLatinPlantName
        Dim result = parser.Parse("Salvia sylvatica")
        Assert.AreEqual("Salvia", result.Genus)
    End Sub

    &lt;Test&gt;
    Public Sub IfSpeciesCanBeFoundWhenOnlyGenusAndSpiecesAreThere()
        Dim parser = New ParseLatinPlantName
        Dim result = parser.Parse("Salvia sylvatica")
        Assert.AreEqual("sylvatica", result.Species)
    End Sub

    &lt;Test&gt;
    Public Sub IfSubSpeciesCanBeFoundWhenSubSpeciesIsProvided()
        Dim parser = New ParseLatinPlantName
        Dim result = parser.Parse("Salvia sylvatica sp. crimsonii")
        Assert.AreEqual("crimsonii", result.SubSpecies)
    End Sub

    &lt;Test&gt;
    Public Sub IfIsHybridIsTrueWhenxIsInNameCanBeFoundWhenSubSpeciesIsProvided()
        Dim parser = New ParseLatinPlantName
        Dim result = parser.Parse("Salvia x jamensis")
        Assert.IsTrue(result.IsHybrid)
    End Sub

End Class```
And for the VB-challenged out there.

```csharp
[TestFixture]
public class ParserTests
{
	[Test]
	public void IfGenusCanBeFoundWhenOnlyGenusAndSpiecesAreThere()
	{
		var parser = new ParseLatinPlantName();
		var result = parser.Parse("Salvia sylvatica");
		Assert.AreEqual("Salvia", result.Genus);
	}

	[Test]
	public void IfSpeciesCanBeFoundWhenOnlyGenusAndSpiecesAreThere()
	{
		var parser = new ParseLatinPlantName();
		var result = parser.Parse("Salvia sylvatica");
		Assert.AreEqual("sylvatica", result.Species);
	}

	[Test]
	public void IfSubSpeciesCanBeFoundWhenSubSpeciesIsProvided()
	{
		var parser = new ParseLatinPlantName();
		var result = parser.Parse("Salvia sylvatica sp. crimsonii");
		Assert.AreEqual("crimsonii", result.SubSpecies);
	}

	[Test]
	public void IfIsHybridIsTrueWhenxIsInNameCanBeFoundWhenSubSpeciesIsProvided()
	{
		var parser = new ParseLatinPlantName();
		var result = parser.Parse("Salvia x jamensis");
		Assert.IsTrue(result.IsHybrid);
	}
}```
In essence, I want to transform the string into an object of type Class. When I have just two strings the first one is the genus and the second the species. If I also have an sp with a name than the name after the sp is the subspecies. When there is an x after the genus than it is a hybrid (if I remember correctly, school was 20+ years ago).

## The code

So let&#8217;s remember that this was the first time for me. And it was harder than I thought it would be. 

But I complained on twitter and Randompunter came to the rescue and made the first two tests pass with this code.

```csharp
public class ParseLatinPlantName
{
	public Plant Parse(string input)
	{
		IFluentParserConfigurator config = ParserFactory.Fluent();
		var alphanumeric = config.Expression();
		alphanumeric.ThatMatches(@"w+").AndReturns(f =&gt; f);

		var rule = config.Rule();
		rule.IsMadeUp.By(alphanumeric).As("Genus")
			.Followed.By(alphanumeric).As("Species")
			.WhenFound(o =&gt; new Plant {Genus = o.Genus, Species = o.Species});
		IParser&lt;object&gt; parser = config.CreateParser();
		return (Plant) parser.Parse(input);
	}
}
```
And for the C#-challenged.

```vbnet
Public Class ParseLatinPlantName

        Public Function Parse(ByVal name As String) As Plant
            Dim config = ParserFactory.Fluent()
            Dim expr = config.Rule()
            Dim name1 = config.Expression()
            name1.ThatMatches("w+").AndReturns(Function(f) f)
            
            expr.IsMadeUp.By(name1).As("Genus") _
                    .Followed.By(name1).As("Species") _
                    .WhenFound(Function(f) New Plant With {.Genus = f.Genus, .Species = f.Species})

            Dim parser = config.CreateParser()
            Dim result = DirectCast(parser.Parse(name), Plant)
            Return result
        End Function
    End Class```
As you can see you still need to have some knowledge of regex to make this work. But it still readable.

This uses the fluent configuration style that Piglet provides. I -n essence we are telling it to look for a string followed by another string of characters then Put the first one in a Property with the name defined by As and the second one in a property defined by Species.

And then I got stuck.

Tomorrow I will show you how this got solved with the help of [Per Dervall][3] the creator of Piglet.

 [1]: https://twitter.com/randompunter
 [2]: https://github.com/Dervall/Piglet
 [3]: https://twitter.com/Perdervall