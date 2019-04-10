---
title: ASP.NET MVC 4 Razor Starter Guide – How to avoid punching your Laptop to Death
author: Tahir Khalid
type: post
date: 2013-12-02T01:16:00+00:00
ID: 2202
excerpt: |
  Hello and welcome to my seocond blog post for LessThanDot (I think) and in this post I will discuss how to setup a working ASP.NET MVC 4 (with Razor) project using the Empty Template.
  That's right, a blank project, I didn't want the templates doing the&hellip;
url: /index.php/webdev/uidevelopment/ajax/asp-net-mvc-4-razor-starter/
views:
  - 23872
rp4wp_auto_linked:
  - 1
categories:
  - AJAX
tags:
  - asp.net
  - 'c#'
  - mvc 4
  - razor
  - starter guide

---
Hello and welcome to my blog post for LessThanDot and in this post I will discuss how to setup a working ASP.NET MVC 4 (with Razor) project using the Empty Template.

That's right, a blank project, I didn't want the templates doing the work for me, I wanted to create a lean project that I had control over and I thought this would be easy enough but I was very wrong and tripped over a couple of times so hopefully this post will avoid any confusion and pain for newbies to the world of ASP.NET MVC (and avoid having to punch their laptop to death).

“Wait you punched your laptop to death?!”

<img src="http://www.vurso.co.uk/ltd/mvc-4-with-razor-starter-guide/punchedmylaptoptodeath.jpg" alt="Punched My Laptop To Death @ vurso.co.uk" width="350" height="85" />

Yes, metaphorically speaking as per my above tweet…I came close to actually doing it in real life, that's how frustrated I was but alas frustration can often lead to lots of fun learning which I did in between the random expletives and “WTF!” shouts throughout this process.

So lets start, the first thing you want to do is <span style="background-color: #ffcc00;">NOT USE THE TEMPLATES!</span> I know I know, sounds crazy but trust me you want to do it right and this is the best way my friends.

Start Visual Studio 2012 and then select the ASP.NET MVC 4 project type:

<img src="http://www.vurso.co.uk/ltd/mvc-4-with-razor-starter-guide/aspdotnetmvc4projtype.JPG" alt="ASP.NET MVC 4 Project Type" width="350" height="35" />

Give the project a meaningful name and then left-click the OK button to continue. You will then be presented with the following screen (Project Tempaltes), select the <span style="background-color: #99ccff;">Empty</span> Project type and left-click OK to continue.

<img src="http://www.vurso.co.uk/ltd/mvc-4-with-razor-starter-guide/projecttemplates.jpg" alt="ASP.NET MVC 4 Project Templates" width="280" height="165" />

If everything goes to plan Visual Studio will start generating your project folders and files and present you with the Empty Solution:

<img src="http://www.vurso.co.uk/ltd/mvc-4-with-razor-starter-guide/emptysolution.jpg" alt="ASP.NET MVC 4 Empty Solution" width="185" height="180" />

You will need to do a few things first before the project can actually work (i.e. if you want to use any kind of JavaScript/jquery/ajax and Web Content).  First we need to add some folders so right-click the project name (MvcApplication3 fro example) and select <span style="background-color: #99ccff;">Add > </span><span style="background-color: #99ccff;">New Folder</span> and label it <span style="background-color: #99ccff;">Scripts</span> (or alternatively if you have created another template based MVC site such as the Internet one just drag the Scripts folder from Windws Explorer into your Visual Studio 2012 IDE and drop it onto the project name which will cause Visual Studio 2012 to take a copy of the folder and create it locally with files below your project).

Expand the Views folder which currently only has the web.config file.  Right-click the Views folder and select <span style="background-color: #99ccff;">Add > </span><span style="background-color: #99ccff;">New Folder</span> labelling it <span style="background-color: #99ccff;">Home</span>.  Create another one and call it <span style="background-color: #99ccff;">Shared</span>, these two folders will contain the default views Index.cshtml and _Layout.cshtml (the shared view is like the MasterPage from the previous ASP.NET Form development days, it is used as a global view providing common page structure and other features across your views).

Now the important bit, you need to download the correct Web.Optimization package as its not included in your project and trying to create, build and compile any kind of web enabled page will cause no end of grief no less messages such as:

<span style="font-size: 14px; line-height: 18px; background-color: #eeeeee;"><span style="font-family: 'courier new', courier;">Compiler Error Message: CS0103: The name 'Scripts' does not exist in the current context</span></span>

You may also see initial errors such as:

