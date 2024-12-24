---
title: Nancy and jtable
author: Christiaan Baes (chrissie1)
type: post
date: 2013-01-26T15:19:00+00:00
ID: 1939
excerpt: Creating a grid with jtable and Nancy.
url: /index.php/webdev/serverprogramming/nancy-and-jtable/
views:
  - 23270
categories:
  - AJAX
  - ASP.NET
  - Server Programming
tags:
  - 'c#'
  - jtable
  - nancy

---
## Introduction.

From time to time you need to show data in a grid. And I picked [jtable][1] and [Nancy][2] to make that happen. Because reinventing the wheel is not something you want to do every week.

## Installation

Everything you need can be found on Nuget. And this is what I needed.

  * jQuery
  * jQuery.UI.Combined
  * jTable
  * Nancy
  * Nancy.Hosting.Aspnet
  * Nancy.Viewengines.Razor

I then copied all the scripts to the content folder, because that is the convention for Nancy. It is kind of sad that the jtable has no dependency on jquery or jqueryUI but I survived.

## Our first grid

So now we need to get our first grid to fill up with some data.

First we need a model.

```csharp
namespace NancyJTable.Models
{
    public class PlantModel
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Genus { get; set; }
        public string Species { get; set; }
    }
}```
The above is for our data.

And we will need another model.

```csharp
using System.Collections.Generic;

namespace NancyJTable.Models
{
    public class PlantsModel
    {
        public string Result { get; set; }
        public IList&lt;PlantModel&gt; Records { get; set; }
    }
}```
We need this model so we can send this data as json to our jtable grid. jTable uses ajax to get the data and the above will make it easy for it to know what to put where.

Then I guess we need a module.

```csharp
using System.Collections.Generic;
using Nancy;
using Nancy.ModelBinding;
using NancyJTable.Models;

namespace NancyJTable.Modules
{
    public class PlantsModule:NancyModule
    {

        public PlantsModule()
        {
            Get["/"] = parameters =&gt; View["Plants"];
            Post["/plants"] = parameters =&gt;
                {
                    var plantsModel = new PlantsModel
                        {
                            Result = "OK",
                            Records = new List&lt;PlantModel&gt;(),
                        };
                    for (var i = 1; i &lt; 25; i++)
                    {
                        plantsModel.Records.Add(new PlantModel()
                            {
                                Id = i,
                                Name = "name" + i,
                                Genus = "genus" + i,
                                Species = "Species" + i
                            });
                    }
                    return Response.AsJson(plantsModel);
                };
        }
    }
}```
In the above we have a Get to show our page and a Post for jtable to get it&#8217;s data in JSON format. A bit strange to use a POST for that but hey who am I to complain.

And last but not least I need a View.

```html
@using Nancy
&lt;!DOCTYPE html&gt;

&lt;html&gt;
    &lt;head&gt;
        &lt;title&gt;Plants&lt;/title&gt;
        &lt;link href="@Url.Content("~/Content/Scripts/jtable/themes/metro/blue/jtable.min.css")" rel="stylesheet" type="text/css" /&gt;
        &lt;script src="@Url.Content("~/Content/Scripts/jquery-1.9.0.min.js")" type="text/javascript"&gt;&lt;/script&gt;
        &lt;script src="@Url.Content("~/Content/Scripts/jquery-ui-1.10.0.min.js")" type="text/javascript"&gt;&lt;/script&gt;
        &lt;script src="@Url.Content("~/Content/Scripts/jtable/jquery.jtable.min.js")" type="text/javascript"&gt;&lt;/script&gt;
        &lt;script type="text/javascript"&gt;
            $(document).ready(function () {
                $('#PlantsTableContainer').jtable({
                    title: 'Table of plants',
                    actions: {
                        listAction: '/plants',
                    },
                    fields: {
                        Id: {
                            key: true,
                            list: true,
                            width: '10%'
                        },
                        Name: {
                            title: 'Name',
                            width: '30%'
                        },
                        Genus: {
                            title: 'Genus',
                            width: '30%'
                        },
                        Species: {
                            title: 'Species',
                            width: '30%'
                        }
                    }
                });
                $('#PlantsTableContainer').jtable('load');
            });
&lt;/script&gt;
    &lt;/head&gt;
    &lt;body&gt;
        &lt;div&gt;
            &lt;div id="PlantsTableContainer"&gt;&lt;/div&gt;
        &lt;/div&gt;
    &lt;/body&gt;
&lt;/html&gt;```
The listAction is the url where jtable can get it&#8217;s list. Then you define the different columns and then you do a load so that the data gets filled.

And you need a div somewhere with an id that you use in the javascript. In my case PlantsTableContainer.

And here is the result of all that hard work.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancyjtable1.png?mtime=1359220672"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancyjtable1.png?mtime=1359220672" width="469" height="741" /></a>
</div>

## Conclusion

The fact that you need a Post to Get the data is a bit of a weird choice but it becomes clear why they did that when we learn how to do paging and sorting in our next post.

 [1]: http://www.jtable.org
 [2]: http://nancy.org