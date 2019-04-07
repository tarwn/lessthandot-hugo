---
title: AngularJS vs Knockout – Validation (3 of 8)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-10-09T12:38:00+00:00
excerpt: "I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. So here's the question, how hard is it going to be to add good, client-side validation to my pages? What about custom validation?"
url: /index.php/webdev/uidevelopment/angularjs-vs-knockout-validation-3/
views:
  - 24847
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - UI Development
tags:
  - angularjs
  - knockout
  - validation

---
I&#8217;m reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. So here&#8217;s the question, how hard is it going to be to add good, client-side validation to my pages? What about custom validation?

<div style="background-color: #eeeeee; padding: 1em;">
  This is the third of eight posts looking at the capabilities of knockout and Angular. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1" title="AngularJS vs Knockout - Introduction">introduction post</a>, I outlined the capabilities that I am evaluating for. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-data-binding" title="AngularJS vs Knockout - Data Binding">second post</a>, I looked at databinding. This post is exploring simple and custom validation in with AngularJS and Knockout.
</div>

All of the examples presented throughout the series are available in the [tarwn/AngularJS-vs-Knockout][1] repository on github.

## Simple Angular Validation

Angular.js provides validation baked in. Using attributes such as <code class="codespan">required</code>, <code class="codespan">min</code>, and <code class="codespan">max</code>, we can define the validation on an input and the controller for the input will reflect the validation state and add respective CSS classes to the element.

Values are evaluated as the new value is entered. The value bound to the ng-model attribute will reflect the value when the input is is valid and _undefine_ when the input is invalid. Unfortunately this means that when an invalid value is entered, the _undefined_ propagates through any computed values or additional bound fields that rely on that bound property.

### AngularJS Simple Validation Example

Full source available at [Angular/SimpleValidation.html][2].

The built-in validation in AngularJS uses the HTML5 validation attributes. In this case, we have a basic backing Controller with a text property <code class="codespan">textValue</code>, a numeric property <code class="codespan">integerValue</code>, and a function that adds 5 to the numeric property <code class="codespan">getCalculatedInteger()</code>, and we&#8217;re binding to those properties with the relevant validation requirements:

<pre><!DOCTYPE html&gt;
<html ng-app="sampleApp"&gt;
<head&gt;
    <!-- ... --&gt;  
    <script type="text/javascript" src="js/lib/angular-1.0.8.min.js"&gt;</script&gt;
    <!-- ... css classes and such ... --&gt;
</head&gt;
<body&gt;
<div ng-controller="SimpleValidationController"&gt;
    <form name="appForm" novalidate&gt;
        Required Text: <input type="text" name="textValueInput" ng-model="textValue" required /&gt;<br /&gt;
        Required Int: <input type="number" ng-model="integerValue" min="0" max="5" ng-pattern="/^-?d+$/" required /&gt;<br /&gt;
        Required Int + 5: {{ getCalculatedInteger() }}<br /&gt;
        <input type="submit" value="Save" /&gt;
    </form&gt;
   <!-- ... --&gt;
</div&gt;
<!-- ... --&gt;  </pre>

In the integer input, I&#8217;ve used the required attribute to indicate the value is required, the min and max attributes to define a valid range for the value, and a pattern to ensure the value is an integer. Behind the scenes, Angular sets additional properties on the form and input elements to define their validity.

In the full sample code above, I have included CSS styles and output the validation properties in a pre-formatted section so I can see what&#8217;s going on behind the scenes:

<pre><pre&gt;
textValue: 
    value="{{ textValue }}"    
    value is null: {{ textValue == null }}
    valid={{ appForm.textValueInput.$valid }}
    error={{ appForm.textValueInput.$error }}

integerValue: 
    value="{{ integerValue }}"
    value is null: {{ integerValue == null }}
    valid={{ appForm.integerValueInput.$valid }}
    error={{ appForm.integerValueInput.$error }}

appForm: 
    valid="{{ appForm.$valid }}"
    error={{ appForm.$error }}
    required={{ appForm.$error.required }}
</pre&gt;</pre>

## More Complex AngularJS Validation

The built-in attributes cover a wide range of needs, but there are also cases where I need a field to validate it&#8217;s value against a prior one on the form. AngularJS does not have a pre-built directive for this, but we can build a custom one and use the [Linking Method][3] in the Directive. One of the arguments supplied is the controller for the element. Using this, we can add methods to to the input pipeline ($parsers) and to the pipeline when values are set directly ($formatters), then set the validity on the controller from those methods.

