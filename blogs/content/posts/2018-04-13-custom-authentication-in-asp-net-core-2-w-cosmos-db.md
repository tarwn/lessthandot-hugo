---
title: Custom Authentication in ASP.Net Core 2 w/ Cosmos DB
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2018-04-13T12:10:01+00:00
ID: 9130
url: /index.php/webdev/serverprogramming/aspnet/custom-authentication-in-asp-net-core-2-w-cosmos-db/
views:
  - 6789
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
tags:
  - ASP.Net Core 2
  - authentication
  - bcrypt
  - CosmosDB

---
I&#8217;m building a B2C website with Cosmos DB as the backend store. This site will have a number of different authentication mechanisms, but I&#8217;m newer to ASP.Net Core 2 and the authentication changes since the prior version so I&#8217;m going to start with a basic Cookie and Login authentication system.

This is the second post in a series documenting the creation of this project. If you haven&#8217;t worked with custom authentication in ASP.Net Core 2, are looking for more examples of interacting with Cosmos DB, or just trying to avoid the overkill of Identity and EntityFramework, I hope this is helpful.

If you want to follow along from the beginning, the series starts with [ASP.Net Core 2 w/ Cosmos DB: Getting Started][1] 

## Getting Organization

Instead of grabbing a cup of coffee and pounding away at my keyboard until I&#8217;m done, I prefer to break work down into a smaller set of steps I can work on one at a time. This helps provide a sense of momentum, knowledge that each new step is starting at a known good state, and the safety to throw away a set of changes that isn&#8217;t working out instead of backtracking manually.

<div id="attachment_9155" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_106-600x406.png" alt="3 Authentication Scenarios: User/Pass, Twitter, API Keys" width="600" height="406" class="size-medium-width wp-image-9155" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_106-600x406.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_106-300x203.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_106-443x300.png 443w, /wp-content/uploads/2018/04/aspnetcore2cosmos_106.png 748w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    3 Authentication Scenarios: User/Pass, Twitter, API Keys
  </p>
</div>

There are two scenarios: Interactive logins and API authentication. For interactive logins, I want folks to have the option of either a username/password registration or a 3rd party OAuth registration (twitter, for example).

At a macro level, I can break this down into:

  * 1: Username/Password Registration, Login, Logout, Sessions + Cookies
  * 2: Twitter Registration, Twitter Login
  * 3: API Key management, API Key Authentication

This post will cover the first set: building standard username/password authentication down to Cosmos DB. It will also include some refactoring of the `Persistence` class I created to interact with Cosmos DB and good, secure hashing of passwords.

This is going to be a long post, it may look a little formidable at first, but actual coding time took less than writing it up as a blog post, it&#8217;s extensible (per the next two posts), and we have wide open freedom to apply any HTML and CSS we want and we can worry less about ASP.Net Core 2.1 releasing and breaking everything while we&#8217;re in the middle.

<div id="attachment_9173" style="width: 505px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_107.png" alt="These are the main changes, plus a few POCOs" width="495" height="746" class="size-full wp-image-9173" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_107.png 495w, /wp-content/uploads/2018/04/aspnetcore2cosmos_107-199x300.png 199w" sizes="(max-width: 495px) 100vw, 495px" />
  
  <p class="wp-caption-text">
    These are the main changes, plus a few POCOs
  </p>
</div>

