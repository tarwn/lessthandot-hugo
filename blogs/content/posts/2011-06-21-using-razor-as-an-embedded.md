---
title: Using Razor as an Embedded Report Engine
author: Alex Ullrich
type: post
date: 2011-06-21T20:30:00+00:00
excerpt: "When the Razor view engine for ASP.net MVC 3 was announced, I was not all that excited.  It is nice, and a bit more compact, but didn't seem to offer anything that special, especially compared to some of the other view engines that are out there.  Fast-&hellip;"
url: /index.php/desktopdev/mstech/csharp/using-razor-as-an-embedded/
views:
  - 9694
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
tags:
  - 'c#'
  - razor
  - reporting
  - winforms

---
When the Razor view engine for ASP.net MVC 3 was announced, I was not all that excited. It is nice, and a bit more compact, but didn&#8217;t seem to offer anything that special, especially compared to some of the other view engines that are out there. Fast-forward a few months and our frustration with the SSRS-derived client reports available in .NET has come to a head. For our needs (basic &#8216;fact sheet&#8217; type reports about entities) they are absurdly slow and support for them within Visual Studio is awful due to the lag between RDL and RDLC dialects. Coming from more of a web development background, I naturally gravitated towards something HTML based. I&#8217;ve had pretty good success with NHaml and Spark in ASP.net MVC before, so I looked at them, but found a need to reference System.Web along with both, which is a deal breaker (we&#8217;re looking to use these in a WinForms client application).

#### Enter Razor

All this searching led me back to [Razor][1], the same view engine I&#8217;d said &#8216;meh&#8217; to when it was first released. What immediately jumped out at me was a feature that I&#8217;d missed the first time around, namely that it can run _outside an asp.net app domain_ for testability. It can be invoked rather easily from code too:

<pre>string template = "Hello @Model.Name! Welcome to Razor!";
string result = Razor.Parse(template, new { Name = "World" });</pre>

This certainly looked promising, so I set up a WinForms project to try it out. Sure enough, it worked against the client profile, and about as easily as I could have hoped. The key seems to be that it brings all of the web components it needs along for the ride in the included System.Web.Razor assembly. 

The main calls to the static &#8220;Razor&#8221; class that we&#8217;re concerned with are:

<pre>string Parse&lt;T&gt; (template, model);
void Compile (template, type, name);
string Run&lt;T&gt; (model, name);</pre>

These methods don&#8217;t include everything available (such as the non-generic parse method used above) but everything we&#8217;ll need. As I think the quoted example above shows, Razor.Parse compiles the supplied template and processes it using the model supplied. The generic version does the same thing, only with a strongly-typed model. Compile and Run are provided for more complex views, where it makes sense to compile once and run several times. As easy as this all is, we can&#8217;t have static calls to Razor throughout our codebase. This post will mainly cover a bit of infrastructure I put around the Razor engine to make it a bit more user friendly.

#### Encapsulating the Engine

I wanted this code to be at least a bit testable, so I put an interface comprised of the three methods listed above around the static engine. Implementation is as you&#8217;d expect:

<pre>using RazorEngine;

namespace RazorReport {
    public class Engine&lt;T&gt; : IEngine&lt;T&gt; {
        public void Compile (string preparedTemplate, string name) {
            Razor.Compile (preparedTemplate, typeof (T), name);
        }

        public string Run (T model, string name) {
            return Razor.Run&lt;T&gt; (model, name);
        }

        public string Parse (string template, T model) {
            return Razor.Parse&lt;T&gt; (template, model);
        }
    }
}</pre>

This makes it easy to confirm that the report building classes we&#8217;ll implement are interacting with the engine as expected later, ie:

<pre>[Test]
public void Recompiles_If_Stylesheet_Changed () {
    var mockery = new MockRepository ();
    var engine = mockery.StrictMock&lt;IEngine&lt;Example&gt;&gt; ();

    var templateName = "recompileIfChange";
    var template = "template";
    var css = "STYLES";
    var model = new Example ();

    using (mockery.Record ()) {
        engine.Compile (template, templateName);
        LastCall.Repeat.Twice ();

        Expect.Call (engine.Run (model, templateName)).Repeat.Twice ().Return ("return");
    }

    using (mockery.Playback ()) {
        var builder = ReportBuilder&lt;Example&gt;.CreateWithEngineInstance (templateName, engine)
            .WithTemplate (template)
            .WithPrecompilation ();

        builder.BuildReport (model);

        builder = builder.WithCss (css);

        builder.BuildReport (model);
    }
}</pre>

