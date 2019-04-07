---
title: Adding Twitter Authentication to an ASP.Net Core 2 site w/ Cosmos DB
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2018-04-17T13:54:52+00:00
ID: 9170
url: /index.php/webdev/serverprogramming/aspnet/adding-twitter-authentication-to-an-asp-net-core-2-site-w-cosmos-db/
views:
  - 2715
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
tags:
  - ASP.Net Core 2
  - CosmosDB

---
I’m building a B2C website with Cosmos DB as the back-end store and starting with common elements like Authentication. In [my prior post][1], we connected the Cookie Middleware with custom membership logic and a standard username/password login method. In this one, we&#8217;ll be extending the system to also allow users to register and login via a third party provider (Twitter). 

<div id="attachment_9155" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_106-600x406.png" alt="3 Authentication Scenarios: User/Pass, Twitter, API Keys" width="600" height="406" class="size-medium-width wp-image-9155" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_106-600x406.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_106-300x203.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_106-443x300.png 443w, /wp-content/uploads/2018/04/aspnetcore2cosmos_106.png 748w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    3 Authentication Scenarios: User/Pass, Twitter, API Keys
  </p>
</div>

In this post I&#8217;ll also start exploring `User Authentications` as a separate document collection, rather than as additional fields on my User document. I&#8217;ve noticed in several past systems I&#8217;ve built API keys and authentication mechanisms as properties on Users, but recently started considering that, like my house keys, mixing properties of the user with authentication methods on a single &#8220;User&#8221; record has been making me uncomfortable.

<div id="attachment_9181" style="width: 236px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_202.png" alt="Defining People (Users) Separate from House Keys (User Authentication)" width="226" height="161" class="size-full wp-image-9181" />
  
  <p class="wp-caption-text">
    Defining People (Users) Separate from House Keys (User Authentication)
  </p>
</div>

Let&#8217;s find out if this works.

## Breaking it down

In the prior post, I outlined the authentication needs of this system and started breaking it into separate steps forward. Interactive logins would be able to take advantage of a standard username/password system or, alternatively, using a third-party system like Twitter. Once a user has an account, they can create and manage API Key/Secret pairs for API access (which is the next post).

So where are we so far? We have built an ASP.net Core 2 website and added Cookie Middleware that is associated with `LoginSession` classes stored in Cosmos DB. Users can register with a username, password, and email address and are stored as `LoginUser` documents in Cosmos DB. Business logic for this is bundled up in a concrete `CosmosDBMembership` class while the Persistence logic to work with Cosmos DB is encapsulated in a UserPersistence class, both of which are registered with the built-in ASP.Net Core 2 Dependency Injeciton services collection. 

Let&#8217;s extend the Membership logic to support Twitter:

<div id="attachment_9182" style="width: 408px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_201.png" alt="File Changes for Twitter Addition" width="398" height="816" class="size-full wp-image-9182" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_201.png 398w, /wp-content/uploads/2018/04/aspnetcore2cosmos_201-146x300.png 146w" sizes="(max-width: 398px) 100vw, 398px" />
  
  <p class="wp-caption-text">
    File Changes for Twitter Addition
  </p>
</div>

Here&#8217;s what we&#8217;ll be adding:

  * A Register via Twitter button, callback URL + form, and registration endpoint
  * A Login via Twitter button and callback URL
  * Some supporting methods in `CosmosDBMembership`
  * Some supporting methods in `Persistence.Users`
  * A new `DocumentCollection` in Cosmos DB for alternative User Authentications

This is a much smaller task then the initial work to put in custom authentication, but like that post I&#8217;ll present the changes file-by-file rather than in the order I developed them in.

