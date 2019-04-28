---
title: An update on the Piglet perfomance tests.
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-24T16:36:00+00:00
ID: 1769
excerpt: I did the performance tests wrong but here is a better and faster way.
url: /index.php/desktopdev/mstech/an-update-on-the-piglet/
views:
  - 1717
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'
  - piglet
  - vb.net

---
In my previous post I did [a few performance tests][1] with piglet. And with all performance related things, I was doing it wrong. I was mainly doing it wrong because I don&#8217;t really know how Piglet works. 

But the person who knows Piglet best came to the rescue.

> You shouldn&#8217;t really construct the parser anew for each parse. The parser is reusable, so build the parser from the grammar only once and you can then call Parse repeatedly. Building the parser is a very labour intensive task, especially compressing the parse tables takes quite a while. Running the parser is fast, it should run in near constant time per input character.
> 
> So, construct the parser in the constructor, and when parsing only call the Parse method on it and you&#8217;d see huge performance increases.

So my Parserclass now looks like this.

```csharp
public class ParseLatinPlantName
    {
        private IParser&lt;object&gt; _parser;

        public ParseLatinPlantName()
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

            _parser = config.CreateParser();
            
        }
        public Plant Parse(string input)
        {
            var result = (Plant) _parser.Parse(input);
            return result;
        }
    }```
And here are the tests.

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
            for (int i = 0; i &lt; 10000; i++)
            {
                result = parser.Parse("Salvia jamensis");
            }
            for (int i = 0; i &lt; 10000; i++)
            {
                result = parser.Parse("Salvia jamensis sp. something");
            }
        }```
The initialisation of the parser takes 172ms

for loop 1 and 2 take around 40ms and for loop 3 takes around 80ms. 

Of course this is just a little tests and teaches me more about how to work with the framework and not that much over the performance in a production environment.

 [1]: /index.php/DesktopDev/MSTech/parsing-text-with-piglet-making-1