---
title: Automated Web Testing with Selenium WebDriver
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-11-02T10:27:00+00:00
ID: 1366
excerpt: Last week we created a pair of smoke tests with the Selenium IDE tool. Several people commented, with varying levels of politeness, about the downsides of Selenium IDE. Today we are going to get into the nuts and bolts of coding automated tests against the WebDriver library.
url: /index.php/webdev/uidevelopment/automated-web-testing-with-selenium-2/
views:
  - 61019
rp4wp_auto_linked:
  - 1
categories:
  - UI Development
tags:
  - automated testing
  - selenium
  - webdriver

---
Last week we created a pair of smoke tests with the [Selenium IDE][1] tool. Several people commented, with varying levels of politeness, about the downsides of Selenium IDE. There are even those that will tell you not to use it at all, to immediately bypass Selenium IDE and go straight to code. My opinion is that this is a matter of context. 

Today we are going to get into the nuts and bolts of coding automated tests against the WebDriver library. Before we get into that, however, let's discuss when it is appropriate to make this transition.

## Context and When to Transition

Most projects that are using browser automation are large and require a fairly complex set of tests. The solutions they are using are at least at the level we will be working at today, if not further down the road to automated acceptance testing. A higher level of complexity across a much larger project is just not manageable with Selenium IDE, so many people are used to bypassing it immediately and going straight to a code solution.

Context is king. For small teams or sites, there is value in being able to put together some quick tests that can be run regularly without making an investment in creating a custom framework. It can also be a useful tool when you have a legacy site and just want to make sure it hasn't fallen over.

Not everyone has worked in the context of these larger projects or has worked extensively with Selenium. If you are getting started for the first time or currently using Selenium IDE, here are some signs to consider moving to a custom solution:

  * You often update a number of tests simultaneously to change element names
  * Sacrificing your computer while the tests run is becoming a nuisance
  * You want to share the tests with team members, others in your company, or your client
  * Maintaining the test suite list in the IDE tasks longer than creating tests
  * You are starting to build tests for every feature you add to the software
  * You want to tie the tests into the automated build system for your software

In these cases, you have likely scaled past the point where Selenium IDE is enabling you, into a range where it's costs outweigh it's gains.

## The Next Step for Web Automation

The next step in automating web interface tests is to use the Selenium WebDriver library to code the tests. Writing our own framework has some inherent advantages and disadvantages.

Pros: Free, Reduced Duplication, Easy to share, Relatively easy to automate
  
Cons: Increased cost, Increased risk due to code errors, Increased complexity
  
Neutral: Level of fragility depends on level of developer

There are Selenium WebDriver libraries for Java, Python, Ruby, and .Net. By using an existing Unit testing framework we don't have to write our own Test Runner and it is likely to have documentation and/or plugins for integrating into most of the popular build engines.

For the purpose of this post, we are again going to create tests against my contact page, but we'll be increasing the number of tests. We will be using the .Net driver, C#, and the MS Test framework.

_Note: I would advise this unit test project be separate from the overall solution for your application. Keeping it separate means you can version it separately, build and run it separately, and easily run the real unit tests in your product solution without accidentally running the interface ones as well.</p> 

Note 2: I had intended to do this in VB.Net originally, but the downside of switching easily between languages in the same IDE is that sometimes you find yourself writing in the wrong one without even realizing it.</em>

## Getting Started

After creating our .Net solution to house the test project, we need to add a reference to the Selenium WebDriver library. This can be downloaded from the [Web Driver Download page][2] or added via NuGet:

<div class="commandWrapper">
  <div class="commandPrompt">
    <p class="command">
      PM> Install-Package Selenium.WebDriver
    </p>
  </div>
</div>

_Note: If you don't have NuGet installed, I highly recommend you go check it out at [http://nuget.org/][3] or read more about it in [this recent MSDN article][4]. NuGet is a package manager that allows us to quickly download, install, and update 3rd party packages in Visual Studio. It not only makes it easier to keep 3rd party libraries up to date, but also makes a number of packages more accessible, since they generally install the appropriate settings and references to easily get us started._

