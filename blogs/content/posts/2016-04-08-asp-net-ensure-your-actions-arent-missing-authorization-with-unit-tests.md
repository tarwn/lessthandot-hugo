---
title: ASP.Net – Ensure your Actions aren't missing Authorization with Unit Tests
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-04-08T13:44:47+00:00
ID: 4470
url: /index.php/webdev/asp-net-ensure-your-actions-arent-missing-authorization-with-unit-tests/
views:
  - 9092
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Web Developer
tags:
  - asp.net
  - reflection

---
Have you ever found yourself working on an ASP.net Action and noticed there isn't a single Authorization attribute in sight? Or gone to edit an endpoint in WebAPI, only to realize you took a coffee break at exactly the wrong time and forgot to come back and add the authorization attribute...a month ago? Or the time you found an MVC endpoint with WebAPI Authorization attributes on it? 

While relying on code reviews and regular reminders to the team can reduce occurrences of this, we're human and can only catch so much. Instead, it would be nice if a warning popped up before we ever pushed the code out: "You haven't defined authentication for the XYZ endpoint yet!". Luckily we have a CI environment (right???), so we can use Unit Tests to provide that warning and serve as a safety net to make sure we can't push unprotected endpoints out to production.

Code for this post: [Github: tarwn/Blog_KnockoutMVVMPatterns/.../AuthorizationSafetyNetTests.cs][1]

## Detecting Authentication Holes for ASP.Net MVC