<span style="font-size: 14px; line-height: 18px; background-color: #eeeeee;"><span style="font-family: 'courier new', courier;">$ is not defined</span></span>

All these can be avoided by running the following command from the nuget package manager command line.  To access the command line you need to display the command window:

<img src="http://www.vurso.co.uk/ltd/mvc-4-with-razor-starter-guide/nugetcommandline.jpg" alt="nuget Package Manager Command Line" width="434" height="185" />

Left-clicking the Command Line option will display a new command window at the bottom of your IDE which lets you enter nuget package manager specific commands.  Enter the following command to download the correct package for your project:

<img src="http://www.vurso.co.uk/ltd/mvc-4-with-razor-starter-guide/nugetpackageget.jpg" alt="Get nuget package from Visual Studio 2012" width="321" height="100" />

Once the package has been downloaded you will need to configure your <span style="background-color: #99ccff;">Web.Config</span> files, <span style="background-color: #99ccff;">Global.asax</span> file and add a new class in the <span style="background-color: #99ccff;">App_Start</span> folder so lets do this now.

Open up your root Web.config file and add the following line below the other namespaces (in the pages > namespaces section):

<pre style="font-family: Consolas; font-size: 13; color: #dfdfbf; background: #333333;"><span style="font-size: small;"><<span style="color: #efc986;">add</span> namespace="<span style="color: #dfaf8f;">System.Web.WebPages</span>" /></span><span style="font-family: Consolas;">
</span></pre>

You will also need to do the same for your other web.config file located in the root of the Views folder.

Next you need to modify your <span style="background-color: #99ccff;">Global.asax</span> file located in the root of the project and add the following line below the other statements in the <span style="background-color: #99ccff;">Application_Start()</span> method:

<pre style="font-family: Consolas; font-size: 13; color: #dfdfbf; background: #333333;"><span style="font-size: small;"><span style="color: #8acccf;">BundleConfig</span>.RegisterBundles(<span style="color: #8acccf;">BundleTable</span>.Bundles);</span><span style="font-family: Consolas;">
</span></pre>

Now you need to add a new class called BundleConfig.cs in the App_Start folder.  Modify the using block to look like this:

<pre style="color: #dfdfbf; background-color: #333333; background-position: initial initial; background-repeat: initial initial;"><span style="font-size: small;"><span style="color: #efc986;">using</span> System.Web;
<span style="color: #efc986;">using</span> System.Web.Optimization;</span></pre>

Modify the contents of the class block to look like this:

<pre style="color: #dfdfbf; background-color: #333333; background-position: initial initial; background-repeat: initial initial;"><span style="font-size: small;"><span style="color: #7a987a;">// For more information on Bundling, visit http://go.microsoft.com/fwlink/?LinkId=254725</span>
<span style="color: #efc986;">public</span> <span style="color: #efc986;">static</span> <span style="color: #efc986;">void</span> RegisterBundles(<span style="color: #8acccf;">BundleCollection</span> bundles)
{
    bundles.Add(<span style="color: #efc986;">new</span> <span style="color: #8acccf;">ScriptBundle</span>(<span style="color: #dfaf8f;">"~/bundles/jquery"</span>).Include(
                <span style="color: #dfaf8f;">"~/Scripts/jquery-{version}.js"</span>));
 
    bundles.Add(<span style="color: #efc986;">new</span> <span style="color: #8acccf;">ScriptBundle</span>(<span style="color: #dfaf8f;">"~/bundles/jqueryui"</span>).Include(
                <span style="color: #dfaf8f;">"~/Scripts/jquery-ui-{version}.js"</span>));
 
    bundles.Add(<span style="color: #efc986;">new</span> <span style="color: #8acccf;">ScriptBundle</span>(<span style="color: #dfaf8f;">"~/bundles/jqueryval"</span>).Include(
                <span style="color: #dfaf8f;">"~/Scripts/jquery.unobtrusive*"</span>,
                <span style="color: #dfaf8f;">"~/Scripts/jquery.validate*"</span>));
 
    <span style="color: #7a987a;">// Use the development version of Modernizr to develop with and learn from. Then, when you're</span>
    <span style="color: #7a987a;">// ready for production, use the build tool at http://modernizr.com to pick only the tests you need.</span>
    bundles.Add(<span style="color: #efc986;">new</span> <span style="color: #8acccf;">ScriptBundle</span>(<span style="color: #dfaf8f;">"~/bundles/modernizr"</span>).Include(
                <span style="color: #dfaf8f;">"~/Scripts/modernizr-*"</span>));
}</span></pre>

