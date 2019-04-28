---
title: Refactoring to a pattern. Theming in WPF.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-08-04T16:03:50+00:00
ID: 861
url: /index.php/desktopdev/mstech/refactoring-to-a-pattern-theming-in-wpf/
views:
  - 4788
categories:
  - Microsoft Technologies
tags:
  - refactoring
  - theming
  - wpf

---
## Introduction

You might have noticed that I am switching to WPF from winforms. I&#8217;m doing this gradually, but DI/IoC is making this very painless. For the moment I have both in the same app. I have not seen any problems yet. One of the advantages of WPF is that you can do theming, this lets you can change the look of your application in a single line of code. I guess you can do something similar in winforms but it needs a lot more work. First I will show you how easy it is to theme your application and then how much easier it is if you refactor your code to a pattern. &#8220;What pattern?&#8221;, I hear you ask. Well I don&#8217;t really care for the moment.

## Changing themes

First thing to do is download some [themes from codeplex][1]. And then we copy these themes into our project under a themes folder.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/WPF/theme1.png" alt="" title="" width="211" height="218" />
</div>

Now I just add 3 buttons to my window, one for each theme.

And this is the code that goes with it to change the themes.

```vbnet
Private Sub btnBlue_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnBlue.Click
        Dim skin = New ResourceDictionary()
        skin.Source = New Uri("ThemesBureauBlueTheme.xaml", UriKind.Relative)
        Application.Current.Resources.MergedDictionaries.Add(skin)
    End Sub

    Private Sub btnBlack_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnBlack.Click
        Dim skin = New ResourceDictionary()
        skin.Source = New Uri("ThemesBureauBlackTheme.xaml", UriKind.Relative)
        Application.Current.Resources.MergedDictionaries.Add(skin)
    End Sub

    Private Sub btnSilver_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnSilver.Click
        Dim skin = New ResourceDictionary()
        skin.Source = New Uri("ThemesExpressionLightTheme.xaml", UriKind.Relative)
        Application.Current.Resources.MergedDictionaries.Add(skin)
    End Sub```
And this is the result.

On startup, no theme.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/WPF/theme2.png" alt="" title="" width="354" height="261" />
</div>

Blue theme.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/WPF/theme3.png" alt="" title="" width="354" height="261" />
</div>

Black theme.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/WPF/theme4.png" alt="" title="" width="354" height="261" />
</div>

And silver theme.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/WPF/theme5.png" alt="" title="" width="354" height="261" />
</div>

That was simple.

## Refactoring

As we can see from the code above we are already repeating ourselves. So we solve that first by Adding a method.

```vbnet
Private Sub btnBlue_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnBlue.Click
        ChangeTheme("BureauBlue")
    End Sub

    Private Sub btnBlack_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnBlack.Click
        ChangeTheme("BureauBlack")
    End Sub

    Private Sub btnSilver_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnSilver.Click
        ChangeTheme("ExpressionLight")
    End Sub

    Private Sub ChangeTheme(ByVal Theme As String)
        Dim skin = New ResourceDictionary()
        skin.Source = New Uri("Themes" & Theme & "Theme.xaml", UriKind.Relative)
        Application.Current.Resources.MergedDictionaries.Add(skin)
    End Sub```
Much better, Now I can add a theme and I just have to add one line of code. 

```vbnet
Private Sub ChangeTheme(ByVal Theme As String)
  Dim _FileName = "Themes" & Theme & "Theme.xaml"
  Dim skin = New ResourceDictionary()
  skin.Source = New Uri(_FileName, UriKind.Relative)
  Application.Current.Resources.MergedDictionaries.Add(skin)
End Sub```
Now it is time to think about writing tests, lets consider the above as a spike to find out how changing themes works. 

I can think of three test I want from the start.

  * Can change theme to Blue
  * Can change theme to Black
  * Can change theme to Silver

This could be one such test.

```vbnet
&lt;Test()&gt; _
Public Sub CanThemeChangeToBlue()
  Dim _ThemeChanger = New ThemeChanger()
  _ThemeChanger.ChangeTheme("Blue")()
  Assert.AreEqual(0, (From e In Application.Current.Resources.MergedDictionaries Where e.Source.AbsolutePath.Contains("ThemesBureauBlueTheme.xaml")).Count)
End Sub
```
This of course will not even compile. But with a bit of reshaper magic we make it run.

So now I need a ThemeChanger Class for easy testing. ThemeChanger Class also has a method called Changetheme.

So ThemeChanger will look like this after we copy paste our code from the window into it.

```vbnet
Public Class ThemeChanger
  Private Sub ChangeTheme(ByVal Theme As String)
    Dim _FileName = "pack://application:,,,/WpfApplication1;component/themes/" & Theme & "/Theme.xaml"
    Dim skin = New ResourceDictionary()
    skin.Source = New Uri(_FileName, UriKind.RelativeOrAbsolute)
    Application.Current.Resources.MergedDictionaries.Add(skin)
  End Sub
End Class```
Now the tests above will go green.

