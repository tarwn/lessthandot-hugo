---
title: MVVM Validation with KnockoutJS – Don’t put it in the View/HTML
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-03-02T18:22:10+00:00
ID: 4396
url: /index.php/webdev/mvvm-validation-with-knockoutjs-dont-put-it-in-the-viewhtml/
views:
  - 5146
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - knockout
  - validation

---
When it comes to input validation for rich websites and Single Page Applications, a lot of the patterns out there rely on markup in the HTML/View. This works OK for smaller applications, but is terrible for larger applications that expect to be maintained and extended over time.

In the past couple years I have used Knockout on sites ranging from toy size ([the SQL Azure post][1]) up to a large modular SPA rewrite of a Silverlight application. This post is based on one of the patterns we created working on the latter, a large platform expected to support continued growth from multiple teams working side-by-side.

<div style="background-color: #eeeeee; margin: .5em; margin-bottom: 1.5em; padding: .5em">
  “Large modular SPA”: It seems like every time I find a post on SPAs or large front-end techniques about a “Big” application, there is almost never a definition for what the author considered “Big”. This rewrite was for a 300 KLOC Silverlight application and has 30+ discrete screens ranging from “simple” in-screen search results that make IE tables weep, to complex SVG dashboards, to a multi-tabbed screen that can scale from 50 inputs to 1000's, depending on the complexity of the user and their use case.
</div>

## Validation Defined in the View

Here are a couple examples of what I mean when I say “validation defined in the view”:

AngularJS Documentation: <a href="https://docs.angularjs.org/guide/forms" title="AngularJS Documentation / Forms / Custom Validation" target="_blank">https://docs.angularjs.org/guide/forms</a>

```HTML
<input type="number" ng-model="size" name="size" min="0" max="10" integer />{{size}}  
```
jQuery Validation: <a href="http://jqueryvalidation.org/documentation/" title="jQuery Validation Plugin" target="_blank">http://jqueryvalidation.org/documentation/</a>

```HTML

  <label for="cname">Name (required, at least 2 characters)</label>
  <input id="cname" name="name" minlength="2" type="text" required />

```

So what's wrong with this approach?

**1. It's difficult to regression test**

Defining the input restrictions in the view reduces our options to using a UI test framework or manual testers, both on the more expensive side of testing when it comes to money or execution time. When we add in the fact that we're talking about definitions defined on 100's or 1000's of inputs, we end up using a lot of expensive bandwidth for some very detailed things, at the expense of whatever else we were going to use it for.

**2. It's difficult to change and it's re-defined every place we display/bind the field**

Have a field that is shown on 3 screens? That means defining the restrictions 3 times and hoping that you either got them all or that someone notices that a long value on one page actually breaks the validation when it's shown on a second one.

**3. The format isn't defined**

Give a group of humans a textbox that says “Amount”, and some of them will include currency symbols and group separators ($ and , for USD) and this is a totally valid thing to do. Or we could force users to change how they type dollars into a computer simply because we want to store it as a number (no, really, please stop doing things like this).

**4. There are fewer rules than you might think**

Sure, each string in your application has some specific length restrictions, but how many variations do you really have for a currency input? or a password input? So take #3 above and multiple it by 100. Then consider how many spots you will have to change if you change from USD to GBP.

Sold yet?

## Validation on the Model

Whether you're using MVVM, MVC, or MV-Whatever in the front-end, you have a Model that represents the data you save to the server and display on the screen. Because it is data, it doesn't really care about how the user can best consume that data, only that it fits a certain structure and type.

Enter an adapter I will call, for this post, the “PresentationModel”:

<div id="attachment_4420" style="width: 410px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/02/Diagram.png"><img src="/wp-content/uploads/2016/02/Diagram.png" alt="PresentationModel - Defines Validation/Formatting" width="400" height="400" class="size-full wp-image-4420" srcset="/wp-content/uploads/2016/02/Diagram.png 400w, /wp-content/uploads/2016/02/Diagram-200x200.png 200w, /wp-content/uploads/2016/02/Diagram-300x300.png 300w" sizes="(max-width: 400px) 100vw, 400px" /></a>
  
  <p class="wp-caption-text">
    PresentationModel – Defines Validation/Formatting
  </p>
</div>

Wrapping around the Model, this adapter defines how a human reads and writes values and defines the contract for how data flows into the Model and how it is surfaced again. Here is an example of what that could look like in knockout.js:

```javascript
// -- Model
function OrderLineModel(rawDTO){
	this.name = ko.observable(rawDTO.name || '');
	this.quantity = ko.observable(rawDTO.quantity);
	this.price = ko.observable(rawDTO.price);
}

// -- Presentation Model
function OrderLinePresModel(orderLineModel){
	this.name = orderLineModel.name.extend({ validate: { type: stringType, min: 1, max: 25, required: true } });
	this.quantity = orderLineModel.quantity.extend({ validate: { type: integerType, min: 1, max: 500, required: true } });
	this.price = orderLineModel.price.extend({ validate: { type: currencyType, min: 0, max: 100, required: true } });
}
```
The example is an Order object with a collection of Order Lines that alow a user to free-type a name, quantity, and price which are then used for various sub-total calculations and presumably saved at some point. In the “PresentationModel”, I've extended the Model's properties with the validation/formatting definitions for each of the values. 

