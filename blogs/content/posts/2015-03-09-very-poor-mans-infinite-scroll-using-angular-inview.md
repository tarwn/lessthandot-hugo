---
title: (Very) Poor Man’s Infinite Scroll using angular-inview
author: Alex Ullrich
type: post
date: 2015-03-09T16:12:37+00:00
ID: 3295
url: /index.php/all/very-poor-mans-infinite-scroll-using-angular-inview/
views:
  - 8068
rp4wp_auto_linked:
  - 1
categories:
  - All Blogs
  - Javascript
  - UI Development
tags:
  - angularjs

---
I've been working on an angular app that allows users to manipulate a very large dataset in memory, using various options to filter what is shown at any given time. For one particular “view” into the data there were still over 4000 items (each needing 2-way binding), which is well beyond the point that angular's ng-repeat starts to drag. I looked at various solutions online, and <a href="http://www.williambrownstreet.net/blog/2013/07/angularjs-my-solution-to-the-ng-repeat-performance-problem/" title="williambrownstreet.net" target="_blank">this solution</a> stood out to me, largely because we already need to apply one round of filtering before getting to ng-repeat, and I didn't want to add a third array that needed to be kept in sync.

The problem I kept running into with these “infinite scrolling” type solutions is that they are all based on your html filling the browser window (or a container, in the case of the modified directive presented above). That is all well and good, but with the data I have the container is filled immediately, and then the directive is immediately called into action loading the remaining 3800 (or so) items. What I really need is to trigger the rendering of the next group when the user brings the tail end of the list into view.

This brings me to a handy bit of code called <a href="https://github.com/thenikso/angular-inview" title="github" target="_blank">angular-inview</a>. This gives you a way to watch whether an element is currently visible in the browser or not, and react accordingly. It actually offers two directives, in-view-container (for the container the user is scrolling within) and in-view (which provides the means to execute code when an element comes into view).

Lets take a look at how these work, starting with a sample controller. All the controller needs to do here is create an array of objects to bind to, provide a scope variable representing the repeater limit and a function that will increase the limit if passed a truthy value. This is wrapped in a $timeout call just to force a delay so you can see the magic happen. 

```javascript
var app = angular.module('inview-example', ['angular-inview']);

app.controller('exampleController', function($scope, $timeout) {
  $scope.limit = 20;
  
  $scope.items = [];
  
  for(var i = 0; i < 500; i++){
    $scope.items.push({ value: i });
  }
  
  $scope.increaseLimit = function (actuallyIncrease) {
    if (actuallyIncrease) { //this will be passed the value of $inview from directive
      $timeout(function() {
        $scope.limit = $scope.limit + 20;
      }, 500);
    }
  };
});
```
And some HTML:

```html

    

# Homeless Man's Infinite Scroll
    
    

<div in-view-container class="container">
  <div ng-repeat="item in items | limitTo: limit">
    <input type="text" ng-model="item.value" /></textbox>
          
  </div>
        
  
  <span in-view="increaseLimit($inview)" ng-show="limit < items.length">Loading more...</span>
      
</div>
  
```

So on initial load 0-19 will be displayed. The span positioned outside the repeater is the element we will use to trigger expanding the limit (causing more items to be rendered / bound). This will pass the value $inview to our expansion method, and when true the limit will be increased, causing more items to be rendered. Once the limit is greater than or equal to the number of items we have, there is no need to show the trigger element anymore.

Thats about all there was to it, seems to be working well enough so far. There is a working plunker <a href="http://embed.plnkr.co/VzDrPH9rwtiF4xokvopJ/preview" title="plunker example" target="_blank">here</a>.