And the window will now look like this.

```vbnet
Class MainWindow

    Private _ThemeChanger As ThemeChanger

    Public Sub New()

        ' This call is required by the designer.
        InitializeComponent()

        ' Add any initialization after the InitializeComponent() call.
        _ThemeChanger = New ThemeChanger
    End Sub

    Private Sub btnBlue_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnBlue.Click
        _ThemeChanger.ChangeTheme("BureauBlue")
    End Sub

    Private Sub btnBlack_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnBlack.Click
        _ThemeChanger.ChangeTheme("BureauBlack")
    End Sub

    Private Sub btnSilver_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnSilver.Click
        _ThemeChanger.ChangeTheme("ExpressionLight")
    End Sub

End Class
```
Ok, all that will work. But I still see a few problem areas. For one I instantiate my themechanger (a dependency in my constructor and for two I have a magic string (magic strings are a path to the dark side.)

Lets solve the magic string first.

I like to use generics for that.

So I need an interface that all my classes will share and they need a method to change the theme.

```vbnet
Public Interface ITheme
  Sub ChangeTheme()
End Interface```
And an implementation would look like this.

```vbnet
Public Class BlackTheme
        Implements ITheme

        Public Sub ChangeTheme() Implements ITheme.ChangeTheme
            Dim _FileName = "pack://application:,,,/WpfApplication1;component/themes/BureauBlack/Theme.xaml"
            Dim skin = New ResourceDictionary()
            skin.Source = New Uri(_FileName, UriKind.RelativeOrAbsolute)
            Application.Current.Resources.MergedDictionaries.Add(skin)
            RaiseEvent ThemeChanged()
        End Sub
    End Class```
But I would have all my classes now have all those lines of code they don&#8217;t really need. Let&#8217;s make that better.

```vbnet
Public MustInherit Class BaseTheme
        Implements ITheme

        Protected Sub ChangeTheme(ByVal Theme As String)
            Dim _FileName = "pack://application:,,,/WpfApplication1;component/themes/" & Theme & "/Theme.xaml"
            Dim skin = New ResourceDictionary()
            skin.Source = New Uri(_FileName, UriKind.RelativeOrAbsolute)
            Application.Current.Resources.MergedDictionaries.Add(skin)
            RaiseEvent ThemeChanged()
        End Sub

        Public MustOverride Sub ChangeTheme() Implements themes.ITheme.ChangeTheme
End Class```
And now the implementation of BlackTheme will look like this.

```vbnet
Public Class BlackTheme
        Inherits BaseTheme

        Public Overloads Overrides Sub ChangeTheme()
            ChangeTheme("BureauBlack")
        End Sub
    End Class```
And the implementation of Themechanger will look like this. And that is easily testable too.

```vbnet
Public Class ThemeChanger
        
  Public Sub ChangeTheme(Of Theme As {New, ITheme})()
     Dim _Theme as new Theme
     _Theme.ChangeTheme()
  End Sub

End Class```
And A little change to our test.

```vbnet
&lt;Test()&gt; _
Public Sub CanThemeChangeToBlue()
  Dim _ThemeChanger = New ThemeChanger()
  _ThemeChanger.ChangeTheme(Of BlueTheme)()
  Assert.AreEqual(0, (From e In Application.Current.Resources.MergedDictionaries Where e.Source.AbsolutePath.Contains("ThemesBureauBlueTheme.xaml")).Count)
End Sub
```
Our window is now looking like this.

```vbnet
Class MainWindow

    Private _ThemeChanger As ThemeChanger

    Public Sub New()

        ' This call is required by the designer.
        InitializeComponent()

        ' Add any initialization after the InitializeComponent() call.
        _ThemeChanger = New ThemeChanger
    End Sub

    Private Sub btnBlue_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnBlue.Click
        _ThemeChanger.ChangeTheme(Of BlueTheme)()
    End Sub

    Private Sub btnBlack_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnBlack.Click
        _ThemeChanger.ChangeTheme(Of BlackTheme)()
    End Sub

    Private Sub btnSilver_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnSilver.Click
        _ThemeChanger.ChangeTheme(Of SilverTheme)()
    End Sub

End Class```
Now I still have that dependecy on Themechanger that gets initialized in the constructor of the window. I don&#8217;t like this and here I have to make a design decision. Do I make Themechanger static/shared or do I make an interface and inject it. I prefer not to make things static since they are not so easy to test but in this case I make an exception.
  
Since it won&#8217;t make a difference to testing.

So in the end we end up with ThemeChanger being this.

```vbnet
Public Class ThemeChanger
        
  Public Shared Sub ChangeTheme(Of Theme As {New, ITheme})()
     Dim _Theme as new Theme
     Theme.ChangeTheme()
  End Sub

End Class```
And the test will now become.

```vbnet
&lt;Test()&gt; _
Public Sub CanThemeChangeToBlue()
  ThemeChanger.ChangeTheme(Of BlueTheme)()
  Assert.AreEqual(0, (From e In Application.Current.Resources.MergedDictionaries Where e.Source.AbsolutePath.Contains("ThemesBureauBlueTheme.xaml")).Count)
End Sub
```
And the window will be.

```vbnet
Class MainWindow

    Private Sub btnBlue_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnBlue.Click
        ThemeChanger.ChangeTheme(Of BlueTheme)()
    End Sub

    Private Sub btnBlack_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnBlack.Click
        ThemeChanger.ChangeTheme(Of BlackTheme)()
    End Sub

    Private Sub btnSilver_Click(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs) Handles btnSilver.Click
        ThemeChanger.ChangeTheme(Of SilverTheme)()
    End Sub

End Class```
## Advantages

I can now add more themes and I only have to add the xaml, an ITheme implementation and a button somewhere.
  
I can have those button on all my windows without having to write much code.
  
If a theme is deleted it will be easy to find out where I have references to it since the compiler will tell me when an ITheme implementation no longer exists.
  
I can easily test most things (uhm that is not really true because WPF doesn&#8217;t let you test things easily.

## Problems

I had some major problems getting my test to work. The code worked just fine when run but the NUnit testrunner gave me an Exception. Namely this one.
   
To begin with I had these uri&#8217;s.

```vbnet
New Uri("Themes/BureauBlue/Theme.xaml", UriKind.RelativeOrAbsolute)```
That gave me this error.

> System.NotSupportedException : The URI prefix is not recognized.
  
> at System.Net.WebRequest.Create(Uri requestUri, Boolean useUriBase)
  
> at System.Net.WebRequest.Create(Uri requestUri)
  
> at MS.Internal.WpfWebRequestHelper.CreateRequest(Uri uri)
  
> at System.IO.Packaging.PackWebRequest.GetRequest(Boolean allowPseudoRequest)
  
> at System.IO.Packaging.PackWebRequest.GetResponse()
  
> at MS.Internal.WpfWebRequestHelper.GetResponse(WebRequest request)
  
> at System.Windows.ResourceDictionary.set_Source(Uri value)
  
> at WpfApplication1.themes.BaseTheme.ChangeTheme(String Theme) in BaseTheme.vb: line 9
  
> at WpfApplication1.themes.BlackTheme.ChangeTheme() in BlackTheme.vb: line 6
  
> at WpfApplication1.themes.ThemeChanger..ctor() in ThemeChanger.vb: line 8
  
> at WpfApplication1.Tests.TestThemeChanger.CanThemeChangeToBlue() in TestThemeChanger.vb: line 25 

I solved that by adding the pack reference to it.

like this.

```vbnet
Dim _FileName = "pack://application:,,,/WpfApplication1;component/themes/" & Theme & "/Theme.xaml"
            ```
But that gave me this exception. 

> System.UriFormatException : Invalid URI: Invalid port specified.
  
> at System.Uri.CreateThis(String uri, Boolean dontEscape, UriKind uriKind)
  
> at System.Uri..ctor(String uriString, UriKind uriKind)
  
> at WpfApplication1.themes.BaseTheme.ChangeTheme(String Theme) in BaseTheme.vb: line 9
  
> at WpfApplication1.themes.BlackTheme.ChangeTheme() in BlackTheme.vb: line 6
  
> at WpfApplication1.themes.ThemeChanger..ctor() in ThemeChanger.vb: line 8
  
> at WpfApplication1.Tests.TestThemeChanger.CanThemeChangeToBlue() in TestThemeChanger.vb: line 25 

And the above is caused bycause WPF is not initalized and thus the resources aren&#8217;t there.

So this needs to be added to the testclass.

```vbnet
&lt;SetUp()&gt;
        Public Sub InitializeWPFToBeAbleToUsePackURIs()
            Dim app = New System.Windows.Application()
            Windows.Application.ResourceAssembly = GetType(InitializingNewItemEventArgs).Assembly
        End Sub```
I found a [blogpost][2] that was really helpfull in solving the above problem and especialy the comments were helpfull. Thank you Puaal for that post and thank you Oskar for the comment.

## Conclusion

You see what a little refactoring can do to your code. It makes it more flexible and more testable. And don&#8217;t worry that it takes a little time to write this stuff you will be rewarded afterwards. 

And what patterns did we use? I find it less important that you can name them, I find it more important that you know how to use them.

But here is a little list.

  * [Command pattern][3] (ITheme and implementations)
  * [Strategy pattern][4] (BaseTheme)

If you see more then I&#8217;ll be glad to know.

I guess this became quit a long post.

 [1]: http://wpfthemes.codeplex.com/
 [2]: http://compilewith.net/2009/03/wpf-unit-testing-trouble-with-pack-uris.html
 [3]: http://www.dofactory.com/Patterns/PatternCommand.aspx
 [4]: http://www.dofactory.com/Patterns/PatternStrategy.aspx