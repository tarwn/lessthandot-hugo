---
title: 'Parsing text with Piglet: making all the tests pass'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-24T10:08:00+00:00
ID: 1768
excerpt: Making yesterdays tests pass and get a good parser for Latin plant names.
url: /index.php/desktopdev/mstech/parsing-text-with-piglet-making-1/
views:
  - 8084
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - piglet
  - vb.net

---
[Yesterday][1] I showed you how we made the first two tests pass. And I also told you that Per Dervall helped to solve the rest. I asked the question on [StackOverflow][2]. And I also pinged him about this on twitter. 

And the poor man made an effort to respond in VB.Net. 

And in short, this is the result.

```vbnet
Public Class ParseLatinPlantName

        Public Function Parse(ByVal name As String) As Plant
            Dim config = ParserFactory.Fluent()
            Dim expr = config.Rule()
            Dim subSpecies = config.Rule()
            Dim hybridIndicator = config.Expression
            hybridIndicator.ThatMatches("x").AndReturns(Function(f) f)

            Dim sp = config.Expression()
            sp.ThatMatches("sp.").AndReturns(Function(f) f)
            Dim name1 = config.Expression()
            name1.ThatMatches("w+").AndReturns(Function(f) f)
            Dim nothing1 = config.Rule()


            expr.IsMadeUp.By(name1).As("Genus") _
                    .Followed.By(name1).As("Species") _
                    .Followed.By(subSpecies).As("Subspecies") _
                    .WhenFound(Function(f) New Plant With {.Genus = f.Genus, .Species = f.Species, .SubSpecies = f.Subspecies}) _
                    .Or.By(name1).As("FirstSpecies").Followed.By(hybridIndicator).Followed.By(name1).As("SecondSpecies") _
                    .WhenFound(Function(f) New Plant With {.IsHybrid = True})

            subSpecies.IsMadeUp.By(sp).Followed.By(name1).As("Subspecies").WhenFound(Function(f) f.Subspecies) _
                .Or.By(nothing1)

            Dim parser = config.CreateParser()
            Dim result = DirectCast(parser.Parse(name), Plant)
            Return result
        End Function
    End Class```
And here is the C# code.

```csharp
public Plant Parse(string input)
        {
            var config = ParserFactory.Fluent();
            var expr = config.Rule();
            var subSpecies = config.Rule();
            var hybridIndicator = config.Expression();
            hybridIndicator.ThatMatches("x").AndReturns(f =&gt; f);

            var sp = config.Expression();
            sp.ThatMatches("sp\.").AndReturns(f =&gt; f);
            var name1 = config.Expression();
            name1.ThatMatches("\w+").AndReturns(f =&gt; f);
            var nothing1 = config.Rule();


            expr.IsMadeUp.By(name1).As("Genus")
                .Followed.By(name1).As("Species")
                .Followed.By(subSpecies).As("Subspecies")
                .WhenFound(f =&gt; new Plant {Genus = f.Genus, Species = f.Species, SubSpecies = f.Subspecies})
                .Or.By(name1).As("FirstSpecies").Followed.By(hybridIndicator).Followed.By(name1).As("SecondSpecies")
                .WhenFound(f =&gt; new Plant {IsHybrid = true});

            subSpecies.IsMadeUp.By(sp).Followed.By(name1).As("Subspecies").WhenFound(f =&gt; f.Subspecies)
                .Or.By(nothing1);

            var parser = config.CreateParser();
            var result = (Plant) parser.Parse(input);
            return result;
        }
    }```
I won&#8217;t repeat the explanation here, I will leave that on SO.

But I did a few performance tests. Nothing very official. Just good to know.

```csharp
[Test]
        public void SpeedTest()
        {
            var parser = new ParseLatinPlantName();
            Plant result;
            for(int i =0; i &lt; 10000; i++)
            {
                result = parser.Parse("Salvia x jamensis");
            }
        }```
Ncrunch tells me the for loop takes 20.717 seconds. 

And then I did this.

```csharp
var parser = new ParseLatinPlantName();
            Plant result;
            for(int i =0; i &lt; 10000; i++)
            {
                result = parser.Parse("Salvia x jamensis");
            }
            for (int i = 0; i &lt; 10000; i++)
            {
                result = parser.Parse("Salvia jamensis");
            }
            for (int i = 0; i &lt; 10000; i++)
            {
                result = parser.Parse("Salvia jamensis sp. something");
            }```
The first for takes around 20 seconds the second around 25 and the third around 13 seconds. 

I let you draw your own conlcusions on why that is. 

I think Piglet is very cool and can be useful The fluent code is very readable. The biggest problem so far is a lack of documentation. But it can only get better.

 [1]: /index.php/DesktopDev/MSTech/parsing-text-with-piglet-making
 [2]: http://stackoverflow.com/questions/13027171/parsing-latin-plant-names-with-piglet-fluent-configuration