---
title: Using Selenium for View testing with knockout and RequireJS
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-12-01T14:20:41+00:00
ID: 3089
url: /index.php/webdev/using-selenium-for-view-testing-with-knockout-and-requirejs/
featured_image: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/11/SeleniumRequireJsResults.png
views:
  - 6863
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - knockout
  - nancy
  - selenium
  - UnitTesting
  - webdriver

---
I've written about using Selenium/WebDriver for automated testing in a C# environment in the past. Some of these posts may be dated, but I've covered everything from [using the Selenium IDE][1], to [using WebDriver and the PageObject pattern][2], to [using SpecFlow to drive Selenium UI testing][3]. But in this age of [MVVM/MVC frameworks and libraries][4], do we really need every single test to hit the database?

# The Integration Testing Tax

UI testing is widely accepted as slow and fragile. We can use patterns like the [Page Object design pattern][5] to reduce the fragility of how we interact with elements on the page, but that doesn't speed things up any. We can merge tests to reduce repetitive actions, but that just adds a bunch of mess to the fragility side of the equation again.

So what to do?

Something I have been considering lately is to use Selenium to test just the View bindings, without the overhead of doing full integration testing. If I keep the logic out of the View (one of the reasons I love MVVM), then I can write some very extensive behavioral unit tests very close to the user and have fast, thorough behavior coverage. My main test concern then becomes how I ensure my bindings stay wired together over time, since I know the behavior under them is working properly. Tightening the focus provides a smaller subsurface to test against then trying to test all of the intricacies all of the way down and reduces the performance drags of anything behind the UI (like network, disk, subsystems, etc), so I could potentially test far more things in less time.

# UI Testing without the Backend

To try this out, I needed a sample application. I wrote a simple application using [knockout][6] and [RequireJS][7]. There is a basic search screen that allows you to "search" against a slow WebApi endpoint, get further details about a product from that endpoint, and add items to a local cart. 

Note: The WebApi actions are slow to reflect performance from larger, more complex applications that have to worry about things like authentication, databases, accessing network stores, logging, contention and retry policies, business logic, etc. 

## The Fake Application + Test Cases

All of the code for the fake application and tests is on github at [tarwn/Blog_RequireJSandSelenium][8].

The fake application looks like this:

[<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/11/SeleniumKnockoutSampleApp.png" alt="SeleniumKnockoutSampleApp" width="639" height="864" class="aligncenter size-full wp-image-3090" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/11/SeleniumKnockoutSampleApp.png 639w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/11/SeleniumKnockoutSampleApp-221x300.png 221w" sizes="(max-width: 639px) 100vw, 639px" />][9]

The ViewModel behind this view contains all of the properties necessary to display the screen and it's actions:

```javascript
define(["knockout", 
        "lodash",
        "app/services/itemService", 
        "app/models/itemSummary",
        "app/models/itemFull",
        "app/models/itemCart",
        "app/models/cart"
],
function (ko,
        _,
        ItemService,
        ItemSummary,
        ItemFull,
        ItemCart,
        Cart) {
   
    function IndexViewModel() {
        var itemService = new ItemService();
        var self = this;

        this.searchText = ko.observable();
        this.isSearching = ko.observable(false);
        this.searchResults = ko.observableArray();
        this.executeSearch = function () {
                /* Search logic with self.itemService.getItems */
        };
        
        this.selectedItem = ko.observable();
        this.isLoadingSelectedItem = ko.observable(false);
        this.selectItem = function (item) {
                /* Selection logic with self.itemService.getItem(item.id) */
        };

        this.cart = new Cart();
        this.addToCart = function (itemToAdd) {
                /* Add item to cart */
        };
    }

    return IndexViewModel;

});
```
All of the code for the site is located here: [github: tarwn/Blog_RequireJSandSelenium – /SampleWebSite][10]

<div style="background-color: #FFFFBB; padding: 1em; margin: .25em 1em">
  If you have not used <a href="http://requirejs.org/" title="RequireJS">RequireJS</a>, the top part of the javascript file may look confusing. define() is used to define all the dependencies I need for the script and a method that accepts those dependencies for us in the scope of that script. When someone in turn asks for an IndexViewModel (or more appropriately: /app/indexViewModel), they will get back this constructor, fully wired with all of it's dependencies. RequireJS ensures dependencies are loaded in the right order, keeps the global window scope clean, and allows us to mock out those dependencies using tools like <a href="https://github.com/iammerrick/Squire.js/" title="Squire.js on github">Squire.js</a>.
</div>

Let's get testing!

## Testing with Selenium – times 8!

While playing with this, I looked at 4 different methods of testing with Selenium across Chrome and Phantom. 

