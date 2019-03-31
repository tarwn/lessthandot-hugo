---
title: Continuous Delivery Project – Making MVCMusicStore Testable
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-12-16T11:28:00+00:00
excerpt: "It can be challenging to add unit testing to a project that was built without planning to incorporate it. The ASP.Net MVC Music Store tutorial was not built with unit testing in mind, but today we're going to walk through the addition of Controller unit tests, focusing on a controller that directly references Entity Framework objects and implicitly interacts with ASP.Net Membership objects and Request data from the current HttpContext."
url: /index.php/enterprisedev/unittest/continuous-delivery-project-making-mvcmusicstore/
views:
  - 19097
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
  - Unit Testing
tags:
  - asp.net mvc
  - continuous delivery
  - ms test
  - mvc music store
  - mvccontrib
  - rhinomocks
  - unit testing

---
It can be challenging to add unit testing to a project that was built without planning to incorporate it. The ASP.Net MVC Music Store tutorial was not built with unit testing in mind, but today we&#8217;re going to walk through the addition of Controller unit tests, focusing on a controller that directly references Entity Framework objects and implicitly interacts with ASP.Net Membership objects and Request data from the current HttpContext.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Overview_p2.png" title="Delivery Pipeline - Focus of Current Post" /><br /> Delivery Pipeline &#8211; Focus of Current Post
</div>

This is the third post in a multi-part series on my Continuous Delivery pipeline project. The [previous post][1] followed the setup of the Continuous Integration engine and the CI build job. This post follows the addition of Unit Tests to the ASP.Net MVC Music Store application so those tests can be incorporated in the CI build job (in the next post).

## Adding the Unit Tests

I chose to use MS Test for the Unit Test project due to it&#8217;s integration into Visual Studio. Later I&#8217;ll use Nunit for the automated interface testing where that integration is not as useful. This will also let us see both integrated into the build engine as we add those portions in.

The first step to adding Unit Testing to an existing project is picking a place to start. I selected the CheckoutController, as it is one of the more complex controllers in the project and will provide the best set of examples. Besides the implicit access of HttpContext data and instantiating the MusicStoreEntities DbContext directly, the Checkout Controller interacts with a cart model object that also interacts with HttpContext and it own instance of the DbContext.

_Note: There is a [MSDN Hands On Lab][2] to add &#8220;Unit Tests&#8221; to the MVCMusicStore site. The methods outlined in that post are lower impact to the production code (little or no changes required), but is actually Integration Testing, since the tests are executing across more than one unit of code and across application boundaries to a database. Integration Tests are useful, but typically more costly to maintain, provide less specific information, take longer to run, and are more fragile than unit tests. This is not to say that they aren&#8217;t useful, just that they are different._

The code for this project is available in a [BitBucket repository][3], but the order I follow here will be somewhat different than the actual order of the changesets, as the changes for this post overlapped some with the content of the next post.

## Testable Entity Framework

Before writing the first test, I need to drive a wedge between the Entity Framework DbContext and the Controllers so I can give the controller a data context that doesn&#8217;t really talk to a database. Currently the CheckoutController creates an instance of the MusicStoreEntities object when it is instantiated, and that instantiated MusicStoreEntities object gets it&#8217;s connection information from the web.config:

**MVCMusicStore/Controllers/CheckoutController.cs**

