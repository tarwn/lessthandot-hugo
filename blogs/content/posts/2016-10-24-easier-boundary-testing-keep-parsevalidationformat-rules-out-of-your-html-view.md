---
title: 'Easier Boundary Testing: Keep Parse/Validation/Format rules out of your HTML View'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-10-24T19:49:12+00:00
url: /index.php/webdev/easier-boundary-testing-keep-parsevalidationformat-rules-out-of-your-html-view/
views:
  - 3263
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - knockoutjs
  - mvvm

---
In a typical single-page application, type and validation logic is entered in the HTML view and we rely on our binding framework or a validation library to layer this behavior onto the form. There are trade-offs to this approach, which are mostly negative as you get into larger, longer-lived applications. 

When we embed validation rules and logic into the View, we&#8217;re mostly limited to UI Testing to validate them (the most costly layer of the <a href="https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid" title=""The Forgotten Layer of the Test Automation Pyramid>testing pyramid</a>). We also pay an ongoing tax of having to re-define rules every place we put an input field (how do we format percentages for this field? ah, the same way as the other 100 places). Some frameworks even pass invalid or undefined values through to the underlying code, forcing us to build extra layers of defensive logic throughout the codebase. 

Meanwhile, testing below the UI means we can do this:

<div id="attachment_4703" style="width: 810px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/10/FasterBoundaryTests1.png"><img src="/wp-content/uploads/2016/10/FasterBoundaryTests1.png" alt="Testing Model Inputs" width="800" class="size-full wp-image-4703" srcset="/wp-content/uploads/2016/10/FasterBoundaryTests1.png 943w, /wp-content/uploads/2016/10/FasterBoundaryTests1-300x133.png 300w" sizes="(max-width: 943px) 100vw, 943px" /></a>
  
  <p class="wp-caption-text">
    Boundary Testing at Unit Test Speeds
  </p>
</div>

In [MVVM Validation with KnockoutJS – Don’t put it in the View/HTML][1], I created a small, artificial codebase to show a method of embedding user input logic in code rather than HTML attributes. Some of the value to this approach includes being able to define a domain-specific set of formats/validation for your application, access to the read/write pipelines to add extra behavior or transformations, freedom to assume only good values make it into your business logic, and the ability to write basic unit tests to verify your application behavior for good and bad inputs. 

Today we&#8217;ll explore one of the opportunities we left the door open to: whether validation rules like min/max are actually working correctly.

_Code for this post is available here: [github.com/tarwn/Blog_KnockoutMVVMPatterns/**/validationWithTests][2]_

## Boundary Testing

Boundary testing is testing the extreme edges of what is valid for our inputs, for instance what happens just inside the minimum value, at the minimum value, and just outside. Fuzz testing (which we&#8217;ll look at in a later post) is a technique that tests what happens when random, unexpected inputs occur. There are entire suites of tools built to do both of these things, but with our input logic abstracted away from the HTML, we can perform a level of these tests from unit tests code and get a large amount of the value we would get from a more costly UI testing setup at a fraction of the cost.

Let&#8217;s look at the basic sample form from the prior post:

<div id="attachment_4416" style="width: 568px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/02/Screenshot.png"><img src="/wp-content/uploads/2016/02/Screenshot.png" alt="Formatted and Validated Inputs" width="558" height="183" class="size-full wp-image-4416" srcset="/wp-content/uploads/2016/02/Screenshot.png 558w, /wp-content/uploads/2016/02/Screenshot-300x98.png 300w" sizes="(max-width: 558px) 100vw, 558px" /></a>
  
  <p class="wp-caption-text">
    Formatted and Validated Inputs
  </p>
</div>

The binding for each of these inputs binds to a userInput extension using [knockoutjs][3]. The user input is defined as an extension of the underlying observable value, with a defined type, optional boundaries, and the source observable we are extending that is only set when the input is valid.

Here is the PresentationModel that wraps around our data Model and defines how we present the model to a human:

<pre>function OrderLinePresentationModel(orderLine){
	var self = this;

	self.model = orderLine;

	self.name = orderLine.name.extend({ validate: { type: stringType, min: 1, max: 25, required: true } });
	self.quantity = orderLine.quantity.extend({ validate: { type: integerType, min: 1, max: 500, required: true } });
	self.price = orderLine.price.extend({ validate: { type: currencyType, min: 0, max: 100, required: true } });

	self.total = ko.pureComputed(function(){
		return currencyType.format(orderLine.total());
	});

	self.isValid = ko.pureComputed(function(){
		return !self.name.validation().isError() &&
			!self.quantity.validation().isError() &&
			!self.price.validation().isError();
	});
}</pre>

In English, we are exposing the following properties from the underlying OrderLine Model:

  * Name &#8211; a string type that must have 1-25 characters
  * Quantity &#8211; an integer type that must be between 1 and 500
  * Price &#8211; a Currency type that must be between $0 and $100
  * Total &#8211; total for the line that is formatted as a Currency type
  * IsValid &#8211; A property that reflects whether all of the inputs have valid values

The &#8220;type&#8221; objects are used during the read/write pipelines:

<div id="attachment_4415" style="width: 510px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/02/PresentationModel.png"><img src="/wp-content/uploads/2016/02/PresentationModel.png" alt="Presentation Model - Read/Write Pipelines" width="500" height="220" class="size-full wp-image-4415" srcset="/wp-content/uploads/2016/02/PresentationModel.png 500w, /wp-content/uploads/2016/02/PresentationModel-300x132.png 300w" sizes="(max-width: 500px) 100vw, 500px" /></a>
  
  <p class="wp-caption-text">
    Presentation Model &#8211; Read/Write Pipelines
  </p>
</div>

  * On Read: Run the raw Model value through the Formatter to produce output
  * On Write/Input: Run the input through tryParse and then tryValidate (and then custom validation properties), only writing it to the underlying Model property if it passes both steps

Here is what one of those &#8220;type&#8221; objects looks like as a literal:

<pre>return {
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
			return inputResult.failedInput("'" + value + "' is not a valid currency value");
		}
		else{
			return inputResult.successfulInput(parsedResult);
		}
	},
	tryValidate: function(value, options){
		if(options.min != undefined && value &lt; options.min){
			return inputResult.failedInput("'" + value + "' is less than the supported minimum of '" + options.min + "'");
		}

		if(options.max != undefined && value &gt; options.max){
			return inputResult.failedInput("'" + value + "' is greater than the supported maximum of '" + options.max + "'");
		}

		return inputResult.successfulInput(value);		
	}
};</pre>