Now save the file and we need to create a controller file and two view pages.  Right-click the Controllers folder and left-click on <span style="background-color: #99ccff;">Add > Controller…</span> to display the New Controller dialog:

<img src="http://www.vurso.co.uk/ltd/mvc-4-with-razor-starter-guide/newcontroller.jpg" alt="New MVC 4 Controller" width="280" height="145" />

Give it the name <span style="background-color: #99ccff;">HomeController</span> and left-click on Add to continue.  Now we need to create the Index view the controller will work with.  Before we do that we need to create a view that will <a title="ASP.NET MVC 3: Layouts with Razor" href="http://weblogs.asp.net/scottgu/archive/2010/10/22/asp-net-mvc-3-layouts.aspx" target="_blank">“automagically” assign the same razor layout</a> to all your views so right-click the <span style="background-color: #99ccff;">Views</span> folder and left-click on <span style="background-color: #99ccff;">Add > View…</span> (VS 2012 is clever enough to recognise the context you're in, in this case the Views folder).  The Add View dialog box will appear:

<img src="http://www.vurso.co.uk/ltd/mvc-4-with-razor-starter-guide/addview.jpg" alt="ASP.NET MVC 4 Add View" width="200" height="252" />

Make sure you un-tick the “Use a layout or master page:” checkbox (as all your views will be using this <span style="background-color: #99ccff;">_ViewStart</span> file).

Now modify the file to look like this:

<pre style="color: #dfdfbf; background-color: #333333; background-position: initial initial; background-repeat: initial initial;"><span style="font-size: small;">@{
    Layout = <span style="color: #dfaf8f;">"~/Views/Shared/_Layout.cshtml"</span>;
}</span></pre>

Next we need to create the Index View so right-click the Home folder (the one you created earlier) and select <span style="background-color: #99ccff;">Add > View…</span> to display the Add View dialog box and label this view page <span style="background-color: #99ccff;">Index</span> and finally left-click Add to create it.

Modify the contents of the file to look like this:

<pre style="color: #dfdfbf; background-color: #333333; background-position: initial initial; background-repeat: initial initial;"><span style="font-size: small;">@{
    ViewBag.Title = <span style="color: #dfaf8f;">"My first LTD Mvc 4 Index Page"</span>;
}
@<span style="color: #efc986;">using</span> (Html.BeginForm())
{
     <<span style="color: #efc986;">h1</span>>Hello, World!</<span style="color: #efc986;">h1</span>>
}</span></pre>

Finally you need to create the shared layout view page, right-click the <span style="background-color: #99ccff;">Shared</span> folder and left-click <span style="background-color: #99ccff;">Add > View…</span> to display the Add View dialog box, label the view page as <span style="background-color: #99ccff;">_Layout</span> and ensure as with the previous pages the “Use a layout or master page” checkbox is un-ticked.

Modify the <span style="background-color: #99ccff;">_Layout.cshtml</span> view page to look like this:

<pre style="color: #dfdfbf; background-color: #333333; background-position: initial initial; background-repeat: initial initial;"><span style="font-size: small;"><!<span style="color: #efc986;">DOCTYPE</span> html>
<<span style="color: #efc986;">html</span> lang=<span style="color: #dfaf8f;">"en"</span>>
<<span style="color: #efc986;">head</span>>
    <<span style="color: #efc986;">meta</span> name=<span style="color: #dfaf8f;">"viewport"</span> content=<span style="color: #dfaf8f;">"width=device-width"</span> />
    <<span style="color: #efc986;">title</span>>@ViewBag.Title</<span style="color: #efc986;">title</span>>
</<span style="color: #efc986;">head</span>>
<<span style="color: #efc986;">body</span>>
    <<span style="color: #efc986;">div</span>>
        @RenderBody()
    </<span style="color: #efc986;">div</span>>
    @<span style="color: #8acccf;">Scripts</span>.Render(<span style="color: #dfaf8f;">"~/bundles/jquery"</span>)
    @RenderSection(<span style="color: #dfaf8f;">"scripts"</span>, required: <span style="color: #efc986;">false</span>)
</<span style="color: #efc986;">body</span>>
</<span style="color: #efc986;">html</span>></span></pre>

This should be enough for you to compile the project however it will still cause you pain unless you save your project and restart Visual Studio 2012, after which the references and page helpers should kick into life.

Build and Compile your project and then press <span style="background-color: #99ccff;">F5</span> or click on the <span style="background-color: #99ccff;">Debug</span> button to fire up your project, if all has gone well you should see a simple page with the words “Hello, World!” across the top left.

Well done, now go make a cup of tea and eat some digestives.