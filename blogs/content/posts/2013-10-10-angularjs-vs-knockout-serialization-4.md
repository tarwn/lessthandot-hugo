---
title: AngularJS vs Knockout – Serialization (4 of 8)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-10-10T13:05:00+00:00
ID: 2166
excerpt: "I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. A key operation will be API GETs and POSTs, so how easy or hard will it be to serialize and send data models? Is my server going to have to wade t&hellip;"
url: /index.php/webdev/uidevelopment/angularjs-vs-knockout-serialization-4/
views:
  - 36180
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - UI Development
tags:
  - angularjs
  - json
  - knockout
  - serialization

---
I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. A key operation will be API GETs and POSTs, so how easy or hard will it be to serialize and send data models? Is my server going to have to wade through extra framework properties?

<div style="background-color: #eeeeee; padding: 1em;">
  This is the fourth of eight posts looking at the capabilities of knockout and Angular. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1" title="AngularJS vs Knockout - Introduction">introduction post</a>, I outlined the capabilities that I am evaluating for. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-validation-3" title="AngularJS vs Knockout - Validation">third post</a>, I looked at validation. This post is exploring serialization of data models and any ability to prevent frontend values from being sent to the server.
</div>

All of the examples presented throughout the series are available in the [tarwn/AngularJS-vs-Knockout][1] repository on github.

## Angular Serialization

Angular provides a [toJson][2] serialization method built in. toJson ignores any values that start with a $, which prevents it from serializing it's internal properties into the JSON.

### Angular Serialization Example

Full source available at [Angular/Serialization.html][3].

Serialization is fairly straightforward. In this example I decided I wanted to see the JSON as I changed values in the form, so I created a [$watch][4] to update a $json property on the scope as I changed values. The form includes a number value, a text value, and my example calculated value (a simple integer + 5 in this case).

<div style="background-color: #eeeeee; padding: 1em;">
  <strong><span class="MT_red">Warning:</span> </strong>I am only outputting the json to a property for illustration purposes so we can see what the output would be as we make changes. Updating a json property via a watch is not something you would do in a real application.
</div>

```javascript
var sampleApp = angular.module('sampleApp', []);

sampleApp.controller('SerializationController', function ($scope) {
    $scope.model = {
        textValue: "",
        integerValue: 0
    };

    // this calculated value won't show up in json
    $scope.model.getCalculatedInteger = function () {
        return $scope.model.integerValue + 5;
    };

    // this one will show up
    $scope.model.calculatedInteger = 0;
    $scope.$watch('model.getCalculatedInteger()', function(newVal, oldVal){
        $scope.model.calculatedInteger = $scope.model.getCalculatedInteger();
    });

    // json output when changes occur inside the model object
    $scope.$watch("model", function () {
        $scope.$json = angular.toJson($scope.model);
    }, true);
});
```
The controller and form is based on ones I used in prior examples, but I found I had to make modifications to make the serialization happy. 

The first issue was that it will not serialize $scope, so I had to move the properties I want to serialize to an object inside scope. This seems reasonable, as the top level is unlikely to be an object I have read or want to write over the wire. It's likely to have one or more data models attached to it and a variety of front-end specific values, so this limitation shouldn't have any real world impact.

The second issue I ran into is the calculation I was using was a function rather than a property, so it isn't serialized. I worked around this by adding a watch for the function result and a field to store the result of the function, using the watch to update the field. This works fine for my overly simplistic "plus 5" function, but I don't know how well it would scale to watching several functions with multiple variables and calls out to complex sub-calculations, conditional operations, etc.

## Knockout Serialization

Knockout includes a built-in [toJson][5] function, just like Angular. Since Knockout tracks data using observables, it first converts all observables into raw values before serializing, so computed observables (roughly parallel to functions in AngularJS) and observables for raw values both get serialized without any difference. 

### Simple Knockout Serialization

Full source available at [Knockout/Serialization.html][6].

Like the AngularJS example, we're serializing a child model with text, an integer, and a calculated value of the integer + 5.

<div style="background-color: #eeeeee; padding: 1em;">
  <strong><span class="MT_red">Warning:</span> </strong>I have only included a json computed property for illustration purposes so we can see what the output would be as we make changes. In the real world you would only generate the JSON when you needed it, not in an observable like this.
</div>

```javascript
var SerializationModel = function () {
    this.model = {
        textValue: ko.observable(""),
        integerValue: ko.observable(0)
    };

    this.model.calculatedInteger = ko.computed(function () {
        return parseInt(this.model.integerValue()) + 5;
    }, this);

    this.json = ko.computed(function () {
        return ko.toJSON(this.model);
    }, this);
};
```
The Knockout example was shorter due to the built-in [computed observable][7]. There weren't really any gotchas to this example.

