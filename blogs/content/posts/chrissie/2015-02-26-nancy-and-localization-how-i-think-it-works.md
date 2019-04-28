---
title: 'Nancy and localization: how I think it works'
author: Christiaan Baes (chrissie1)
type: post
date: 2015-02-26T10:15:15+00:00
ID: 3265
url: /index.php/uncategorized/nancy-and-localization-how-i-think-it-works/
views:
  - 1293
categories:
  - Uncategorized

---
## Introduction

In Belgium localization of your website is very important. So I set out to test how well it worked for my use case. I need a website in Dutch, French and English. For most of my website dev I use [Nancy][1] with razor viewengine and the asp.net hosting.

As you can see the [wiki][2] is less then informative on this feature. I will change that and make it more informative. You can also take look at the [localization demo app][3].

## Complete view

If you want to translate a whole view then you can just add an extra file with the culture behind that.

If for example you have a razor view named Home.vbhtml which is in the Views folder. This will be the deafult view for all the cultures we have no specific view. 

```html
@Code
    Layout = Nothing
End Code






    

<div>
  <p>
    Text in English
  </p>
      
</div>


```

For which we have this Module.

```vbnet
Imports Nancy

Namespace Modules
    Public Class HomeModule
        Inherits NancyModule

        Public Sub New()
            [Get]("/") = Function(parameters)
                             Return View("Home")
                         End Function
        End Sub
    End Class
End Namespace
```
than you can just add another view Home-nl-BE.vbhtml (look how it uses a &#8211; between the viewname and the locale, remember that it&#8217;s important).

```html
@Code
    Layout = Nothing
End Code






    

<div>
  <p>
    Tekst in het Nederlands.
  </p>
      
</div>


```

Now how do we tell Nancy which locale we want to see? Simple we just change the culture of our Context.

```vbnet
Imports Nancy

Namespace Modules
    Public Class HomeModule
        Inherits NancyModule

        Public Sub New()
            [Get]("/") = Function(parameters)
                             Context.Culture = New Globalization.CultureInfo("nl-BE")
                             Return View("Home")
                         End Function
        End Sub
    End Class
End Namespace
```
That&#8217;s not very practical I hear you think. You&#8217;ll have to read my next blogpost to find out how I made it more practical.

And now stop interrupting me.

So that&#8217;s easy but sometimes you just don&#8217;t want to have copies of the same view in 25 different languages. Changing the view would become a nightmare if you did. Sometimes you just want to translate the important words.

## Just some words

For this I will create two .resx file (resources) in the Resources folder I just created.
  
One named Home.resx (which will be the default for all the cultures for which we have no specific .resx file) and Home.nl.BE.resx (see the dot between Home and nl, remember what we had there in the views?) 

Yes, yes I hate .resx files just as much as everybody else. They are horribly over-engineered pieces of crap. But they work in this case.

In the Strings Section I create a value with name MyString and a value you hink is appropriate.

We delete our view Home-nl-BE.vbhtml and change the Home.vbhtml to.

```html
@Code
    Layout = Nothing
End Code






    

<div>
  <p>
    @Text.Home.MyString
  </p>
      
</div>


```

The key in the above is that the @Text means you want some text from the resource file, the .Home part means you want it from the Home resource file (you can have as many as you like) and .MyString tells you which value you want. This was the part I didn&#8217;t get at first. 

## Conclusion

I&#8217;m brilliant and so is localization in Nancy.

 [1]: https://nancyfx.org
 [2]: https://github.com/NancyFx/Nancy/wiki/Localization
 [3]: https://github.com/NancyFx/Nancy/tree/master/src/Nancy.Demo.Razor.Localization