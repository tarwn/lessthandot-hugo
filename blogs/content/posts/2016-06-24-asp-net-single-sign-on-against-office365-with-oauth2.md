---
title: ASP.Net – Single-sign on against Office365 with OAuth2
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-06-24T11:08:03+00:00
url: /index.php/webdev/serverprogramming/aspnet/asp-net-single-sign-on-against-office365-with-oauth2/
views:
  - 6889
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
tags:
  - Azure AD
  - OAuth2
  - Office365

---
Recently I was experimenting with Office 365 as a single-sign on source for an existing ASP.Net application. Unfortunately, most of the documentation I found focused on the use cases of having Visual Studio automatically add it as part of a new project, multiple versions of a very similar looking OWIN sample using built-in (black box) OpenId calls, and using Microsoft.Identity.Clients.ActiveDirectory in not terribly well explained example code to (I think) call the OAuth endpoints.

The examples did not fit well with what I already know about the mechanics of OAuth, so that made it harder to determine what they were doing and how I would wedge them into my codebase (and I can&#8217;t justify converting the entire codebase to OWIN to use those examples). Eventually, I decided that learning a one off solution to do something I already knew how to do may not be worth the investment, so I did a direct implementation instead. 

<div style="background-color: #eeeeee; margin: 1em; padding: 1em">
  Links referenced in this post:</p> 
  
  <ul>
    <li>
      <a href="https://msdn.microsoft.com/office/office365/howto/setup-development-environment#bk_CreateAzureSubscription">Office365 &#8211; Associate your Office 365 with an Azure AD Account</a></a> <li>
        <a href="https://msdn.microsoft.com/en-us/office/office365/howto/add-common-consent-manually">MSDN &#8211; Manually register your app with Azure AD</a>
      </li>
      <li>
        <a href="https://msdn.microsoft.com//en-us/library/azure/dn645542.aspx" title="MSDN - Authorization Code Grant Flow">MSDN &#8211; Authorization Code Grant Flow</a>
      </li>
      <li>
        <a href="https://www.owasp.org/index.php/Man-in-the-middle_attack" title="OWASP - Man-in-the-middle attack">OWASP &#8211; Man-in-the-middle attack</a>
      </li>
      <li>
        <a href="https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)" title="OWASP - Cross Site Request Forgery">OWASP &#8211; Cross Site Request Forgery</a>
      </li>
      <li>
        <a href="https://jwt.io/" title="JSON Web Token">JWT (JSON Web Token)</a>
      </li>
      <li>
        <a href="http://www.newtonsoft.com/json/help/html/CustomJsonConverter.htm" title="Newtonsoft - Custom JsonConverter">Newtonsoft JSON.Net &#8211; Custom JsonConverter</a>
      </li></ul> </div> 
      <h1>
        How OAuth2 Works
      </h1>
      
      <p>
        You can find a more details (and likely correct) description in the <a href="https://msdn.microsoft.com/en-us/library/azure/dn645542.aspx">MSDN Authorization Code Grant Flow</a> article.
      </p>
      
      <p>
        Loosely:
      </p>
      
      <ol style="margin-left: 2em;">
        <li>
          We redirect the user&#8217;s browser to the Office365 login portal
        </li>
        <li>
          They log in and the portal redirects them back to us, with a single use authorization code
        </li>
        <li>
          We use that authorization code to then directly ask the portal for access to a specific resource (API)
        </li>
        <li>
          The portal returns an access token, refresh token, and so on to grant us access to the resource
        </li>
        <li>
          We can now call the resource with our granted access.
        </li>
      </ol>
      
      <p>
        Behind the scenes, we previously defined an application in Azure AD that described the level of permissions we were going to use as the user, so when ask and receive access to the resource, it&#8217;s only those rights we defined for the application (and if we didn&#8217;t define any, we&#8217;ll receive an error). Because we are passing re-usable tokens around that are not encrypted or a public/private key, we need to be especially concerned about <A href="https://www.owasp.org/index.php/Man-in-the-middle_attack" title="OWASP - Man-in-the-middle attack">man-in-the-middle attacks</a> and ensure all calls happen over HTTPS.
      </p>
      
      <h1>
        Implementing a base Authorization
      </h1>
      
      <p>
        For my initial purposes, I want to be able to define a security group in Office 365 and then only authorize folks in that security group to access a part of my application. This will all be single-tenant authorizations because I am authorizing my users to use my application.
      </p>
      
      <h2>
        Get Azure AD Ready
      </h2>
      
      <p>
        First steps, you need to get Azure AD talking to your Office 365 accounts (and this assumes business accounts):
      </p>
      
      <p>
        Step 1: <a href="https://msdn.microsoft.com/office/office365/howto/setup-development-environment#bk_CreateAzureSubscription">Associate your Office 365 with an Azure AD Account</a>
      </p>
      
      <p>
        Step 2: <a href="https://msdn.microsoft.com/en-us/office/office365/howto/add-common-consent-manually">Register your application in Azure AD</a> (Follow the &#8220;Register your web server app with the Azure Management Portal&#8221; instructions)
      </p>
      
      <p>
        After following step 2, you should have a copied secret value.
      </p>
      
      <h2>
        ASP.Net
      </h2>
      
      <p>
        I&#8217;m going to focus down on just the barest of examples, a simple controller that sends you to Office 365 for authorization, then uses that returned code to ask for an access token (which will also return back information like the name of the person that has logged in).
      </p>
      
      <h3>
        Request an Authorization Code
      </h3>
      
      <p>
        Requesting an Authorization Code: <a href="https://msdn.microsoft.com//en-us/library/azure/dn645542.aspx#Anchor_2" title="MSDN - Authorization Code Grant Flow - Request an authorization code">MSDN</a>
      </p>
      
      <pre>public ActionResult Index()
{
    var state = Guid.NewGuid().ToString();
    Response.Cookies.Add(new HttpCookie("myo365auth", state) { 
                                Secure = true, 
                                HttpOnly = true, 
                                Expires = DateTime.UtcNow.AddMinutes(2) });

    string url = String.Format("https://login.microsoftonline.com/{0}/oauth2/authorize" +
                               "?client_id={1}&redirect_uri={2}&response_type=code&state={2}",
                                TENANT_ID,
                                CLIENT_ID,
                                CALLBACK_URL,
                                state);

    return Redirect(url);
}</pre>
      
      <p>
        State: The State variable is a random value I generate and pass along for the roundtrip to prevent <a href="https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)" title="OWASP - Cross Site Request Forgery">CSRF</a> attempts.
      </p>
      
      <p>
        URL: The URL is the OAuth2 authorize URL for Office365. This URL can also be found in the application configuration screens for your application in Azure AD, where it will include the TENANT_ID value directly in the string (but it&#8217;s still a good idea to extract it out).
      </p>
      
      <p>
        CLIENT_ID: The CLIENT_ID will also be found in your Azure AD application configuration.
      </p>
      
      <p>
        CALLBACK_URL: The CALLBACK_URL is a value you specify that will be the URL of the next Controller Action created and needs to be registered in the application in Azure AD. Office 365 will only redirect with a code to callback URLs that have been pre-registered in the application settings to protect against Open Redirector attacks.
      </p>
      
      <h3>
        Authorization Response
      </h3>
      
      <p>
        First things first, let&#8217;s see if we have an error and, if not, harvest the returned values out of the response.
      </p>
      
      <pre>public ActionResult Office365Callback()
{
    // error looks like: http://localhost:35426/Office365Callback?error=unsupported_response_type&error_description=AADSTS70005%3a+The+WS-Federation+sign-in+response+message+contains+an+unsupported+OAuth+parameter+value+in+the+encoded+wctx%3a+%27response_type%27%0d%0aTrace+ID......
    if (Request.QueryString["error"] != null)
    {
        throw new HttpException((int)HttpStatusCode.Forbidden, 
                                "Error while authenticating your Office365 account", 
                                new Exception("Office365 Error (1:auth): " + Request.QueryString["error_description"]));
    }

    // get the state cookie
    var rememberedState = Request.Cookies["myo365auth"];

    // good looks like: 
    var admin_consent = Request.QueryString["admin_consent"];
    var code = Request.QueryString["code"];
    var session_state = Request.QueryString["session_state"];
    var responseState = Request.QueryString["state"];

    if (responseState != rememberedState.Value)
    {
        throw new Exception("Detected a potential man-in-the-middle attack, specified state" +
                            "value changed during sign-on with Microsoft");
    }</pre>
      
      <p>
        The response fields are defined in the response section of the <a href="https://msdn.microsoft.com//en-us/library/azure/dn645542.aspx#Anchor_2" title="MSDN - Authorization Code Grant Flow - Request an authorization code">&#8220;Request an authorization code&#8221; MSDN article above</a>.
      </p>
      
      <p>
        I am reading all of the return values, but it is likely you will only need the <em>code</em> and <em>state</em> value for an authorization process.
      </p>
      
      <h3>
        User Information + Access Tokens
      </h3>
      
      <p>
        The user has been authorized, but we still don&#8217;t know who they are. Also, if we intend to ask about group memberships or similar AD queries, we will need access to Azure AD. So our next request is to ask for an Access Token that will grant us access, as it also returns the user information back as a <a href="https://jwt.io/" title="JSON Web Token">JWT (JSON Web Token)</a>.
      </p>
      
      <p>
        Use the Authorization Code to Request an Access Token: <a href="https://msdn.microsoft.com//en-us/library/azure/dn645542.aspx#Anchor_3" title="MSDN - Authorization Code Grant Flow - Use the Authorization Code to Request an Access Token">MSDN</a>
      </p>
      
      <pre>   // get a token and some user info
    var url = String.Format("https://login.microsoftonline.com/{0}/oauth2/token", TENANT_ID);
    var content = String.Format("client_id={0}&code={1}&grant_type=authorization_code&redirect_uri={2}" +
                                "&resource={3}&client_secret={4}",
                                CLIENT_ID,
                                code,
                                CALLBACK_URL,
                                "https://graph.windows.net",
                                HttpUtility.UrlEncode(CLIENT_SECRET));</pre>
      
      <p>
        Resource: The resource &#8220;https://graph.windows.net&#8221; is the Azure AD directory (which no documentation I read anywhere made particularly clear). I had a lot of confusion about this from early on because the documentation is just a little too generic for it&#8217;s own good.
      </p>
      
      <p>
        TENANT_ID, CLIENT_ID, and CALLBACK_URL match the values from the initial request above.
      </p>
      
      <p>
        CLIENT_SECRET is the secret that you copied during the application setup in the first section above.
      </p>
      
      <p>
        &#8220;code&#8221; is the code we extracted from the querystring above that we received in the Callback from Office365.
      </p>
      
      <p>
        Successfully POSTing this information to Office 365 results in a much larger object. In this example, I used JSON.Net to deserialize the JSON result into an object with matching properties (using the MSDN article above).
      </p>
      
      <pre>    string tokenResponse = "";
    try
    {
	var webRequest = HttpWebRequest.Create(url);
	webRequest.Method = "POST";
	webRequest.ContentLength = content.Length;

	using (var reqStream = webRequest.GetRequestStream())
	{
	    using (var writer = new StreamWriter(reqStream))
	    {
		writer.Write(content);
	    }
	}


	var webResponse = webRequest.GetResponse();
	using (var respStream = webResponse.GetResponseStream())
	{
	    using (var reader = new StreamReader(respStream))
	    {
		tokenResponse = reader.ReadToEnd();
	    }
	}

	var response = JsonConvert.DeserializeObject&lt;Office365TokenResponse&gt;(tokenResponse);
	response.ParseToken();

	return View(response);
    }
    catch (WebException wexc)
    {
	// ...
    }
}</pre>
      
      <p>
        There are two tricks necessary to parse the response object, the date fields and the JWT id_token. I imported the dates as longs and the id_token as a string.
      </p>
      
      <pre>public class Office365TokenResponse
{
	[JsonProperty("access_token")]
	public string AccessToken { get; set; }

	[JsonProperty("expires_in")]
	public long ExpiresIn { get; set; }

	[JsonProperty("expires_on")]
	public long ExpiresOn { get; set; }

	[JsonProperty("id_token")]
	public string IdToken { get; set; }

	[JsonProperty("refresh_token")]
	public string RefreshToken { get; set; }

	[JsonProperty("resource")]
	public string Resource { get; set; }

	[JsonProperty("scope")]
	public string Scope { get; set; }

	[JsonProperty("token_type")]
	public string TokenType { get; set; }

	// ...
}</pre>
      
      <p>
        Note: It is possible to parse the date values to DateTime, you just need to build a <a href="http://www.newtonsoft.com/json/help/html/CustomJsonConverter.htm" title="Newtonsoft - Custom JsonConverter">Custom JsonConverter</a>. The returned integer value is the number of seconds from 1970-01-01T0:0:0Z UTC, so the converter just needs to read that value and perform the date math to return a DateTime.
      </p>
      
      <h3>
        Parsing the JWT Token
      </h3>
      
      <p>
        To parse the JWT Token and extract the user&#8217;s information, we need a couple additional assemblies: System.IdentityModel.Tokens.Jwt and System.IdentityModel
      </p>
      
      <p>
        System.IdentityModel can be added via the Add Reference and is only required because types used in the Jwt assembly are defined in this one.
      </p>
      
      <p>
        System.IdentityModel.Tokens.Jwt has a nuget package: <code>Install-Package System.IdentityModel.Tokens.Jwt</code>
      </p>
      
      <p>
        As you are installing these extra assemblies, think about how much safer you feel that it required 2 assemblies to decrypt the name of the person you already have access and refresh tokens for instead of having the entire payload encrypted.
      </p>
      
      <pre>public void ParseToken()
{
    var handler = new JwtSecurityTokenHandler();
    var tokenContent = (JwtSecurityToken)handler.ReadToken(IdToken);
    Token = new Office365Token();
    foreach (var claim in tokenContent.Claims)
    {
        switch (claim.Type)
        {
            case "aud":
                Token.Audience = claim.Value;
                break;
            case "exp":
                Token.ExpirationTime = Convert.ToInt64(claim.Value);
                break;
            case "family_name":
                Token.FamilyName = claim.Value;
                break;
            case "given_name":
                Token.GivenName = claim.Value;
                break;
            case "iat":
                Token.IssuedAtTime = Convert.ToInt64(claim.Value);
                break;
            case "iss":
                Token.TokenIssuer = claim.Value;
                break;
            case "nbf":
                Token.NotBeforeTime = Convert.ToInt64(claim.Value);
                break;
            case "oid":
                Token.UserObjectIdentifier = claim.Value;
                break;
            case "sub":
                Token.TokenSubjectIdentifier = claim.Value;
                break;
            case "tid":
                Token.TenantIdentifier = claim.Value;
                break;
            case "unique_name":
                Token.UserUniqueIdentifier = claim.Value;
                break;
            case "upn":
                Token.UserPrincipalName = claim.Value;
                break;
            case "ver":
                Token.Version = claim.Value;
                break;

        }
    }
}</pre>
      
      <p>
        And there we go, the user is logged in and we know who they are.
      </p>
      
      <h2>
        Locking it down to a security group
      </h2>
      
      <p>
        To lock the application down to a security group, go back to your Azure AD application configuration and select the option to require unique users, then add the Security Group on the &#8220;Users and Groups&#8221; tab.
      </p>
      
      <h2>
        Errors
      </h2>
      
      <p>
        I ran into a long list of errors during this process, hopefully you wil have bypassed most or all of them if you followed this far. Most of those errors were not knowing that that resource for the second URL is expected to be &#8220;https://graph.windows.net&#8221; and not your own application.
      </p>