### Knockout Serialization with Filtering

Full source available at [Knockout/SerializationWithFiltering.html][8].

Unlike Angular, Knockout's toJson method accepts an argument that allows you to filter or transform values as they are serialized. The function accepts a key value pair. Returning the value causes it to be included in the output, while returning undefined causes it to be left out of the serialization.

```javascript
var SerializationModel = function () {
    this.textValue = ko.observable("");
    this.integerValue = ko.observable(0);
    this.soonToBeFake = ko.observable("I will never get transmitted");

    this.calculatedInteger = ko.computed(function () {
        return parseInt(this.integerValue()) + 5;
    }, this);

    // calculate json, but filter out json property while doing so
    this.json = ko.computed(function () {
        return ko.toJSON(this, function (key, value) {
            if (key == "json")
                return undefined;
            else if (key == "soonToBeFake")
                return "xxxxxx masked xxxxxx";
            else
                return value;
        }, " ");
    }, this);
};
```
In this case I am serializing the top level viewmodel instead of sub-object. I am filtering out the "json" property and masking the content of the "soonToBeFake" property.

## Some Difference

There's not much to this one, but we did see a couple differences.

**Computeds/Functions**

Knockout computed values are automatically included in the serialization, while AngularJS requires extra steps of setting up a $watch and populating a field. 

Unfortunately, AngularJS's watches are updated far more frequently then knockout's computeds and run the risk of causing infinite loops (or actually a maximum iteration error). At least [one post][9] I've read also suggest strongly not to use watches in controllers (lots of good stuff on Ben's blog). 

Note: Knockout has a section on [Circular Dependencies][10] that explains how computed observables avoid the infinite loop issue.

**!Computed/Functions <span class="MT_orange">(Updated)</span>**

Phillip brought up a good point below. I'm reviewing these with some capabilities in mind, but also some ideas about projects I'm planning to use them in. I want my computed values to get sent over the wire, but he's absolutely right that many projects do not.

In this case, Angular ignoring functions is a plus. There's no extra work required to communicate only your model properties over the wire. With knockout, you would need to separate the model properties from the computed ones as two objects (a backing model + one with computeds wrapped around it), add a sub-object to hold your computeds and filter it out of serialization, use a naming convention that you can easily filter out, or assign them all as additional functions on the observable properties (thus accessible for binding, but ignored when evaluating the value of the observable). And someone will probably add some more options that I didn't think of below.

**Filtering and Masking**

AngularJS automatically filters out anything starting with $, which is nice and requires no extra work. Knockout, on the other hand, doesn't automatically filter anything out but does provide the ability to define your own filter and masking routine. 

## Final Thoughts <span class="MT_orange">(Updated)</span>

Overall these were both fairly straight-forward. I feel the Knockout method was easier (and safer) to use _for the projects I had in mind_, since it has a mechanism to treat computed values the same as regular observables. I appreciate the ease of use of the Angular toJson function, but feel a little dirty using their internal variable naming pattern just to ignore values in serialization, especially when knockout (for a couple more lines of code) could duplicate this ([sample on github][11] for any special character I wanted to use.

On the other hand, if you only want your raw model properties communicated to the server, then Angular's going to be cleaner because you now have to add the extra logic to Knockout to suppress or separate the computed values from your model.

<div style="background-color: #DDDDDD; padding: 8px; width: 400px;">
  <h3>
    Knockout vs AngularJS
  </h3>
  
  <ul style="margin: 0px; padding: 4px;">
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1">Introductory Post</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-data-binding-2">Data Binding</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-validation-3">Validation</a>
    </li>
    <li>
      <b>Serialization</b>
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
 [2]: http://docs.angularjs.org/api/angular.toJson "angular.toJson in Angular Documentation"
 [3]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/Serialization.html "View full example file on github"
 [4]: http://docs.angularjs.org/api/ng.$rootScope.Scope#$watch "AngularJS: Scope - $watch"
 [5]: http://knockoutjs.com/documentation/json-data.html "Knockout: Loading and Saving JSON data"
 [6]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/Serialization.html "View full example file on github"
 [7]: http://knockoutjs.com/documentation/computedObservables.html "Knockout: Computed Observables"
 [8]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/SerializationWithFiltering.html "View full example file on github"
 [9]: http://www.benlesh.com/2013/08/angularjs-watch-digest-and-apply-oh-my.html
 [10]: http://knockoutjs.com/documentation/computedObservables.html#note_why_circular_dependencies_arent_meaningful "knockout - observables - circular dependencies"
 [11]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/SerializationWithSpecialCharacterFiltering.html "Knockout example"