---
title: MVVM – The User as an Asynchronous API (w/ Knockout)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-03-18T12:38:37+00:00
url: /index.php/webdev/mvvm-the-user-as-an-asynchronous-api/
views:
  - 4303
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - knockout
  - mvvm
  - Promises

---
Early into development of a fairly large modular SPA, we found ourselves needing to ask the user a series of complex questions during a complex chain of business logic. We built a quick little ViewModel and template to display to the user, added some assignable callbacks that it would generate buttons for, and called it good.

Except it wasn&#8217;t.

The first issue we ran into was the difficulty in writing tests around this already incredibly complex business case and trying to fake expected user interaction with the callbacks. The next issue was how difficult it was for the next developer (and sometimes just us, a week later) to figure out which magic properties had to be set for dialogs to work correctly. Then we noticed that debugging was about 100x harder than we expected. So we went back to the drawing board.

Somewhere between looking into examples that use promises and talking through how a perfect test would look, we came upon something profound (to us at least): &#8220;the user is an asynchronous API&#8221;.

<div style="background-color: #eeeeee; margin: .5em; margin-bottom: 1.5em; padding: .5em">
  Like the <a href="/index.php/webdev/mvvm-validation-with-knockoutjs-dont-put-it-in-the-viewhtml/" title="MVVM Validation with KnockoutJS – Don’t put it in the View/HTML">prior post</a>, the &#8220;Large modular SPA&#8221; in this case was a complete replatform of a 300+ KLOC Silverlight application with 30+ discrete screens ranging from &#8220;simple&#8221; in-screen search results that make IE tables weep, to complex SVG dashboards, to a multi-tabbed screen that can scale from 50 inputs to 1000&#8217;s, depending on the complexity of the user and their use case.
</div>

Th fulle sample code for these posts is on github here: [tarwn/Blog_KnockoutMVVMPatterns][1]

## An Example of Promises and API calls

Let&#8217;s start with a sample of what it looks like when we call a regular server-side API.

<div id="attachment_4454" style="width: 439px" class="wp-caption aligncenter">
  <a href="http://blogs.ltd.local/wp-content/uploads/2016/03/ShippingForm.png"><img src="http://blogs.ltd.local/wp-content/uploads/2016/03/ShippingForm.png" alt="Sample Shipping Address Form" width="429" height="299" class="size-full wp-image-4454" srcset="http://blogs.ltd.local/wp-content/uploads/2016/03/ShippingForm.png 429w, http://blogs.ltd.local/wp-content/uploads/2016/03/ShippingForm-300x209.png 300w" sizes="(max-width: 429px) 100vw, 429px" /></a>
  
  <p class="wp-caption-text">
    Sample Shipping Address Form
  </p>
</div>

<pre>&lt;h1&gt;A Basic Form + Fake API Call (Save Button)&lt;/h1&gt;
&lt;div class="form-area" data-bind="with: shippingForm"&gt;
	&lt;h3&gt;Shipping Information&lt;/h3&gt;

	&lt;label for="txtName"&gt;Name&lt;/label&gt;
		&lt;input type="text" id="txtName" data-bind="value: newEntry().name" /&gt;&lt;br /&gt;

	&lt;!-- ... etc ... --&gt;

	&lt;div class="button-strip"&gt;
		&lt;input type="button" data-bind="click: save, disable: isSaving, value: saveText" /&gt;
	&lt;/div&gt;
&lt;/div&gt;

&lt;script type="text/javascript"&gt;

//- Shipping Form Example
function ShippingFormViewModel(dataService){
	var self = this;

	// ...

	self.save = function(){
		if(self.isSaving())
			return;

		self.isSaving(true);
		self.saveStatus("Saving...");

		dataService.saveShippingAddress(self.newEntry())
		.then(function(status){
			self.isSaving(false);
			self.saveStatus(status);
		})
		
		// ...
	};
}

&lt;/script&gt;</pre>

