---
title: WPF and wordwrap/wordtrimming in a listbox
author: Christiaan Baes (chrissie1)
type: post
date: 2010-07-16T10:26:49+00:00
ID: 847
url: /index.php/desktopdev/mstech/wpf-and-wordwrap-wordtrimming-in-a-listb/
views:
  - 5726
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - .net 4.0
  - listbox
  - wpf

---
I have been doing most of my frontend in windowsforms but I thought it was time to switch to WPF after all it is already at version 4.0 so most of the little quirks should be gone by now. 

First thing I did was to convert my dashboard over to it. I can use all the same controllers and model so I only need to worry about the views.
  
On my dashboard there is a ListBox which shows the user some messages of the system (not that anybody really reads them, but it looks cool).

The text for each item can be longer than the width of the ListBox. This is no problem since there is a horizontal scrollbar that can be seen. But I don&#8217;t like that.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/WPFListBox/WPFListBox1.png" alt="" title="" width="219" height="326" />
</div>

And this is the XAML you use for this.

```xml
&lt;ListBox Name ="lstMessages" /&gt;
```
And I filled the ListBox via this method. Which is an event coming from the ViewModel. Witch needs to be made threadsafe.

```vbnet
Private Delegate Sub MessagesCallBack(ByVal Messages As System.Collections.Generic.Queue(Of String))

        Private Sub _Messages_MessageAdded(ByVal Messages As System.Collections.Generic.Queue(Of String)) Handles _Messages.MessageAdded
            If lstMessages.Dispatcher.CheckAccess Then
                lstMessages.ItemsSource = Messages.Reverse.ToArray()
            Else
                Dim d = New MessagesCallBack(AddressOf _Messages_MessageAdded)
                lstMessages.Dispatcher.Invoke(d, New Object() {Messages})
            End If
        End Sub```
So I&#8217;m using the Itemsource for this.

I would prefer some text trimming (the three dots) and a tooltip with the complete text. 

In Windowsforms this would just be difficult to do. In WPF this is very easy.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/WPFListBox/WPFListBox2.png" alt="" title="" width="280" height="120" />
</div>

You just need to add a Datatemplate like this.

```xml
&lt;DataTemplate x:Key="itemTemplate"&gt;
  &lt;TextBlock TextTrimming="CharacterEllipsis" Text="{Binding}" ToolTip="{Binding}"&gt;&lt;/TextBlock&gt;
&lt;/DataTemplate&gt;```
And then change your ListBox a little.

```xml
&lt;ListBox Name ="lstMessages" ScrollViewer.HorizontalScrollBarVisibility="Disabled" ItemTemplate="{StaticResource itemTemplate}"&gt;
            ```
And that is it. As you can see I removed the Horizontalscrollbar and added an ItemTemplate.

Or you can make it wrap instead. Just a little change in the XAML.

```xml
&lt;DataTemplate x:Key="itemTemplate"&gt;
  &lt;TextBlock TextWrapping="Wrap" Text="{Binding}"&gt;&lt;/TextBlock&gt;
&lt;/DataTemplate&gt;```
And then it looks like this. I guess you could then do without the tooltip so I removed that.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/WPFListBox/WPFListBox3.png" alt="" title="" width="217" height="147" />
</div>

The hardest thing to find on the internets was the Text=&#8221;{Binding}&#8221;. I actually didn&#8217;t find it on the internets I just got lucky.