### AngularJS Custom Validation Example

Full source available at [Angular/ComplexValidation.html][4].

For the purposes of this example, we&#8217;ll have a controller with two integer properties, <code class="codespan">ceilingValue</code> and <code class="codespan">integerValue</code>. Rather than use a hardcoded max value for the input responsible for integerValue, I&#8217;m going to define a custom directive to use the value of the first input as the ceiling. 

<pre><!-- ... ---&gt;
        Ceiling Input: <input type="number" name="ceilingValueInput" ng-model="ceilingValue" required /&gt;<br /&gt;
        Integer Input: <input type="number" name="integerValueInput" ng-model="integerValue" ng-pattern="/^-?d+$/" ceiling-validate="{{ ceilingValue }}" required /&gt;<br /&gt;
<!-- ... --&gt;</pre>

The <code class="codespan">ceilingValidate</code> directive then defines the behavior for the new attribute:

<pre>sampleApp.directive('ceilingValidate', function () {
    return {
        // can only be used as an attribute
        restrict: 'A',
        // must also have an ngModel attribute defined
        require: 'ngModel',
        // explicitly create one-way binding to attribute value so we can watch it
        scope: {
            ceilingValidate: "@"
        },
        // and define the logic for the link
        link: function (scope, instanceElement, instanceAttributes, controller) {
            // per http://www.benlesh.com/2012/12/angular-js-custom-validation-via.html
            // let's add a parser (html input -&gt; model) and a formatter (model -&gt; html input)

            // when the value in the ceiling changes, re-evaluate validity
            scope.$watch('ceilingValidate', function (newValue, oldValue) {
                if (newValue !== undefined) {
                    var valid = (parseInt(controller.$modelValue) <= parseInt(newValue));
                    controller.$setValidity('ceilingValidate', valid);
                }
            }, true);

            // when a new value comes in, evaluate validity
            controller.$parsers.unshift(function (value) {
                var valid = (parseInt(value) <= parseInt(instanceAttributes.ceilingValidate));
                controller.$setValidity('ceilingValidate', valid);
                return valid ? value : undefined;
            });

            // when a value is set from the mode, evaluate validity
            controller.$formatters.unshift(function (value) {
                if (instanceAttributes.ceilingValidate !== undefined) {
                    var valid = (parseInt(value) <= parseInt(instanceAttributes.ceilingValidate));
                    controller.$setValidity('ceilingValidate', valid);
                }
                else {
                        
                }
                return value;
            });

        }
    };
});</pre>

The comments tell the story. I&#8217;ve limited this directive to be used as an attribute and only on an element that has an ng-model defined. The value of the ceiling-validate attribute will be available in the local scope as a read-only attribute. The linking function initializes the behavior for the element, adding validity checks to the $parsers and $formatters pipelines.

When I enter a ceiling and then an integer, the integer is validated against that ceiling. When I modify the ceiling value, the integer value is re-validated against that new ceiling value.

## Simple Knockout Validation