Clicking the &#8220;Save&#8221; button updates some local values that will be used to modify the display, then calls saveShippingAddress on the service, which returns a promise. Once that service call is complete and the promise is resolved successfully, the display is updated again accordingly. 

A test for the save method could then look like this:

<pre>it("clears saving status when server save is successful", function(done){
	// arrange
	var fakeService = {
		saveShippingAddress: function(){ return Promise.resolve('Success'); }
	};
	var vm = new ShippingFormViewModel(fakeService);

	// act
	var afterSave = vm.save();
	
	// assert
	afterSave.then(function(){			
		expect(vm.saveStatus()).toBeNull();
		expect(vm.isSaving()).toBe(false);
	}).finally(done, done.fail);
});</pre>

The services are passed into the ViewModel when it&#8217;s created, keeping that logic separate and easy to maintain or change. We can write unit tests that pass in a fake version of the service, ready to pass a specific good or bad result, and ensure our business logic in the ViewModel continues to match our intent as the rest of the team extends it.

## Promises and User Dialogs

The services for an API call are built as injectable components that we can easily fake and pre-program with good and bad responses for tests. After throwing out all the mess we did with objects with magic callback properties, label collections, and so on, we realized that asking the user a question is basically the same thing and we could build a similar looking component to abstract that away from our logic.

In this example, we are displaying a list of past orders and giving the user an opportunity to quickly reorder. 

<div id="attachment_4455" style="width: 426px" class="wp-caption aligncenter">
  <a href="http://blogs.ltd.local/wp-content/uploads/2016/03/OrderForm.png"><img src="http://blogs.ltd.local/wp-content/uploads/2016/03/OrderForm.png" alt="Sample Reorder Form" width="416" height="283" class="size-full wp-image-4455" srcset="http://blogs.ltd.local/wp-content/uploads/2016/03/OrderForm.png 416w, http://blogs.ltd.local/wp-content/uploads/2016/03/OrderForm-300x204.png 300w" sizes="(max-width: 416px) 100vw, 416px" /></a>
  
  <p class="wp-caption-text">
    Sample Reorder Form
  </p>
</div>

However, we have a business case that requires we check that each of those products is still supported before placing the re-order. If any are no longer available, we need to ask the user to choose to leave that product off their reorder or choose one of several alternatives.

<div id="attachment_4456" style="width: 421px" class="wp-caption aligncenter">
  <a href="http://blogs.ltd.local/wp-content/uploads/2016/03/ReorderFlow.png"><img src="http://blogs.ltd.local/wp-content/uploads/2016/03/ReorderFlow.png" alt="Reorder Logic" width="411" height="565" class="size-full wp-image-4456" srcset="http://blogs.ltd.local/wp-content/uploads/2016/03/ReorderFlow.png 411w, http://blogs.ltd.local/wp-content/uploads/2016/03/ReorderFlow-218x300.png 218w" sizes="(max-width: 411px) 100vw, 411px" /></a>
  
  <p class="wp-caption-text">
    Reorder Logic
  </p>
</div>

Here is what the reorder function looks like with both the user and API treated as services:

