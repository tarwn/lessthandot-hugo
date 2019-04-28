---
title: Angularjs and ng-grid
author: Christiaan Baes (chrissie1)
type: post
date: 2013-02-03T17:23:00+00:00
ID: 1964
excerpt: Trying ng-grid and angularjs.
url: /index.php/webdev/uidevelopment/javascript/angularjs-and-ng-grid/
views:
  - 20477
categories:
  - AJAX
  - Javascript
tags:
  - angularjs
  - 'c#'
  - nancy
  - ng-grid

---
## Introduction

Today I tried out [Angularjs][1] and [posted about it][2]. After that it was time to add a grid to make my data look better. And I picked [ng-grid][3].

## Installation

These are the packages I am using.

  * angularjs 1.0.4
  * jQuery 1.9.0
  * Moment.js 1.7.2
  * ng-grid 1.6.0

But the ng-grid package does not contain the scripts. So I had to get those manually. I took the ones on github. The ng-grid.1.6.3.js and ng-grid.css.

## Server

See my [previous post][2] on angularjs for this.

## Client

My app.js changes a little.

```js
angular.module('plantsapp', ['ngGrid']).
  config(['$routeProvider', function ($routeProvider) {
      $routeProvider.
          when('/plants', { templateUrl: 'partials/plants.html', controller: PlantsController }).
          when('/plants/:Id', { templateUrl: 'partials/plant.html', controller: PlantController }).
          otherwise({ redirectTo: '/plants' });
  }]);```
I just added the ngGrid string.

My controller.js will change a little too. And then specifically my PlantsController.

```js
function PlantsController($scope, $http) {
    $scope.plants = [];
    $scope.myOptions = { 
        data: 'plants'
    };
    $http.get('http://localhost:59025/plants').success(function (thisdata) {
        $scope.plants = thisdata;
    });
}
```
See how I instantiate my plants outside the get. And then I add the myOptions line.

In the Index.html I will add these two lines.

```html
&lt;link href="css/ng-grid.css" rel="stylesheet" /&gt;
    &lt;script src="/Scripts/jquery-1.9.0.min.js"&gt;&lt;/script&gt;
    &lt;script src="/Scripts/ng-grid.js"&gt;&lt;/script&gt;```
And the plants.html file changes to this.

```html
&lt;div ng-grid="myOptions"&gt;&lt;/div&gt;```
The myOptions is the same as what property I added in my PlantsController.

And tada.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/angularjs/angularjs4.png?mtime=1359918800"><img alt="" src="/wp-content/uploads/users/chrissie1/angularjs/angularjs4.png?mtime=1359918800" width="910" height="498" /></a>
</div>

But you might see that the date is not well formatted. Let&#8217;s fix that.

## Formatting the date

First we need to add moment.js import to our index.html.

Then we need to add a filter to our app.js.

Like this.

```js
angular.module('plantsapp', ['ngGrid']).
  config(['$routeProvider', function ($routeProvider) {
      $routeProvider.
          when('/plants', { templateUrl: 'partials/plants.html', controller: PlantsController }).
          when('/plants/:Id', { templateUrl: 'partials/plant.html', controller: PlantController }).
          otherwise({ redirectTo: '/plants' });
  }]).filter('moment', function () {
      return function (dateString, format) {
          return moment(dateString).format(format);
      };
  });;
```
And now we need to change our controller so that we can add this filter to our column definition for DateAdded.

Like this.

```js
function PlantsController($scope, $http) {
    $scope.plants = [];
    $scope.myOptions = { 
        data: 'plants', 
        columnDefs: [
                        {field:'Id', displayName:'Id'},
                        {field:'Name', displayName:'Name'},
                        { field: 'Genus', displayName: 'Genus' },
                        { field: 'DateAdded', displayName: 'Date added', cellFilter:"moment:'DD/MM/YYYY h:m'" }
        ]
    };
    $http.get('http://localhost:59025/plants').success(function (thisdata) {
        $scope.plants = thisdata;
    });
}```
Of course we could set our fofrmat straight in the filter and and then just use the moment filter.

For this we just need to change the filter to this.

```js
return moment(dateString).format('DD/MM/YYYY h:m');```
and the columndefinition then becomes this.

```js
{ field: 'DateAdded', displayName: 'Date added', cellFilter:"moment" }```
And tada!!!!

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/angularjs/angularjs3.png?mtime=1359918693"><img alt="" src="/wp-content/uploads/users/chrissie1/angularjs/angularjs3.png?mtime=1359918693" width="910" height="498" /></a>
</div>

## Conclusion

Once you get it ng-grid is pretty easy to set up. I just didn&#8217;t get it for the first hour or so. Because I had my $scope.myOptions in my Get function, because that looked so much like what they did in the getting started things. But I was wrong.

 [1]: http://angularjs.org/
 [2]: /index.php/WebDev/UIDevelopment/Javascript/angularjs
 [3]: http://angular-ui.github.com/ng-grid/#/overview