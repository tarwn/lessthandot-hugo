---
title: qunit and my nancy project.
author: Christiaan Baes (chrissie1)
type: post
date: 2015-02-27T13:02:09+00:00
ID: 3272
url: /index.php/uncategorized/qunit-and-my-nancy-project/
views:
  - 823
categories:
  - Uncategorized

---
I&#8217;ve been doing some webdevelopment lately (something I don&#8217;t do all that often) and found the need to test my javascript code. I find the need to test most of my code. 

I came to the conclusion that qunit was the first to show up in a Google search so it must be really good. Thank you Google. I asked on twitter, but people were less then useful on there.

So how do I get started with qunit?

Well first of all I downloaded qunit.js and qunit.css from the [qunit site][1].

Then I needed some code to test (truncate.js).

```javascript
String.prototype.trunc = String.prototype.trunc ||
        function (n) {
            return this.length > n ? this.substr(0, n) + '&hellip;' : this;
        };
```
And some tests (tests.js)

```javascript
test("truncate tests", function () {
    equal("test".trunc(10) , "test", "Truncate test to 10.");
    equal("test".trunc(1), "t&hellip;", "Truncate test to 1.");
    equal("test".trunc(2), "te&hellip;", "Truncate test to 2.");
    equal("test".trunc(3), "tes&hellip;", "Truncate test to 3.");
    equal("test".trunc(4), "test", "Truncate test to 4.");
    equal("".trunc(10), "", "Truncate empty string.");
});
```
And then a nancymodule to open the testpage that could run my tests.

```vbnet
Imports Nancy

Namespace Modules
    Public Class TestModule
        Inherits NancyModule

        Public Sub New()
            [Get]("/") = Function(parameters)
                                  Return View("Tests")
                              End Function
        End Sub
    End Class
End Namespace
```
Next up was the Tests.vbhtml file that would visualize the tests.

```html




    

<div id="qunit">
  
</div>
    

<div id="qunit-fixture">
  
</div>
    
    
    


```

And here is my folder structure in case it wasn&#8217;t clear.

[<img src="/wp-content/uploads/2015/02/filestructure-257x300.png" alt="filestructure" width="257" height="300" class="alignnone size-medium wp-image-3273" srcset="/wp-content/uploads/2015/02/filestructure-257x300.png 257w, /wp-content/uploads/2015/02/filestructure.png 319w" sizes="(max-width: 257px) 100vw, 257px" />][2]

This is what you will see when you run the project.

[<img src="/wp-content/uploads/2015/02/qunit-300x138.png" alt="qunit" width="300" height="138" class="alignnone size-medium wp-image-3274" srcset="/wp-content/uploads/2015/02/qunit-300x138.png 300w, /wp-content/uploads/2015/02/qunit.png 827w" sizes="(max-width: 300px) 100vw, 300px" />][3]

Which is fantastic.

And they even run in the resharper testrunner.

[<img src="/wp-content/uploads/2015/02/resharperqunit-206x300.png" alt="resharperqunit" width="206" height="300" class="alignnone size-medium wp-image-3275" srcset="/wp-content/uploads/2015/02/resharperqunit-206x300.png 206w, /wp-content/uploads/2015/02/resharperqunit.png 450w" sizes="(max-width: 206px) 100vw, 206px" />][4]

Nearly anyway.

You have to tell resharper where to get the truncate.js file from.

Which you do like this.

```javascript
/// &lt;reference path="truncate.js" />

test("truncate tests", function () {

    equal("test".trunc(10) , "test", "Truncate test to 10.");
    equal("test".trunc(1), "t&hellip;", "Truncate test to 1.");
    equal("test".trunc(2), "te&hellip;", "Truncate test to 2.");
    equal("test".trunc(3), "tes&hellip;", "Truncate test to 3.");
    equal("test".trunc(4), "test", "Truncate test to 4.");
    equal("".trunc(10), "", "Truncate empty string.");
});
```
[<img src="/wp-content/uploads/2015/02/resharperqunit2-228x300.png" alt="resharperqunit2" width="228" height="300" class="alignnone size-medium wp-image-3276" srcset="/wp-content/uploads/2015/02/resharperqunit2-228x300.png 228w, /wp-content/uploads/2015/02/resharperqunit2.png 450w" sizes="(max-width: 228px) 100vw, 228px" />][5]

Next up is to get it to work in teamcity.

Sorry for the few words.

 [1]: http://qunitjs.com/
 [2]: /wp-content/uploads/2015/02/filestructure.png
 [3]: /wp-content/uploads/2015/02/qunit.png
 [4]: /wp-content/uploads/2015/02/resharperqunit.png
 [5]: /wp-content/uploads/2015/02/resharperqunit2.png