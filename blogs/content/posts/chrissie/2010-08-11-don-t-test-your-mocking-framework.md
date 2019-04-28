---
title: Don’t test your mocking framework.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-08-11T08:15:31+00:00
ID: 868
url: /index.php/desktopdev/mstech/don-t-test-your-mocking-framework/
views:
  - 2644
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET

---
Today I was writing some tests (as I do every day), and I wrote this.

```csharp
[Test]
public void IfLanguageChangesToNlWhenbtnDutchIsClicked()
{
  var _mocks = new StructureMap.AutoMocking.RhinoAutoMocker&lt;FrmMenu&gt;();
  var _menu = _mocks.ClassUnderTest;
  var language = _mocks.Get&lt;ILanguageSettings&gt;();
  language.Expect(x =&gt; x.Language)
    .Return(Enumerations.Languageoptions.nl);
  _menu.btnDutch.RaiseClick();
  Assert.AreEqual(Enumerations.Languageoptions.nl,language.Language);
}```
I used [Rhino mocks][1] for this.

FrmMenu has these three buttons.

```vbnet
''' &lt;summary&gt;
        Private Sub btnDutch_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles btnDutch.Click
            Me.Language.Language = Enumerations.Languageoptions.nl
        End Sub
```
So the test should check if the language changes to dutch when I clikc that button.

But I&#8217;m not testing my code I&#8217;m testing [Rhino mocks][1]. And that can never be the point. Because that test will always pass (unless [Ayende][2] did something really stupid)

I rewrote the test like this.

```csharp
[Test]
public void IfLanguageChangesToNlWhenbtnDutchIsClicked()
{
  var _mocks = new StructureMap.AutoMocking.RhinoAutoMocker&lt;FrmMenu&gt;();
  var _menu = _mocks.ClassUnderTest;
  var language = _mocks.Get&lt;ILanguageSettings&gt;();
  _menu.btnDutch.RaiseClick();
  language.AssertWasCalled(x =&gt; x.Language = Enumerations.Languageoptions.nl);
}```
And now I&#8217;m testing the right thing.

 [1]: http://www.ayende.com/projects/rhino-mocks.aspx
 [2]: http://www.ayende.com