At first I kind of lamented the fact that this stuff is offered through a static class (primarily for testability reasons) but kind of came around after a while. I&#8217;m sure having the engine static helps keep performance acceptable, and I&#8217;d rather be able to easily define a simple interface like this than be stuck with an interface that doesn&#8217;t quite do what I&#8217;d like.

#### Finding Templates

The other bit of code we need before getting started is a means of finding templates, both those included as embedded resources and those on the file system:

<pre>using System.IO;
using System.Reflection;

namespace RazorReport {
    class TemplateFinder {

        public static string GetTemplateFromResource (string resourceName, Assembly assembly) {
            using (var stream = assembly.GetManifestResourceStream (resourceName))
            using (TextReader reader = new StreamReader (stream)) {
                return reader.ReadToEnd ();
            }
        }

        public static string GetTemplateFromFileSystem (string templatePath) {
            return File.ReadAllText (templatePath);
        }
    }
}</pre>

I guess you could argue that this needs to be a non-static class with an interface for testability. And you&#8217;d be right. But I am not sure I&#8217;d be convinced that it&#8217;s needed.

#### Building Reports

I think the idea of using a fluent interface for report builder configuration came up in a conversation with my usual [remote pairing partner][2]. The idea is that you would set up a report builder like this:

<pre>var builder = ReportBuilder.Create&lt;Foo&gt;()
                  .WithTemplate("template")
                  .WithStylesheet("stylesheet")
                  .WithPrecompilation();</pre>

Or something along those lines. It seemed to work well enough so I rolled with it. The complete interface looks like this:

<pre>using System.Reflection;

namespace RazorReport {
    public interface IReportBuilder&lt;T&gt; {
        IReportBuilder&lt;T&gt; WithTemplate (string template);
        IReportBuilder&lt;T&gt; WithCss (string css);
        IReportBuilder&lt;T&gt; WithTemplateFromFileSystem (string templatePath);
        IReportBuilder&lt;T&gt; WithCssFromFileSystem (string cssPath);
        IReportBuilder&lt;T&gt; WithTemplateFromResource (string resourceName, Assembly assembly);
        IReportBuilder&lt;T&gt; WithCssFromResource (string resourceName, Assembly assembly);
        IReportBuilder&lt;T&gt; WithPrecompilation ();

        string BuildReport (T model);
    }
}</pre>

The only thing added was some methods to get templates / stylesheets from the file system or embedded resources if needed. I thought about (and continue to think about) adding some kind of base template functionality, but I haven&#8217;t quite settled on how it should work so I&#8217;ve left it out for now. There is definitely some interesting stuff in Razor that could help with this though.

There isn&#8217;t time to cover everything, but calling BuildReport sends you through the following methods:

<pre>public string BuildReport (T model) {
    return precompile ? CompiledReport (model) : Report (model);
}

string CompiledReport (T model) {
    if (needsCompilation) {
        engine.Compile (PrepareTemplate (), name);
        needsCompilation = false;
    }
    return engine.Run (model, name);
}

string Report (T model) {
    return engine.Parse (PrepareTemplate (), model);
}</pre>

The needsCompilation flag gets flipped whenever the template or stylesheet gets changed, to ensure that any template modifications are picked up when using precompilation.

#### Enough Already, Where&#8217;s the Code?

If you&#8217;re interested in taking a look what I&#8217;ve got so far is over at [github][3]. It&#8217;s still a work in progress, and may undergo significant change. I tagged the current state just so it will reflect what&#8217;s discussed here, but the trunk may be more interesting. Feel free to offer suggestions that would make it more useful to you. They will always be considered (especially if submitted as pull requests 🙂 )

 [1]: http://razorengine.codeplex.com/
 [2]: /index.php/All/?disp=authdir&author=225
 [3]: https://github.com/AlexCuse/RazorReport/tree/initial-blogpost