Once we have all of that set up, we can create a single class file with a quick test function to verify we're ready to go:

```csharp
using System;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using OpenQA.Selenium.Firefox;

namespace SampleWebDriverUnitTest {

	[TestClass]
	public class GettingStartedTest {
		public GettingStartedTest() { }

		[TestMethod]
		public void GettingStarted_BasicFireFoxGET_LoadsPage() {
			using (FirefoxDriver driver = new FirefoxDriver()) {
				driver.Url = "http://www.tiernok.com";

				driver.Navigate();

				Assert.AreEqual("Eli Weinstock-Herman | Tarwn", driver.Title);
			}
		}
	}
}
```
This is all it takes to get our first test. Create the solution, add the reference, create the test class and method. Since it's MS Test, Ctrl+R, A will run all of our tests in the integrated test runner.

Selenium WebDriver is written in a somewhat fluent syntax, so for the rest of the post we will combine the URL and navigation into a single statement: driver.Navigate().GoToUrl("http://www.tiernok.com");

## The First Test Cases

Last time we started off by testing navigation to the home page and the presence of two books, then we tested submitting a form would correctly fail validation when it was missing a required field. Today we're going to extend that form validation check, a more complex example in Selenium IDE that will be handled fairly easily in our new framework.

### The Email Form

These are the situations we want to test for the email form:

> When I submit the form without an email address, it displays an error.
	  
> When I submit the form without a name, it displays an error.
	  
> When I submit the form without a message, it displays an error.
	  
> When I submit the form with all of the fields filled in, it displays a success message. 

The steps for these tests are going to be similar to the second test we created in Selenium IDE last week. 

### Exporting a Selenium IDE Test Case

Selenium IDE has a feature that allows us to export tests, so when we are getting started we can look at that export to get a feel for how the code version will function. To export a test, open Selenium IDE, go to File, Export Text Case As, and choose C# (WebDriver). A C# file will be generated with the test case converted to an NUnit Test. The file includes some test setup and teardown methods, as well as the following test method:

```csharp
[Test]
public void TheContactPageReturnsErrorWhenEmailFieldEmptyTest()
{
    driver.Navigate().GoToUrl("/");
    driver.FindElement(By.LinkText("Contact")).Click();
    driver.FindElement(By.Id("itxtFromName")).Clear();
    driver.FindElement(By.Id("itxtFromName")).SendKeys("Selenium Test");
    driver.FindElement(By.Id("itxtBody")).Clear();
    driver.FindElement(By.Id("itxtBody")).SendKeys("This is my Selenium Message");
    driver.FindElement(By.CssSelector("input[type="submit"]")).Click();
    Assert.AreEqual("Please fill in all three entries before sending the message.", driver.FindElement(By.CssSelector(".err")).Text);
}
```
Initially this may look more complicated then the Selenium IDE command steps, but really the only difference is that we are explicitly finding the elements we want to act on before acting, which was was more implicit in Selenium IDE. 