<pre>namespace MvcMusicStore.Controllers {

	[Authorize]
	public class CheckoutController : Controller {
		MusicStoreEntities storeDB = new MusicStoreEntities();
		const string PromoCode = "FREE";

		//
		// GET: /Checkout/AddressAndPayment
		public ActionResult AddressAndPayment() {
			return View();
		}
...</pre>

Replacing this concrete object with an interface will allow the production version of the site to continue working with a live database context while providing the ability to use a fake version for testing. 

To create the replacement, I&#8217;ll start replacing the concrete context with the name of an interface, then use the errors from the compiler to help define the minimum set of interface members required to satisfy the production code.

**MVCMusicStore/Controllers/CheckoutController.cs**

<pre>namespace MvcMusicStore.Controllers {

	[Authorize]
	public class CheckoutController : Controller {
		IMusicStoreEntities storeDB;
		const string PromoCode = "FREE";

		public CheckoutController() : this(new MusicStoreEntities()) { }

		public CheckoutController(IMusicStoreEntities storeDb) {
			this.storeDB = storeDb;
		}
...</pre>

_Why define the interface first and debug forward? Why not build a copy of the DbContext first? Starting with a minimal interface like this will help me keep the interface to the minimum necessary functionality. Had I started with the DbContext I could easily start defining methods that seem like they will be useful at some point, but don&#8217;t reflect what I will actually need or may never be used. Extra code is extra maintenance and finding out sooner that something doesn&#8217;t work (or is unnecessary) leads to less wasted effort._

The first error is the attempted assignment of the new MusicStoreEntities to the IMusicStoreEntities constructor. That one is easy enough to resolve:

**MVCMusicStore/Models/MusicStoreEntities.cs**

<pre>namespace MvcMusicStore.Models {
	public class MusicStoreEntities : DbContext, IMusicStoreEntities {
		// ...
	}

	public interface IMusicStoreEntities { }
}</pre>

I&#8217;ve added the interface declaration and the implements statement to MusicStoreEntities. Next I&#8217;ll define the collections and make sure the interface implements IDisposable:

**MVCMusicStore/Models/MusicStoreEntities.cs**

<pre>namespace MvcMusicStore.Models {
	public class MusicStoreEntities : DbContext, IMusicStoreEntities {
		public IDbSet&lt;Album&gt; Albums { get; set; }
		public IDbSet&lt;Genre&gt; Genres { get; set; }

		public IDbSet&lt;Artist&gt; Artists { get; set; }

		public IDbSet&lt;Cart&gt; Carts { get; set; }
		public IDbSet&lt;Order&gt; Orders { get; set; }
		public IDbSet&lt;OrderDetail&gt; OrderDetails { get; set; }

	}

	public interface IMusicStoreEntities : IDisposable {
		IDbSet&lt;Album&gt; Albums { get; set; }
		IDbSet&lt;Genre&gt; Genres { get; set; }

		IDbSet&lt;Artist&gt; Artists { get; set; }

		IDbSet&lt;Cart&gt; Carts { get; set; }
		IDbSet&lt;Order&gt; Orders { get; set; }
		IDbSet&lt;OrderDetail&gt; OrderDetails { get; set; }
	}
}</pre>

At this point I have a couple errors to clean up. In one case the compiler is upset with using Include() off of an IDbSet instance, this is easily solved by adding <code class="codespan">using System.Data.Entity;</code> to the file so the extension will be available. The second error points out a missing SaveChanges call on my interface which I can easily add:

**MVCMusicStore/Models/MusicStoreEntities.cs**

<pre>namespace MvcMusicStore.Models {
	public class MusicStoreEntities : DbContext, IMusicStoreEntities {
		// ...
	}

	public interface IMusicStoreEntities : IDisposable {
		// ...

		int SaveChanges();
	}
}</pre>

With those last couple changes completed, the build is happy and I have a minimal interface. 

Next I want to replace the behavior in the controllers of creating their own local DbContext instance with using one that is provided to them. I started this by defining the two constructors above for my CheckoutController, but rather than copy and paste this new behavior to all of the controllers, I&#8217;ll move the responsibility to a ControllerBase class:

**MVCMusicStore/Controllers/ControllerBase.cs** (New File)

<pre>namespace MvcMusicStore.Controllers {
	public class ControllerBase :Controller {
		private IMusicStoreEntities _storeDB;

		protected IMusicStoreEntities StoreDB { get { return _storeDB; } }

		public ControllerBase() : this(new MusicStoreEntities()) { }

		public ControllerBase(IMusicStoreEntities storeDb) {
			_storeDB = storeDb;
		}
	}
}</pre>

**MVCMusicStore/Controllers/CheckoutController.cs** (New File)

<pre>namespace MvcMusicStore.Controllers {

	[Authorize]
	public class CheckoutController : ControllerBase {
		const string PromoCode = "FREE";

		public CheckoutController() { }
		public CheckoutController(IMusicStoreEntities storeDb) : base(storeDb) { }

		// ...

		//
		// POST: /Checkout/AddressAndPayment
		[HttpPost]
		public ActionResult AddressAndPayment(FormCollection values) {
			// ...
				//Save Order
				StoreDB.Orders.Add(order);
				StoreDB.SaveChanges();
			// ...
		}

		// ...
	}
}</pre>

After replacing the private variable and constructors from the CheckoutController with inheritance from the ControllerBase, the three places referencing the old variable are showing as errors and I&#8217;ll simply update them to the public property in the ControllerBase. 

The last place I need to change is the ShoppingCart object. Despite being a model object, the ShoppingCart object instantiates it&#8217;s own local instance of the MusicStoreEntities context. The first time I converted the project, I missed this case and had some odd unit test results until I realized the cart was still accessing a real database.

_In larger projects it can be common to have components separately instantiated in random nooks and crannies, not only making it tricky to convert for unit testing but also making the production code more fragile and harder to change and troubleshoot. After replacing the local ones, it&#8217;s a good idea to execute some searches through the codebase to find other references to the concrete classes._

Just like the Controllers, I&#8217;ll update the ShoppingCart object to use the interface and use [Dependancy Injection][4] to pass in the context I expect it to use. Besides updating the constructor to require an IMusicStoreEntities context, I&#8217;ll also need to update the static methods that return instances of the cart:

**MVCMusicStore/Models/ShoppingCart.cs** (New File)

<pre>namespace MvcMusicStore.Models {
	public partial class ShoppingCart {
		IMusicStoreEntities storeDB;

		// ...

		public ShoppingCart(IMusicStoreEntities dbContext) {
			this.storeDB = dbContext;
		}
		
		public static ShoppingCart GetCart(HttpContextBase context, IMusicStoreEntities dbContext) {
			var cart = new ShoppingCart(dbContext);
			cart.ShoppingCartId = cart.GetCartId(context);
			return cart;
		}

		// Helper method to simplify shopping cart calls
		public static ShoppingCart GetCart(Controller controller, IMusicStoreEntities dbContext) {
			return GetCart(controller.HttpContext, dbContext);
		}

		// ...
	}
}</pre>

With these changes added, the next build errors direct me to the places that need to pass the extra argument. For the CheckoutController, I&#8217;ll use the public property exposed by the Controllerbase. For the AccountController I need to instantiate a MusicStoreEntities object to pass (or convert it to use ControllerBase), and for the others I can plug in their local storeDb variable.

_Note: In my [implementation][5], I went ahead and converted all of my controllers over to the new ControllerBase. The downside of this method is that the more you convert, the more you have to test. Since I don&#8217;t have unit tests in place, this means a full manual regression test. On a larger project I would limit my changes only to the pieces I was planning on adding unit tests to and had time to manually regression test, but I would build my objects (like the ControllerBase) in such a way that the next conversions could take advantage and extend them when it&#8217;s their turn._

## Adding a Test Project

After manually regression testing my changes to ensure they work, I&#8217;ll add the test project and create the first test class.

To get started, I&#8217;ll create the test project and use the package manager to get the RhinoMocks package. This will allow me to mock some of the resources the controller requires. With the project and resuorces ready, I can create the first CheckoutController test.

**MvcMusicStoreTests/Controllers/CheckoutController.cs**

<pre>namespace MvcMusicStoreTests.Controllers {

	[TestClass]
	public class CheckoutControllerTests {

		[TestMethod]
		public void AddressAndPayment_ReturnsView() {
			CheckoutController controller = new CheckoutController(MusicStoreEntitiesFactory.GetEmpty());

			ActionResult result = controller.AddressAndPayment();

			Assert.IsNotNull(result);
		}
	}
}</pre>

This test is using the Arrange, Act, Assert (AAA) unit testing pattern and is a basic test that asserts that the CheckoutController returns a result when we call AddressAndPayment. In the first step I call a Factory class to populate the data context of our CheckoutController. I have also started abstracting out obvious resources that will need to be fleshed out later, but haven&#8217;t started to define what those behaviors will be (I&#8217;ll let future tests decide that for me).

_You may also notice that the folder structure for my test matches the structure for the class that is under tests, this makes it easier to keep the project organized and to find matching files across the projects._

**MvcMusicStoreTests/MusicStoreEntitiesFactory.cs**

<pre>namespace MvcMusicStoreTests {
	class MusicStoreEntitiesFactory {
		public static IMusicStoreEntities GetEmpty() {
			return MockRepository.GenerateMock&lt;IMusicStoreEntities&gt;();
		}
	}
}</pre>

I don&#8217;t actually need any data yet, so I can use RhinoMock&#8217;s MockRepository to automaitcally create a stub implementation of the IMusicStoreEntities interface the controller requires.

## Fake Db and Http Contexts

Now that I have a basic unit test working, I can start moving into more complex (and useful) tests. This is where things start to get challenging. The data context is already abstracted from the controllers, but the framework also depends heavily on the web server context, including bits like querystring and form post variables from the client browser, session and cookie state containers, and additional context for Membership information.

While it is possible to start mocking or stubbing our way through this whole list, it can be pretty painful and isn&#8217;t really necessary. This particular problem has already been solved before, so I&#8217;ll import the MVC3 TestHelper package ([Install-Package MvcContrib.Mvc3.TestHelper-ci][6]) to do the work for me.

### Initial Data-Free Tests

I&#8217;m going to continue to ease into making this controller testable by choosing an action that has minimal data store interactions. This will allow me to focus on getting the HttpContext work out of the way first, instead of trying to do both at the same time.

Rather than trying to guess ahead as to what pieces of the package I&#8217;ll need, I&#8217;m going to create an instance of the test and build out just the logic I need to make it pass (Test Driven Test Development?). Here is that test:

**MvcMusicStoreTests/Controllers/CheckoutController.cs**

<pre>namespace MvcMusicStoreTests.Controllers {

	[TestClass]
	public class CheckoutControllerTests {
		// ...

		[TestMethod]
		public void AddressAndPayment_PostInvalidOrderNoPromotion_ReturnsOrderWithErrors() {
			CheckoutController controller = GetWiredUpController();
			FormCollection orderCollection = new FormCollection() {
				{"FirstName","fn"}
			};
			controller.ValueProvider = orderCollection.ToValueProvider();

			ViewResult result = controller.AddressAndPayment(orderCollection) as ViewResult;

			Assert.IsInstanceOfType(result.ViewData.Model, typeof(Order));
			Assert.AreNotEqual(0, result.ViewData.ModelState.Count);
		}
	}
}</pre>

I&#8217;m relegating the logic of creating the controller to a local function called <code class="codespan">GetWiredUpController()</code>, trusting it to return a functioning controller. I then create a FormCollection of values and assign it to the controller as if they had been sent from a client browser. The rest of the code is the Act and Assert steps of the test to call the controller and verify the result.

On the first run, the <code class="codespan">GetWiredUpController()</code> method isn&#8217;t giving me everything I need, but I can work through that iteratively until I have all the pieces I need. This took several iterations, so I&#8217;ll skip ahead to the end results. 

**MvcMusicStoreTests/Controllers/CheckoutController.cs**

<pre>namespace MvcMusicStoreTests.Controllers {

	[TestClass]
	public class CheckoutControllerTests {
		// ...

		private CheckoutController GetWiredUpController() {
			CheckoutController controller = new CheckoutController(MusicStoreEntitiesFactory.GetEmpty());
			TestControllerBuilder _builder = new TestControllerBuilder();
			_builder.HttpContext.User = new FakeUser();

			_builder.InitializeController(controller);
			return controller;
		}
	}
}</pre>

Like the initial test, I create the controller and populate it with an empty IMusicStoreEntities call from the factory. I then create an instance of the TestControllerBuilder class from the MVCContrib package, which will wire together all the stubs and fakes necessary to present Application, Session, and other necessary HttpContext values to the controller. I&#8217;ll add my own FakeUser object (an implementation of IPrincipal) to the builder, then have it do it&#8217;s magic on the CheckoutController instance. Voila, one fully wired up CheckoutController.

### Totally Faked Out, Just Add Data&#8230;

Now that I have the controller logic able to run independently from a real HTTP request, I can return to finish work on the methods that interact more heavily with the data store.

In order to inject some fake data, I need to replace the stubbed out IMusicStoreEntities data context in the MusicStoreEntitiesFactory with a concrete Fake implementation. This will allow me to add collections that I can locally push data into in order to setup scenarios for individual tests.

**MvcMusicStoreTests/MusicStoreEntitiesFactory.cs**

<pre>namespace MvcMusicStoreTests {
	class MusicStoreEntitiesFactory {
		public static IMusicStoreEntities GetEmpty() {
			FakeDataStore datastore = new FakeDataStore();
			datastore.Albums = new FakeDbSet&lt;Album&gt;();
			datastore.Artists = new FakeDbSet&lt;Artist&gt;();
			datastore.Carts = new FakeDbSet&lt;Cart&gt;();
			datastore.Genres = new FakeDbSet&lt;Genre&gt;();
			datastore.OrderDetails = new FakeDbSet&lt;OrderDetail&gt;();
			datastore.Orders = new FakeDbSet&lt;Order&gt;();
			return datastore;
		}
	}
}</pre>

The fake implementations of the <a href="https://bitbucket.org/tarwn/mvcmusicstore.main/src/8831221efe43/MvcMusicStoreTests/Fakes/FakeDataStore.cs" title="See the FakeDataStore class" target="_blank">datastore</a> exposes collections that implement <a href="https://bitbucket.org/tarwn/mvcmusicstore.main/src/8831221efe43/MvcMusicStoreTests/Fakes/FakeDbSet.cs" title="See the FakeDbSet class" target="_blank">IDbSet</a>. With this setup, it is easy to add test data on a per-test basis and without the overhead of a database (work) or some form of test data management (more work).

Using this new capability, I can start building out more extensive tests.

**MvcMusicStoreTests/Controllers/CheckoutController.cs**

<pre>namespace MvcMusicStoreTests.Controllers {

	[TestClass]
	public class CheckoutControllerTests {
		// ...

		[TestMethod]
		public void Complete_ValidOrderIdAndUser_ReturnsProperView() {
			FakeDataStore dataStore = MusicStoreEntitiesFactory.GetEmpty();
			dataStore.Orders.Add(new Order() { OrderId=5, Username="Bob" });
			FakeUser user = new FakeUser(new FakeIdentity("Bob","",true));
			CheckoutController controller = GetWiredUpController(store: dataStore, user: user);
			
			ViewResult result = controller.Complete(5) as ViewResult;

			Assert.AreEqual(5, result.ViewData.Model);
		}</pre>

Without any database or HttpContext, I can now test that a valid user with a valid order id will complete processing successfully. I&#8217;ve extended the WiredUpController to take optional arguments to simplify creating scenarios specific to an individual test, again adding functionality only as we need it to satisfy our tests.

With a working fake data context a working fake HttpContext and sample tests that interact with both, I can make additional tests very easily and have the groundwork in place to start adding test coverage to other controllers.

## Finishing Up

The source code is available [on BitBucket][7]. Initially I went down a number of blind alleys before I started using the MVC3 Contrib package, that one decision greatly simplified seperating the test code from it&#8217;s expectations of a real HttpContext. I tried to cover the most important parts and this same process should be applicable to other projects as well. If you have any questions about how I got from one step to the next, or what happened between changesets in the source repository, please don&#8217;t hesitate to ask here, in the forum, or via the contact form on my website.

Creating the tests incrementally and writing only the minimum code necessary may have looked longer, but it actually helped create a pretty tight codebase for the testing and helped to uncover the lack of server-side validation in the checkout routine, a bug in the tutorial code. Had I tried to build everything I needed up front, I probably would have gone further down several blind alleys and ended up with a much larger codebase then I actually needed.

## Next Steps

The project now has the beginning of unit test coverage and the tools necessary to start spreading those tests to the rest of our controllers. In the next post I&#8217;ll incorporate these test into the build process, running and capturing the test results as part of the CI build job.

<ul class="thelist">
  <li>
    <a href="http://wiki.ltd.local/index.php/Eli%27s_Continuous_Delivery_Project" title="Wiki post for Eli's Continuous Delivery Project">Wiki Post</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/starting-a-continuous-delivery-project" title="Starting a Continuous Delivery Project">Starting a Continuous Delivery Project</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-project-setting-up" title="Setting up the Continuous Integration stage">Setting up the Continuous Integration stage</a>
  </li>
  <li class="cur">
    <a href="/index.php/EnterpriseDev/UnitTest/continuous-delivery-project-making-mvcmusicstore" title="Making ASP.Net MVC Music Store Testable">Making ASP.Net MVC Music Store Testable</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/UnitTest/continuous-delivery-project-incorporating-the" title="Incorporating Unit Tests in the CI stage">Incorporating Unit Tests in the CI stage</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-project-deploy-and" title="Deploy and Smoke Test">Deploy and Smoke Test</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-adding-an-automated" title="Adding an Automated Interface Testing stage">Adding an Automated Interface Testing stage</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-dashboard-qa-and" title="Dashboard, QA and Production Deployment stage">Dashboard, QA and Production Deployment stage</a>
  </li>
</ul>

 [1]: /index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-project-setting-up "Starting the Continuous Delivery project"
 [2]: http://msdn.microsoft.com/en-us/gg618510 "ASP.NET MVC 3 Testing"
 [3]: https://bitbucket.org/tarwn/mvcmusicstore.main/changesets "Changesets for the source code"
 [4]: http://en.wikipedia.org/wiki/Dependency_injection "Dependancy Injection at Wikipedia"
 [5]: https://bitbucket.org/tarwn/mvcmusicstore.main/changeset/82d254cd9f1d "First Changeset for Unit Test Changes"
 [6]: http://nuget.org/List/Packages/MvcContrib.Mvc3.TestHelper-ci "Install the MvcContrib.Mvc3.TestHelper-ci package from Nuget"
 [7]: https://bitbucket.org/tarwn/mvcmusicstore.main/src "Source code on BitBucket"