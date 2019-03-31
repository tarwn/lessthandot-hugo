---
title: Custom Authentication in ASP.Net Core 1 (without Identity)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-09-08T11:59:48+00:00
url: /index.php/webdev/serverprogramming/aspnet/custom-authentication-in-asp-net-core-without-identity/
views:
  - 6558
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
tags:
  - asp.net core
  - authentication
  - bcrypt

---
Performing Authentication and Authorization has changed from ASP.Net to ASP.Net Core. Rather than relying on attributes, ASP.Net Core uses middleware similar to NancyFX and Rails. This is a short, step-by-step approach to implementing custom Authentication in ASP.Net Core without the overhead (and assumptions) of the new Identity model. 

The goal is to support basic necessities like a Login page with cookie-based authentication tickets that properly require HTTPS in production, but gracefully fail back to HTTP in local development.

## Cookie Middleware

ASP.Net Core has Cookie Middleware we can use out of the box: [Using Cookie Authentication without ASP.NET Core Identity][1]

<div style="border: 1px solid #EEBBBB; border-left: 16px solid #EEBBBB; padding: 1em">
  <div style="color: #883333; font-weight: bold">
    Warning: This only applies for ASP.Net Core 1.0, not 2.0.
  </div>
  
  <p>
    ASP.Net Core 2.0 has pivoted authentication in a different direction, ASP.Net Core 2 Custom Authentication is covered in a <a href="/index.php/webdev/serverprogramming/aspnet/custom-authentication-in-asp-net-core-2-w-cosmos-db/">newer post</a>. </div> 
    
    <p>
      This middleware provides support for a number of things we want: directing unauthenticated users to a LoginPath, redirecting access denied requests, authentication tickets with sliding expirations and encryption, and hooks to tie into the process for additional custom logic.
    </p>
    
    <p>
      Add the Cookie Authentication middleware in the Startup.Configure method:
    </p>
    
    <p>
      <b>APIProject/Startup.cs</b>
    </p>
    
    <pre> public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
	// ... other setup ...

	app.UseCookieAuthentication(new CookieAuthenticationOptions() {

		// A string to identify the authentication scheme we used, useful for filtering in Authorize attributes
		AuthenticationScheme = "NAME_OF_YOUR_COOKIE_SCHEME",

		// Location to send people too to log in
		LoginPath = new PathString("/Account/Login"),

		// Location to send people who fail to authenticate when required
		AccessDeniedPath = new PathString("/Account/Forbidden"),

		// Automatically check authentication on every call?
		AutomaticAuthenticate = true,

		// Challenge every request (enforce requirement to have this auth scheme if it got this far)
		AutomaticChallenge = true,

		// Expiration of the authentication ticket 
		ExpireTimeSpan = TimeSpan.FromMinutes(30),

		// Update the timeout each time a new request comes in
		SlidingExpiration = true,

		Events = new CookieAuthenticationEvents() {

			// Overwrite the attempt to redirect API calls to login page w/ 401 response instead
			OnRedirectToLogin = (ctx) =&gt; {
				// return true 401 to API calls, continue redirect to login for interactive
				if (ctx.Request.Path.StartsWithSegments("/api") && ctx.Response.StatusCode == 200)
				{
					ctx.Response.StatusCode = 401;
					return Task.FromResult&lt;object&gt;(null);
				}

				ctx.Response.Redirect(ctx.RedirectUri);
				return Task.FromResult&lt;object&gt;(null);
			},

		}
	});

	// ... other setup ...
}</pre>
    
    <p>
      <i>Note the OnRedirectToLogin logic, this causes the middleware to return basic 401 HTTP errors for calls through the &#8220;/api&#8221; path instead of redirects to the login page</i>
    </p>
    
    <h2>
      Account Controller
    </h2>
    
    <p>
      To support the endpoints above, and some others that may be useful, we need a simple Account controller:
    </p>
    
    <p>
      <b>APIProject/Controllers/InteractiveAccountController.cs</b>
    </p>
    
    <pre>[ApiExceptionFilter]