<pre>self.reorder = function(priorOrder){
	var newOrder = new OrderModel({ id: 'pending', contents:[], price: 0 });
	self.isReordering(true);

	//= 1: begin checks on each product to verify we have stock for each of them
	var stockChecks = priorOrder.contents().map(function(product){
		return dataService.verifyStock(product);
	});

	//= 2: add 'in stock' items to list, ask user about out of stock products
	Promise.all(stockChecks)
	.then(function(results){
		//= 2.1: add 'in stock' products to the new order
		results.filter(function(result){
			if(result.status == 'in stock'){
				newOrder.contents.push(result.product);
			}
		});

		//= 2.2: Pass the list of 'out of stock' products to the next step to determine alternatives
		var outOfStockResults = results.filter(function(result){
			return result.status == 'out of stock';
		});

		return outOfStockResults;
	})
	.then(function(outOfStockResults){
		//= 3: If there are 'out of stock' products, ask the user what to do for each of them
		if(outOfStockResults.length &gt; 0){
			return userDialogService.askAboutOutOfStockProductAlternatives(outOfStockResults)
			.then(function(answers){
				if(answers == 'cancel'){
					//= 3.1: if the user cancelled, cancel the order
					newOrder = null;	
				}
				else{
					//= 3.2 if alternatives were chosen, add them to the order
					answers.forEach(function(selectedChoice){
						if(selectedChoice != null){
							newOrder.contents.push(selectedChoice.product);
						}
					});
				}
			});
		}
	})
	.then(function(){
		//= 4: the order hasn't been cancelled, add it to the order list
		if(newOrder != null){
			self.orders.unshift(newOrder);
		}
		self.isReordering(false);
	});
};</pre>

Writing tests for the user dialog looks just the same as for the API.

We can test that the user is given choices and those choices are used for the new order:

<pre>it("creates the order with the user alternatives for out of stock products when options are selected", function(done){
	// arrange
	var altProduct = { product: 'JKL'};
	var fakeDataService = {
		verifyStock: function(product){ 
			return Promise.resolve({ product: product, status: 'out of stock', alternatives: [ altProduct ] });
		}
	};
	var fakeUserService = {
		askAboutOutOfStockProductAlternatives: function(){ return Promise.resolve([altProduct, altProduct, altProduct]); }
	};
	var sampleOrder = { id: '1', price: 2, contents: [ 'ABC', 'DEF', 'GHI' ] };
	var vm = new OrderHistoryViewModel(fakeDataService, fakeUserService, [ sampleOrder ]);

	// act
	var afterReorder = vm.reorder(vm.orders()[0]);
	
	// assert
	afterReorder.then(function(){
		expect(vm.orders().length).toBe(2);
		expect(vm.orders()[1].contents()).toEqual(sampleOrder.contents);
		expect(vm.orders()[0].contents()).toEqual([altProduct.product, altProduct.product, altProduct.product]);
	}).finally(done, done.fail);
});</pre>

We can also test that we&#8217;re handling the user cancelling the dialog the way we want to:

<pre>it("cancels the order when the user cancels the dialog", function(done){
	// arrange
	var altProduct = { product: 'JKL'};
	var fakeDataService = {
		verifyStock: function(product){ 
			return Promise.resolve({ product: product, status: 'out of stock', alternatives: [ altProduct ] });
		}
	};
	var fakeUserService = {
		askAboutOutOfStockProductAlternatives: function(){ return Promise.resolve('cancel'); }
	};
	var sampleOrder = { id: '1', price: 2, contents: [ 'ABC', 'DEF', 'GHI' ] };
	var vm = new OrderHistoryViewModel(fakeDataService, fakeUserService, [ sampleOrder ]);

	// act
	var afterReorder = vm.reorder(vm.orders()[0]);
	
	// assert
	afterReorder.then(function(){
		expect(vm.orders().length).toBe(1);
	}).finally(done, done.fail);
});</pre>

The full sample implementation, including a working (but not production ready) dialog can be found here: [github: tarwn/Blog_KnockoutMVVMPatterns/tree/master/userDialogs][1]

## The User is an Asynchronous API

MVVM treats everything as a contract or service, whether it&#8217;s the surface it exposes to the View to be displayed, the methods that are exposed to the View to be called, or the API the ViewModel consumes to call remote services. Abstracting user dialogs as another asynchronous service makes the flow of the business logic cleaner and easier to test, while freeing us to work mostly independently on getting the mechanics and design of the user dialog experience the way we want.

 [1]: https://github.com/tarwn/Blog_KnockoutMVVMPatterns/tree/master/userDialogs "tarwn/Blog_KnockoutMVVMPatterns/tree/master/userDialogs on github"