The 4 methods are:

  * [IndexTests.FullIntegration][11] – launch the site locally and run my UI tests against it, with the "real" WebAPI service
  * [IndexTests.ClientSideInjection][12] – Execute a script to stub the itemService.js logic to run locally
  * [IndexTests.NancyServer][13] – Self-host a Nancy server with fake versions of the server-side API
  * [IndexTests.NancyServer][14] – Self-host a Nancy server that serves a stubbed itemService.js file

These tests only cover the case where the HTML page is already a static file. If my page had instead been server-side generated from something like ASP.Net MVC or Web Pages, there would be additional work involved.

My goal was to keep the tests consistent across methods. This is a sample test:

```csharp
[Test]
public void WhenUserSearchesForItemsAndSelectsOne_ThenDetailsAreDisplayedForTheSelectedProduct()
{
    var indexPage = new IndexPage(_webDriver, _url, "Sample App");

    indexPage.SearchButton.Click();
    Utility.WaitUpTo(5000, () => Utility.IsElementPresent(indexPage.SearchResultsTable) 
				  && indexPage.SearchResultsTable.Displayed, "Search results");
    Assert.AreNotEqual(0, indexPage.GetNumberOfSearchResults());

    indexPage.ClickSearchResults(0);
    Utility.WaitUpTo(5000, () => Utility.IsElementPresent(indexPage.ItemDetails) 
				  && indexPage.ItemDetails.Displayed, "Item Details");

    Assert.AreEqual(indexPage.GetSelectedRowItemName(), indexPage.ItemDetailsName.Text);
}
```
Translated into English:

  * Open the Index Page
  * Click the Search button
  * Wait up to 5 seconds for the search results table to be displayed
  * Verify there are more than 0 results displayed
  * Click the 0th search result (to select it)
  * Wait up to 5 seconds for the selected Item to load in the Item Details section
  * Verify the name from the selected row matches the name in the details

<div style="background-color: #FFFFBB; padding: 1em; margin: 1em 1em .25em 1em">
  Note: I used the PageObject pattern very lightly to try and keep the tests readable and easily repeatable for each test method, but did not spend a lot of time following good patterns to create maintainable logic, as this is just experimental code.
</div>

Rather than go through all of the cases, I'll touch on just the basic FullIntegration case and one of the Nancy cases. The ClientSide injection case felt really hacky and fragile and I don't think it's a good choice.

### IndexTests.FullIntegration

This method is really slow and you have to have a working web server. The setup is quick and easy though:

```csharp
[TestFixture(typeof(ChromeDriver))]
[TestFixture(typeof(PhantomJSDriver))]
public class IndexTests_FullIntegration<TDriver>
where TDriver : IWebDriver, new()
{

	private IWebDriver _webDriver;
	private string _url = "http://localhost:63431/";

	[TestFixtureSetUp]
	public void TestFixtureSetup()
	{
	    _webDriver = new TDriver();
	}

	[TestFixtureTearDown]
	public void TestFixtureTearDown()
	{
	    _webDriver.Quit();
	}

	// ... tests here ...

}
```
Besides the performance, the other downside of this method is the hosting. In the [Using SpecFlow to drive Selenium UI Testing][3] post, I already had the steps necessary to deploy a staging site to test against, but this equates to more overhead and could drive where in your build process you perform the tests as well as make it harder to run them locally.

### IndexTests.Nancy

In this case, I created a self-hosting Nancy site that copies all of the static content from my Sample site and exposes fake versions of the API. The downsides of the two Nancy methods are the restriction to static content (no MVC pages) and that you're reimplementing a fake API for the system. 

This second issue actually bothers me a bit, as it means you are creating a fake set of data that all of your tests are going to rely on. Typically when you have one big shared pool of test data, it makes your systems harder to maintain, as that test data turns into a bog of magic values, some of which have to be set just so for tests to succeed. Allowing the tests to define the values that would be returned when they have specific needs would make this a lot more maintainable and help surface those critical data assumptions in the tests.