Knockout does not have validation cooked in the way Angular does, but the [Knockout Validation][5] project on github provides a comparable set of features (and it was the first google search result for &#8216;knockout validation&#8217;). The library provides two options for defining validation, either as &#8220;extends&#8221; calls directly in on the observable properties on models/viewmodels or as HTML5 attributes.

### Knockout Simple Validation Example

Full source available at [Knockout/SimpleValidation.html][6].

To compare with the Angular method, I&#8217;ll be configuring the validation library to work like the method above. I&#8217;ll be using HTML5 attributes to define the validation and include the same text and integer values for sample fields.

<pre><!DOCTYPE html&gt;
<html&gt;
<head&gt;
    <!-- ... --&gt;
    <script type="text/javascript" src="js/lib/knockout-2.3.0.min.js"&gt;</script&gt;
    <script type="text/javascript" src="js/lib/knockout.validation.min.js"&gt;</script&gt;
    <!-- ... css classes and such ... --&gt;
</head&gt;
<body&gt;
<div&gt;
    <form novalidate data-bind="css: { invalid: isValid() == false }"&gt;
        Required Text: <input type="text" data-bind="value: textValue" required /&gt;<br /&gt;
        Required Int: <input type="number" data-bind="value: integerValue" min="0" max="5" pattern="^-?d+$" required /&gt;<br /&gt;
        Required Int + 5: <span data-bind="text: calculatedInteger"&gt;</span&gt;<br /&gt;
        <input type="submit" value="Save" /&gt;
        <!-- ... --&gt;
    </form&gt;</pre>

Using this extra library added an additional 19KB of minified JS to the 42KB we already have for knockout, which still comes in under the 80KB for Angular, but does not include a DOM manipulation library yet either (like [zepto][7] or [jQuery][8]). With the exception of updating after the full value is updated vs on on key down, this knockout version works just like the AngularJS example at the top.

Like the AngularJS version, I included a pre section to output validation states:

<pre><pre&gt;
textValue:
    value="<span data-bind="text: textValue"&gt;</span&gt;"    
    valid=<span data-bind="text: textValue.__valid__"&gt;</span&gt;
    error=<span data-bind="validationMessage: textValue"&gt;</span&gt;
    or=<span data-bind="text: textValue.error"&gt;</span&gt;

integerValue: 
    value="<span data-bind="text: integerValue"&gt;</span&gt;"
    valid=<span data-bind="text: integerValue.__valid__"&gt;</span&gt;
    error=<span data-bind="validationMessage: integerValue"&gt;</span&gt;

appForm: 
    valid="<span data-bind="text: isValid()"&gt;</span&gt;"
    error= <i&gt;no collection at form level</i&gt;
</pre&gt;</pre>

Rather than attach the validation state to the form elements, the knockout validation library attaches them as properties on the observable that is being validated, then provides a binding you can use to get a friendly message.

Using the [configurations][9], I have made this work pretty closely to AngularJS above. There are additional configurations that include outputting error message elements, fine tuning the CSS classes, and even provide the name of a template to use for rendering error messages. 

## Custom Knockout Validation

Like the AngularJS example, I want to explore outside of the box I was provided and define my own validation. 

The knockout-validation library includes the ability to define custom validation methods, though I don&#8217;t see an easy way to use the HTML attributes for custom rules. Like the custom validation example above, I am going to define a ceiling validation call that uses another observable. It will not only need to validate when I change the target input but also re-validate the target input when I change the ceiling input.

### Knockout Custom Validation Example

Full source available at [Knockout /ComplexValidation.html][10].

Nothing changes for the HTML:

<pre><!-- ... --&gt;
Ceiling Input: <input type="number" data-bind="value: ceilingValue" required /&gt;<br /&gt;
Integer Input: <input type="number" data-bind="value: integerValue" pattern="^-?d+$" required /&gt;<br /&gt;
<!-- ... --&gt;</pre>

As I&#8217;ll be applying this custom rule via an extend call on the viewmodel:

<pre>var ComplexValidationModel = function () {
    this.ceilingValue = ko.observable(10);
    this.integerValue = ko.observable(0).extend({ ceiling: this.ceilingValue });
};</pre>

Defining this new validation rule is fairly straightforward:

<pre>// add the custom validation rule
ko.validation.rules['ceiling'] = {
    validator: function (val, otherVal) {
        var actualOther = parseInt(ko.isObservable(otherVal) ? otherVal() : otherVal);
        if (isNaN(actualOther))
            return true;
        else
            return parseInt(val) <= actualOther;
    },
    message: 'The field must be less than or equal to {0}'
};
ko.validation.registerExtenders();</pre>

And everything works exactly as you would expect, since it is just an additional rule on the existing library.

### Knockout Completely Custom Validation Example

Full source available at [Knockout /ComplexValidation2.html][11].

On the other extreme, we can also create a validation library from scratch using knockout extensions. I only offer this example because there is an interesting parallel to wiring into the $formatters and $parsers pipelines from AngularJS and because the custom AngularJS example got to dive into Directives, but the Knockout example hasn&#8217;t really dived into extensions yet.

Warning: Don&#8217;t use this in production. It&#8217;s a one off piece of example code that has had only limited testing.

I&#8217;ve moved everything to extends calls, so here is the updated viewmodel:

<pre>var ComplexValidationModel = function () {
    var rawCeilingValue = ko.observable(10),
        rawIntegerValue = ko.observable(0);

    this.ceilingValue = rawCeilingValue.extend({ validate: { required: true, isNumber: true} });
    this.integerValue = rawIntegerValue.extend({ validate: { required: true, isNumber: true, max: rawCeilingValue } });
	this.isValid = ko.computed(function(){
		return this.ceilingValue.isValid() && this.integerValue.isValid();
	}, this);
};</pre>

I only implemented the required, isNumber, and max/ceiling rules, but the pattern and min wouldn&#8217;t be too hard to add.

The core of this logic is the addition of a <code class="codespan">validate</code> extension. The extension wraps around the observable, replacing the read and write pipelines with it&#8217;s own:

<pre>ko.extenders.validate = function (target, options) {

    // ... logic for validation methods + validate call ...

    var _rawValue = ko.observable(target());
    var result = ko.computed({
        read: _rawValue,
        write: function (newValue) {
            _rawValue(newValue);

            // determine validity, updating state of isValid and error properties
            var validationResult = validate(newValue);
            if (validationResult.isValid) {
                result.isValid(validationResult.isValid);
                result.error("");
                // only pass the write through to raw observable if valid
                target(newValue);
            }
            else {
                result.isValid(validationResult.isValid);
                result.error(validationResult.check);
            }
        }
    });
    result.isValid = ko.observable(true);
    result.error = ko.observable("");

    result._watchDependentCheckArgs = ko.computed(function(){
        // ... hacky little method to watch changes on observable arguments ..
    });

    // write initial value so we can get initial validity
    result(target());
    return result;
}</pre>

The full code is available via the github link above.

## Some Differences

So where are the differences here?

**Extra Library for Knockout**

The first, and most obvious, difference is that AngularJS&#8217;s validation is built-in and I had to go with an additional package for Knockout. From reading other comparisons, composing together a matching set of packages around Knockout is supposed to be tough, and I went into this thinking it would be. I&#8217;m pleasantly surprised by how easy it was to match Angular&#8217;s validation capabilities in Knockout with the addition of a single library. 

There is still an additional trade-off, though. Where we still have a single package with AngularJS, we now have two with Knockout. That&#8217;s two packages we have to keep a watch out for security updates on, two separate projects with separate groups of people submitting changes and maintaining them. So there is some extra, non-technical overhead here.

**Validation Style**

Both examples offered the capability to use HTML attributes to drive validation. When I needed custom validation logic, AngularJS&#8217;s was slightly more complex to write, but was an additional attribute I added to the element. With the Knockout validation example, it was an extension that was added in the viewmodel. Though now that I think of it, I probably could have gone the custom binding route as well, but I would not have been tied into the validation library then.

The library used with Knockout also offers defining your rules in the viewmodel, whereas Angular doesn&#8217;t have this option out of the box.

**Configurability**

The Knockout-validation library wins here hands down. It has a much higher level of configurability, allowing you to change out not just classes for the inputs, but automatically produce error elements, bind templates to each error message, use 9or not) HTML5 validation attributes, and so on. AngularJS mostly focuses on delivering one method of validation (attributes and CSS classes).

