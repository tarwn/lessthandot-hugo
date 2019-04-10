---
title: Using Razor as an Embedded Report Engine
author: Alex Ullrich
type: post
date: 2011-06-21T20:30:00+00:00
ID: 1145
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
When the Razor view engine for ASP.net MVC 3 was announced, I was not all that excited. It is nice, and a bit more compact, but didn't seem to offer anything that special, especially compared to some of the other view engines that are out there. Fast-forward a few months and our frustration with the SSRS-derived client reports available in .NET has come to a head. For our needs (basic 'fact sheet' type reports about entities) they are absurdly slow and support for them within Visual Studio is awful due to the lag between RDL and RDLC dialects. Coming from more of a web development background, I naturally gravitated towards something HTML based. I've had pretty good success with NHaml and Spark in ASP.net MVC before, so I looked at them, but found a need to reference System.Web along with both, which is a deal breaker (we're looking to use these in a WinForms client application).

#### Enter Razor

All this searching led me back to [Razor][1], the same view engine I'd said 'meh' to when it was first released. What immediately jumped out at me was a feature that I'd missed the first time around, namely that it can run _outside an asp.net app domain_ for testability. It can be invoked rather easily from code too:

```csharp
string template = "Hello @Model.Name! Welcome to Razor!";
string result = Razor.Parse(template, new { Name = "World" });
```

This certainly looked promising, so I set up a WinForms project to try it out. Sure enough, it worked against the client profile, and about as easily as I could have hoped. The key seems to be that it brings all of the web components it needs along for the ride in the included System.Web.Razor assembly. 

The main calls to the static “Razor” class that we're concerned with are:

```csharp
string Parse<T> (template, model);
void Compile (template, type, name);
string Run<T> (model, name);
```

These methods don't include everything available (such as the non-generic parse method used above) but everything we'll need. As I think the quoted example above shows, Razor.Parse compiles the supplied template and processes it using the model supplied. The generic version does the same thing, only with a strongly-typed model. Compile and Run are provided for more complex views, where it makes sense to compile once and run several times. As easy as this all is, we can't have static calls to Razor throughout our codebase. This post will mainly cover a bit of infrastructure I put around the Razor engine to make it a bit more user friendly.

#### Encapsulating the Engine

I wanted this code to be at least a bit testable, so I put an interface comprised of the three methods listed above around the static engine. Implementation is as you'd expect:

```csharp
using RazorEngine;

namespace RazorReport {
    public class Engine<T> : IEngine<T> {
        public void Compile (string preparedTemplate, string name) {
            Razor.Compile (preparedTemplate, typeof (T), name);
        }

        public string Run (T model, string name) {
            return Razor.Run<T> (model, name);
        }

        public string Parse (string template, T model) {
            return Razor.Parse<T> (template, model);
        }
    }
}
```
This makes it easy to confirm that the report building classes we'll implement are interacting with the engine as expected later, ie:

```csharp
[Test]
public void Recompiles_If_Stylesheet_Changed () {
    var mockery = new MockRepository ();
    var engine = mockery.StrictMock<IEngine<Example>> ();

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
        var builder = ReportBuilder<Example>.CreateWithEngineInstance (templateName, engine)
            .WithTemplate (template)
            .WithPrecompilation ();

        builder.BuildReport (model);

        builder = builder.WithCss (css);

        builder.BuildReport (model);
    }
}
```

At first I kind of lamented the fact that this stuff is offered through a static class (primarily for testability reasons) but kind of came around after a while. I'm sure having the engine static helps keep performance acceptable, and I'd rather be able to easily define a simple interface like this than be stuck with an interface that doesn't quite do what I'd like.

#### Finding Templates

The other bit of code we need before getting started is a means of finding templates, both those included as embedded resources and those on the file system:

```csharp
using System.IO;
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
}
```

I guess you could argue that this needs to be a non-static class with an interface for testability. And you'd be right. But I am not sure I'd be convinced that it's needed.

#### Building Reports

I think the idea of using a fluent interface for report builder configuration came up in a conversation with my usual [remote pairing partner][2]. The idea is that you would set up a report builder like this:

```csharp
var builder = ReportBuilder.Create<Foo>()
                  .WithTemplate("template")
                  .WithStylesheet("stylesheet")
                  .WithPrecompilation();
```

Or something along those lines. It seemed to work well enough so I rolled with it. The complete interface looks like this:

```csharp
using System.Reflection;

namespace RazorReport {
    public interface IReportBuilder<T> {
        IReportBuilder<T> WithTemplate (string template);
        IReportBuilder<T> WithCss (string css);
        IReportBuilder<T> WithTemplateFromFileSystem (string templatePath);
        IReportBuilder<T> WithCssFromFileSystem (string cssPath);
        IReportBuilder<T> WithTemplateFromResource (string resourceName, Assembly assembly);
        IReportBuilder<T> WithCssFromResource (string resourceName, Assembly assembly);
        IReportBuilder<T> WithPrecompilation ();

        string BuildReport (T model);
    }
}
```

The only thing added was some methods to get templates / stylesheets from the file system or embedded resources if needed. I thought about (and continue to think about) adding some kind of base template functionality, but I haven't quite settled on how it should work so I've left it out for now. There is definitely some interesting stuff in Razor that could help with this though.

There isn't time to cover everything, but calling BuildReport sends you through the following methods:

```csharp
public string BuildReport (T model) {
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
}
```

The needsCompilation flag gets flipped whenever the template or stylesheet gets changed, to ensure that any template modifications are picked up when using precompilation.

#### Enough Already, Where's the Code?

If you're interested in taking a look what I've got so far is over at [github][3]. It's still a work in progress, and may undergo significant change. I tagged the current state just so it will reflect what's discussed here, but the trunk may be more interesting. Feel free to offer suggestions that would make it more useful to you. They will always be considered (especially if submitted as pull requests 🙂 )

 [1]: http://razorengine.codeplex.com/
 [2]: /index.php/All/?disp=authdir&author=225
 [3]: https://github.com/AlexCuse/RazorReport/tree/initial-blogpost