---
title: 'Nancy and jtable: formatting your columns'
author: Christiaan Baes (chrissie1)
type: post
date: 2013-01-28T16:52:00+00:00
ID: 1945
excerpt: Today we look at how to make a link in a column and format a datetime so that it shows the time part too.
url: /index.php/webdev/serverprogramming/nancy-and-jtable-formatting-your/
views:
  - 21792
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

So last weekend I poste about Nancy an jtable and all was well. This morning I wanted to add a column with a link and a date with a datetime to it.
  
Neither were obvious but it is possible. And pretty easy once you know how.
  
I have put the [code on github][1].

## Column with a link

This is what I want.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancyjtable6.png?mtime=1359398031"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancyjtable6.png?mtime=1359398031" width="473" height="470" /></a>
</div>

And in a sense it is very easy to do.

When we configure our fields for jtable we have a display attribute that takes a function and in that function we can do our formatting.

```javascript
Name: {
                            title: 'Name',
                            width: '20%',
                            display: function (data) {
                                return $('&lt;a href="/plants/' + data.record.Id + '"&gt;' + data.record.Name + '&lt;/a&gt;');
                            }
                        },```
The parameter that I called data has an attribute record which contains our data and our data as an object. And via that object we can get all the fields of our data. 

## A column with date and time

First of all I added a DateAdded property to my PlantModel and I gave it a value. Then I needed to tell jtable how I want to format it.

You can do this in jtable.

```javascript
DateAdded: {
                            title: 'Date added',
                            width: '20%',
                            sorting: false,
                            type: 'date',
                            displayFormat: 'dd.mm.yy',
                        }```
But this does not allow us to format the time. Because behind the scene it uses the jquery ui datepicker format function and that is limited to the date part.

But not to worry [moment.js][2] to the rescue. Just add it via nuget, move the scripts to your content scripts file. Add this line.

```javascript
&lt;script src="@Url.Content("~/Content/Scripts/moment.min.js")" type="text/javascript"&gt;&lt;/script&gt;```
And then we change our field declaration in jtable.

```javascript
DateAdded: {
                            title: 'Date added',
                            width: '20%',
                            sorting: false,
                            display: function (data) {
                                return moment(data.record.DateAdded).format('DD/MM/YYYY HH:mm:ss');
                            }
                        }```
And that gives the above result.

## Conclusion

jtable has some formatting posibilities builtin (date formatting, add textbox, textarea, checkbox, combos) but it&#8217;s not so much. Luckily you can do everything else with display, so that makes it all good.

 [1]: https://github.com/chrissie1/nancyjtable
 [2]: http://momentjs.com/