**Validation State**

AngularJS tracks the validation state at the form level, indicating which validation rules are in error and doing so in a way that we could programmatically write more logic off of that state. If only it also told you which inputs or models were in error. Here is an example of the error state from the form when I have errors on two text boxes:  <code class="codespan">error={"required":[{}],"ceilingValidate":[{}],"number":[{}]}</code>

Knockout-validation does not expose this type of information.

## Final Thoughts

Knockout-validation and Angular both require less work than rolling your own. Angular seems to have a fixed method of operating while Knockout-validation has a higher level of configuration and is easier to add additional validation methods to.

I personally have issues with both methods. I don&#8217;t like that an invalid entry causes changes to propagate (whether bad values or _undefined_) into the rest of the model. I also don&#8217;t like the use of HTML directives for defining requirements, it ties the view more intimately to the model in Angular&#8217;s case (which is common for the MVC pattern) and loses some of the MVVM separation I would expect for knockout. 

My preference in both cases would be to define these at the model or viewmodel level (which knockout-validation supports) so that my viewmodel has validation requirements and a valid or invalid state and the view (HTML) only reflects this state. I would then have the choice of testing directly against raw inputs and outputs, including putting in bad values and checking formatting, or testing against the core model properties and bypassing formatting and such.

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
      <b>Validation</b>
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
 [2]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/SimpleValidation.html "View full example file on github"
 [3]: http://docs.angularjs.org/guide/directive "AngularJS: Directive"
 [4]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/ComplexValidation.html "View full example file on github"
 [5]: https://github.com/Knockout-Contrib/Knockout-Validation
 [6]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/SimpleValidation.html "View full example file on github"
 [7]: http://zeptojs.com/
 [8]: http://jquery.com/
 [9]: https://github.com/Knockout-Contrib/Knockout-Validation/wiki/Configuration "Configurations for knockout validation"
 [10]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout /ComplexValidation.html "View full example file on github"
 [11]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout /ComplexValidation2.html "View full example file on github"