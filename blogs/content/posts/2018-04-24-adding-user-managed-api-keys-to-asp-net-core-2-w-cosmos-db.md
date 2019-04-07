---
title: Adding User-Managed API Keys to ASP.Net Core 2 w/ Cosmos DB
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2018-04-24T12:58:45+00:00
ID: 9201
url: /index.php/uncategorized/adding-user-managed-api-keys-to-asp-net-core-2-w-cosmos-db/
views:
  - 3238
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Uncategorized
tags:
  - ASP.Net Core 2
  - CosmosDB

---
I’m building the foundation for an ASP.Net Core 2 site with [Cosmos DB][1] as the back-end store and want to build in the idea of user-manageable API keys. In the past two posts, I&#8217;ve added interactive registration and login to the application using built-in Cookie and Twitter middleware on top of custom authorization logic and Cosmos DB. In this one, we&#8217;ll be adding endpoints that require API Keys that can be created and revoked by the user.

<div id="attachment_9155" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_106-600x406.png" alt="3 Authentication Scenarios: User/Pass, Twitter, API Keys" width="600" height="406" class="size-medium-width wp-image-9155" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_106-600x406.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_106-300x203.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_106-443x300.png 443w, /wp-content/uploads/2018/04/aspnetcore2cosmos_106.png 748w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    3 Authentication Scenarios: User/Pass, Twitter, API Keys
  </p>
</div>

While I started out with credentials stored directly in the `LoginUser` Document, in the prior post I decided to start treating authentication mechanisms as separate documents (my house keys are not a property of me). 

<div id="attachment_9181" style="width: 236px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_202.png" alt="Defining People (Users) Separate from House Keys (User Authentication)" width="226" height="161" class="size-full wp-image-9181" />
  
  <p class="wp-caption-text">
    Defining People (Users) Separate from House Keys (User Authentication)
  </p>
</div>

In this post, that separation will start paying off, as it will allow us to add API key entries for the user and easily &#8220;revoke&#8221; them by switching their authentication type from &#8220;APIKey&#8221; to &#8220;RevokedAPIKey&#8221;, keeping the data available for audits but ensuring it&#8217;s no longer valid for API authentication.

<div class="note-area">
  Posts in this series:</p> 
  
  <ul style="margin: .5em">
    <li>
      <a href="/index.php/webdev/serverprogramming/aspnet/asp-net-core-2-w-cosmosdb-getting-started/">ASP.Net Core 2 w/ Cosmos DB: Getting Started</a>
    </li>
    <li>
      <a href="/index.php/webdev/serverprogramming/aspnet/custom-authentication-in-asp-net-core-2-w-cosmos-db/">Custom Authentication in ASP.Net Core 2 w/ Cosmos DB</a>
    </li>
    <li>
      <a href="/index.php/webdev/serverprogramming/aspnet/adding-twitter-authentication-to-an-asp-net-core-2-site-w-cosmos-db/">Adding Twitter Authentication to an ASP.Net Core 2 site w/ Cosmos DB</a>
    </li>
    <li>
      Adding User-Managed API Keys to ASP.Net Core 2 w/ Cosmos DB (You Are Here!)
    </li>
  </ul>
</div>

## Breaking it down

In the last two posts, we laid the groundwork for authentication from the UI down to Cosmos DB. With this post, we&#8217;re going to build a minimal screen to let users generate API Keys, some protected API endpoints, and the logic to tie these into the `LoginUserAuthentications` DocumentCollection in Cosmos DB.

<div id="attachment_9202" style="width: 426px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_301.png" alt="From UI down to Cosmos DB back up to Middleware" width="416" height="874" class="size-full wp-image-9202" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_301.png 416w, /wp-content/uploads/2018/04/aspnetcore2cosmos_301-143x300.png 143w" sizes="(max-width: 416px) 100vw, 416px" />
  
  <p class="wp-caption-text">
    From UI down to Cosmos DB back up to Middleware
  </p>
</div>

Most of the work is building on the [previous post](/index.php/webdev/serverprogramming/aspnet/adding-twitter-authentication-to-an-asp-net-core-2-site-w-cosmos-db/), the big difference is a new way to authenticate and some middleware to do the work.

