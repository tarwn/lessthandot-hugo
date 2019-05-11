---
title: AngularJS vs Knockout – Data Binding (2 of 8)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-10-08T12:23:00+00:00
ID: 2163
excerpt: "I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. First things first, how does the data binding work with each of these libraries?"
url: /index.php/webdev/uidevelopment/angularjs-vs-knockout-data-binding-2/
views:
  - 38332
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - UI Development
tags:
  - angularjs
  - databinding
  - knockout

---
I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. First things first, how does the data binding work with each of these libraries?

<div style="background-color: #eeeeee; padding: 1em;">
  This is the second of eight posts looking at the capabilities of knockout and Angular. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1" title="AngularJS vs Knockout - Introduction">introduction post</a>, I outlined the capabilities that I am evaluating for and the variety of scripts used in the series. This post is looking specifically at how to use data binding with both frameworks, as that will be used through the rest of the series.
</div>

All of the examples presented throughout the series are available in the [tarwn/AngularJS-vs-Knockout][1] repository on github.

## Angular Binding

Angular allows you to easily bind to any variables or functions on the scope object, without any extra properties or JavaScript functions. A binding can be in the form of an HTML attribute, like ng-model, or in the form of {{ property }}, which evaluates it's contents and can be used to output to either the screen or as part of an HTML attribute. 

### Angular Binding Example

Full source available at [Angular/SimpleBinding.html][2].

Defining a controller is relatively straightforward. We create a module that will hold our app and then define a controller by passing a string as the name and a function for initialization.

```javascript
var sampleApp = angular.module('sampleApp', []);

sampleApp.controller('SimpleBindingController', function ($scope) {
$scope.aValue = "some text";
	$scope.radioValue = 'red';
	$scope.listOfItems = [
		{ name: "first value", number: 123 },
		{ name: "second value", number: 456 },
		{ name: "third value", number: 789 }
	] ;
});
```
On the HTML side, the ng-app attribute defines the main module for the page and the ng-controller attribute tells Angular which controller to bind to that section of HTML. Using the controller above, here is an example of outputting a value, binding to an input, using radio buttons to select a value, conditional CSS based on a value, and a loop through an array.

```html
<!DOCTYPE html>
<html ng-app="sampleApp">
<head>
    <!-- ... -->
    <script type="text/javascript" src="js/lib/angular-1.0.8.min.js"></script>
    <!-- ... -->
</head>
<body>
<div ng-controller="SimpleBindingController">
	Displaying a value: {{ aValue }} <br />
	Editing a value: <input type="text" ng-model="aValue" /><br />
	Editing with radio buttons: <br />
	<input type="radio" ng-model="radioValue" value="red"> red <br />
	<input type="radio" ng-model="radioValue" value="blue"> blue <br />

	<div ng-class="{ redClass: radioValue == 'red', blueClass: radioValue == 'blue' }">CSS Based on a value (border changes color)</div>
	
	Looping through an array of values: <br/>
	<ul ng-repeat="item in listOfItems">
		<li>#{{ $index }}: {{ item.number }} - {{ item.name }}</li>
	</ul>
</div>
```
As I modify an input wired to a property in $scope, other elements bound to the same property update automatically (as will the property itself). Besides the straightforward "for loop"-like behavior of ng-repeat in this example, they also offer other variants, which are covered in detail in the [documentation][3]. 

## Knockout Binding

Knockout's bindings are different from Angular's in a few ways. With Knockout, you can data bind to regular fields or to special "observable" properties. When binding to regular fields, the value is only read once and changes are not tracked. Observables track their changes, updating bound elements and referencing calculated observables when they are changed. 

Knockout bindings are defined on elements using a single "data-bind" attribute or without elements using a container-less style that depends on comments (not covered here). All of the bindings for an element go in that attribute, and are evaluated from left to right. Where Angular uses the presence of the [ng-app][4] and [ng-controller][5] directives to initialize the application, Knockout relies on an explicit "ko.applyBindings(_viewmodel_)" call.

### Knockout Binding Example

Full source available at [Knockout/SimpleBinding.html][6].

For Knockout we define a ViewModel object to bind to. In this case the properties are defined as ko.observable objects so we can have change tracking. To initialize the page, we instantiate the ViewModel and call ko.applyBindings(_viewmodel_).

