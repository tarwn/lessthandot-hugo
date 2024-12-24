---
title: Nancy and jquery datatables
author: Christiaan Baes (chrissie1)
type: post
date: 2013-02-02T17:03:00+00:00
ID: 1958
excerpt: Another jquery script named datatables which I tried to use with nancy.
url: /index.php/webdev/serverprogramming/nancy-and-jquery-datatables/
views:
  - 10118
categories:
  - AJAX
  - ASP.NET
  - Server Programming
tags:
  - 'c#'
  - datatables
  - nancy

---
## Introduction

Another weekend, another bit of testing to do. Lately I have been doing a lot of webdev so I am looking to improve my skills and test things during the weekend. Last weekend I tested [jtable][1] and even have it in production. But we are always on the lookout for better and it was suggested to me to try [datatables][2]. Which I did.

## Installation

Documentation for datatables is kind of lacking in that it leaves out important information to get all the bits working.

So here is the list of nuget packages I used.

  * jQuery 1.9.0
  * jquery.datatables 1.9.4
  * jQuery.UI.Combined 1.10.0
  * Nancy 0.15.3
  * Nancy.Hosting.Aspnet 0.15.3
  * Nancy.Viewengines.Razor 0.15.3

One thing that is on nuget but does not really work are the jquery-ui themes for the themeroller. But not to worry you can [download them from the site][3] and just add them manually (yuck).

Don&#8217;t forget to copy your Scripts folder to the Content folder. Not sure if I can tell nuget how to do this in future but that would be nice. Anyway.

Now we have all the bits. Here is the code.

## Nancy

I will use my PlantModel from [the previous blogpost][4].

But I will have to change my PLantsmodule to look like this.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Nancy;
using Nancy.ModelBinding;
using NancyJTable.Models;
using System.Linq;

namespace NancyJTable.Modules
{
    public class PlantsModule:NancyModule
    {
        public PlantsModule()
        {
            Get["/"] = parameters =&gt; View["Plants"];
            Get["/plants/{Id}"] = parameters =&gt; View[GetPlantModels().SingleOrDefault(x =&gt; x.Id == parameters.Id)];
            Get["/plants"] = parameters =&gt;
                {
                    int start = Convert.ToInt32(Request.Query.iDisplayStart.ToString());
                    int length = Convert.ToInt32(Request.Query.iDisplayLength.ToString());
                    var totalRecords = GetPlantModels().Count;
                    var secho = Request.Query.sEcho;
                    var sorting = Request.Query.sSortDir_0;
                    if (sorting == "asc")
                    {
                        return Response.AsJson(new { aaData = GetPlantModels().OrderBy(x =&gt; x.Id).Skip(start).Take(length), sEcho = secho, iTotalRecords = totalRecords, iTotalDisplayRecords = totalRecords });
                    }
                    else
                    {
                        return Response.AsJson(new { aaData = GetPlantModels().OrderByDescending(x =&gt; x.Id).Skip(start).Take(length), sEcho = secho.ToString(), iTotalRecords = totalRecords, iTotalDisplayRecords = totalRecords });
                    }
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
                    Species = "Species" + j,
                    DateAdded = DateTime.Now
                });
            }
            return plantModels;
        }
    }
}```
See how use a Get? See how I get the paging parameters (iDisplayStart and iDisplayLength). I only allow the user to resort the Id column so I am slightly cheating here. You can find all [the parameters here.][5] You also need to get sEcho and just pass it back out. 

The output will look something like this in json.

```
{
    "sEcho": 3,
    "iTotalRecords": 57,
    "iTotalDisplayRecords": 57,
    "aaData": [
        {
            "Id": "1",
            "Name": "Name1",
            "Genus": "Genus1",
            "Species": "Species1",
            "DateAdded": "",
        },
        ...
    ]
}```
Which is an objectnotation. Let&#8217;s remember that fact because it is pretty important later on. 😉

## Datatables

And here is the view code.

```html
@using Nancy
&lt;!DOCTYPE html&gt;