The key ingredients of the test are going to be searching all of the Actions in Controllers and identifying whether they have a specific type of attribute at the Action, Controller, or Global level. A quick search could end you up on a StackOverflow post like these:

  * [Is there a way to Iterate all Controllers/Actions in an ASP.NET MVC3 Site?][2]
  * [Getting All Controllers and Actions names in C#][3]

The second actually has better examples which we can repurpose into a unit test:

```c#
Assembly asm = Assembly.GetExecutingAssembly();

asm.GetTypes()
    .Where(type=> typeof(Controller).IsAssignableFrom(type)) //filter controllers
    .SelectMany(type => type.GetMethods())
    .Where(method => method.IsPublic && ! method.IsDefined(typeof(NonActionAttribute)));
```
For the purposes of this example, I know all of my MVC actions will have either an Authorization attribute implementing IAuthorizationFilter or the AllowAnonymous Attribute, so I can write a test that loops through each controller and then each action capturing a list of Actions that do not have one of these:

```c#
// 0
Func<object, bool> IsMVCAttributeAuth = (o) => (o is System.Web.Mvc.IAuthorizationFilter || 
                                                o is System.Web.Mvc.AllowAnonymousAttribute);

[Test]
public void AllMvcActionsHaveExplicitAuthorizationDefined_UsingStandardReflection()
{
    var actionsMissingAuth = new List<string>();

    // 1
    var controllers = Assembly.GetAssembly(typeof(HomeController)).GetTypes()
			      .Where(t => typeof(IController).IsAssignableFrom(t));

    foreach (var controller in controllers)
    {
        // 2
        // if the controller has it, all it's actions are covered also
        if (controller.GetCustomAttributes().Any(a => IsMVCAttributeAuth(a)))
            continue;

        var actions = controller.GetMethods(BindingFlags.Instance | 
                                            BindingFlags.DeclaredOnly | 
                                            BindingFlags.Public);
        foreach (var action in actions)
        {
            // 3
            // if the action has a defined authorization filter, it's covered
            if (action.GetCustomAttributes().Any(a => IsMVCAttributeAuth(a)))
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
```
  * 0: For readability in the tests, I moved the attribute check to an external variable
  * 1: We're in a separate test assembly, so get the assembly for the HomeController and get all types that implement IController
  * 2: If any attributes on the controller match the attribute test, continue to the next controller (all of the actions are covered)
  * 3: If any attributes on each action match the test, skip to the next attribute
  * 4: After collecting a list of actions that are missing auth, we can now product a test failure message with the relevant information

In my sample code I have an Action called "AccidentalOpenEndpoint" in my HomeController to show the test in action:

```text
1 action(s) do not have explicit authorization: HomeController.AccidentalOpenEndpoint
   at NUnit.Framework.Assert.Fail(String message, Object[] args)
   at NUnit.Framework.Assert.Fail(String message)
   at CrossPlatformAppTests.AuthorizationSafetyNetTests.AllMvcActionsHaveExplicitAuthorizationDefined_UsingStandardReflection() in E:\programming\KnockoutPostBigApp\CrossPlatformApp\CrossPlatformAppTests\AuthorizationSafetyNetTests.cs:line 68
```
This is good, but we can do better by taking advantage of the built in [ReflectedControllerDescriptor][4] class.

```c#
[Test]
public void AllMvcActionsHaveExplicitAuthorizationDefined()
{
    // 1
    var controllers = Assembly.GetAssembly(typeof(HomeController)).GetTypes()
                              .Where(t => typeof(IController).IsAssignableFrom(t))
                              .Select(c => new ReflectedControllerDescriptor(c));

    // 2
    var actionsMissingAuth = controllers.SelectMany(c => c.GetCanonicalActions())
    // 3
                                        .Where(a => !a.GetCustomAttributes(true).Any(ca => IsMVCAttributeAuth(ca)) &&
                                                    !a.ControllerDescriptor.ControllerType.GetCustomAttributes(true)
                                                                                .Any(c => IsMVCAttributeAuth(c)));
    // 4
    if (actionsMissingAuth.Any())
    {
        var errorStrings = actionsMissingAuth.Select(a => String.Format("{0}.{1}", a.ControllerDescriptor.ControllerType.Name, a.ActionName));
        Assert.Fail(String.Format("{0} action(s) do not have explicit authorization: {1}",
                                  errorStrings.Count(),
                                  String.Join(",", errorStrings)));
    }
}
```
  * 1: Once again get all of the IController implementations in the assembly for HomeController, but this time wrap them in ReflectedControllerDescriptor's
  * 2: Use the ReflectedController's built in "GetCanonicalActions" method to get a collection of ActionDescriptors
  * 3: Get the attributes from the Action and it's Controller and run them through the IsMVCAttributeAuth test
  * 4: Once again, output a test failure message for Actions that didn't pass the test

This version is a lot more concise and has the additional advantage that it is using the same black magic internally to find Actions that MVC is, as opposed to use putting together a Flag enum for the fetMethods reflection call.

The output is the same:

```text
1 action(s) do not have explicit authorization: HomeController.AccidentalOpenEndpoint
   at NUnit.Framework.Assert.Fail(String message, Object[] args)
   at NUnit.Framework.Assert.Fail(String message)
   at CrossPlatformAppTests.AuthorizationSafetyNetTests.AllMvcActionsHaveExplicitAuthorizationDefined() in E:\programming\KnockoutPostBigApp\CrossPlatformApp\CrossPlatformAppTests\AuthorizationSafetyNetTests.cs:line 34
```
## Detecting Authentication Holes for WebAPI 2

While we could use a reflection method for WebAPI also, there is actually a much better option available. WebAPI includes an [APIExplorer][5] object that provides programmatic access to your WebAPI actions. It was built with documentation in mind, but also gives us exactly what we need to build an authentication verification test.

```c#
// 0
Func<object, bool> IsAPIAttributeAuth = (o) => (o is System.Web.Http.Filters.IAuthorizationFilter || 
                                                o is System.Web.Http.AllowAnonymousAttribute);

[Test]
public void AllApiActionsHaveExplicitAuthorizationDefined()
{
    // 1
    var httpConfiguration = new HttpConfiguration();
    WebApiConfig.Register(httpConfiguration);
    httpConfiguration.EnsureInitialized();
    var explorer = httpConfiguration.Services.GetApiExplorer();

    // 2
    var actionsMissingAuth = explorer.ApiDescriptions.Where(a => !a.ActionDescriptor.GetCustomAttributes

<object>
  (true)
                                                                                      .Any(o => IsAPIAttributeAuth(o))
                                                                && !a.ActionDescriptor.ControllerDescriptor.ControllerType.GetCustomAttributes(true)
                                                                                       .Any(o => IsAPIAttributeAuth(o)));
  
      // 3
      if (actionsMissingAuth.Any())
      {
          var errorStrings = actionsMissingAuth.Select(a => String.Format("{0}.{1}",
                                                                          a.ActionDescriptor.ControllerDescriptor.ControllerType.Name,                                                                                 
                                                                          a.ActionDescriptor.ActionName));
          Assert.Fail(String.Format("{0} action(s) do not have explicit authorization: {1}",
                                    errorStrings.Count(),
                                    String.Join(",", errorStrings)));
      }
  }
  
  </pre>
  
  
  <p>
    Similar to the MVC test, we are looking for any actions that don't have an attribute implementing IAuthorizationFilter or AllowAnonymous attribute.
  </p>
  
  
  <ul>
    <li>
      0: Once again, I extracted the attribute test out for readability
    </li>
    
    
    <li>
      1: Set up an HttpConfiguration object using the the same WebApiConfig we call during the global.asax and get an ApiExplorer instance
    </li>
    
    
    <li>
      2: Get all of the custom attributes for each action + action's controller and find the ones that don't have a match for the attribute test
    </li>
    
    
    <li>
      3: produce a test failure error mssage if any actions are uncovered
    </li>
    
  </ul>
  
  
  <p>
    If, like me, you use NCrunch locally, then you get this nice Red warning without any extra effort:
  </p>
  
  
  <a href="/wp-content/uploads/2016/03/ASPNetAuthTests.png"><img src="/wp-content/uploads/2016/03/ASPNetAuthTests.png" alt="NCrunch Test Output" class="size-full wp-image-4472" srcset="/wp-content/uploads/2016/03/ASPNetAuthTests.png 1014w, /wp-content/uploads/2016/03/ASPNetAuthTests-300x85.png 300w" sizes="(max-width: 1014px) 100vw, 1014px" /></a>
  
  
  <p>
    And there we go, a safety net against accidentally pushing an open endpoint that is built in to every single unit test run. 
  </p>

 [1]: https://github.com/tarwn/Blog_KnockoutMVVMPatterns/blob/master/CrossPlatformApp/CrossPlatformAppTests/AuthorizationSafetyNetTests.cs "Github: tarwn/Blog_KnockoutMVVMPatterns/.../AuthorizationSafetyNetTests.cs on github"
 [2]: http://stackoverflow.com/questions/5796909/is-there-a-way-to-iterate-all-controllers-actions-in-an-asp-net-mvc3-site "StackOverflow: Is there a way to Iterate all Controllers/Actions in an ASP.NET MVC3 Site?"
 [3]: http://stackoverflow.com/questions/21583278/getting-all-controllers-and-actions-names-in-c-sharp "StackOverflow: Getting All Controllers and Actions names in C#"
 [4]: https://msdn.microsoft.com/en-us/library/system.web.mvc.reflectedcontrollerdescriptor%28v=vs.118%29.aspx "MSDN: ReflectedControllerDescriptor"
 [5]: https://blogs.msdn.microsoft.com/yaohuang1/2012/05/13/asp-net-web-api-introducing-iapiexplorerapiexplorer/ "MSDN Blogs: ASP.NET Web API: Introducing IApiExplorer/ApiExplorer"