[Route("Account")]
public class InteractiveAccountController : Controller
{
	[HttpGet("Unauthorized")]
	public async Task&lt;string&gt; GetUnauthorizedAsync(string returnUrl)
	{
		return "You really should login first.";
	}

	[HttpGet("Forbidden")]
	public string GetForbidden()
	{
		return "Forbidden";
	}

	[HttpGet("TimedOut")]
	public string GetTimedOut()
	{
		return "TimedOut";
	}

	[HttpGet("Login")]
	public async Task&lt;string&gt; GetLogin()
	{
		var principal = new ClaimsPrincipal(new StandardUser());
		await HttpContext.Authentication.SignInAsync("NAME_OF_YOUR_COOKIE_SCHEME", principal);
		return "Success!";
	}

	[HttpGet("Logout")]
	public async Task&lt;string&gt; GetLogout()
	{
		await HttpContext.Authentication.SignOutAsync("NAME_OF_YOUR_COOKIE_SCHEME");
		// TODO replace with redirect to login once it exists
		return "Goodbye!";
	}
}</pre>
    
    <h2>
      Authorize
    </h2>
    
    <p>
      Once we have an authenticated user, we can add the standard <code>[Authorize]</code> attribute on any controllers or methods to ensure access will require authentication (or challenge if it&#8217;s not present). If we had multiple methods of authentication (interactive user, some API access, etc), we could further restrict these actions to just the cookie-based authenticated requests by specifying that authentication scheme in the attribute:<br /> <code>[Authorize(ActiveAuthenticationSchemes = "NAME_OF_YOUR_COOKIE_SCHEME")]</code>
    </p>
    
    <p>
      An easy way to verify things are in working order is to decorate another Controller/Action with Authorize and attempt to visit it. We should get redirected to <code>Unauthorized</code> on the Account controller above. If we visit <code>Login</code> first, then visit our sample Action we should be allowed in. Visit <code>Logout</code> and then our sample Action, we&#8217;re back at Unauthorized.
    </p>
    
    <h2>
      Ensure Endpoints have Authorization
    </h2>
    
    <p>
      Now that we have the middleware in place, the Account pages, and a sample page, we should add some protection to make sure we don&#8217;t leave any pages exposed accidentally. This step is optional, but it&#8217;s something I prefer to do for every application I work on.
    </p>
    
    <p>
      Using the sample code from <a href="/index.php/webdev/asp-net-ensure-your-actions-arent-missing-authorization-with-unit-tests/">a prior ASP.Net post</a>, we can write a quick unit test that will inspect all Actions and require them to either have explicit [Authorize] or [AllowAnonymous] attributes. This will protect us from accidentally pushing an unprotected endpoint.
    </p>
    
    <p>
      Unfortunately, APIExplorer in ASP.Net Core is even less documented so we have to rely on Reflection instead:
    </p>
    
    <p>
      <b>APIProject.Tests/SecurityTests.cs</b>
    </p>
    
    <pre>    [TestFixture]
    public class SecurityTests
    {
        Func&lt;object, bool&gt; IsMVCAttributeAuth = (o) =&gt; (o is AuthorizeAttribute || o is AllowAnonymousAttribute);

        [Test]
        public void AllMvcActionsHaveExplicitAuthorizationDefined_UsingStandardReflection()
        {
            var actionsMissingAuth = new List&lt;string&gt;();

            // 1
            var controllers = Assembly.GetAssembly(typeof(InteractiveAccountController)).GetLoadableTypes()
                          .Where(t =&gt; typeof(Controller).IsAssignableFrom(t));

            foreach (var controller in controllers)
            {
                // 2
                // if the controller has it, all it's actions are covered also
                if (controller.GetCustomAttributes().Any(a =&gt; IsMVCAttributeAuth(a)))
                    continue;

                var actions = controller.GetMethods(BindingFlags.Instance |
                                                    BindingFlags.DeclaredOnly |
                                                    BindingFlags.Public);
                foreach (var action in actions)
                {
                    // 3
                    // if the action has a defined authorization filter, it's covered
                    if (action.GetCustomAttributes().Any(a =&gt; IsMVCAttributeAuth(a)))
                        continue;

                    // no controller or action defined, add it to the list
                    actionsMissingAuth.Add(String.Format("{0}.{1}", controller.Name, action.Name));
                }
            }

            // 4
            if (actionsMissingAuth.Any())
            {
                Assert.Fail(String.Format("{0} action(s) do not have explicit authorization: {1}",
                              actionsMissingAuth.Count,
                              String.Join(",", actionsMissingAuth)));
            }
        }
    }


    public static class AssemblyExtensions
    {
        public static IEnumerable&lt;Type&gt; GetLoadableTypes(this Assembly assembly)
        {
            try
            {
                return assembly.GetTypes();
            }
            catch (ReflectionTypeLoadException e)
            {
                return e.Types.Where(t =&gt; t != null);
            }
        }
    }</pre>
    
    <h2>
      Add Real Login Pages and Logic
    </h2>
    
    <p>
      I&#8217;m not going to push a particular storage solution on you, so I have two interfaces I&#8217;ll be using as stand-ins for all of my &#8220;storage&#8221; needs:
    </p>
    
    <pre>public interface IUserStorage
{
    Task&lt;User&gt; GetUserByUsernameAsync(string username);
    Task&lt;User&gt; GetUserAsync(Guid userId);
}

public interface ISessionManager
{
    Task&lt;IPrincipal&gt; CreateSessionAsync(Guid userId);
    Task&lt;bool&gt; IsSessionValidAsync(IPrincipal principal);
    bool IsUserValidForSession(User user);
}</pre>
    
    <p>
      I am also using <a href="https://en.wikipedia.org/wiki/Bcrypt">BCrypt</a> for password hashing. BCrypt is a slow hashing function that makes brute force attempts computationally expensive. To verify a hashed BCrypt password, we have to pull it out of our store and ask BCrypt to verify it (instead of some methods that allow you to hash a new value and compare at the storage level).
    </p>
    
    <p>
      <b>APIProject/Controllers/InteractiveAccountController.cs</b>
    </p>
    
    <pre>...

[HttpGet("Login")]
public async Task&lt;IActionResult&gt; GetLoginAsync(string returnUrl)
{
    return View("Login", new LoginModel() { ReturnURL = returnUrl });
}

[HttpPost("Login")]
public async Task&lt;IActionResult&gt; PostLoginAsync(string username, string password, string returnUrl)
{
    // Did they provide all the details?
    if (string.IsNullOrEmpty(username) || String.IsNullOrEmpty(password))
        return View("Login", new LoginModel() { ReturnURL = returnUrl, UserName = username, Error = "Please provide both a username and password." });

    // Load the User and see if BCrypt can verify the entered password matches the stored hash
    var secrets = await _userStorage.GetUserByUsernameAsync(username);
    if (secrets == null || !BCrypt.Net.BCrypt.Verify(password, secrets.PasswordHash))
        return View("Login", new LoginModel() { ReturnURL = returnUrl, UserName = username, Error = "Sorry, could not find a user with that name and password." });
   
    // Verify the user is allowed to start a session (record is enabled, etc)
    if(!_sessionManager.IsUserValidForSession(user))
        return View("Login", new LoginModel() { ReturnURL = returnUrl, UserName = username, Error = "Sorry, your account is currently disabled." });

    // Create a principal for the session/user and put it in the cookie
    var principal = await _sessionManager.CreateSessionAsync(user);
    await HttpContext.Authentication.SignInAsync("NAME_OF_YOUR_COOKIE_SCHEME", principal);

    // Redirect the user
    if (String.IsNullOrWhiteSpace(returnUrl) || returnUrl.ToLower().StartsWith("/account/login"))
        returnUrl = "/";

    return Redirect(returnUrl);
}

[HttpGet("Logout")]
public async Task&lt;IActionResult&gt; GetLogout()
{
    await HttpContext.Authentication.SignOutAsync("NAME_OF_YOUR_COOKIE_SCHEME");
    return Redirect("/account/login");
}

...</pre>
    
    <p>
      Inside the <code>ISessionManager.CreateSessionAsync</code>, we create a session in storage with an id and then when the Principal is created we add that Id as a claim in the Principal. This is how we&#8217;ll look up the session on later requests to get the user information.
    </p>
    
    <p>
      The Cookie middleware will take care of built-in timeouts, but we also need to add a check in case someone disables the user (or whatever criteria is necessary in your system). Add this to the Startup.cs setup.
    </p>
    
    <p>
      <b>APIProject/Startup.cs</b>
    </p>
    
    <pre>app.UseCookieAuthentication(new CookieAuthenticationOptions() {

	...

	Events = new CookieAuthenticationEvents() {

		...

		// Use my ISessionManager to ensure session is still valid (user not disabled) or reject the principal if it is no longer valid
		OnValidatePrincipal = async (ctx) =&gt; {
			var sessionManager = ctx.HttpContext.RequestServices.GetRequiredService&lt;ISessionManager&gt;();
			var isSessionValid = await sessionManager.IsSessionValidAsync(ctx.Principal);
			if (!isSessionValid) {
				ctx.RejectPrincipal();
			}
		}
	}
});</pre>
    
    <p>
      <code>ISessionManager.IsSessionValidAsync</code> can now pull that Id claim we created above, get the associated user, and do any number of additional validations on the session length, user enabled state, and so on.
    </p>
    
    <h2>
      Accessing the User/Session
    </h2>
    
    <p>
      The last step is to the ability to access the User in other controllers. Controllers have a <code>User</code> property that grants access to the ClaimsPrincipal created during login. Assuming we had also stored a property like &#8220;UserId&#8221; in <code>ISessionManager.CreateSessionAsync</code> then we can access it like this:
    </p>
    
    <p>
      <b>APIProject/Controllers/AnyOldController.cs</b>
    </p>
    
    <pre>var userIdClaim = User.FindFirst("UserId");
var userId = Guid.Parse(userIdClaim.Value);</pre>
    
    <p>
      The second time I jumped through this particular hoop, I added a method to my SessionManager that accepted a Controller and looked the user up from the database, so I could do this instead:
    </p>
    
    <p>
      <b>APIProject/Controllers/AnyOldController.cs</b>
    </p>
    
    <pre>var user = await _sessionManager.GetCurrentUserAsync(this);</pre>
    
    <p>
      The Session Id mentioned in the prior section is handled the same way.
    </p>
    
    <h2>
      Final Checklist
    </h2>
    
    <p>
      If you&#8217;re using this to follow along and implement, here are the pieces you should have:
    </p>
    
    <ul>
      <li>
        Startup.cs: The CookieMiddleware configured with expiration, automatic challenge, 401 redirects for APIs, and re-validation of the session
      </li>
      <li>
        InteractiveAccountController: GET/POST for Login, GET for Logout, pages for Forbidden plus the Views for these pages
      </li>
      <li>
        UserStore and SessionManager implementations, with just a few functions needed and ability to extend the ClaimsPrincipal with more fields as you grow
      </li>
    </ul>
    
    <p>
      You can go as light or heavy as you need to with storage, relational or non-relational, as needed. You have BCrypt, one of the small set of recommended options from <a href="https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet">OWASP</a> (or upgrade to <a href="https://www.nuget.org/packages/Liphsoft.Crypto.Argon2">Argon2</a>). You have cookies with encrypted contents (including the expiration date) that automatically default to <code>secure</code> for HTTPS websites.
    </p>

 [1]: https://docs.microsoft.com/en-us/aspnet/core/security/authentication/cookie "Using Cookie Authentication without ASP.NET Core Identity"