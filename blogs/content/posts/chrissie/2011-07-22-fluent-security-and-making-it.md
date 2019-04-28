---
title: fluent security and making it work in VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-22T07:01:00+00:00
ID: 1263
excerpt: |
  Introduction
  
  I like most fluent frameworks, especially if they prevent the use of attributes. In essence there is nothing wrong with attributes but they kind of violate SRP. With fluent-security you can avoid that and keep the security code in separa&hellip;
url: /index.php/webdev/serverprogramming/aspnet/fluent-security-and-making-it/
views:
  - 3315
categories:
  - ASP.NET
tags:
  - 'c#'
  - fluent security
  - github
  - vb.net

---
## Introduction

I like most fluent frameworks, especially if they prevent the use of attributes. In essence there is nothing wrong with attributes but they kind of violate SRP. With [fluent-security][1] you can avoid that and keep the security code in separate files. 

I did not see any VB.Net examples so I will post some here. 

## The beginning

First I had to install MVC 3 ;-), I swear I&#8217;m ashamed.

Then I created an MVC 3 internet application.

After that I nugetted the fluent-security package. I found out it did not really work with VB.Net. I tried it with this piece of code.

```vbnet
SecurityConfigurator.Configure(Sub(configuration)
                                           configuration.GetAuthenticationStatusFrom(Function() HttpContext.Current.User.Identity.IsAuthenticated)
                                           configuration.For(Of HomeController)().Ignore()
                                           configuration.For(Of AccountController)().DenyAuthenticatedAccess()
                                           configuration.For(Of AccountController)(Function(x) x.LogOff()).DenyAnonymousAccess()
                                       End Sub)

        GlobalFilters.Filters.Add(New HandleSecurityAttribute(), 0)```
It bombed on the line.

```vbnet
configuration.For(Of AccountController)(Function(x) x.LogOff()).DenyAnonymousAccess()```
> System.InvalidCastException was unhandled by user code
    
> Message=Unable to cast object of type &#8216;System.Linq.Expressions.UnaryExpression&#8217; to type &#8216;System.Linq.Expressions.MethodCallExpression&#8217;.
    
> Source=FluentSecurity
    
> StackTrace:
         
> at FluentSecurity.Extensions.GetActionName(LambdaExpression actionExpression) in C:UserschristiaanSourcecodefluentsecurityFluentSecurityExtensions.cs:line 56
         
> at FluentSecurity.ConfigurationExpression.For\[TController\](Expression\`1 propertyExpression) in C:UserschristiaanSourcecodefluentsecurityFluentSecurityConfigurationExpression.cs:line 29
         
> at MvcApplication1.MvcApplication.\_Lambda$\__1(ConfigurationExpression configuration) in f:mydocumentsvisual studio 2010ProjectsMvcApplication1MvcApplication1Global.asax.vb:line 38
         
> at FluentSecurity.SecurityConfiguration..ctor(Action\`1 configurationExpression) in C:UserschristiaanSourcecodefluentsecurityFluentSecuritySecurityConfiguration.cs:line 14
         
> at FluentSecurity.SecurityConfigurator.Configure(Action\`1 configurationExpression) in C:UserschristiaanSourcecodefluentsecurityFluentSecuritySecurityConfigurator.cs:line 18
         
> at MvcApplication1.MvcApplication.Application_Start() in f:mydocumentsvisual studio 2010ProjectsMvcApplication1MvcApplication1Global.asax.vb:line 34
    
> InnerException:

The solution was to git the code and change it a little (it took me an hour to found out what to fix. And [this stackoverflow question][2] helped.

So I changed the line.

```csharp
///&lt;summary&gt;
		/// Gets the action name for the specified action expression
		///&lt;/summary&gt;
		public static string GetActionName(this LambdaExpression actionExpression)
		{
			return ((MethodCallExpression)actionExpression.Body).Method.Name;
		}```
to this.

```csharp
///&lt;summary&gt;
		/// Gets the action name for the specified action expression
		///&lt;/summary&gt;
		public static string GetActionName(this LambdaExpression actionExpression)
		{
            var expression = (MethodCallExpression)(actionExpression.Body is UnaryExpression ? ((UnaryExpression)actionExpression.Body).Operand : actionExpression.Body);
            return expression.Method.Name;
		}```
and I contacted the owner of the project to tell him about this. 

## Conclusion

What was supposed to be a 10 minute blogpost turned into a debugfest but only made possible by the power of opensource. I love it. I hope the fix gets added to the sourcecode now.

 [1]: http://www.fluentsecurity.net
 [2]: http://stackoverflow.com/questions/5438701/can-we-modify-this-code-to-return-the-name-of-a-method-instead-of-a-property