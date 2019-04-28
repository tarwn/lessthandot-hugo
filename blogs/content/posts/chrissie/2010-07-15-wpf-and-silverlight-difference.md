---
title: WPF and Silverlight difference
author: Christiaan Baes (chrissie1)
type: post
date: 2010-07-15T11:43:07+00:00
ID: 846
url: /index.php/desktopdev/mstech/wpf-and-silverlight-difference/
views:
  - 2679
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - .net 4.0
  - silverlight
  - wpf

---
Today I was in the process of making my own copy-paste Analog clock. I went aruond the internet and found [this pretty thing][1]. Of course I could have used that code and gone on with life. But that was not the point of the exercise. I wanted to learn something of this. 

I liked the way he did the animations, no more need for a timer to update the angles, all is done in XAML. 

So his project is Silverlight. And he has this to aniamte the hands of the clock.

```xml
&lt;Canvas.Resources&gt;
  &lt;Storyboard x:Name="SecondsHandStoryboard"&gt;
    &lt;DoubleAnimation From="0" To="360" Duration="00:01:00" RepeatBehavior="Forever"
								Storyboard.TargetProperty="(Polygon.RenderTransform).(RotateTransform.Angle)" Storyboard.TargetName="SecondHand"/&gt;
  &lt;/Storyboard&gt;
&lt;/Canvas.Resources&gt;```
and this in his codebehind

```csharp
private void SecondsHandCanvas_Loaded( object sender, RoutedEventArgs e )
		{
            this.SecondsHandStoryboard.Begin();
            this.SecondsHandStoryboard.Seek( DateTime.Now.TimeOfDay );
		}```
```vbnet
Private Sub SecondsHandCanvas_Loaded( ByVal sender As object, ByVal e As RoutedEventArgs)
  Me.SecondsHandStoryboard.Begin()
  Me.SecondsHandStoryboard.Seek( DateTime.Now.TimeOfDay );
End Sub```
Cool I can just copy paste that and get on with it.

But Silverlight and WPF aren&#8217;t exactly the same they just look the same.

In WPF A name is no good for a resource apparently. It needs a Key.
  
So the above XAML becomes this instead of the above Silverlight code.

```xml
&lt;Canvas.Resources&gt;
  &lt;Storyboard x:Key="HourHandStoryboard"&gt;
    &lt;DoubleAnimation From="0" To="360" Duration="12:00:00" RepeatBehavior="Forever" Storyboard.TargetProperty="(Polygon.RenderTransform).(RotateTransform.Angle)" Storyboard.TargetName="HourHand"/&gt;
  &lt;/Storyboard&gt;
&lt;/Canvas.Resources&gt;```
And then you find out that the code you have doesn&#8217;t work either. YOu need to change it to this.

```vbnet
Private Sub HoursHandCanvas_Loaded(ByVal sender As System.Object, ByVal e As System.Windows.RoutedEventArgs)
            Dim s = CType(Me.HoursHandCanvas.Resources("SecondHandStoryboard"), Storyboard)
            s.Begin()
            s.Seek(DateTime.Now.TimeOfDay)
        End Sub```
```csharp
private void HoursHandCanvas_Loaded(System.Object, sender,System.Windows.RoutedEventArgs e)
{
  var s = (StoryBoard)this.HoursHandCanvas.Resources("SecondHandStoryboard");
  s.Begin();
  s.Seek(DateTime.Now.TimeOfDay);
}```
And BTW doing s.Begin(Me, True) does not work whatever MSDN say.

 [1]: http://www.silverlightshow.net/items/Developing-Silverlight-AnalogClock-part-2-Enhancing-the-view.aspx