I worked on this in two pieces:

  * **User Interface:** Create/Revoke API keys in the UI down to Cosmos DB
  * **Making API Calls**: Protected API endpoints, Middleware, Membership business logic

The source code through this set of changes is available here: [github: Sample_ASPNetCore2AndCosmosDB, Post #4 Branch][2]

## Task 1: User-managed API Keys

In this set of changes, we&#8217;re going to add some very basic screens to show the list of API Keys, add a new one, and revoke an existing one. 

<div class="note-area">
  Why Revoke instead of Delete? I chose to revoke API Keys to help support audit logs and instrumentation down the road. This may be a case of YAGNI, but it was easy enough to implement and also tries a pattern I may refactor to using for passwords and password history.
</div>

### UI Screens

Let&#8217;s start with the screens and work down the stack. I&#8217;ve created a new (and poorly named) `UserController` for the new screens.

[SampleCosmosCore2App/Controllers/UserController.cs][3]

```csharp
[HttpGet("")]
public async Task<IActionResult> IndexAsync(string error)
{
	var sessionId = _membership.GetSessionId(HttpContext.User);
	var user = await _persistence.Users.GetUserBySessionIdAsync(sessionId);
	var auths = await _persistence.Users.GetUserAuthenticationsAsync(user.Id);

	// ... grouping logic to create model ...
   
	return View("Index", model);
}

[HttpGet("addKey")]
public IActionResult AddKey()
{
	var model = new NewKeyModel();
	return View("AddKey", model);
}

[HttpPost("addKey")]
public async Task<IActionResult> PostAddKeyAsync(NewKeyModel model)
{
	if (!ModelState.IsValid)
	{
		return View("AddKey", model);
	}

	var sessionId = _membership.GetSessionId(HttpContext.User);
	var user = await _persistence.Users.GetUserBySessionIdAsync(sessionId);

	var generatedKey = _membership.GenerateAPIKey(user.Id);
	var result = await _membership.AddAuthenticationAsync(user.Id, "APIKey", generatedKey, model.Name);
	var resultModel = // ... create the model ...

	return View("ShowKey", resultModel);
}

[HttpGet("revoke")]
public async Task<IActionResult> Revoke(string id)
{
	var sessionId = _membership.GetSessionId(HttpContext.User);
	var user = await _persistence.Users.GetUserBySessionIdAsync(sessionId);

	var result = await _membership.RevokeAuthenticationAsync(user.Id, id);
	if (result.Failed)
	{
		return RedirectToAction("IndexAsync", new { error = result.Error });
	}
	else
	{
		return RedirectToAction("IndexAsync");
	}
}
```
`IndexAsync` uses `ICustomMembership` to get the current logged in user&#8217;s information, then uses a new `UserPersistence` method to get all available `LoginUserAuthentications` from Cosmos DB (which will include Twitter, revoked API Tokens, and more as we go along).

`AddKey` and `PostAddKeyAsync` display a form to create a new API Token and receive the POST, respectively. When we receive the POST, we rely on a new method in `ICustomMembership` to generate a token then use the existing method (built while adding Twitter) to store that token.

`Revoke` uses a new `ICustomMembership` method to revoke a given key (and I only just noticed I left off the conventional Async suffix, oops).

The AddKey view is a basic 1-field form (included more to show you I don&#8217;t have anything up my sleeves):

[SampleCosmosCore2App/Views/User/AddKey.cshtml][4]

```html
@model SampleCosmosCore2App.Models.User.NewKeyModel
@{
    ViewData["Title"] = "AddKey";
    Layout = "~/Views/Shared/Layout.cshtml";
}

<div class="box">
  <h2>AddKey</h2>
</div>

<div>
  <a asp-action="IndexAsync">Back to List</a>
</div>
```

And once it&#8217;s created, we then show it to you with &#8220;ShowKey&#8221;:

[SampleCosmosCore2App/Views/User/ShowKey.cshtml][5]

```html
@model SampleCosmosCore2App.Models.User.UserAuthenticationModel
@{
    ViewData["Title"] = "ShowKey";
    Layout = "~/Views/Shared/Layout.cshtml";
}
<div class="box">
  <h2>Your New API Key</h2>
  <p>
    You will need the API Key Id and API Key Secret to make an API call. Save your API Key Secret now, we won't show it again!
  </p>

  <div>
    Name: @Model.Name<br />
    API Key Id: @Model.Id<br />
    API Key Secret: @Model.Identity<br />
  </div>
</div>

<div>
  <a asp-action="IndexAsync">Back to List</a>
</div>
```

We&#8217;ll be requiring the pair of API Key Id and API Key Secret to authorize API calls later. The Id is the generated Cosmos DB `id` and the Key is the `ICustomMembership` generated value.

Displaying the index is a little more complex, as there are potentially several types of `LoginUserAuthentications` records available, so we include areas for specific keys and ignore the ones we don&#8217;t currently recognize:

[SampleCosmosCore2App/Views/User/Index.cshtml][6]

```html
@model SampleCosmosCore2App.Models.User.UserIndexModel

@{
    ViewData["Title"] = "Index";
    Layout = "~/Views/Shared/Layout.cshtml";
}

<h2>Your Account</h2>

<div asp-validation-summary="ModelOnly" class="text-danger"></div>

Username: @Model.User.Username <br/>
Registered: @Model.User.CreationTime  <br/>
Twitter Status: @if (Model.UserAuthentications.ContainsKey("Twitter"))
{
    var twitter = Model.UserAuthentications["Twitter"].Single();
    <text>@twitter.Name at @twitter.CreationTime</text>
}
else
{
    <text>Not Linked</text>
}

<h3>API Keys</h3>

<table>
  <tr>
    <th>Created</th>
    <th>Name</th>
    <th>API Key Id</th>
    <th>API Key Secret</th>
    <th></th>
  </tr>
  @if (Model.UserAuthentications.ContainsKey("APIKey"))
  {
      var keys = Model.UserAuthentications["APIKey"];
      foreach (var key in keys)
      {
      <tr>
        <td>@key.CreationTime</td>
        <td>@key.Name</td>
        <td>@key.Id</td>
        <td>@key.GetMaskedIdentity()</td>    
        <td>
          <a asp-action="Revoke" asp-route-id="@key.Id">Revoke</a>
        </td>
      </tr>
      }
  }
  <tr>
    <td colspan="5">
      <a asp-action="AddKey">Add a key</a>
    </td>
  </tr>
</table>
```

Again, this HTML won&#8217;t win any awards, it&#8217;s here to let us functionally build out what we need. Later in a real application we would replace this with a better form or front-end templates and JSON.

### Membership + Persistence

The screens above need some new capabilities in the membership logic (`CosmosDBMembership`) and queries to Cosmos DB (`UserPersistence`), so before we test we need to add those in.

The new methods are:

  * CosmosDBMembership.GenerateAPIKey
  * CosmosDBMembership.AddAuthenticationAsync
  * CosmosDBMembership.RevokeAuthenticationAsync
  * UserPersistence.GetUserAuthenticationAsync
  * UserPersistence.UpdateUserAuthenticationAsync

<div class="note-area">
  If you look at the <a href="https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/commit/1404bde5677301f385fe9189eb89ce710745685e" title="commit diff on github">commit details</a>, you&#8217;ll also see an un-committed fix to UserPersistence.GetUserBySessionIdAsync from the prior post (oops).
</div>

[SampleCosmosCore2App/Membership/CosmosDBMembership.cs][7]

```csharp
public string GenerateAPIKey(string userId)
{
    var key = new byte[32];
    using (var generator = RandomNumberGenerator.Create())
    {
        generator.GetBytes(key);
    }
    return Convert.ToBase64String(key);
}
```
`GenerateAPIKey` is starting with a simple random generator scheme to generate new keys.

```csharp
public async Task<AuthenticationDetails> AddAuthenticationAsync(string userId, string scheme, string identity, string identityName)
{
    var userAuth = new LoginUserAuthentication()
    {
        // ... warning below ...
    };

    userAuth = await _persistence.Users.CreateUserAuthenticationAsync(userAuth);

    return new AuthenticationDetails()
    {
        // ... warning below ...
    };
}
```
`AddAuthenticationAsync` is just a CRUD method to write the new data and return the results.

```csharp
public async Task<RevocationDetails> RevokeAuthenticationAsync(string userId, string identity)
{
    var userAuth = await _persistence.Users.GetUserAuthenticationAsync(identity);
    if (!userAuth.UserId.Equals(userId))
    {
        return RevocationDetails.GetFailed("Could not find specified API Key for your account");
    }

    if (userAuth.Scheme == Core.Users.AuthenticationScheme.RevokedAPIKey)
    {
        return RevocationDetails.GetFailed("APIKey has already been revoked");
    }

    if (userAuth.Scheme != Core.Users.AuthenticationScheme.APIKey)
    {
        return RevocationDetails.GetFailed("Could not find specified API Key for your account");
    }

    userAuth.Scheme = Core.Users.AuthenticationScheme.RevokedAPIKey;
    await _persistence.Users.UpdateUserAuthenticationAsync(userAuth);

    return RevocationDetails.GetSuccess();
}
```
`RevokeAuthenticationAsync` is a little more involved due to validation that the key you&#8217;re trying to revoke exists, is yours, isn&#8217;t already revoked, and so on, but then it updates the key type from `APIKey` to `RevokedAPIKey` and updates it in Cosmos DB.

<div class="warning-area">
  <p>
    So, ask me about class initializers&#8230;
  </p>
  
  <p>
    There are times that using class initializers is an <b>anti-pattern</b>, like the case above. If the properties are required for the object to be valid, put it in the constructor and don&#8217;t use class initializers for it. Everything else is fair game.
  </p>
  
  <p>
    I recognize that people really like them, but it misleads the next developer and makes it easy to introduce bugs when you&#8217;re adding or changing &#8220;required&#8221; fields.
  </p>
</div>

Now that we have the new `ICustomMembership` behavior, we can add the `UserPersistence` requirements.


```csharp
public enum AuthenticationScheme
{
    Twitter = 1,
    APIKey = 2,
    RevokedAPIKey = 3
}
```
First we add the two new Authentication types to the enum.

<div class="note-area">
  I really shouldn&#8217;t have named this enum &#8220;AuthenticationScheme&#8221;, since that has a specific meaning for ASP.Net Core 2 already, sorry about that. Future refactor opportunity.
</div>


```csharp
// ...

public async Task<LoginUserAuthentication> GetUserAuthenticationAsync(string id)
{
    var query = _client.CreateDocumentQuery<LoginUserAuthentication>(GetAuthenticationsCollectionUri(), new SqlQuerySpec()
    {
        QueryText = "SELECT * FROM UserAuthentications UA WHERE UA.id = @id",
        Parameters = new SqlParameterCollection()
        {
            new SqlParameter("@id", id)
        }
    });

    var result = await query.AsDocumentQuery()
                            .ExecuteNextAsync<LoginUserAuthentication>();
    return result.SingleOrDefault();
}

// ...

public async Task UpdateUserAuthenticationAsync(LoginUserAuthentication userAuth)
{
    await _client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(_databaseId, AUTHS_DOCUMENT_COLLECTION_ID, userAuth.Id), userAuth, new RequestOptions() { });
}

// ...
```
Once again, the Cosmos DB logic is pretty straightforward. I&#8217;m still getting used to the Query result handling, which feels a little convoluted, but everything else has been absurdly smooth because we&#8217;re just doing usual Document store logic (please take this JSON serialized object and give it back later) instead of having to map relational concepts to objects.

Now we can test out this process.

First we&#8217;ll add a new Key:

<div id="attachment_9203" style="width: 368px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_302.png" alt="Adding an API Key" width="358" height="254" class="size-full wp-image-9203" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_302.png 358w, /wp-content/uploads/2018/04/aspnetcore2cosmos_302-300x213.png 300w" sizes="(max-width: 358px) 100vw, 358px" />
  
  <p class="wp-caption-text">
    Adding an API Key
  </p>
</div>

And here it is:

<div id="attachment_9204" style="width: 435px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_303.png" alt="New API Key, Fancy UI :)" width="425" height="337" class="size-full wp-image-9204" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_303.png 425w, /wp-content/uploads/2018/04/aspnetcore2cosmos_303-300x238.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_303-378x300.png 378w" sizes="(max-width: 425px) 100vw, 425px" />
  
  <p class="wp-caption-text">
    New API Key, Fancy UI 🙂
  </p>
</div>

And it shows up in our fancy API Key list like so:

<div id="attachment_9205" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_304-600x92.png" alt="Viewing created API Keys" width="600" height="92" class="size-medium-width wp-image-9205" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_304-600x92.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_304-300x46.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_304-768x118.png 768w, /wp-content/uploads/2018/04/aspnetcore2cosmos_304-1024x157.png 1024w, /wp-content/uploads/2018/04/aspnetcore2cosmos_304.png 1141w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    Viewing created API Keys
  </p>
</div>

And we can revoke one of the keys easily:

<div id="attachment_9206" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_305-600x77.png" alt="Revoked API Key disappears, success!" width="600" height="77" class="size-medium-width wp-image-9206" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_305-600x77.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_305-300x38.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_305-768x98.png 768w, /wp-content/uploads/2018/04/aspnetcore2cosmos_305-1024x131.png 1024w, /wp-content/uploads/2018/04/aspnetcore2cosmos_305.png 1129w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    Revoked API Key disappears, success!
  </p>
</div>

And here it is in Cosmos DB Data Explorer, with the poorly named &#8220;Scheme&#8221; property indicating &#8220;3&#8221;, which is &#8220;RevokedAPIKey&#8221;:

<div id="attachment_9207" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_306-600x236.png" alt="Viewing Revoked API Key in Cosmos DB Data Explorer" width="600" height="236" class="size-medium-width wp-image-9207" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_306-600x236.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_306-300x118.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_306-768x302.png 768w, /wp-content/uploads/2018/04/aspnetcore2cosmos_306-763x300.png 763w, /wp-content/uploads/2018/04/aspnetcore2cosmos_306.png 885w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    Viewing Revoked API Key in Cosmos DB Data Explorer
  </p>
</div>

Now that our fictitious users can generate and revoke API keys and we&#8217;ve locked in those changes, it&#8217;s time to protect some API endpoints.

## Task 2: Protected API Endpoints

What we&#8217;re going to be doing for this case is adding in a new AuthenticationHandler middleware. 

**Other resources**

  * Adding a custom AuthenticationHandler: [Custom Authentication in ASP.NET Core 2.0][8]
  * More detailed: [Creating an authentication scheme in ASP.NET Core 2.0][9]

We&#8217;ll be adding a `CustomMembershipAPIAuthHandler` and an options object to hold the name of the `AuthenticationScheme` and `Realm` configured from startup.

The very short version of AuthenticationHandlers is that when the server receives a request, it will go through the whole list of available AuthenticationHandlers and ask each one if the request is: Pass, Fail, or NoResult:

  * NoResult: This request doesn&#8217;t have anything relevant to me, thank you drive through!
  * Fail: This request has relevant info for me and it&#8217;s wrong!
  * Success: This request has relevant info for me and it&#8217;s right, here&#8217;s a ClaimsPrincipal!

### Add the AuthenticationHandler

For this case, we&#8217;re going to expect an `Authorization` header on requests formatted as: `Scheme Id:Secret`, similar to Basic and Bearer authentication methods. The `Scheme` comes from the Options that will be set in Startup, the Id and Secret are the API Token values we created above.

[SampleCosmosCore2App/Membership/CustomMembershipAPIAuthHandler.cs][10] (HandleAuthenticateAsync)

```csharp
public class CustomMembershipAPIAuthHandler : AuthenticationHandler<CustomMembershipAPIOptions>
{
    // ...

    protected override async Task<AuthenticateResult> HandleAuthenticateAsync()
    {
        // Is this relevant to us?
        if (!Request.Headers.TryGetValue(HeaderNames.Authorization, out var authorization))
        {
            return AuthenticateResult.NoResult();
        }

        var actualAuthValue = authorization.FirstOrDefault(s => s.StartsWith(Options.Scheme, StringComparison.CurrentCultureIgnoreCase));
        if (actualAuthValue == null)
        {
            return AuthenticateResult.NoResult();
        }

        // Is it a good pair?
        var apiPair = actualAuthValue.Substring(Options.Scheme.Length + 1);
        var apiValues = apiPair.Split(':', 2);
        if (apiValues.Length != 2 || String.IsNullOrEmpty(apiValues[0]) || String.IsNullOrEmpty(apiValues[1]))
        {
            return AuthenticateResult.Fail($"Invalid authentication format, expected '{Options.Scheme} id:secret'");
        }

        var principal = await _membership.GetOneTimeLoginAsync("APIKey", apiValues[0], apiValues[1], Options.Scheme);
        if (principal == null)
        {
            return AuthenticateResult.Fail("Invalid authentication provided, access denied.");
        }

        var ticket = new AuthenticationTicket(principal, Options.Scheme);
        return AuthenticateResult.Success(ticket);
    }

    // ...
}
```
Notice we&#8217;ve added the concept of a &#8220;OneTimeLogin&#8221; to `ICustomMembership`. This will lead to a membership refactor to make it clearer which methods are relevant to &#8220;Interactive&#8221; logins and which are relevant to &#8220;OneTimeLogin&#8221; types like API request authentication.

On top of having logic to look at handle Authenticate requests, we also want to add logic to handle Challenge and Forbidden requests. In this case, we are going to add a nice `WWW-Authenticate` header before letting the base class send it back as a 401, which is consistent with how most APIs handle challenges. We&#8217;re also going to just let the base class handle the Forbidden cases as designed.

[SampleCosmosCore2App/Membership/CustomMembershipAPIAuthHandler.cs][10] (HandleAuthenticateAsync)

```csharp
public class CustomMembershipAPIAuthHandler : AuthenticationHandler<CustomMembershipAPIOptions>
{
    // ...

   protected override async Task HandleChallengeAsync(AuthenticationProperties properties)
    {
        Response.Headers["WWW-Authenticate"] = $"Basic realm=\"{Options.Realm}\", charset=\"UTF-8\"";
        await base.HandleChallengeAsync(properties);
    }

    protected override Task HandleForbiddenAsync(AuthenticationProperties properties)
    {
        return base.HandleForbiddenAsync(properties);
    }

    // ...
}
```
After an [addition of an extension method][11], we can register this in `Startup`.

[SampleCosmosCore2App/Startup.cs][12]

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    // ... add persistence, membership ...

    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        /* Custom Membership API Provider */
        .AddCustomMembershipAPIAuth("APIToken", "SampleCosmosCore2App")
        /* External Auth Providers */
        .AddCookie("ExternalCookie")
        .AddTwitter("Twitter", options => /* ... */ )
        /* 'Session' Cookie Provider */
        .AddCookie((options) => /* ... */);

    services.AddAuthorization(options => {
        options.AddPolicy("APIAccessOnly", policy =>
        {
            policy.AddAuthenticationSchemes("APIToken");
            policy.RequireAuthenticatedUser();
        });
    });
}
```
First we make a one line addition to the `AddCustomMembershipAPIAuth` Extension method, passing in a `Scheme` and `Realm`. Next we add in a new &#8220;APIAccessOnly&#8221; policy that we will use to enforce API Access authentication only for API endpoints, which we can do next.

[SampleCosmosCore2App/Controllers/ValuesController.cs][13]<pre lang="csharp""> \[Route("api/[controller]")\] \[Authorize(Policy = "APIAccessOnly")\] public class ValuesController : Controller { // GET api/values [HttpGet] public IEnumerable<string> Get() { return new string[] { "value1", "value2" }; } // ... } </pre> 

I&#8217;ve decorated the ValuesController with an `Authorize(Policy = ...)` attribute, but I could also have used `Authorize(AuthenticationScheme = ...)` and skipped the policy definition in the Startup file. However, I like the idea that all of my Authentication schemes are defined in Startup consistently, the policy for accessing API endpoints is defined in one place, and it looks more readable to me.

### Membership Changes

To support the middleware above, we need a `GetOneTimeLoginAsync` method in membership. This method will accept the authentication details, verify them, and return a ClaimsPrincipal (or null).

[SampleCosmosCore2App/Membership/CosmosDBMembership.cs][14]

```csharp
public async Task<ClaimsPrincipal> GetOneTimeLoginAsync(string scheme, string userAuthId, string identity, string authenticationScheme)
{
    var authScheme = StringToScheme(scheme);
    var userAuth = await _persistence.Users.GetUserAuthenticationAsync(userAuthId);

    // are the passed auth details valid?
    if (userAuth == null)
    {
        return null;
    }

    if (userAuth.Scheme != authScheme || !userAuth.Identity.Equals(identity, StringComparison.CurrentCultureIgnoreCase))
    {
        return null;
    }

    // is the user allowed to log in? We don't have addtl checks yet
    var user = await _persistence.Users.GetUserAsync(userAuth.UserId);

    // create a claims principal
    var claimsIdentity = new ClaimsIdentity(authenticationScheme);
    claimsIdentity.AddClaim(new Claim("userId", userAuth.UserId));
    claimsIdentity.AddClaim(new Claim("userAuthId", userAuth.Id));
    return new ClaimsPrincipal(claimsIdentity);
}
```
Currently this method accepts an authentication scheme for the &#8220;OneTimeLogin&#8221;, later this will be refactored so that the membership class supports a separate &#8220;InteractiveAuthenticationScheme&#8221; and &#8220;OneTimeLoginScheme&#8221; that are configured in Startup and they will consistently set either `userId` and `userAuthId` claims or `userId` and `sessionid` claims.

That&#8217;s all we needed! Let&#8217;s try it out with [postman][15].

Let&#8217;s start with the happy path, passing in good credentials:

<div id="attachment_9209" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_308-600x230.png" alt="Testing the successful path w/ good API Credentials" width="600" height="230" class="size-medium-width wp-image-9209" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_308-600x230.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_308-300x115.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_308-768x294.png 768w, /wp-content/uploads/2018/04/aspnetcore2cosmos_308-784x300.png 784w, /wp-content/uploads/2018/04/aspnetcore2cosmos_308.png 888w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    Testing the successful path w/ good API Credentials
  </p>
</div>

Then we should try a number of failure situations, like no credentials, bad credentials, and invalidly formatted credentials:

<div id="attachment_9208" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_307-600x331.png" alt="Verifying Bad Token reports 401" width="600" height="331" class="size-medium-width wp-image-9208" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_307-600x331.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_307-300x166.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_307-768x424.png 768w, /wp-content/uploads/2018/04/aspnetcore2cosmos_307-544x300.png 544w, /wp-content/uploads/2018/04/aspnetcore2cosmos_307.png 888w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    Verifying Bad Token reports 401
  </p>
</div>

Excellent! We now have working API Authentication!

## Wrapping up, next steps

Moving right along. We now have an ASP.net Core 2 website that can perform really basic CRUD logic against Cosmos DB. We&#8217;ve then layered standard username/password authentication on it, with storage in Cosmos DB as well. Then we extended this to support a second interactive login method (Twitter), opening the door to adding as many of those as we need. Then in this post, we&#8217;ve added API authorization for per-request authorization. So far, so good.

Next up, we&#8217;re going to take a break from Cosmos DB for a bit to do some general refactoring (which won&#8217;t be a blog post) and add in error handling logic. From there, we&#8217;ll shift focus to front-end tooling to ensure we have a good developer and production experience with CSS and JavaScript. Once we&#8217;re done there, we&#8217;ll be back to add in API Rate Limiting with Cosmos DB as the backing store. See you soon!

 [1]: https://azure.microsoft.com/en-us/services/cosmos-db/
 [2]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/tree/Post-%234 "Github: Source as of this post"
 [3]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%234/SampleCosmosCore2App/Controllers/UserController.cs
 [4]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%234/SampleCosmosCore2App/Views/User/AddKey.cshtml
 [5]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%234/SampleCosmosCore2App/Views/User/ShowKey.cshtml
 [6]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%234/SampleCosmosCore2App/Views/User/Index.cshtml
 [7]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%234/SampleCosmosCore2App/Membership/CosmosDBMembership.cs
 [8]: https://ignas.me/tech/custom-authentication-asp-net-core-20/
 [9]: https://joonasw.net/view/creating-auth-scheme-in-aspnet-core-2
 [10]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%234/SampleCosmosCore2App/Membership/CustomMembershipAPIAuthHandler.cs
 [11]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/commit/60a3a47a03cec8eda5a99cd82ec87b6601e2507c#diff-3e209446f0e03a762826159f3c03decf "CustomMembershipExtensions.cs diff at github"
 [12]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%234/SampleCosmosCore2App/Startup.cs
 [13]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%234/SampleCosmosCore2App/Controllers/ValuesController.cs
 [14]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%234/SampleCosmosCore2App/Membership/CosmosDBMembership.cs#L166
 [15]: https://www.getpostman.com/