Using a knockout extension, we can ensure all writes pass through the parse and validate logic before being written to the true underlying model, surface an error state if either fails, and display a formatted value independent of the raw value in that underlying model.

## Adding Boundary Tests

So we have two options for boundary testing, we can test each available &#8220;type&#8221; with some common boundary settings or we can test each PresentationModel&#8217;s inputs with the defined ones. The intent of the test is to make sure we handle boundaries the way we think we do, so I lean towards the more target &#8220;type&#8221; tests as getting the most bang for the buck.

<div id="attachment_4703" style="width: 810px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/10/FasterBoundaryTests1.png"><img src="/wp-content/uploads/2016/10/FasterBoundaryTests1.png" alt="Testing Model Inputs" width="800" class="size-full wp-image-4703" srcset="/wp-content/uploads/2016/10/FasterBoundaryTests1.png 943w, /wp-content/uploads/2016/10/FasterBoundaryTests1-300x133.png 300w" sizes="(max-width: 943px) 100vw, 943px" /></a>
  
  <p class="wp-caption-text">
    Testing Model Inputs
  </p>
</div>

Here is sample code for validating the boundaries on the Order Line, which has a text, integer, and currency input:
  
**specs/models/orderLinePresentationModel.boundary.spec.js**

<pre>describe('boundary tests', function(){

	var testCases = [
		{ name: 'name input - shorter than min', field: 'name', input: '', isError: true },
		{ name: 'name input - longer than min', field: 'name', input: '1', isError: false },
		{ name: 'name input - shorter than max', field: 'name', input: '123456789012345678901234', isError: false },
		{ name: 'name input - max', field: 'name', input: '1234567890123456789012345', isError: false },
		{ name: 'name input - longer than max', field: 'name', input: '12345678901234567890123456', isError: true },

		{ name: 'quantity input - less than min', field: 'quantity', input: '-1', isError: true },
		{ name: 'quantity input - min', field: 'quantity', input: '1', isError: false },
		{ name: 'quantity input - above min', field: 'quantity', input: '2', isError: false },
		{ name: 'quantity input - below max', field: 'quantity', input: '499', isError: false },
		{ name: 'quantity input - max', field: 'quantity', input: '500', isError: false },
		{ name: 'quantity input - above max', field: 'quantity', input: '501', isError: true },

		{ name: 'price input - less than min', field: 'price', input: '-1', isError: true },
		{ name: 'price input - min', field: 'price', input: '1', isError: false },
		{ name: 'price input - above min', field: 'price', input: '2', isError: false },
		{ name: 'price input - below max', field: 'price', input: '99', isError: false },
		{ name: 'price input - max', field: 'price', input: '100', isError: false },
		{ name: 'price input - above max', field: 'price', input: '101', isError: true },

	];

	testCases.forEach(function(testCase){
		var testName = testCase.name + ': is a ' + (testCase.isError ? 'validation error' : 'successful input');

		it(testName, function(){
			var testLine = new OrderLineModel({});
			var testLineP = new OrderLinePresentationModel(testLine);
			var inputUnderTest = testLineP[testCase.field];

			inputUnderTest(testCase.input);				

			expect(inputUnderTest.validation().isError()).toBe(testCase.isError);
		});

	});

});</pre>

