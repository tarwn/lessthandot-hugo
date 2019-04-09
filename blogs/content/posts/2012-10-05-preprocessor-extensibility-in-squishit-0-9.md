---
title: Preprocessor Extensibility in SquishIt 0.9
author: Alex Ullrich
type: post
date: 2012-10-05T12:38:00+00:00
ID: 1666
excerpt: "For the past couple years, .net developers have been embracing various content preprocessors as they become more accessible.  For the same couple of years, we've been trying to keep up.  The dotLess port of the popular .less CSS extension has been getti&hellip;"
url: /index.php/webdev/serverprogramming/preprocessor-extensibility-in-squishit-0-9/
views:
  - 21304
rp4wp_auto_linked:
  - 1
categories:
  - AJAX
  - ASP.NET
  - Javascript
  - Server Programming
  - XHTML and CSS
tags:
  - asp.net
  - squishit

---
For the past couple years, .net developers have been embracing various content preprocessors as they become more accessible. For the same couple of years, we've been trying to keep up. The [dotLess][1] port of the popular .less CSS extension has been getting better by leaps and bounds. It has become almost trivial to embed a javascript compiler in .net these days (thanks to projects like [Jurassic][2]), enabling us to support things like coffeescript. So we're doing the obvious thing &#8211; stripping preprocessor support from our core library.

There are some good reasons for this. Why force people to download things like Jurassic or dotLess if they don't have the need? The flipside of this is that we'd been deliberately avoiding adding support for SASS/SCSS because of concerns about linking to IronRuby &#8211; these concerns largely disappear when preprocessing becomes an opt-in behavior. Some of these libraries don't even work on Mono (I think .less might be the only one that works currently) so I feel extra bitter downloading code that won't run on my platform of choice. Finally, the growth in adoption has been so fast that frankly, we're unable to keep up.

So let's take a look at some of the original code (well not original as some of our refactorings did find their way to the 0.8.x branch).

```csharp
internal override string PreprocessForDebugging(string filename)
{
    if(filename.ToLower().EndsWith(".coffee"))
    {
        string js = ProcessCoffee(filename);
        filename += debugExtension;
        using(var fileWriter = fileWriterFactory.GetFileWriter(filename))
        {
            fileWriter.Write(js);
        }
    }
    return filename;
}
```
As you can see, the trigger for preprocessing is the extension. This is the desired behavior, but the way it was coded left it very brittle and made adding new preprocessors unwieldy. So we set out to find a way to break this code out of the core library. 

The approach that we used was plugin based &#8211; we defined an interface and exposed a mechanism to register implementations of this interface with the core library. Our original interface actually checked a file name to see if it needed preprocessing, so you could define any logic you wanted to determine whether to preprocess &#8211; we ended up eschewing this to go back to the extension-based decisions, for reasons that will be discussed later. The interface looks like this:

```csharp
public interface IPreprocessor
{
    bool ValidFor(string extension);
    IProcessResult Process(string filePath, string content);
    string[] Extensions { get; }
}
```
The “ValidFor” method does exactly what it says &#8211; check if the preprocessor should be used with the supplied extension. “Process” is where the actual preprocessing happens. The array of extensions is exposed publicly to be used in registering the preprocessor &#8211; this is because each type of content bundle has a list of allowed extensions that is used to filter what gets included when we add a directory full of files. Finally, the ProcessResult type includes a string representing preprocessed content and a list of any dependent files that were changed. This last part was added by [Simon Stevens][3] to enable [inclusion of .less imports as dependent files][4].

Preprocessors can be registered two ways &#8211; both statically and with a particular bundle instance. For the instance level configuration there is a method in the bundle's fluent API called “WithPreprocessor” that allows inclusion of a preprocessor with that bundle instance. Globally, we used the static “Bundle” class to allow preprocessor registration &#8211; methods exist there for registering script, style, and global preprocessors. If preprocessors of the same type are registered both statically and with a bundle instance, the instance-level preprocessor will be used.