```csharp
[TestFixture(typeof(ChromeDriver))]
[TestFixture(typeof(PhantomJSDriver))]
public class IndexTests_NancyServer<TDriver>
where TDriver : IWebDriver, new()
{

	private string _baseUrl = "http://localhost:5000";
	private NancyHost _webServer;
	private IWebDriver _webDriver;

	[TestFixtureSetUp]
	public void TestFixtureSetup()
	{
	    _webServer = SetupServer();
	    _webDriver = new TDriver();
	}

	private NancyHost SetupServer()
	{
	    var dnfo = new DirectoryInfo("TestSampleWebSite");
	    if (dnfo.Exists)
		dnfo.Delete(true);

	    var proc = new Process();
	    proc.StartInfo.UseShellExecute = true;
	    proc.StartInfo.FileName = @"C:\WINDOWS\system32\xcopy.exe";
	    proc.StartInfo.Arguments = "\"../../../SampleWebSite\" TestSampleWebSite /E /I";
	    proc.Start();
	    proc.WaitForExit();

	    var config = new HostConfiguration()
	    {
		UrlReservations = new UrlReservations()
		{
		    User = "Everyone",
		    CreateAutomatically = true
		}
	    };

	    var host = new NancyHost(new LocalServerBootstrapper(dnfo.FullName), config, new Uri(_baseUrl));
	    host.Start();
	    return host;
	}

	[TestFixtureTearDown]
	public void TestFixtureTearDown()
	{
	    _webDriver.Quit();
	    _webServer.Stop();
	}

	// ... tests ...

}
```
[LocalServerBootstrapper][15] defines the static content folders (in this case, /styles, /Scripts, and the /index.html file). There is a single Module, [LocalServer][16], that serves up the 2 item API endpoints.

In a larger test suite, I would move this test code to a single startup method for the whole assembly.

### The Performance Results

Running the same 3 tests for each of the 4 methods across two different browsers helped see the difference between startup and ongoing performance costs.

[<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/11/SeleniumRequireJsResults.png" alt="SeleniumRequireJsResults" width="584" height="645" class="aligncenter size-full wp-image-3098" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/11/SeleniumRequireJsResults.png 584w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/11/SeleniumRequireJsResults-271x300.png 271w" sizes="(max-width: 584px) 100vw, 584px" />][17]

We pick up quite a bit of performance when we remove the backend server from the tests. one other thing to note is that the startup time for Phantom is quite a bit faster, but there is a slightly higher ongoing cost.

## Conclusions

This turned out to be a pretty nice little experiment. I wouldn't use any of these methods for a production test suite as they are now, but they definitely have promise and I'll certainly be trying out some more things with that Nancy setup. 

Switching from a full integration focus to more of a View focus did make things faster, but not to the degree I had hoped. I intend to spend some further thought on how to turn the dial up further without making this really painful to maintain.

 [1]: /index.php/webdev/uidevelopment/automated-web-testing-with-selenium/ "Automated testing with Selenium IDE"
 [2]: /index.php/webdev/uidevelopment/automated-web-testing-with-selenium-2/ "Automated testing with Selenium WebDriver"
 [3]: /index.php/enterprisedev/application-lifecycle-management/using-specflow-to/ "Using SpecFlow to drive Selenium WebDriver Tests"
 [4]: /index.php/webdev/uidevelopment/angularjs-vs-knockout-introduction-1/ "AngularJS vs Knockout - Introduction (1 of 8)"
 [5]: http://docs.seleniumhq.org/docs/06_test_design_considerations.jsp#page-object-design-pattern "Selenium: Page Object Design Pattern"
 [6]: http://knockoutjs.com/
 [7]: http://requirejs.org/
 [8]: https://github.com/tarwn/Blog_RequireJSandSelenium "tarwn/Blog_RequireJSandSelenium on github"
 [9]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/11/SeleniumKnockoutSampleApp.png
 [10]: https://github.com/tarwn/Blog_RequireJSandSelenium/tree/master/SampleWebSite "SampleWebSite on github"
 [11]: https://github.com/tarwn/Blog_RequireJSandSelenium/blob/master/SampleWebSite.UITests/IndexTests.FullIntegration.cs
 [12]: https://github.com/tarwn/Blog_RequireJSandSelenium/blob/master/SampleWebSite.UITests/IndexTests.ClientSideInjection.cs
 [13]: https://github.com/tarwn/Blog_RequireJSandSelenium/blob/master/SampleWebSite.UITests/IndexTests.NancyServer.cs
 [14]: https://github.com/tarwn/Blog_RequireJSandSelenium/blob/master/SampleWebSite.UITests/IndexTests.NancyServerWithScriptInjection.cs
 [15]: https://github.com/tarwn/Blog_RequireJSandSelenium/blob/master/SampleWebSite.UITests/NancyServer/LocalServerBootstrapper.cs "/SampleWebSite.UITests/NancyServer/LocalServerBootstrapper.cs on github"
 [16]: https://github.com/tarwn/Blog_RequireJSandSelenium/blob/master/SampleWebSite.UITests/NancyServer/LocalServer.cs "/SampleWebSite.UITests/NancyServer/LocalServer.cs on github"
 [17]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/11/SeleniumRequireJsResults.png