&lt;html&gt;
&lt;head&gt;
    &lt;title&gt;Plants&lt;/title&gt;
    &lt;link href="@Url.Content("~/Content/themes/smoothness/jquery-ui.css")" rel="stylesheet" /&gt;
    &lt;link href="@Url.Content("~/Content/themes/smoothness/jquery.ui.theme.css")" rel="stylesheet" /&gt;
    &lt;link href="@Url.Content("~/Content/DataTables-1.9.4/media/css/jquery.dataTables_themeroller.css")" rel="stylesheet" /&gt;
    &lt;script src="@Url.Content("~/Content/Scripts/jquery-1.9.0.min.js")" type="text/javascript"&gt;&lt;/script&gt;
    &lt;script src="@Url.Content("~/Content/Scripts/jquery-ui-1.10.0.min.js")" type="text/javascript"&gt;&lt;/script&gt;
    &lt;script src="@Url.Content("~/Content/Scripts/DataTables-1.9.4/media/js/jquery.dataTables.min.js")" type="text/javascript"&gt;&lt;/script&gt;
    &lt;script src="@Url.Content("~/Content/Scripts/DataTables-1.9.4/media/js/jquery.dataTables.ColumnResizeAndReorder.js")"&gt;&lt;/script&gt;&lt;script type="text/javascript"&gt;
        $(document).ready(function () {
            $('#table_id').dataTable({
                "bProcessing": true,
                "bStateSave": true,
                "bServerSide": true,
                "bJQueryUI": true,
                "bFilter": false,
                "sAjaxSource": "/plants",
                "sDom": 'R&lt;"dataTables_HeaderWrapper"&lt;"H"lfr&gt;&gt;&lt;"dataTables_BodyWrapper"t&gt;&lt;"dataTables_FooterWrapper"&lt;"F"ip&gt;&gt;',
                "aoColumns": [
                    { "mData": "Id", "sTitle": "Id", "sWidth": "20%", "bSortable": true },
                    { "mData": "Name", "sTitle": "Name", "sWidth": "40%", "bSortable": false },
                    { "mData": "Genus", "sTitle": "Genus1", "sWidth": "40%", "bSortable": false }
                ],
                "sPaginationType": "full_numbers",
                "fnServerData": function (sSource, aoData, fnCallback) {
                    $.getJSON(sSource, aoData, function (jsondata) {
                        fnCallback(jsondata);
                    });
                }
            });
        });
    &lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;table id="table_id"&gt;
        &lt;thead&gt;
            &lt;tr&gt;
                &lt;th&gt;Id&lt;/th&gt;
                &lt;th&gt;Name&lt;/th&gt;
                &lt;th&gt;Genus&lt;/th&gt;
            &lt;/tr&gt;
        &lt;/thead&gt;
        &lt;tbody&gt;
            &lt;tr&gt;
                &lt;td colspan="3" class="dataTables_empty"&gt;Loading data from server&lt;/td&gt;
            &lt;/tr&gt;
        &lt;/tbody&gt;
    &lt;/table&gt;
    &lt;div id="dataoutput"&gt;&lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;
```
I also have a plugin to resize the columns because that is not something that is builtin with datatables. You can find [that plugin][6] on the extras page of the datatables website.

It is important that we use mData for our columns for because we use objectnotation in our json. The fnServerData has the function that gets our json data. 

Look at the html. You need a lot more html than most other frameworks. But it works. Well not actually sure you need that much html because this is one of the parts where the docs are a bit lacking. 

It was also lacking on which libraries to import (js and css) but you now have all of them in the code above. 

And this is the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancydatatables1.png?mtime=1359831744"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancydatatables1.png?mtime=1359831744" width="857" height="560" /></a>
</div>

## Conclusion

Is datatables better than jtable? Pff, nah. But I can use a Get instead of a Post to get my data and that is an improvement.

 [1]: http://jtable.org/
 [2]: http://www.datatables.net/index
 [3]: http://jqueryui.com/download
 [4]: /index.php/All/?p=2049
 [5]: http://www.datatables.net/usage/server-side
 [6]: http://www.datatables.net/extras/