```javascript
<script type="text/javascript">
var SimpleBindingModel = function () {
	this.aValue = ko.observable("some text");
	this.radioValue = ko.observable('red');
	this.listOfItems = ko.observableArray([
		{ name: "first value", number: 123 },
		{ name: "second value", number: 456 },
		{ name: "third value", number: 789 }
	]);
};

var viewmodel = new SimpleBindingModel();
ko.applyBindings(viewmodel)
```
As I mentioned above, bindings are defined in data-bind properties on HTML elements. Just like the Angular example, there is a displayed and editable version of a value, radio button selections, conditional CSS, and a loop through an array.

```html
<!DOCTYPE html>
<html>
<head>
	<!-- ... -->
	<script type="text/javascript" src="js/lib/knockout-2.3.0.min.js"></script>
	<!-- ... -->
</head>
<body>
<div>
	Displaying a value: <span data-bind="text: aValue"></span><br />
	Editing a value: <input type="text" data-bind="value: aValue" /><br />
	Editing with radio buttons: <br />
	<input type="radio" data-bind="checked: radioValue" value="red"> red <br />
	<input type="radio" data-bind="checked: radioValue" value="blue"> blue <br />

	<div data-bind="css: { redClass: radioValue() == 'red', blueClass: radioValue() == 'blue' }">CSS Based on a value (border changes color)</div>
	
	Looping through an array of values: <br/>
	<ul data-bind="foreach: listOfItems">
		<li>
			#<span data-bind="text: $index"></span>:
			<span data-bind="text: number"></span> - <span data-bind="text: name"></span>
		</li>
	</ul>
</div>
```
Unlike Angular, knockout bindings are much more specific. In the example above, we bound specifically to the text attribute of the span but to the value attribute of the input.

## Some Differences

There are quite a few differences in the two approaches. 

**Updates – Full Change or Per Key**
  
Knockout's default behavior for inputs is to update the backing value after the full input has changed. Angular updates on individual key presses. This behavior is customizable in both frameworks, Knockout provides a binding named 'valueUpdate' to override the behavior with options of keyup, keypress, and afterkeydown. Angular does not offer a built-in switch, but you can build a custom directive to provide the exact behavior you want (or [copy one from stackoverflow][7]).

**Specific Bindings vs Element Controllers**
  
Angular's ng-model attribute defines a property to use as a model for the input, attaching extra properties for state (validation, clean/dirty, etc) and sharing that value/model/controller with other directives on the element (wording depends where you look in the documentation). Knockout's bindings are much more specific and don't interact or rely on one another directly.

**Binding Verbosity and Erroring**
  
Knockout is much wordier for simple values, requiring an element (or the container-less comments) to output even a single value. Angular's ability to output values from {{}}'s provides a more concise binding. On the other hand, when a knockout binding is incorrect, you get an error message and no extra output on the screen. When the shorter {{}} form of Angular binding fails, it fails silently and leaves the binding text visible to the user. 

## Final Thoughts

The 'everything in one data-bind' attribute method for knockout can be a little annoying when dealing with simple single-value outputs but it's rare that I notice it (typically only after doing the same form in Angular). In the opposite direction (knockout example to Angular), I find that Angular's approach of separate attributes can get annoying when you start to stack on a few directives and 3-4 validation attributes. 

The most frustrating aspect of either framework, though, has to be Angular's silent failures. There is no value, in my mind, to failing silently, it simply makes identifying and fixing errors harder and more frustrating.

<div style="background-color: #DDDDDD; padding: 8px; width: 400px;">
  <h3>
    Knockout vs AngularJS
  </h3>
  
  <ul style="margin: 0px; padding: 4px;">
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1">Introductory Post</a>
    </li>
    <li>
      <b>Data Binding</b>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-validation-3">Validation</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-serialization-4">Serialization</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-templating-5">Templating</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-modules-and-di-6">Modules + Dependency Injection</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-automated-testing-7">Automated Testing</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/AJAX/angularjs-vs-knockout-spa-routing-history-8">SPA Routing/History</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-final-thoughts-9">Final, Final Thoughts</a>
    </li>
  </ul>
</div>

 [1]: https://github.com/tarwn/AngularJS-vs-Knockout/ "View all of the post examples on github"
 [2]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/SimpleBinding.html "View full example file on github"
 [3]: http://docs.angularjs.org/api/ng.directive:ngRepeat "ngRepeat on docs.angularjs.org"
 [4]: http://docs.angularjs.org/api/ng.directive:ngApp
 [5]: http://docs.angularjs.org/api/ng.directive:ngController
 [6]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/SimpleBinding.html "View full example file on github"
 [7]: http://stackoverflow.com/questions/14477904/how-to-create-on-change-directive-for-angularjs