Converting this to an MS Test is not difficult, but we will want to wrap the tests in a using statement so that driver gets properly disposed (MS Test doesn't run cleanup properly when an exception occurs).

```csharp
[TestMethod]
public void ContactPageReturnsErrorWhenEmailFieldEmpty() {
	using (FirefoxDriver driver = new FirefoxDriver()) {
		driver.Navigate().GoToUrl("http://www.tiernok.com/");
		driver.FindElement(By.LinkText("Contact")).Click();
		driver.FindElement(By.Id("itxtFromName")).Clear();
		driver.FindElement(By.Id("itxtFromName")).SendKeys("Selenium Test");
		driver.FindElement(By.Id("itxtBody")).Clear();
		driver.FindElement(By.Id("itxtBody")).SendKeys("This is my Selenium Message");
		driver.FindElement(By.CssSelector("input[type="submit"]")).Click();
		Assert.AreEqual("Please fill in all three entries before sending the message.", driver.FindElement(By.CssSelector(".err")).Text);
	}
}
```
The only real changes have been the addition of the using statement and changing the decorated attribute from NUnit's Test to MS Test's TestMethod. 

### The Rest of the Test Cases

Based on this one test, we can easily create tests to satisfy the other conditions:

```csharp
[TestMethod]
public void ContactPageReturnsErrorWhenNameFieldEmpty() {
	using (FirefoxDriver driver = new FirefoxDriver()) {
		driver.Navigate().GoToUrl("http://www.tiernok.com/");
		driver.FindElement(By.LinkText("Contact")).Click();
		driver.FindElement(By.Id("itxtFromEmail")).Clear();
		driver.FindElement(By.Id("itxtFromEmail")).SendKeys(EmailAddress);
		driver.FindElement(By.Id("itxtBody")).Clear();
		driver.FindElement(By.Id("itxtBody")).SendKeys("This is my Selenium Message");
		driver.FindElement(By.CssSelector("input[type="submit"]")).Click();
		Assert.AreEqual("Please fill in all three entries before sending the message.", driver.FindElement(By.CssSelector(".err")).Text);
	}
}

[TestMethod]
public void ContactPageReturnsErrorWhenMessageFieldEmpty() {
	using (FirefoxDriver driver = new FirefoxDriver()) {
		driver.Navigate().GoToUrl("http://www.tiernok.com/");
		driver.FindElement(By.LinkText("Contact")).Click();
		driver.FindElement(By.Id("itxtFromName")).Clear();
		driver.FindElement(By.Id("itxtFromName")).SendKeys("Selenium Test");
		driver.FindElement(By.Id("itxtFromEmail")).Clear();
		driver.FindElement(By.Id("itxtFromEmail")).SendKeys(EmailAddress);
		driver.FindElement(By.CssSelector("input[type="submit"]")).Click();
		Assert.AreEqual("Please fill in all three entries before sending the message.", driver.FindElement(By.CssSelector(".err")).Text);
	}
}

[TestMethod]
public void ContactPageReturnsSuccessWhenAllFieldsProvided() {
	using (FirefoxDriver driver = new FirefoxDriver()) {
		driver.Navigate().GoToUrl("http://www.tiernok.com/");
		driver.FindElement(By.LinkText("Contact")).Click();
		driver.FindElement(By.Id("itxtFromName")).Clear();
		driver.FindElement(By.Id("itxtFromName")).SendKeys("Selenium Test");
		driver.FindElement(By.Id("itxtFromEmail")).Clear();
		driver.FindElement(By.Id("itxtFromEmail")).SendKeys(EmailAddress);
		driver.FindElement(By.Id("itxtBody")).Clear();
		driver.FindElement(By.Id("itxtBody")).SendKeys("This is my Selenium Message");
		driver.FindElement(By.CssSelector("input[type="submit"]")).Click();
		Assert.AreEqual("Thanks, your message has been sent successfully.", driver.FindElement(By.CssSelector(".suc")).Text);
	}
}
```
Theoretically we're done, if we had a few more cases we could keep copying and pasting our way to the finish line. If you recall, however, one of the goals of moving from Selenium IDE method to a custom solution was to reduce the amount of duplication and the fragility that duplication causes. So far all we have done is transfer that fragility from a series of test cases in the IDE to a series of unit tests in C#.

## Reducing Test Fragility

When we start writing more than basic smoke tests, we are going to be accessing items repetitively and duplicating ourselves more often. Switching to code has allowed us to create additional tests very quickly, but we haven't really solved the duplication issue yet.

There's two approaches we can take at this point, abstracting the duplication on our own or using the Page Factory from the Support library to implement the [Page Object][5] pattern. 

The idea of the Page Object pattern is to represent each page in our target application as a class in our test framework. This allows us to reflect functionality and changes in the real page with functionality or changes in our class. Rather than locating and interacting with individual controls in a browser, our tests than interact with these Page Object classes as representations of what the browser should be seeing. 

Given we are just getting started with the code approach, lets use the Page Object approach for the Contact page and use the PageFactory that is provided in the Support library to help with the wiring.

### Creating the Contact PageObject

Before we start making code changes, we need to get the Support library. As before, we can either download it from the [Selenium website][2] or install it via NuGet:

<div class="commandWrapper">
  <div class="commandPrompt">
    <p class="command">
      PM> Install-Package Selenium.Support
    </p>
  </div>
</div>

Next we are going to start modeling our page. Since there is a common layout throughout the site, I'll first create a base page object to represent the common elements (like navigation menu items) we will be interacting with:

```csharp
namespace SampleWebDriverUnitTest.Pages {
	class PageBase {

		[FindsBy(How = How.LinkText, Using = "Contact")]
		IWebElement ContactLink;

		public void NavigateContactLink() {
			ContactLink.Click();
		}

	}
}
```
Then we can extend this class to define the elements that are specific to the Contact form:

```csharp
namespace SampleWebDriverUnitTest.Pages {
	class ContactPage : PageBase {

		[FindsBy(How=How.Id, Using="itxtFromEmail")]
		IWebElement EmailInput;

		[FindsBy(How = How.Id, Using = "itxtFromName")]
		IWebElement NameInput;

		[FindsBy(How = How.Id, Using = "itxtBody")]
		IWebElement BodyInput;

		[FindsBy(How = How.CssSelector, Using = "input[type=submit]")]
		IWebElement SubmitInput;

		[FindsBy(How = How.CssSelector, Using = ".err")]
		IWebElement ErrorMessage;

		[FindsBy(How = How.CssSelector, Using = ".suc")]
		IWebElement SuccessMessage;

		public void SendEmail(string name, string email, string body) {
			if (!string.IsNullOrEmpty(email)) {
				EmailInput.Clear();
				EmailInput.SendKeys(email);
			}

			if (!string.IsNullOrEmpty(name)) {
				NameInput.Clear();
				NameInput.SendKeys(name);
			}

			if (!string.IsNullOrEmpty(body)) {
				BodyInput.Clear();
				BodyInput.SendKeys(body);
			}

			SubmitInput.Click();
		}

		// try/catches below are due to selenium using exceptions to indicate search failures rather than an empty result or null

		public bool IsDisplayingValidationError{
			get {
				try {
					return ErrorMessage.Displayed;
				}
				catch {
					return false;
				}
			}
		}

		public bool IsDisplayingSuccessMessage {
			get {
				try {
					return SuccessMessage.Displayed;
				}
				catch {
					return false;
				}	
			}
		}
	}
}
```
The PageFactory object is responsible for wiring our page objects to the actual page displayed in the browser. It attempts to match each IWebElement in the class to an element in the web page. By default, if you do not decorate the field with the FindsBy decorator then it will search for an element who's ID matches the variable name. I prefer to explicitly specify the find criteria, though, as this lets me name my variables consistently and limits the impact of an HTML change to a single decorator instead of causing a property name change.

```csharp
[TestMethod]
public void ContactPageReturnsErrorWhenEmailFieldEmpty_PageObjectVersion() {
	using (FirefoxDriver driver = new FirefoxDriver()) {
		driver.Navigate().GoToUrl("http://www.tiernok.com/");

		Pages.PageBase homepage = new Pages.PageBase();
		PageFactory.InitElements(driver, homepage);
		homepage.NavigateContactLink();

		Pages.ContactPage contactPage = new Pages.ContactPage();
		PageFactory.InitElements(driver, contactPage);
		contactPage.SendEmail("Selenium Test", "", "This is my Selenium Test Message");

		Assert.IsTrue(contactPage.IsDisplayingValidationError);
	}
}
```
The new code:

navigates to the site

creates an instance of the base page

clicks the navigation link for the Contact Page

assumes it is on the contact page and creates an instance of that object

Sends an email with two of the three values filled in

Asserts that the error message is displayed

However, this code still has problems. 

### Refining and Correcting the First Test

The current code, in my opinion, doesn't fail soon enough if it is on the wrong page. In addition, this first test we've converted is not very readable and I can already tell there is going to be a lot of duplication in later tests. Let's do some refactoring.

Rather than repetitively initializing page objects in each test and and adding title verification to each test, lets move that behavior to the PageBase so we can easily do it for every page we load.

```csharp
class PageBase {
	public RemoteWebDriver Driver { get; set; }
	public string ExpectedTitle { get;set; }

	// ... unchanged code ...

	public static TPage GetInstance<TPage>(RemoteWebDriver driver, string expectedTitle) where TPage : PageBase, new() {
		TPage pageInstance = new TPage() { 
			ExpectedTitle = expectedTitle,
			Driver = driver
		};
		PageFactory.InitElements(driver, pageInstance);

		Assert.AreEqual(pageInstance.ExpectedTitle, driver.Title);

		return pageInstance;
	}
}
```
Now we have a single generic call that can create a Page (provided it inherits from PageBase and has a constructor) and execute an assertion on it's title. Lets update the NavigateContactLink method to return an initialized ContactPage instance, since this will be a consistent next step each time we navigate to a new page. This reduces the amount of code in the test and adds an automatic check to ensure we have reached the page we were expecting.

```csharp
class PageBase {
	// ... unchanged ...

	public ContactPage NavigateContactLink() {
		ContactLink.Click();
		return GetInstance<ContactPage>(Driver, "Eli Weinstock-Herman | Tarwn - Contact");
	}

	// ... unchanged ...
}
```
Our refactored test now looks like this:

```csharp
[TestMethod]
public void ContactPageReturnsErrorWhenEmailFieldEmpty_PageObjectVersion() {
	using (FirefoxDriver driver = new FirefoxDriver()) {
		driver.Navigate().GoToUrl("http://www.tiernok.com/");

		PageBase homepage = PageBase.GetInstance<PageBase>(driver, "Eli Weinstock-Herman | Tarwn");
		ContactPage contactPage = homepage.NavigateContactLink();
		
		contactPage.SendEmail("Selenium Test", "", "This is my Selenium Test Message");

		Assert.IsTrue(contactPage.IsDisplayingValidationError);
	}
}
```
There is still some additional refactoring we could do, but this is far more readable, has reduced the level of duplication, and added title checks.

### Converting the Remaining Tests

Converting the remining tests is a straightforward exercise.

```csharp
[TestMethod]
public void ContactPageReturnsErrorWhenEmailFieldEmpty() {
	using (FirefoxDriver driver = new FirefoxDriver()) {
		driver.Navigate().GoToUrl("http://www.tiernok.com/");
		PageBase homepage = PageBase.GetInstance<PageBase>(driver, "Eli Weinstock-Herman | Tarwn");
		ContactPage contactPage = homepage.NavigateContactLink();

		contactPage.SendEmail("Selenium Test", "", "This is my Selenium Test Message");

		Assert.IsTrue(contactPage.IsDisplayingValidationError);
	}
}

[TestMethod]
public void ContactPageReturnsErrorWhenNameFieldEmpty() {
	using (FirefoxDriver driver = new FirefoxDriver()) {
		driver.Navigate().GoToUrl("http://www.tiernok.com/");
		PageBase homepage = PageBase.GetInstance<PageBase>(driver, "Eli Weinstock-Herman | Tarwn");
		ContactPage contactPage = homepage.NavigateContactLink();

		contactPage.SendEmail("", EmailAddress, "This is my Selenium Test Message");

		Assert.IsTrue(contactPage.IsDisplayingValidationError);
	}
}

[TestMethod]
public void ContactPageReturnsErrorWhenMessageFieldEmpty() {
	using (FirefoxDriver driver = new FirefoxDriver()) {
		driver.Navigate().GoToUrl("http://www.tiernok.com/");
		PageBase homepage = PageBase.GetInstance<PageBase>(driver, "Eli Weinstock-Herman | Tarwn");
		ContactPage contactPage = homepage.NavigateContactLink();

		contactPage.SendEmail("Selenium Test", EmailAddress, "");

		Assert.IsTrue(contactPage.IsDisplayingValidationError);
	}
}

[TestMethod]
public void ContactPageReturnsSuccessWhenAllFieldsProvided() {
	using (FirefoxDriver driver = new FirefoxDriver()) {
		driver.Navigate().GoToUrl("http://www.tiernok.com/");
		PageBase homepage = PageBase.GetInstance<PageBase>(driver, "Eli Weinstock-Herman | Tarwn");
		ContactPage contactPage = homepage.NavigateContactLink();

		contactPage.SendEmail("Selenium Test", EmailAddress, "This is my Selenium Message");

		Assert.IsTrue(contactPage.IsDisplayingSuccessMessage);
	}
}
```
There is additional refactoring we could do at this stage. The hardcoded page titles and repeated driver.Navigate() calls could be cleaned up to further reduce duplication and make the tests more readable. As we add more pages and more tests, we are going to find other common functionality to refactor and other necessary functionality to add. The framework I end up building through aggressive refactoring of these tests may not look like the one that you end up with, but we will be reaching in the same direction.

## Review

We have created the beginning of a more extensive test framework that enables us to create more complex tests with less duplication and effort. Refactoring common elements improved maintainability and our ability to react to changes, while using the PageObject pattern has started to separate the subject of our tests from the mechanics of how we interact with the browser.

As before, though, there are some points to return to.

### Complexity, Investment, and Code Errors

Getting started with some Selenium IDE tests was pretty easy. A customer solution, by comparison, scales farther but requires more up front investment and introduces the potential for errors inside the framework that could adversely affect the test results. Despite using an existing Test Runner and the PageFactory object for wiring, I still managed to make errors along the way. In one case, I actually lost time chasing down something that I thought was a timing issue (which you will run into at some point) that was actually a simple error in my code.

Experience helps a lot in this area, as does following good code craftsmanship standards, refactoring aggressively, using consistent naming, and trying to keep the bigger picture of the framework in mind. Along the way I made variable type selections to specifically set myself up for later additions I want (choosing RemoteWebDriver over IWebDriver in several cases, for instance) and despite needing only one NavigateClick function for this sample, I created a pattern I would intend to follow for the rest of the navigation menu. Experience will help tell us these things are available, but first we have to make the mistakes and experiments along the way to gain that experience.

### Moving Forward

From automating basic smoke tests and HTML element interactions we have moved into the realm of interacting with web pages and actions on those web pages. We can abstract a step further, though, and start focusing on validating what the end user has asked for. The next level is a framework that abstracts the web page away, leaving us to interact with higher level functions and processes, validating whether the application fulfills the users acceptance criteria.

### Finishing Up

The Selenium WebDriver library offers some great capabilities. Using a handy Unit Testing framework and Test Runner can help give us a solution that is halfway between automated acceptance testing and Selenium IDE in a fairly short period of time. There are still a lot of areas we haven't discussed, such as integrating with a build server or tying into multiple browser drivers, but this post has already run a bit longer than the prior one. If you are interested, the code for this post is in a [mercurial repository on bitbucket][6] and as always we can continue discussing any of the points in the post in the comments here or in the forum area.

 [1]: /index.php/WebDev/UIDevelopment/automated-web-testing-with-selenium "Read last week's post"
 [2]: http://seleniumhq.org/download/ "Selenium WebDriver download page"
 [3]: http://nuget.org/ "NuGet.org"
 [4]: http://msdn.microsoft.com/en-us/magazine/hh547106.aspx "Manage Project Libraries with NuGet by Phil Haack"
 [5]: http://code.google.com/p/selenium/wiki/PageObjects "More information on Page Object"
 [6]: https://bitbucket.org/tarwn/seleniumwebdriversample/ "Source code for the post"