The source code through this set of changes is available here: [github: Sample_ASPNetCore2AndCosmosDB, Post #3 Branch][2]

## Using the Twitter Middleware

The Twitter middleware uses a standard OAuth sign-in process, but does so in a way that takes care of all the details for us. 

When a `Challenge` is issued through the twitter middleware, it takes precautions like generating a one time roundtrip token and stores that token and additional parameters for us in a shortlived cookie. It then sends the user over to twitter with a callback URL and that roundtrip token which Twitter uses once the User has logged in and granted us access. Twitter calls back to the callback, which is handled 100% by the middleware, including verifying that roundtrip token came back around successfully. Information from Twitter is added to the Claims on the HttpContext, just as cookie informaiton is when we use the Cookie Middleware, so we can then use that information to perform our internal authorization logic.

<div id="attachment_9183" style="width: 595px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_203.png" alt="Twitter OAuth and Middleware Flow" width="585" height="624" class="size-full wp-image-9183" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_203.png 585w, /wp-content/uploads/2018/04/aspnetcore2cosmos_203-281x300.png 281w" sizes="(max-width: 585px) 100vw, 585px" />
  
  <p class="wp-caption-text">
    Twitter OAuth and Middleware Flow
  </p>
</div>

Once they&#8217;re logged in (or registered), we can generate a session and forward them to the URL they were originally headed to.

### Set Up Twitter

The first step is to set up credentials with Twitter for the application. The documented [MSDN setup documentation][3] has good steps for this, so we can follow through on the twitter side to get an app and access key setup.

<div id="attachment_9184" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_204-600x495.png" alt="Twitter App Setup" width="600" height="495" class="size-medium-width wp-image-9184" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_204-600x495.png 600w, /wp-content/uploads/2018/04/aspnetcore2cosmos_204-300x248.png 300w, /wp-content/uploads/2018/04/aspnetcore2cosmos_204-364x300.png 364w, /wp-content/uploads/2018/04/aspnetcore2cosmos_204.png 760w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    Twitter App Setup
  </p>
</div>

We&#8217;ll be storing the API Key and access token in our settings file for now, later we&#8217;ll take advantage of secrets management to store it in a way that doesn&#8217;t expose it to git.

### Add Middleware

The next part is to configure the middleware to use those App settings from Twitter. Once we receive the information back from Twitter, we will use that twitter Id to either continue user registration or attempt to log the user in, depending on where they started. Rather than build conditional logic into the Twitter options for the middleware, we can instead provide a final URL to redirect to for each endpoint:

  * `/account/login/twitter` should finally redirect to `/account/login/twitter/continue`
  * `/account/register/twitter` should finally redirect to `/account/login/register/continue`

When the redirect comes back from Twitter, it passed back account information that is parsed into claims on the HttpContext. 

<div class="note-area">
  During the Twitter authentication challenge, there is an internal cookie to carry state between the call to twitter and callback. Once the account information has been parsed, however, that information exists purely in the HttpContext for that request.
</div>

To bridge the gap between receiving the Twitter information and being able to use it on the final redirect, we can register a second Cookie middleware and tell twitter to use that middleware&#8217;s `AuthenticationScheme` for SignIn. This way, once the Twitter middleware has finished parsing the Claims, it will turn around and &#8220;SignIn&#8221; via the new Cookie Middleware. This writes the claims from Twitter to a new cookie for subsequent requests.

[SampleCosmosCore2App/Startup.cs][4]

```csharp
public void ConfigureServices(IServiceCollection services)
{
   // ... MVC, Membership

    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        /* External Auth Providers */
        .AddCookie("ExternalCookie")
        .AddTwitter("Twitter", options =>
        {
            options.SignInScheme = "ExternalCookie";

            options.ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"];
            options.ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"];
        })
        /* 'Session' Cookie Provider */
        .AddCookie((options) =>
        {
            // ...
        });
}
```
So the only three configurations we need here are the new Cookie provider with an explicit `AuthenticationScheme`, configuring Twitter to Sign In via that `AuthenticationScheme`, and then adding our Twitter Key and Secret via the appsettings.json file.

## Login Endpoints

To support the Login flow, we need a set of new endpoints and a button.

We&#8217;ll add a new `/account/login/twitter` endpoint with a final callback of `/account/login/twitter/continue` redirect URL to perform the actual Sign On, and we&#8217;ll add a button to the existing Login view:

<div id="attachment_9185" style="width: 361px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_205.png" alt="Addition of a &quot;Login with Twitter&quot; button" width="351" height="379" class="size-full wp-image-9185" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_205.png 351w, /wp-content/uploads/2018/04/aspnetcore2cosmos_205-278x300.png 278w" sizes="(max-width: 351px) 100vw, 351px" />
  
  <p class="wp-caption-text">
    Addition of a &#8220;Login with Twitter&#8221; button
  </p>
</div>

[SampleCosmosCore2App/Views/Account/Login.cshtml][5]

```html
...


<div class="box">
    <h2>Login</h2>

    <a asp-action="LoginWithTwitter" asp-route-returnUrl="@TempData["returnUrl"]" class="btn-white">Login with Twitter</a>

      ...
  
</div>
```

Then we add the endpoints to the Account controller to start the Twitter authentication process and capture the callback values at the end to start a new login session.



```csharp
[HttpGet("login/twitter")]
[AllowAnonymous]
public IActionResult LoginWithTwitter(string returnUrl = null)
{
    var props = new AuthenticationProperties()
    {
        RedirectUri = "account/login/twitter/continue?returnUrl=" + HttpUtility.UrlEncode(returnUrl)
    };
    return Challenge(props, "Twitter");
}

[HttpGet("login/twitter/continue")]
[AllowAnonymous]
public async Task<IActionResult> LoginWithTwitterContinueAsync(string returnUrl = null)
{
    // use twitter info to create a session
    var cookie = await HttpContext.AuthenticateAsync("ExternalCookie");
    var twitterId = cookie.Principal.FindFirst("urn:twitter:userid");

    var result = await _membership.LoginExternalAsync("Twitter", twitterId.Value);
    if (result.Failed)
    {
        ModelState.AddModelError("", "Twitter account not recognized, have you registered yet?");
        return View("Login");
    }

    await HttpContext.SignOutAsync("ExternalCookie");

    return LocalRedirect(returnUrl ?? _membership.Options.DefaultPathAfterLogin);
}
```
The Twitter information comes back in the &#8220;ExternalCookie&#8221; we registered in the Startup configuration. We&#8217;ll add a method to `CosmosDBMembership` to create a `LoginSession` just like we do with a username/password, except for a third-party identity instead. The last step is to clean up the &#8220;External Cookie&#8221; using it&#8217;s `SignOut` method.

<div class="note-area">
  Originally I added the <code>SignOut</code> for cleanliness, but it was later pointed out to me that this is extra overhead going across the wire on every Request/Response and, in some cases, can actually result in &#8220;Request Too Long&#8221; web server errors if you stack up too many cookies.
</div>

### Registration Endpoints

The registration flow is similar to the login flow, but we need one additional endpoint to serve up the registration form once we have the user&#8217;s twitter information for authentication.

<div id="attachment_9186" style="width: 357px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_206.png" alt="&quot;Continue with Twitter&quot; on Register Form" width="347" height="395" class="size-full wp-image-9186" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_206.png 347w, /wp-content/uploads/2018/04/aspnetcore2cosmos_206-264x300.png 264w" sizes="(max-width: 347px) 100vw, 347px" />
  
  <p class="wp-caption-text">
    &#8220;Continue with Twitter&#8221; on Register Form
  </p>
</div>

<div id="attachment_9187" style="width: 354px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/04/aspnetcore2cosmos_207.png" alt="Continuing Registration after logging in as @sqlishard" width="344" height="276" class="size-full wp-image-9187" srcset="/wp-content/uploads/2018/04/aspnetcore2cosmos_207.png 344w, /wp-content/uploads/2018/04/aspnetcore2cosmos_207-300x241.png 300w" sizes="(max-width: 344px) 100vw, 344px" />
  
  <p class="wp-caption-text">
    Continuing Registration after logging in as @sqlishard
  </p>
</div>

[SampleCosmosCore2App/Views/Account/Register.cshtml][6]

```html
...



<div class="box">
  <h2>
    Create Account
  </h2>
  
      
  
  <a asp-action="RegisterWithTwitter" class="btn-white">Continue with Twitter</a>
  
      ...
  
</div>
```

Like the Login form, we add a link to the Registration form.

[SampleCosmosCore2App/Views/Account/RegisterWithTwitterContinue.cshtml][7]

```html
@model SampleCosmosCore2App.Models.Account.RegisterWithTwitterModel

@{
    ViewData["Title"] = "Register";
    Layout = "~/Views/Shared/Layout.cshtml";
}



<div class="box">
  <h2>
    Create Account
  </h2>
  
      
  
</div>

```

And a view that collects a username (for display purposes) once they&#8217;ve authenticated with Twitter.

Then we can add the 3 endpoints to start the twitter `Challenge`, extract the details and feed them into the Registration form, then complete the Registration and log the user in for the first time:



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

[HttpGet("register/twitter/continue")]
[AllowAnonymous]
public async Task<IActionResult> RegisterWithTwitterContinueAsync()
{
    // use twitter info to set some sensible defaults
    var cookie = await HttpContext.AuthenticateAsync("ExternalCookie");
    var twitterId = cookie.Principal.FindFirst("urn:twitter:userid");
    var twitterUsername = cookie.Principal.FindFirst("urn:twitter:screenname");

    // verify the id is not already registered, short circuit to login screen
    if (await _membership.IsAlreadyRegisteredAsync("Twitter", twitterId.Value))
    {
        ModelState.AddModelError("", $"Welcome back! Your twitter account @{twitterUsername.Value} is already registered. Maybe login instead?");
        return View("Login");
    }

    var suggestedUsername = await FindUniqueSuggestionAsync(twitterUsername.Value);

    var model = new RegisterWithTwitterModel() {
        TwitterId = twitterId.Value,
        TwitterUsername = twitterUsername.Value,
        UserName = suggestedUsername
    };

    return View("RegisterWithTwitterContinue", model);
}

[HttpPost("register/twitter/continue")]
public async Task<IActionResult> RegisterWithTwitterContinueAsync(RegisterWithTwitterModel model)
{
    if (!ModelState.IsValid)
    {
        return View("RegisterWithTwitterContinue", model);
    }

    var result = await _membership.RegisterExternalAsync(model.UserName, model.Email, "Twitter", model.TwitterId);
    if (result.Failed)
    {
        ModelState.AddModelError("", result.ErrorMessage);
        return View("RegisterWithTwitterContinue", model);
    }

    await HttpContext.SignOutAsync("ExternalCookie");

    return LocalRedirect(_membership.Options.DefaultPathAfterLogin);
}
```
Once again, the last step in the process is to SignOut of the &#8220;ExternalCookie&#8221;, cleaning up after the stored Twitter information.

## Membership and Persistence

The changes for the membership object are fairly light. We need to be able to:

  * `Register(username, email, "Twitter", twitterId)`: Register a new account w/ Twitter authentication
  * `Login("Twitter", twitterId)`: Login with &#8220;Twitter&#8221; authentication

These will expose the new Persistence methods we need against Cosmos DB.



```csharp
public class CosmosDBMembership : ICustomMembership
{
    // ...

    public async Task<LoginResult> LoginExternalAsync(string scheme, string identity)
    {
        var authScheme = StringToScheme(scheme);
        var user = await _persistence.Users.GetUserByAuthenticationAsync(authScheme, identity);
        if (user == null)
        {
            return LoginResult.GetFailed();
        }

        await SignInAsync(user);

        return LoginResult.GetSuccess();
    }

    public async Task<RegisterResult> RegisterExternalAsync(string username, string email, string scheme, string identity)
    {
        var user = new LoginUser()
        {
            Username = username,
            Email = email
        };
        var userAuth = new LoginUserAuthentication()
        {
            Scheme = StringToScheme(scheme),
            Identity = identity
        };

        try
        {
            user = await _persistence.Users.CreateUserAsync(user);
        }
        catch (Exception)
        {
            //TODO reduce breadth of exception statement
            return RegisterResult.GetFailed("Username is already in use");
        }

        try
        {
            userAuth.UserId = user.Id;
            userAuth = await _persistence.Users.CreateUserAuthenticationAsync(userAuth);
        }
        catch (Exception)
        {
            // cleanup
            await _persistence.Users.DeleteUserAsync(user);
            throw;
        }

        await SignInAsync(user);

        return RegisterResult.GetSuccess();
    }

    public async Task<bool> IsAlreadyRegisteredAsync(string scheme, string identity)
    {
        return await _persistence.Users.IsIdentityRegisteredAsync(StringToScheme(scheme), identity);
    }

    // ...
}
```
The Login method is straightforward: Find a user that has a UserAuthentication of Twitter with the given twitter id. If we can&#8217;t find a user, we don&#8217;t know who they are.

Registration is a little trickier. We have to create and store both a `LoginUser` and `LoginUserAuthentication` object to successfully register the user and we need the generated `id` from the `LoginUser` that Cosmos DB generates from that save to populate the `UserId` property on the &#8220;LoginUserAuthentication</code> before saving. So we create both classes, populate the `UserId` in between saves, and if the second save fails for any reason we delete the initial `LoginUser` document.

I&#8217;m not sure that I like the &#8220;Twitter&#8221; string being passed around, so you can see I&#8217;ve switched to an enumerated value for Persistence and will likely refactor that back up the stack later.

Persistence needs some additional setup to create the new DocumentCollection, a method to retrieve a `LoginUser` document for a given Twitter identity, ability to Delete a user document, and methods to create and check given Twitter identities in the system.
  


```csharp
public class UserPersistence
{
    // ...

    public async Task EnsureSetupAsync()
    {
        var databaseUri = UriFactory.CreateDatabaseUri(_databaseId);

        // Collections
        // ...
        await _client.CreateDocumentCollectionIfNotExistsAsync(databaseUri, new DocumentCollection() { Id = AUTHS_DOCUMENT_COLLECTION_ID });
        // ...
    }

    #region Users

    // ...
        
    public async Task<LoginUser> GetUserByAuthenticationAsync(AuthenticationScheme authenticationScheme, string identity)
    {
        var query = _client.CreateDocumentQuery<LoginUserAuthentication>(GetAuthenticationsCollectionUri(), new SqlQuerySpec()
        {
            QueryText = "SELECT * FROM UserAuthentications UA WHERE UA.Scheme = @scheme AND UA.Identity = @identity",
            Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@scheme", authenticationScheme),
                new SqlParameter("@identity", identity)
            }
        });
        var results = await query.AsDocumentQuery()
                                    .ExecuteNextAsync<LoginUserAuthentication>();
        if (results.Count == 0)
        {
            return null;
        }
        else
        {
            return await GetUserAsync(results.First().UserId);
        }
    }

    // ...

    public async Task DeleteUserAsync(LoginUser user)
    {
        await _client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(_databaseId, USERS_DOCUMENT_COLLECTION_ID, user.Id));
    }

    #endregion

    #region Additional Authentication Methods

    public async Task<LoginUserAuthentication> CreateUserAuthenticationAsync(LoginUserAuthentication userAuth)
    {
        var result = await _client.CreateDocumentAsync(GetAuthenticationsCollectionUri(), userAuth, new RequestOptions() { });
        return JsonConvert.DeserializeObject<LoginUserAuthentication>(result.Resource.ToString());
    }


    public async Task<bool> IsIdentityRegisteredAsync(AuthenticationScheme authenticationScheme, string identity)
    {
        var query = _client.CreateDocumentQuery<int>(GetAuthenticationsCollectionUri(), new SqlQuerySpec()
        {
            QueryText = "SELECT VALUE COUNT(1) FROM UserAuthentications UA WHERE UA.Scheme = @scheme AND UA.Identity = @identity",
            Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@scheme", authenticationScheme),
                new SqlParameter("@identity", identity)
            }
        });
        var result = await query.AsDocumentQuery()
                                .ExecuteNextAsync<int>();
        return result.Single() == 1;
    }

    #endregion

    // ...
}
```
These all follow naturally from the methods created for prior posts. 

## Wrapping Up, Next Steps

Adding Twitter as an alternative interactive login method was pretty easy, once I figured out how the Middleware worked behind the scenes. Adding a second or third method would also be relatively easy, though I would likely want to use the Middleware options to remap specific claims like &#8220;urn:twitter:userid&#8221; and the matching value for LinkedIn or others to a common set of named claims to funnel redirects into a common set of callback URls.

Next up is the final Authentication post, adding in a per-request API key/secret method that relies on revocable API keys the user will manage as additional `LoginUserAuthentication` identities.

 [1]: /index.php/webdev/serverprogramming/aspnet/custom-authentication-in-asp-net-core-2-w-cosmos-db/ "Custom Authentication for ASP.Net Core 2 with Cosmos DB"
 [2]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/commits/Post-%233 "Sample_ASPNetCore2AndCosmosDB, Post #3 Branch"
 [3]: https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/twitter-logins?view=aspnetcore-2.1&tabs=aspnetcore2x "MSDN: ASP.Net Core 2 Twitter Setup"
 [4]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%233/SampleCosmosCore2App/Startup.cs
 [5]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%233/SampleCosmosCore2App/Views/Account/Login.cshtml
 [6]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%233/SampleCosmosCore2App/Views/Account/Register.cshtml
 [7]: https://github.com/tarwn/Sample_ASPNetCore2AndCosmosDB/blob/Post-%233/SampleCosmosCore2App/Views/Account/RegisterWithTwitterContinue.cshtml