So far not a lot of duplication of effort, but in a larger application we would expect to have 10s or 100s of integer, text, currency, date, etc inputs. So the effort to test at this Model level doesn&#8217;t seem to add much value over testing directly at the type level, unless we&#8217;re passing in custom validation that would cause a specific input to operate differently than others of a similar type.

<div id="attachment_4702" style="width: 810px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/10/FasterBoundaryTests2.png"><img src="/wp-content/uploads/2016/10/FasterBoundaryTests2.png" alt="Testing Input Types" width="800" class="size-full wp-image-4702" srcset="/wp-content/uploads/2016/10/FasterBoundaryTests2.png 940w, /wp-content/uploads/2016/10/FasterBoundaryTests2-300x103.png 300w" sizes="(max-width: 940px) 100vw, 940px" /></a>
  
  <p class="wp-caption-text">
    Testing Input Types
  </p>
</div>

Here&#8217;s how we can instead do boundary testing for the more generalized input type level:
  
**specs/inputTypes/all.boundary.spec.js**

<pre>describe('boundary tests', function(){
	
		var testCases = [
			{ name: 'currency input - less than min',	options: { type: currencyType, min: 0, max: 10 }, input: '-.5', isError: true },
			{ name: 'currency input - min length',		options: { type: currencyType, min: 0, max: 10 }, input: '0', isError: false },
			{ name: 'currency input - more than min',	options: { type: currencyType, min: 0, max: 10 }, input: '2.5', isError: false },
			{ name: 'currency input - less than max',	options: { type: currencyType, min: 0, max: 10 }, input: '7.5', isError: false },
			{ name: 'currency input - max',				options: { type: currencyType, min: 0, max: 10 }, input: '10', isError: false },
			{ name: 'currency input - more than max',	options: { type: currencyType, min: 0, max: 10 }, input: '10.5', isError: true },

			…
			
			{ name: 'string input - longer than max',	options: { type: stringType, min: 5, max: 8 }, input: '123456789', isError: true },

		];

		testCases.forEach(function(testCase){
			var testName = testCase.name + ': is a ' + (testCase.isError ? 'validation error' : 'successful input');

			it(testName, function(){
				var testField = ko.observable();
				var presentableTestField = testField.extend({ validate: testCase.options });

				presentableTestField(testCase.input);

				expect(presentableTestField.validation().isError()).toBe(testCase.isError);
			});
		});

	});</pre>

We have a common component that handles all input logic that is governed by each type, so now our tests focus just on the collision of the types and input logic. Rather than require our teammates to enter tests every time they re-use a type for a new field in a completely expected way, we now only need to add tests when they want to add in some custom validation specific to that field, getting the value from the boundary testing while minimizing the cost. 

## But that&#8217;s not real user input?

That&#8217;s true, it isn&#8217;t. And since we already put some thought into our abstraction of the user input logic, it&#8217;s possible this won&#8217;t catch any errors that a unit test would miss. On the other hand, had we included the validation and formatting as attributes in the HTML, not only would we have a lot of duplication of effort, this would have required us to pull out our UI automation frameworks, with all the performance and ongoing maintenance costs that assumes. Instead, we can implement boundary testing fairly cheaply even if we thin it might be overkill, and let it run with our unit test suite at unit test speeds. The only gap that real user inputs would add, at far higher cost, is whether we had bound the UI component correctly to the input behind the scenes, which feels like another class of problem which would have effect reaching far beyond boundary conditions and probably [should be tested on it&#8217;s own][4].

 [1]: /index.php/webdev/mvvm-validation-with-knockoutjs-dont-put-it-in-the-viewhtml/ "MVVM Validation with KnockoutJS – Don’t put it in the View/HTML"
 [2]: https://github.com/tarwn/Blog_KnockoutMVVMPatterns/tree/master/validationWithTests
 [3]: http://knockoutjs.com/
 [4]: /index.php/webdev/using-selenium-for-view-testing-with-knockout-and-requirejs/ "Using Selenium for View testing with knockout and RequireJS"