All of the noted files above are included in the post. There are also a few POCOs (LoginResults, etc). You can view the full code for this post on github: [tarwn/Sample_ASPNetCore2AndCosmosDB, Post-#2 branch][2]

## An Overview of ASP.Net Core 2 Authentication

ASP.Net Core 2 ships with built-in modules for a variety of use cases: [MSDN: ASP.Net Core Authentication][3] 

<div class="warning-area">
  Be aware that most the MSDN examples mix the concepts of the Authentication middleware with the <code>Identity</code> model. ASP.net is not required and is, in my opinion, a fairly clumsy abstraction. Even the article on <a href="https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity-custom-storage-providers">custom authentication</a> assumes you want to use Identity for the business logic and will just be supplying data store implementations that are lightly documented and much wider than needed.</p> 
  
  <p>
    We will be using Principals and Identities for HttpContext, these are standard HttpContext concepts that are not related to &#8220;ASP.Net Core Identity&#8221;.
  </p>
</div>

The purpose of the Authentication modules are to interact with the outside world and provide some standard behavior and configurable options for their specific capabilities. The key behaviors are:

  * Translating external information to Claims: Reading a cookie, receiving an OAuth callback
  * Challenging a user: Sending them to a login page, starting an OAuth process
  * Sign In/Out: Persisting claims information back out into the world (writing a cookie)
  * External behavior for Unauthenticated/Forbidden requests: Sending a 401 Response

`Authentication Schemes` uniquely identify each Authentication module, for instance a request that comes in with two cookies would show two sets of Claims, each identified by an `AuthenticationScheme` from the Authentication module that read it. The `AuthenticationScheme` can also be used to enforce authentication to a specific `Scheme` for an endpoint (ignore claims from other modules, does this `Scheme` say they are allowed in?). A `DefaultAuthenticationScheme` configuration tell the system which Authentication module to challenge un-authentication users with. Or you can can have an endpoint explicitly issue a Challenge by `Scheme`, &#8220;Twitter&#8221; for example:

**Starting a Twitter OAuth Sign-in**

```csharp
[HttpGet("register/twitter")]
[AllowAnonymous]
public IActionResult RegisterWithTwitter()
{
    var props = new AuthenticationProperties()
    {
        RedirectUri = "account/register/twitter/continue"
    };
    return Challenge(props, "Twitter");
}
```
There&#8217;s a lot more, here is a great post: [ASP.NET Core 2.0 Authentication and Authorization System Demystified][4].

## Task 1: Cookies, custom Membership, Cosmos DB, and basic pages

The first piece we have sliced off is the workflow of Registering, Login, and Logout with cookies for validation. Though I executed this in vertical slices (for instance, I built the registration form from HTML down to Cosmo sDB before starting the login form), the post is presented in finished files or we&#8217;d be staring at the same file 10-12 times in a row.

### Authentication and ICustomMembership

First, let&#8217;s focus on getting the custom authentication wired in with the Cookies authentication module and a custom Membership object to include business logic.

The concrete `CosmosDBMembership` object will be responsible for creating and validating users internally against the Cosmos DB store:

[SampleCosmosCore2App/Membership/ICustomMembership.cs][5]

```csharp
public interface ICustomMembership
{
	CustomMembershipOptions Options { get; }

	Task<RegisterResult> RegisterAsync(string userName, string email, string password);
	Task<LoginResult> LoginAsync(string userName, string password);
	Task LogoutAsync();
	Task<bool> ValidateLoginAsync(ClaimsPrincipal principal);

	Task<SessionDetails> GetSessionDetailsAsync(ClaimsPrincipal principal);
}
```
This reflects the basic set of behaviors we intend to perform, with an options object that includes a `DefaultPathAfterLogin` and `AuthenticationType` to use when interacting with Claims and HttpContext SignIn/SignOut methods.

Next, we switch to the `Startup.cs` and register the concrete `CosmosDBMembership` class for this interface, register the default scheme (&#8220;Cookies&#8221;), and configure a Cookie authentication module for the &#8220;Cookies&#8221; scheme:

[SampleCosmosCore2App/Startup.cs][6]

```csharp
public void ConfigureServices(IServiceCollection services)
{
	services.AddMvc();

	// ...

	// #1
	services.AddCustomMembership<CosmosDBMembership>((options) => {
		options.AuthenticationType = CookieAuthenticationDefaults.AuthenticationScheme;
		options.DefaultPathAfterLogin = "/";
	});

	// #2
	services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
	// #3
	.AddCookie((options) =>
	{
		options.LoginPath = new PathString("/account/login");
		options.LogoutPath = new PathString("/account/logout");
		options.Events = new CookieAuthenticationEvents()
		{
			// #3
			OnValidatePrincipal = async (c) =>
			{
				var membership = c.HttpContext.RequestServices.GetRequiredService<ICustomMembership>();
				var isValid = await membership.ValidateLoginAsync(c.Principal);
				if (!isValid)
				{
					c.RejectPrincipal();
				}
			}
		};
	});

}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
	// ...

	// #4
	app.UseAuthentication();

	app.UseMvc();
}
```
_Note: While I refer to the &#8220;Cookies&#8221; scheme by name, you&#8217;ll note above I actually use the constant CookieAuthenticationDefaults.AuthenticationScheme_

**#1 &#8211; services.AddCustomMembership:** Register CosmosDBMembership as a Transient dependency for ICustomMembership vi an [extension method (github)][7].

**#2 &#8211; services.AddAuthentication:** Set the Default Authentication Scheme to our &#8220;Cookies&#8221; scheme to tell the system to use the &#8220;Cookies&#8221; module for challenging Unauthorized users.

**#3 &#8211; .AddCookie:** Lightly configure a Cookies module to use the &#8220;Cookies&#8221; scheme and provide the &#8220;LoginPath&#8221; used for challenging a user. (I don&#8217;t recall why I included &#8220;LogoutPath&#8221;). Wire an event in for `OnValidatePrincipal` that gets the registered Membership object and asks it if the user is valid according to our business logic.

**#4 &#8211; app.UseAuthentication:** Include the configured authentication middleware in the application

Now that we have wired ourselves into the outside world, let&#8217;s move on to build the business logic and persistence.

### Cosmos DB Refactoring

In the prior post, I had very basic CRUD logic to read/write a `Sample` object to Cosmos DB. Now we need to add the concepts of a registered user (`LoginUser`) and an authorized user session (`LoginSession`). First we&#8217;re going to do some refactoring.

Here are the challenges:

  * It will be a mess if we add `LoginUser` logic to `Persistence` on top of `Sample`
  * [Microsoft&#8217;s performance guide][8] says we should use a Singleton for the DocumentClient for performance

<div class="note-area">
  The three most notable changes from the <a href="https://azure.microsoft.com/en-us/blog/performance-tips-for-azure-documentdb-part-1-2/" title="MSDN: CosmosDB Performance Tips">Performance Tips</a> are configuring the <code>Persistence</code> class (and it&#8217;s DocumentClient) to be a singleton over the lifetime of the web server, calling <code>OpenAsync</code> early so the connection is opened prior to the first real call, and moving the call to <code>EnsureSetupAsync</code> to the registration of the Singleton in <code>Startup.cs</code> so it happens only one time.
</div>

To solve this, Persistence will be split into a top-level class with general behavior and responsibility for the DocumentClient instance, while actual persistence logic will be moved into individual, scoped data classes:

[SampleCosmosCore2App.Core/Persistence.cs][9]

```csharp
public class Persistence : IDisposable
{
	private string _databaseId;
	private Uri _endpointUri;
	private string _primaryKey;

	private DocumentClient _client;
	private bool _isDisposing;

	public Persistence(Uri endpointUri, string primaryKey, string databaseId)
	{
		_databaseId = databaseId;
		_endpointUri = endpointUri;
		_primaryKey = primaryKey;

		_client = new DocumentClient(endpointUri, primaryKey);
		_client.OpenAsync();

		Samples = new SamplePersistence(_client, _databaseId);
		Users = new UserPersistence(_client, _databaseId);
	}

	public UserPersistence Users { get; private set; }

	public SamplePersistence Samples { get; private set; }

	public async Task EnsureSetupAsync()
	{
		await _client.CreateDatabaseIfNotExistsAsync(new Database { Id = _databaseId });
		
		await Task.WhenAll(new Task[] {
			Samples.EnsureSetupAsync(),
			Users.EnsureSetupAsync()
		});
	}
	
	public void Dispose()
	{
		if (!_isDisposing)
		{
			_isDisposing = true;
			if (_client != null)
			{
				_client.Dispose();
			}
		}
	}
}
```
And we&#8217;ve exposed access to `Sample` and `LoginUser` data through two properties, which are also referenced during the `EnsureSetupAsync` call (btw: don&#8217;t use a Task.WhenAll for that like I did, bad things happen).

Next, we&#8217;ll stop calling the `EnsureSetupAsync` method on every persistence call and instead call this one time during the setup of the service.

[SampleCosmosCore2App/Startup.cs][10]

```csharp
public void ConfigureServices(IServiceCollection services)
{
	// ...

	services.AddSingleton<Persistence>((s) =>
	{
		var p = new Persistence(
			new Uri(Configuration["CosmosDB:URL"]),
					Configuration["CosmosDB:PrimaryKey"],
					Configuration["CosmosDB:DatabaseId"]);
		p.EnsureSetupAsync().Wait();
		return p;
	});

	// ... auth stuff
}
```
Now the setup for CosmosDB will be called just the one time, instead of on every call where it was from the early experimentation.

The last step is actually using these changes to read and write `LoginUser` and `LoginSession` instances to CosmosDB.

### Cosmos DB &#8211; Membership

We have an Interface defined above and the `Persistence` class has been refactored so we can add new Cosmos DB methods as we need them. Now we can focus on fleshing out the concrete `CosmosDBMembership` class. This wil then define the Persistence methods we need to add for Cosmos DB.

  * RegisterAsync
  * LoginAsync
  * LogoutAsync
  * ValidateLoginAsync

[Membership/CosmosDBMembership.cs &#8211; RegisterAsync][11]

```csharp
public async Task<RegisterResult> RegisterAsync(string userName, string email, string password)
{
	var user = new LoginUser()
	{
		Username = userName,
		Email = email,
		PasswordHash = password
	};

	try
	{
		user = await _persistence.Users.CreateUserAsync(user);
	}
	catch (Exception exc)
	{
		//TODO reduce breadth of exception statement
		return RegisterResult.GetFailed("Username is already in use");
	}

	await SignInAsync(user);

	return RegisterResult.GetSuccess();
}
```
Registration is pretty straightforward. Take the 3 required properties, load them into a `LoginUser` object, persist it, then sign the user in. The one non-obvious piece is the reliance on an `Exception` to detect duplicate Usernames. We&#8217;re going to add a unique constraint to Cosmos DB later to help enforce unique usernames.

[SampleCosmosCore2App/Membership/CosmosDBMembership.cs &#8211; LoginAsync][12]

```csharp
public async Task<LoginResult> LoginAsync(string userName, string password)
{
	var user = await _persistence.Users.GetUserByUsernameAsync(userName);
	if (user == null)
	{
		return LoginResult.GetFailed();
	}

	if (user.PasswordHash != password)
	{
		return LoginResult.GetFailed();
	}

	await SignInAsync(user);

	return LoginResult.GetSuccess();
}
```
We don&#8217;t have a password hash function yet, so for Login we take a username and password to load a `LoginUer` from the database, perform a direct comparison, and sign the user in if it is a match.

These both use the SignInAsync method, which creates a `LoginSession` for the user and uses the Authentication&#8217;s SignIn method for the `Options.AuthenticationType` we configured back in `Startup`:

[SampleCosmosCore2App/Membership/CosmosDBMembership.cs &#8211; SignInAsync][13]

```csharp
private async Task SignInAsync(LoginUser user)
{
	// key the login to a server-side session id to make it easy to invalidate later
	var session = new LoginSession()
	{
		UserId = user.Id,
		CreationTime = DateTime.UtcNow
	};
	session = await _persistence.Users.CreateSessionAsync(session);

	var identity = new ClaimsIdentity(Options.AuthenticationType);
	identity.AddClaim(new Claim("sessionId", session.Id));
	await _context.HttpContext.SignInAsync(new ClaimsPrincipal(identity));
}
```
Behind the scenes, ASP.Net is calling the `SignIn` method on the &#8220;Cookies&#8221; authentication module. The Cookies module will take the ClaimsPrincipal we just passed it and write it as a cookie on the outgoing Response (and then read it back in on subsequent Requests). This is how we&#8217;ll know who the user is later. Which brings us to ValidateLoginAsync, which is called from our custom OnValidatePrincipal logic.

[SampleCosmosCore2App/Membership/CosmosDBMembership.cs &#8211; ValidateLoginAsync][14]

```csharp
public async Task<bool> ValidateLoginAsync(ClaimsPrincipal principal)
{
	var sessionId = principal.FindFirstValue("sessionId");
	if (sessionId == null)
	{
		return false;
	}

	var session = await _persistence.Users.GetSessionAsync(sessionId);
	if (session.LogoutTime.HasValue)
	{
		return false;
	}

	return true;
}
```
We attempt to read the &#8220;sessionId&#8221; claim from the passed Principal and associate it with a saved `LoginSession` in the database, with a basic verification check to make sure the user has not logged out. In the event that we call false, our OnValidatePrincipal call will reject this principal (ensure it&#8217;s not flagged as &#8220;Authenticated&#8221;).

Finally, we have the last step for a user: Logging out.

[SampleCosmosCore2App/Membership/CosmosDBMembership.cs &#8211; LogoutAsync][15]

```csharp
public async Task LogoutAsync()
{
	await _context.HttpContext.SignOutAsync();

	var sessionId = _context.HttpContext.User.FindFirstValue("sessionId");
	if (sessionId != null)
	{
		var session = await _persistence.Users.GetSessionAsync(sessionId);
		session.LogoutTime = DateTime.UtcNow;
		await _persistence.Users.UpdateSessionAsync(session);
	}
}
```
Like the `LoginAsync<code> call, we use <code>SignOut` to sign out of the configured authentication module. I have not specific the `Scheme` here, so this may be signing us out of all the things.

Once we've signed out, we can mark the session as logged out too. This is more for informational purposes than anything else, as we've already revoke the cookie above and wouldn't be able to connect the user back to this session on their next page load.

### Cosmos DB - Persistence

With the business logic defined, now we can create the 6 Persistence calls to Cosmos DB: `CreateUserAsync`, `GetUserAsync`, `GetUserByUsernameAsync`, `CreateSessionAsync`, `GetSessionAsync`, `UpdateSessionAsync`

The setup logic has two points of interest. The first is that we make sure we store and re-use the `DocumentClient` object that is managed from `Persistence` rather than creating one locally for this class. Secondly, the `EnsureSetupAsync` method ensures we have a `DocumentCollection` set up for both Users and Sessions. 

[SampleCosmosCore2App.Core/Users/UserPersistence.cs][16]

```csharp
public class UserPersistence
{
	// ...

	public UserPersistence(DocumentClient client, string databaseId)
	{
		_client = client;
		_databaseId = databaseId;
	}

	public async Task EnsureSetupAsync()
	{
		var databaseUri = UriFactory.CreateDatabaseUri(_databaseId);

		// Collections
		await _client.CreateDocumentCollectionIfNotExistsAsync(databaseUri, new DocumentCollection() { Id = SESSIONS_DOCUMENT_COLLECTION_ID });
		var users = new DocumentCollection();
		users.Id = USERS_DOCUMENT_COLLECTION_ID;
		users.UniqueKeyPolicy = new UniqueKeyPolicy()
		{
			UniqueKeys = new Collection<UniqueKey>()
			{
				new UniqueKey{ Paths = new Collection<string>{ "/Username" } }
			}
		};
		await _client.CreateDocumentCollectionIfNotExistsAsync(databaseUri, users);
	}
	
	// ... CRUD methods
}
```
The Sessions collection is a straightforward, 1-line `CreateDocumentCollectionIfNotExistsAsync`, but for the Users we've used a longer form to create the collection so we can define `Username` as a unique key. This is the second half of the logic in the Membership object that prevents users from registering with a duplicate `Username`.

With the setup out of the way, now we can concentrate on supplying the necessary CRUD methods that `CosmosDBMembership` relies on.

[SampleCosmosCore2App.Core/Users/UserPersistence.cs][16]

```csharp
public class UserPersistence
{
	// ... ctor, EnsureSetupAsync ...

	public async Task<LoginUser> CreateUserAsync(LoginUser user)
	{
		var result = await _client.CreateDocumentAsync(GetUsersCollectionUri(), user, new RequestOptions() { });
		return JsonConvert.DeserializeObject<LoginUser>(result.Resource.ToString());
	}

	public async Task<LoginUser> GetUserAsync(string userId)
	{
		var result = await _client.ReadDocumentAsync<LoginUser>(UriFactory.CreateDocumentUri(_databaseId, USERS_DOCUMENT_COLLECTION_ID, userId));
		return result.Document;
	}

	public async Task<LoginUser> GetUserByUsernameAsync(string userName)
	{
		var query = _client.CreateDocumentQuery<LoginUser>(GetUsersCollectionUri(), new SqlQuerySpec()
		{
			QueryText = "SELECT * FROM Users U WHERE U.Username = @username",
			Parameters = new SqlParameterCollection()
		   {
			   new SqlParameter("@username", userName)
		   }
		});
		var results = await query.AsDocumentQuery()
								 .ExecuteNextAsync<LoginUser>();
		return results.FirstOrDefault();
	}

	public async Task<LoginSession> CreateSessionAsync(LoginSession session)
	{
		var result = await _client.CreateDocumentAsync(GetSessionsCollectionUri(), session);
		return JsonConvert.DeserializeObject<LoginSession>(result.Resource.ToString());
	}

	public async Task<LoginSession> GetSessionAsync(string sessionId)
	{
		var result = await _client.ReadDocumentAsync<LoginSession>(UriFactory.CreateDocumentUri(_databaseId, SESSIONS_DOCUMENT_COLLECTION_ID, sessionId));
		return result.Document;
	}

	public async Task UpdateSessionAsync(LoginSession session)
	{
		await _client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(_databaseId, SESSIONS_DOCUMENT_COLLECTION_ID, session.Id), session);
	}

	// ...
}
```
With two exceptions, these calls are 1-2 line methods to write a document to Cosmos DB or read it. Here are the exceptions:

**CreateUserAsync uses JsonConvert** to deserialize the result after inserting it into Cosmos DB. This is not automatic, but since we are relying on the generated `id` that Cosmos DB assigns our document then we want an object back. The one danger to wtach out for is if we had set some JSON.Net configurations with the `DocumentClient` we would want to use those here too.

**GetUserByUsernameAsync uses SQL** to query the `DocumentCollection`. Be very careful about getting your casing correct, this bit me several times. Cosmos DB supports [a limited SQL syntax][17] with support for parameterization.

Now that we have our business and persistence logic, we just need to add some UI on top!

### Adding Registration and Login pages

The final piece is the Registration and Login pages the user will interact with. Here are the endpoints we need:

  * GET /account/login: display a login page
  * POST /account/login: perform login + redirect
  * GET /account/register: display a registration form
  * POST /account/register: perform registration, login, redirect
  * GET /account/logout: perform logout + redirect somewhere
  * GET /account/protected: a page that requires authorization for testing

Add a new `AccountController` to the Controllers folder (Right click, Add, Controller). We'll start out with the registration logic:

[SampleCosmosCore2App/Controllers/AccountController.cs][18]

```csharp
[Route("account")]
public class AccountController : Controller
{
	private ICustomMembership _membership;

	public AccountController(ICustomMembership membership)
	{
		_membership = membership;
	}

	[HttpGet("register")]
	[AllowAnonymous]
	public IActionResult Register()
	{
		return View("Register");
	}

	[HttpPost("register")]
	[AllowAnonymous]
	[ValidateAntiForgeryToken]
	public async Task<IActionResult> RegisterPostAsync(RegisterModel model)
	{
		if (!ModelState.IsValid)
		{
			return View("Register");
		}

		var result = await _membership.RegisterAsync(model.UserName, model.Email, model.Password);
		if (result.Failed)
		{
			ModelState.AddModelError("", result.ErrorMessage);
			return View("Register", model);
		}

		return LocalRedirect(_membership.Options.DefaultPathAfterLogin);
	}

	// ...
}
```
The `GET` call simply returns a View named "Register". Once they fill in this form, it is `POSTed` to `RegisterPostAsync` which verifies the Model is valid, attempts to use our new `RegisterAsync` method, and then directs to configured default path for newly registered and sign-ed in users. If it fails at any point, it directs the user back to the "Register" view with an error in the `ModelState`.

Add a new "Account" folder to "Views", then add a new "Register.cshtml" view to this folder.

[SampleCosmosCore2App/Views/Account/Register.cshtml][19]

```html
@model SampleCosmosCore2App.Models.Account.RegisterModel

@{
    ViewData["Title"] = "Register";
    Layout = "~/Views/Shared/Layout.cshtml";
}

<h2>Register</h2>

<form asp-action="Register" method="post">
    <div asp-validation-summary="ModelOnly" class="text-danger"></div>
    <div class="form-group">
        <label asp-for="UserName" class="control-label"></label>
        <input asp-for="UserName" class="form-control" />
        <span asp-validation-for="UserName" class="text-danger"></span>
    </div>
    <div class="form-group">
        <label asp-for="Password" class="control-label"></label>
        <input asp-for="Password" class="form-control" />
        <span asp-validation-for="Password" class="text-danger"></span>
    </div>
    <div class="form-group">
        <label asp-for="Email" class="control-label"></label>
        <input asp-for="Email" class="form-control" />
        <span asp-validation-for="Email" class="text-danger"></span>
    </div>
    <div class="form-group">
        <input type="submit" value="Register" class="btn btn-default" />
    </div>
</form>
```

And there we go!

<div id="attachment_9137" style="width: 359px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_101.png" alt="Working Registration Form" width="349" height="300" class="size-full wp-image-9137" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_101.png 349w, /wp-content/uploads/2018/04/aspnetcore2cosmos_101-300x258.png 300w" sizes="(max-width: 349px) 100vw, 349px" />
  
  <p class="wp-caption-text">
    Working Registration Form
  </p>
</div>

Next up we can follow the same process for wiring in actions for Login GET/POST:

[SampleCosmosCore2App/Controllers/AccountController.cs][20]

```csharp
[Route("account")]
public class AccountController : Controller
{
	// ...

	[HttpGet("login")]
	[AllowAnonymous]
	public IActionResult Login(string returnUrl = null)
	{
		TempData["returnUrl"] = returnUrl;
		return View("Login");
	}

	[HttpPost("login")]
	[AllowAnonymous]
	[ValidateAntiForgeryToken]
	public async Task<IActionResult> LoginPostAsync(LoginModel user,  string returnUrl = null)
	{
		if (!ModelState.IsValid) {
			ModelState.AddModelError("", "Username or password is incorrect");
			return View("Login", user);
		}

		var result = await _membership.LoginAsync(user.UserName, user.Password);
		if (result.Failed)
		{
			ModelState.AddModelError("", "Username or password is incorrect");
			return View("Login", user);
		}

		return LocalRedirect(returnUrl ?? _membership.Options.DefaultPathAfterLogin);
	}

	// ...
}
```
Like the Registration actions, the `GET` simply returns a view, the user enters their information, and the `POST` is validated, sent to our Membership object to create a session (or not), and then the user is redirected to either the passed URL or back to the Login form with an error.

`returnUrl` is provided by the Cookie Authentication module when someone attempts to access a page requiring Authorization and the cookie authentication module challenges them by sending them to this login page.

Next, create a "Login" view in the Views/Account folder like we did with the "Register" View.

[Views/Account/Login.cshtml][21]

```html
@model SampleCosmosCore2App.Models.Account.LoginModel

@{
    ViewData["Title"] = "Login";
    Layout = "~/Views/Shared/Layout.cshtml";
}

<h2>Login</h2>

<form asp-action="Login" method="post">
    <div asp-validation-summary="ModelOnly" class="text-danger"></div>
    <div class="form-group">
        <label asp-for="UserName" class="control-label"></label>
        <input asp-for="UserName" class="form-control" />
        <span asp-validation-for="UserName" class="text-danger"></span>
    </div>
    <div class="form-group">
        <label asp-for="Password" class="control-label"></label>
        <input asp-for="Password" class="form-control" />
        <span asp-validation-for="Password" class="text-danger"></span>
    </div>
    <div class="form-group">
        <input type="submit" value="Login" class="btn btn-default" />
        <a asp-action="Register">Register</a>
    </div>
</form>
```

And there we go, a new login form!

<div id="attachment_9138" style="width: 362px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_102.png" alt="Working Login Form!" width="352" height="281" class="size-full wp-image-9138" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_102.png 352w, /wp-content/uploads/2018/04/aspnetcore2cosmos_102-300x239.png 300w" sizes="(max-width: 352px) 100vw, 352px" />
  
  <p class="wp-caption-text">
    Working Login Form!
  </p>
</div>

Finally we need a way for the user to logout and a protected endpoint to test against. These are pretty minimal and my test "protected" endpoint is barely more than an empty view.

[SampleCosmosCore2App/Controllers/AccountController.cs][22]

```csharp
public class AccountController : Controller
{
	// ...

	[HttpGet("logout")]
	[Authorize]
	public async Task<IActionResult> LogoutAsync()
	{
		await _membership.LogoutAsync();
		return RedirectToAction("login");
	}

	[HttpGet("protected")]
	[Authorize]
	public async Task<IActionResult> Protected()
	{
		// ... get some session details and return the view
	}

	// ...
}
```
For the logout, we're using the `LogoutAsync` method that signs out of the authentication module (clearing the cookie) then redirect to the login screen. The "Protected" endpoint has a couple more lines of logic to get soe data from the Membership object and pass it to the view, but an empty View would work just as well (look at the github link above for the full detail if you want it).

Success, that's a fully functional Membership system! With a really poor password strategy.

## Task 2: Password Hashing

If you recall, back 60 pages ago, we passed the plain text password from the user straight into Cosmos DB and later relied on basic string comparison to validate user logins. This is something you never want to do in the real world, so the next step is to add a one-way cryptographic hash.

<div id="attachment_9140" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_104-600x213.png" alt="CosmosDB User - Before Hashing" width="600" height="213" class="size-medium-width wp-image-9140" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_104-600x213.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_104-300x106.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_104-768x273.png 768w, /wp-content/uploads/2018/04/aspnetcore2cosmos_104-845x300.png 845w, /wp-content/uploads/2018/04/aspnetcore2cosmos_104.png 868w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    CosmosDB User - Before Hashing
  </p>
</div>

Don't worry, this is a lot shorter than the previous steps. 

[OWASP's guidance][23] is to use an Argon2 library, but I wasn't comfortable with the maturity of those projects for .Net, so I'm using a BCrypt implementation instead.

<div class="note-area">
  The goal of libraries like Argon2 and BCrypt is to not only hash the password value in a way that can't be reversed, but also do so in a way that is slow, which limits the ability to brute force guess the password.
</div>

To add BCrypt to the solution, install the [nuget package][24] from the UI or the Package Manager Console: `Install-Package BCrypt.Net-Core`.

Now we just have to make two small changes to implement it during registration and login.

[SampleCosmosCore2App/Membership/CosmosDBMembership.cs][25]

```csharp
public class CosmosDBMembership : ICustomMembership
{
	// ...

	public async Task<LoginResult> LoginAsync(string userName, string password)
	{
		var user = await _persistence.Users.GetUserByUsernameAsync(userName);
		if (user == null)
		{
			return LoginResult.GetFailed();
		}

		// CHANGE #1
		if (!BCrypt.Net.BCrypt.Verify(password, user.PasswordHash))
		{
			return LoginResult.GetFailed();
		}

		await SignInAsync(user);

		return LoginResult.GetSuccess();
	}
	
	// ...
	
	public async Task<RegisterResult> RegisterAsync(string userName, string email, string password)
	{
		// CHANGE #2
		var passwordHash = BCrypt.Net.BCrypt.HashPassword(password);
		var user = new LoginUser()
		{
			Username = userName,
			Email = email,
			PasswordHash = passwordHash
		};

		// ...
	}

	// ...
}
```
`LoginAsync`: replace the string comparison with `BCrypt.Net.BCrypt.Verify`.

`RegisterAsync`: hash the password before populating the `PasswordHash` property.

And now when we look at a registered user in the CosmosDB Data Explorer, we can see a nicely hashed password instead of plain text:

<div id="attachment_9141" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_105-600x191.png" alt="Data Explorer view of User - Now with Hashed Password!" width="600" height="191" class="size-medium-width wp-image-9141" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_105-600x191.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_105-300x96.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_105-768x245.png 768w, /wp-content/uploads/2018/04/aspnetcore2cosmos_105.png 916w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    Data Explorer view of User - Now with Hashed Password!
  </p>
</div>

Success!

## Next Steps

That probably felt pretty involved. I've written custom auth logic for just about every version of ASP.Net and I've found that once you get a handle on the theory and changes that have occurred to the Request/Response lifecycle, it becomes relatively easy to write your own custom auth logic that will continue to work abd be extended for many versions of ASP.Net to come. 

Between this post and the last one, we now have an ASP.Net Core 2 site running against CosmosDB with authentication. Next we'll extend the interactive authentication to include a third-party Twitter login, still using the built-in middleware to do the heavy lifting. Then after we buidl that, we'll layer in API authentication with user-manageable API tokens.

 [1]: /index.php/webdev/serverprogramming/aspnet/asp-net-core-2-w-cosmosdb-getting-started/ "Read the first post in the series"
 [2]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%232/SampleCosmosCore2App/Membership/CustomMembershipExtensions.cs "View on github"
 [3]: https://docs.microsoft.com/en-us/aspnet/core/security/authentication/ "MSDN: ASP.Net Core Authentication"
 [4]: https://digitalmccullough.com/posts/aspnetcore-auth-system-demystified.html "Deep dive into ASP.Net Core 2 Auth"
 [5]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Membership/ICustomMembership.cs
 [6]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Startup.cs#L44
 [7]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Membership/CustomMembershipExtensions.cs
 [8]: https://azure.microsoft.com/en-us/blog/performance-tips-for-azure-documentdb-part-1-2/ "MSDN: CosmosDB Performance Tips"
 [9]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App.Core/Persistence.cs
 [10]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Startup.cs
 [11]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Membership/CosmosDBMembership.cs#L86
 [12]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Membership/CosmosDBMembership.cs#L52
 [13]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Membership/CosmosDBMembership.cs#L133
 [14]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Membership/CosmosDBMembership.cs#L113
 [15]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Membership/CosmosDBMembership.cs#L73
 [16]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App.Core/Users/UserPersistence.cs
 [17]: https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-sql-query "MSDN: Cosmos DB SQL Queries"
 [18]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Controllers/AccountController.cs#L13
 [19]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Views/Account/Register.cshtml
 [20]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Controllers/AccountController.cs#L50
 [21]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Views/Account/Login.cshtml
 [22]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/d7c5907d45154685b62127c714394b29734f8e60/SampleCosmosCore2App/Controllers/AccountController.cs#L80
 [23]: https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet "OWASP Password Storage Cheat Sheet"
 [24]: https://www.nuget.org/packages/BCrypt.Net-Core/ "BCrypt.Net-Core on Nuget.org"
 [25]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%232/SampleCosmosCore2App/Membership/CosmosDBMembership.cs