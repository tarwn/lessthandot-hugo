---
title: 'Nancy and jtable: paging and sorting'
author: Christiaan Baes (chrissie1)
type: post
date: 2013-01-27T06:41:00+00:00
ID: 1940
excerpt: Using Nancy and jtable to show a grid that uses paging and sorting.
url: /index.php/webdev/serverprogramming/nancy-and-jtable-paging-and/
views:
  - 33239
categories:
  - AJAX
  - ASP.NET
  - Server Programming
tags:
  - 'c#'
  - jtable
  - nancy

---
## Introduction

[Yesterday I wrote about Nancy and jtable][1] and how to get it working and showing some data. Today I want to add serverside paging and sorting to that.

## Paging

This is where the Post is actually sending some data along, namely the paging data. Of course they could have used a Get and some urlparameters to make this work but they didn&#8217;t and I can live with that.

So you will &#8220;need&#8221; a class to put the pagingparameters in so you can Bind to those.

```csharp
public class PagingParameters
    {
        public int jtStartIndex { get; set; }
        public int jtPageSize { get; set; }
    }```
So the Post will give you two fields jtStartIndex which is the number of the first row it will want to show and the jtPageSize which is the size of the page. Most databases will find this sufficient. This is how limit works in mysql and skip/take in linq. 

And a slight update to my module so that I can page through my data.

```csharp
public class PlantsModule:NancyModule
    {
        public PlantsModule()
        {
            Get["/"] = parameters =&gt; View["Plants"];
            Post["/plants"] = parameters =&gt;
                {
                    var paging = this.Bind&lt;PagingParameters&gt;();
                    var plantsModel = new PlantsModel
                        {
                            Result = "OK",
                            Records = new List&lt;PlantModel&gt;(),
                            TotalRecordCount = 25
                        };
                    plantsModel.Records = GetPlantModels().Skip(paging.jtStartIndex).Take(paging.jtPageSize).ToList();
                    return Response.AsJson(plantsModel);
                };
        }

        private IList&lt;PlantModel&gt; GetPlantModels()
        {
            var plantModels = new List&lt;PlantModel&gt;();
            for (var i = 1; i &lt;= 25; i++)
            {
                var j = i.ToString("000");
                plantModels.Add(new PlantModel()
                {
                    Id = i,
                    Name = "name" + j,
                    Genus = "genus" + j,
                    Species = "Species" + j
                });
            }
            return plantModels;
        }
    }```
As you can see I also had to add a property to my PlantsModel. Namely TotalRecordCount so jtable can figure out how many pages there will be. 

So this is now PlantsModel.

```csharp
using System.Collections.Generic;

namespace NancyJTable.Models
{
    public class PlantsModel
    {
        public string Result { get; set; }
        public IList&lt;PlantModel&gt; Records { get; set; }
        public int TotalRecordCount { get; set; }
    }
}```
And of course we also need to tell our view that it should use paging from now on.

Which is nothing special.

```html
@using Nancy
&lt;!DOCTYPE html&gt;

&lt;html&gt;
    &lt;head&gt;
        &lt;title&gt;Plants&lt;/title&gt;
        &lt;link href="@Url.Content("~/Content/Scripts/jtable/themes/lightcolor/gray/jtable.min.css")" rel="stylesheet" type="text/css" /&gt;
        &lt;script src="@Url.Content("~/Content/Scripts/jquery-1.9.0.min.js")" type="text/javascript"&gt;&lt;/script&gt;
        &lt;script src="@Url.Content("~/Content/Scripts/jquery-ui-1.10.0.min.js")" type="text/javascript"&gt;&lt;/script&gt;
        &lt;script src="@Url.Content("~/Content/Scripts/jtable/jquery.jtable.min.js")" type="text/javascript"&gt;&lt;/script&gt;
        &lt;script type="text/javascript"&gt;
            $(document).ready(function () {
                $('#PlantsTableContainer').jtable({
                    title: 'Table of plants',
                    paging: true, 
                    pageSize: 10,
                    actions: {
                        listAction: '/plants',
                    },
                    fields: {
                        Id: {
                            title: 'Id',
                            key: true,
                            list: true,
                            width: '10%',
                        },
                        Name: {
                            title: 'Name',
                            width: '30%'
                        },
                        Genus: {
                            title: 'Genus',
                            width: '30%',
                        },
                        Species: {
                            title: 'Species',
                            width: '30%',
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
As you can see I added a paging attribute and set it to true and a pageSize that I set to 10.

And here is the proof that it works. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nancy/nancyjtable2.png?mtime=1359274900"><img alt="" src="/wp-content/uploads/users/chrissie1/nancy/nancyjtable2.png?mtime=1359274900" width="474" height="404" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nancy/nancyjtable3.png?mtime=1359274910"><img alt="" src="/wp-content/uploads/users/chrissie1/nancy/nancyjtable3.png?mtime=1359274910" width="473" height="256" /></a>
</div>

## Sorting

Of course our users probably also want to sort. And I (their master) will let them do this on name.

So I have to change the javascript in the view a little more.

```javascript
$(document).ready(function () {
                $('#PlantsTableContainer').jtable({
                    title: 'Table of plants',
                    paging: true, 
                    pageSize: 10,
                    sorting: true, 
                    defaultSorting: 'Name ASC', 
                    actions: {
                        listAction: '/plants',
                    },
                    fields: {
                        Id: {
                            title: 'Id',
                            key: true,
                            list: true,
                            width: '10%',
                            sorting: false
                        },
                        Name: {
                            title: 'Name',
                            width: '30%'
                        },
                        Genus: {
                            title: 'Genus',
                            width: '30%',
                            sorting: false
                        },
                        Species: {
                            title: 'Species',
                            width: '30%',
                            sorting: false
                        }
                    }
                });
                $('#PlantsTableContainer').jtable('load');
            });```
So I added sorting is true and set the defaultsorting to name and ascending and I turned off the sorting for all the columns except name.

Now I just have to adapt my module a bit.

Just change this line

```
plantsModel.Records = GetPlantModels().Skip(paging.jtStartIndex).Take(paging.jtPageSize).ToList();
```
to this

```csharp
plantsModel.Records = paging.jtSorting == "Name ASC" ? GetPlantModels().OrderBy(x =&gt; x.Name).Skip(paging.jtStartIndex).Take(paging.jtPageSize).ToList() : GetPlantModels().OrderByDescending(x =&gt; x.Name).Skip(paging.jtStartIndex).Take(paging.jtPageSize).ToList();
```
To get a sorted list.

And add the jtSorting property to our PagingParameters class.

```csharp
public class PagingParameters
    {
        public int jtStartIndex { get; set; }
        public int jtPageSize { get; set; }
        public string jtSorting { get; set; }
    }```
And you&#8217;re done.

Just look at thos cute little arrows in the columnheader of the name column.

Ascending 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nancy/nancyjtable4.png?mtime=1359275725"><img alt="" src="/wp-content/uploads/users/chrissie1/nancy/nancyjtable4.png?mtime=1359275725" width="473" height="405" /></a>
</div>

Descending

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nancy/nancyjtable5.png?mtime=1359275736"><img alt="" src="/wp-content/uploads/users/chrissie1/nancy/nancyjtable5.png?mtime=1359275736" width="474" height="404" /></a>
</div>

## Conclusion

Paging and sorting is something you do a lot with these kinds of grids and with jtable it is made pretty easy for you to do.

 [1]: /index.php/WebDev/ServerProgramming/nancy-and-jtable