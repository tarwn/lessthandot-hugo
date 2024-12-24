---
title: Trying out Angularjs
author: Christiaan Baes (chrissie1)
type: post
date: 2013-02-03T12:50:00+00:00
ID: 1961
excerpt: Trying out angularjs.
url: /index.php/webdev/serverprogramming/aspnet/angularjs/
views:
  - 16219
categories:
  - AJAX
  - ASP.NET
  - Javascript
tags:
  - angularjs
  - 'c#'
  - nancy

---
## Introduction

It&#8217;s superbowl Sunday and between the snacks it is time to learn something new. Today my eye fell on [Angularjs][1]. Angularjs is an MVC framework for javascript. It kinda puts the controllers and model on the clientside and not on the serverside like ASP.Net MVC does. I will use Nancy as our service to provide us with json data. 

## Server

Our server is pretty simple.

Just make an empty asp.net application and add a Models folder.
  
Here is my model.

```csharp
using System;

namespace NancyJTable.Models
{
    public class PlantModel
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Genus { get; set; }
        public string Species { get; set; }
        public DateTime DateAdded { get; set; }
    }
}```
And here is my module, which I put in the Modules folder.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Nancy;
using NancyJTable.Models;

namespace NancyJTable.Modules
{
    public class PlantsModule:NancyModule
    {
        public PlantsModule()
        {
            Get["/plants/{Id}"] = parameters =&gt; Response.AsJson(GetPlantModels().SingleOrDefault(x =&gt; x.Id == parameters.Id));
            Get["/plants"] = parameters =&gt;
                {
                    return Response.AsJson(GetPlantModels());
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
## Client

First I added another empty ASP.Net web application project to our solution.

I added angularjs to my project. It is on nuget so no problems there.

I want to add a list of plants and I want a details page. 

First I need to start with adding my app.j in the js folder. This will take care of the routes.

```
angular.module('plantsapp', []).
  config(['$routeProvider', function ($routeProvider) {
      $routeProvider.
          when('/plants', { templateUrl: 'partials/plants.html', controller: PlantsController }).
          when('/plants/:Id', { templateUrl: 'partials/plant.html', controller: PlantController }).
          otherwise({ redirectTo: '/plants' });
  }]);```
You already see that I have two partial views and 2 controllers.

The controllers are in my controllers.js file.

```
function PlantsController($scope, $http) {
    $http.get('http://localhost:59025/plants').success(function (data) {
        $scope.plants = data;
    });
}

function PlantController($scope, $routeParams, $http) {
    $http.get('http://localhost:59025/plants/' + $routeParams.Id).success(function (data) {
        $scope.plant = data;
    });
}```
So I have a Plantscontroller that uses a get to get my json from my nancy service. And I have a PlantController that uses the routeparameter to tell which plant to get from my service. 

See how I inject http and how it magically gets injected for me.

The next thing is to create an index.html which is the base of our views.

```html
&lt;!DOCTYPE html&gt;

&lt;html lang="en" ng-app="plantsapp"&gt;
&lt;head&gt;
    &lt;title&gt;Plants&lt;/title&gt;
    &lt;script src="/Scripts/angular.js"&gt;&lt;/script&gt;
    &lt;script src="/js/controller.js"&gt;&lt;/script&gt;
    &lt;script src="/js/app.js"&gt;&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;div ng-view&gt;&lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;
```
In the html tag I added an ng-app with the name I provided in my app.js.

I import angular.js, controller.js and app.js.

And I added a div with an ng-view attribute.

Now I need the partials which I put in the partials folder.

```html
&lt;table id="table_id"&gt;
    &lt;thead&gt;
        &lt;tr&gt;
            &lt;th&gt;Id&lt;/th&gt;
            &lt;th&gt;Name&lt;/th&gt;
            &lt;th&gt;Genus&lt;/th&gt;
        &lt;/tr&gt;
    &lt;/thead&gt;
    &lt;tbody ng-repeat="plant in plants"&gt;
        &lt;tr&gt;
            &lt;td&gt;{{plant.Id}}&lt;/td&gt;
            &lt;td&gt;&lt;a href="#/plants/{{plant.Id}}"&gt;{{plant.Name}}&lt;/a&gt;&lt;/td&gt;
            &lt;td&gt;{{plant.Genus}}&lt;/td&gt;
        &lt;/tr&gt;
    &lt;/tbody&gt;
&lt;/table&gt;
```
This is my plants.html file and it uses the plants object which I have added to the scope in my controller. 

See how I add the link (the # is important).

The next file is the detail view, plant.html.

```html
&lt;table id="table_id"&gt;
    &lt;tr&gt;
        &lt;th&gt;Id&lt;/th&gt;
        &lt;th&gt;{{plant.Id}}&lt;/th&gt;
    &lt;/tr&gt;
    &lt;tr&gt;
        &lt;th&gt;Name&lt;/th&gt;
        &lt;th&gt;{{plant.Name}}&lt;/th&gt;
    &lt;/tr&gt;
    &lt;tr&gt;
        &lt;th&gt;Genus&lt;/th&gt;
        &lt;th&gt;{{plant.Genus}}&lt;/th&gt;
    &lt;/tr&gt;
&lt;/table&gt;
```
Here I use plant because that is what I used to add the the scope in my controller.

And that is it.

Here are the screenshots. Look also at the url in the addressbarr.

The plants.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/angularjs/angularjs1.png?mtime=1359902777"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/angularjs/angularjs1.png?mtime=1359902777" width="733" height="463" /></a>
</div>

And here the detail view.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/angularjs/angularjs2.png?mtime=1359902788"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/angularjs/angularjs2.png?mtime=1359902788" width="733" height="463" /></a>
</div>

## Conclusion

Yeah, uhm. Not sure. Not saying this is bad, but how will this scale ;-). One thing is for sure, the documentation was very good to get my started. Not that I read it before beginning but once I did, it helped.

 [1]: http://angularjs.org/