Now, back to why we decided to make preprocessor selection based on extension rather than the complete file name. To understand, I guess all you have to do is read about the [Asset Pipeline][5] in Ruby on Rails, but I will attempt to summarize here. The beautiful thing about the pipeline approach is the ability to chain preprocessing steps. This allows you to use ERB's helper methods in your file **prior to** other preprocessing. For example, if you wanted to use ERB helpers in a coffeescript file you can name your file file.js.coffee.erb &#8211; when an asset has the .coffee and .erb extensions, both preprocessors will be applied. The order they are applied is driven by the reverse order of extensions, so *.coffee.erb would be preprocessed first by ERB and then by the coffeescript compiler. Our goal was to emulate this behavior in SquishIt, and without matching preprocessors to extensions rather than filenames we wouldn't have been able to.

Enabling this behavior is mostly a matter of finding preprocessors correctly. We find them like so:

```csharp
protected IPreprocessor[] FindPreprocessors(string file)
{
    return file.Split('.')
        .Skip(1)
        .Reverse()
        .Select(FindPreprocessor)
        .Where(p => p != null)
        .ToArray();
}
```

It's important to note here that “FindPreprocessor” uses the firstpreprocessor it finds for a given extension &#8211; so we need to take care if implementing preprocessors for common file extensions like “.js”. We can then use the preprocessors in the default order to process our content:

```csharp
protected string PreprocessFile(string file, IPreprocessor[] preprocessors)
{
    return directoryWrapper.ExecuteInDirectory(Path.GetDirectoryName(file),
        () => PreprocessContent(file, preprocessors, ReadFile(file)));
}

protected string PreprocessContent(string file, IPreprocessor[] preprocessors, string content)
{
    return preprocessors.NullSafeAny()
               ? preprocessors.Aggregate(content, (cntnt, pp) =>
                                                      {
                                                          var result = pp.Process(file, cntnt);
                                                          bundleState.DependentFiles.AddRange(result.Dependencies);
                                                          return result.Result;
                                                      })
               : content;
}
```

Despite the fact that we have totally broken everything users have come to depend on, we really do want to make the transition easier for people who were using .less or coffeescript with SquishIt. This is where the tremendous [WebActivator][6] library comes in. By including this library in our project, it allows us to define bits of code to run when the application starts up, like so:

```csharp
[assembly: WebActivator.PreApplicationStartMethod(typeof($rootnamespace$.App_Start.SquishItHogan), "Start")]

namespace $rootnamespace$.App_Start
{
    using SquishIt.Framework;
    using SquishIt.Hogan;

    public class SquishItHogan
    {
        public static void Start()
        {
            Bundle.RegisterScriptPreprocessor(new HoganPreprocessor());
        }
    }
}
```

Thanks to this snippet, you don't actually need to do anything to hook up global preprocessing &#8211; just reference the dll containing your preprocessor and WebActivator. This example is from the Hogan preprocessor, submitted by [Abdrashitov Vadim][7]. This pull request made me smile more than any I've seen in recent memory &#8211; a big part of the reason we moved to this model was to make it easier for people to define their own preprocessors and share them with the community. To have one submitted by a user before we even had a production-ready release was just so cool.

I think this covers most of the changes, at least at a cursory level. I hope to find the time to put together a bit of proper documentation in the next few months, but hopefully this will help in the meantime. I'd like to extend a huge thanks to everyone who reported bugs in our pre-release versions, and to [Rodrigo Dumont][8] who provided the spark to get started on this stuff late last year.

 [1]: http://www.dotlesscss.org/
 [2]: http://jurassic.codeplex.com/
 [3]: http://twitter.com/SimonPStevens
 [4]: https://github.com/jetheredge/SquishIt/pull/211
 [5]: http://guides.rubyonrails.org/asset_pipeline.html
 [6]: http://nuget.org/packages/WebActivator
 [7]: https://twitter.com/jincod
 [8]: https://twitter.com/rlsdumont