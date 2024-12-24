---
title: Fluent security with VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-22T11:00:00+00:00
ID: 1264
excerpt: |
  So after solving the little bug with regards to fluent security and VB.Net I now can try out fluent security.
  
  How it works
  
  So now that I have it working I needed to find out how it works. My first attempt was to make the About Func&hellip;
url: /index.php/webdev/serverprogramming/aspnet/fluent-security-with-vb-net/
views:
  - 10575
categories:
  - ASP.NET
tags:
  - 'c#'
  - fluent security
  - vb.net

---
## Introduction

So after solving the little bug with regards to [fluent security and VB.Net][1] I now can try out [fluent security][2].

## How it works

So now that I have it working I needed to find out how it works. My first attempt was to make the About Function in the Homecontroller only accessible for logged in users. Yes I know this seems illogical but who cares about that anyway.

For this I need to change the configuration code in my Global.asax to this.

```vbnet
SecurityConfigurator.Configure(Sub(configuration)
                                           configuration.GetAuthenticationStatusFrom(Function() HttpContext.Current.User.Identity.IsAuthenticated)
                                           configuration.For(Of HomeController)().Ignore()
                                           configuration.For(Of HomeController)(Function(x) x.About).DenyAnonymousAccess()

                                           configuration.For(Of AccountController)().DenyAuthenticatedAccess()
                                           configuration.For(Of AccountController)(Function(x) x.LogOff()).DenyAnonymousAccess()
                                       End Sub)

        GlobalFilters.Filters.Add(New HandleSecurityAttribute(), 0)```
Do you see the first line of the HomeController configuration that says ignore? That means that all functions in Homecontroller will have no security rules so everyone can use them. As with any good policy we can however override this and we do this in the next line, where we say that the About function must Deny Anonymous access. Which is what we want. In short the Index function of our standard HomeController will be accessible for everyone and About just for logged in users.

When our users see this and click the about button/link.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/fluentsecurity/fluentsecurity1.png?mtime=1311338511"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/fluentsecurity/fluentsecurity1.png?mtime=1311338511" width="204" height="132" /></a>
</div>

they will get this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/fluentsecurity/fluentsecurity2.png?mtime=1311338609"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/fluentsecurity/fluentsecurity2.png?mtime=1311338609" width="1031" height="365" /></a>
</div>

Neither are ideal situations but we now know what happens.

You get this exeptions that your users should never see because that is the default. Just go look at the code.

```csharp
using System.Web.Mvc;

namespace FluentSecurity
{
	public class ExceptionPolicyViolationHandler : IPolicyViolationHandler
	{
		public ActionResult Handle(PolicyViolationException exception)
		{
			throw exception;
		}
	}
}```
You can now also conclude that this behaviour is overridable. [Which it is][3].

We will however need structuremap or another IoC container. So I created a new handler with the correct name.

```vbnet
Namespace Security
    Public Class DenyAnonymousAccessPolicyViolationHandler
        Implements FluentSecurity.IPolicyViolationHandler


        Public Function Handle(ByVal exception As FluentSecurity.PolicyViolationException) As System.Web.Mvc.ActionResult Implements FluentSecurity.IPolicyViolationHandler.Handle
            Return New HttpUnauthorizedResult(exception.Message)
        End Function

    End Class
End Namespace```
And I change my global.asax to this after adding structuremap via nuget.

```vbnet
ObjectFactory.Configure(Sub(x) x.For(Of IPolicyViolationHandler).Add(Of Security.DenyAnonymousAccessPolicyViolationHandler)())

        SecurityConfigurator.Configure(Sub(configuration)
                                           configuration.GetAuthenticationStatusFrom(Function() HttpContext.Current.User.Identity.IsAuthenticated)
                                           configuration.ResolveServicesUsing(Function(type) ObjectFactory.GetAllInstances(type).Cast(Of Object)())
                                           configuration.For(Of HomeController)().Ignore()
                                           configuration.For(Of HomeController)(Function(x) x.About).DenyAnonymousAccess()

                                           configuration.For(Of AccountController)().DenyAuthenticatedAccess()
                                           configuration.For(Of AccountController)(Function(x) x.LogOff()).DenyAnonymousAccess()
                                       End Sub)

        GlobalFilters.Filters.Add(New HandleSecurityAttribute(), 0)```
And now I get the logon screen when I click on about.

## Conclusion

It works, what can I say. You can now easily add security to your asp.net MVC 3 application without having to add attributes all over the place.

 [1]: /index.php/WebDev/ServerProgramming/ASPNET/fluent-security-and-making-it
 [2]: http://www.fluentsecurity.net/
 [3]: https://github.com/kristofferahl/FluentSecurity/wiki/Policy-violation-handlers