<div id="attachment_4416" style="width: 568px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/02/Screenshot.png"><img src="/wp-content/uploads/2016/02/Screenshot.png" alt="Formatted and Validated Inputs" width="558" height="183" class="size-full wp-image-4416" srcset="/wp-content/uploads/2016/02/Screenshot.png 558w, /wp-content/uploads/2016/02/Screenshot-300x98.png 300w" sizes="(max-width: 558px) 100vw, 558px" /></a>
  
  <p class="wp-caption-text">
    Formatted and Validated Inputs
  </p>
</div>

I now have several levels of tests I can easily write, as well:

  * Parsing and Formatting tests – make sure “currencyType” works consistently, instead of testing 500 different currency inputs in my application
  * PresentationModel regression tests – make sure each field in the “PresentationModel” has the correct type and length requirements

When I later re-use this model to display a summary of the order, I already have all of the types defined (and tested) in the “PresentationModel”, making it impossible to accidentally format one as a currency amount and the other as a plain decimal value.

## A Knockout Implementation

I have a sample implementation available here: <a href="https://github.com/tarwn/Blog_KnockoutMVVMPatterns/blob/master/validation/index.html" title="https://github.com/tarwn/Blog_KnockoutMVVMPatterns/blob/master/validation/index.html" target="_blank">/validation/index.html (github)</a>

There are 4 key parts to this: the Model, the PresentationModel, the Type, and the Validate extender (I also have a basic component for displaying the inputs)

### Validate Extender

The validate extender is a computed observable that we use in the PresentationModel to define the type and additional validation parameters. It is the main workhorse behind the scenes that attaches the read and write behavior to the Model's observable property.

<div id="attachment_4415" style="width: 510px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/02/PresentationModel.png"><img src="/wp-content/uploads/2016/02/PresentationModel.png" alt="Validate Extender: Input/Output Handling" width="500" height="220" class="size-full wp-image-4415" srcset="/wp-content/uploads/2016/02/PresentationModel.png 500w, /wp-content/uploads/2016/02/PresentationModel-300x132.png 300w" sizes="(max-width: 500px) 100vw, 500px" /></a>
  
  <p class="wp-caption-text">
    Validate Extender: Input/Output Handling
  </p>
</div>

When a new value comes in, it uses the Type to try and parse the value, performs any validations supported by the type, runs custom validations that are defined directly on that field, then writes to the underlying Model's property/observable. When an update is made to the Model's observable, a read is triggered back up and runs through the read side of the validate extender, formatting it using the Type's format method.

```javascript
//-- extender definition

ko.extenders.validate = function (target, options) {
	// ...

	var readFunction = function(){
		return type.format(target()); //...
	};

	var writeFunction = function(newValue){
		// allow empty values if this is a non-required field
		if(options.required === false && newValue === ''){
			// ... clear error properties and return
		}

		// will it parse?
		var parseResult = type.tryParse(newValue);
		if(parseResult.isError){
			// ... set error properties and return
		}

		// will it validate for type validation?
		var validationResult = type.tryValidate(parseResult.value, options);
		if(validationResult.isError){
			// ... set error properties and return
		}

		// custom validation?
		if(options.validate != null){
			validationResult = options.validate(validationResult.value);
			if(validationResult.isError){
				// ... set error properties and return
			}
		}

		// must be good, write it through
		// ...
		target(validationResult.value);
		// ... clear error properties
	};

	var computed = ko.computed({
		read: readFunction,
		write: writeFunction
	});
	computed.validation = validationProperties;
	return computed;
}
```
### Type Definitions

Adding types is then fairly simple, they just need to implement the parse, validate, and error methods:

```javascript
var currencyType = {
	emptyValue: null,
	format: function(value){
		if(value == null){
			return '';
		}
		else{
			return value.toLocaleString('en-US', {
				style: 'currency',
				currency: 'USD',
				currencyDisplay: 'symbol',
				useGrouping: true
			});
		}
	},
	tryParse: function(value){
		// strip out commas and $
		var parsedResult = parseFloat(value.replace(/[\$,]/g,''));
		if(isNaN(parsedResult)){
			return failedInput("'" + value + "' is not a valid currency value");
		}
		else{
			return successfulInput(parsedResult);
		}
	},
	tryValidate: function(value, options){
		if(options.min != undefined && value < options.min){
			return failedInput("'" + value + "' is less than the supported minimum of '" + options.min + "'");
		}

		if(options.max != undefined &#038;&#038; value > options.max){
			return failedInput("'" + value + "' is greater than the supported maximum of '" + options.max + "'");
		}

		return successfulInput(value);		
	}
};
```
This is a pretty basic example. Starting here, we could easily come back through and pass the field's name though for richer error messages, use the format method for the values in the tryValidate error messages, and so on. We could also extend the tryParse method to accept and expand on values like “$100K”, converting something that would be natural to the user to a value that is natural to the inner Model (and then doing the reverse in the format method). 

## What we gain

Switching from validation in the view to format/validation as a contract in the code reduces the number of places we have to repeat ourselves, makes it easy to extend a consistently richer experience to the end user, acts like a contact that keeps invalid values out of our model, is clearer and easier to read than having it mixed in among the HTML, and greatly increases our ability to write basic unit tests to serve as a safety net for future us.

 [1]: /index.php/architect/distributed-storage-how-sql-azure-replicas-work/ "Distributed Storage: How SQL Azure Replicas Work"