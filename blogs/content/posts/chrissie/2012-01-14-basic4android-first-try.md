---
title: Basic4Android first try
author: Christiaan Baes (chrissie1)
type: post
date: 2012-01-14T17:10:00+00:00
ID: 1494
excerpt: I tried out Basic4Android, yes you can use Basic to program for android. Now that is cool.
url: /index.php/desktopdev/mstech/vbnet/basic4android-first-try/
views:
  - 6749
categories:
  - Java
  - VB.NET
tags:
  - basic4android

---
Some people just have weird ideas and making Basic4Android could be categorized as one of those. 

Yes it let&#8217;s you create android applications using Basic. And how could I pass that up. So I installed the trail version and gave it a spin.

So I tried to make a textview that I would change to hello world when the application starts.

First thing you get to see when is this screen.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/basic4android/basic4android1.png?mtime=1326567552"><img alt="" src="/wp-content/uploads/users/chrissie1/basic4android/basic4android1.png?mtime=1326567552" width="785" height="393" /></a>
</div>

I guess that if you want to use it for real you should buy it.

Then I clicked on designer and was greeted with these two windows.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/basic4android/basic4android3.png?mtime=1326567574"><img alt="" src="/wp-content/uploads/users/chrissie1/basic4android/basic4android3.png?mtime=1326567574" width="999" height="667" /></a>
</div>

And as you can see I added a label, yeah that&#8217;s what they call a TextView.

And then I changed the code to this.

```vbnet
'Activity module
Sub Process_Globals
	'These global variables will be declared once when the application starts.
	'These variables can be accessed from all modules.

End Sub

Sub Globals
	Dim label1 As Label

End Sub

Sub Activity_Create(FirstTime As Boolean)
	Activity.LoadLayout("main")
	label1.Text = "Hello world"
End Sub

Sub Activity_Resume
	
End Sub

Sub Activity_Pause (UserClosed As Boolean)

End Sub


```
you declare your views in global and you just give them the same name as you used in your layout and things are supposed to work.

Then you load the layout in your create method and change the text of the label.

After that you run it by clicking, uhm, run. And you will see that it starts converting your code to Java code. But first you open an emulator with the AVD manager.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/basic4android/basic4android2.png?mtime=1326567562"><img alt="" src="/wp-content/uploads/users/chrissie1/basic4android/basic4android2.png?mtime=1326567562" width="512" height="291" /></a>
</div>

And does it work you ask?

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/basic4android/basic4android4.png?mtime=1326568124"><img alt="" src="/wp-content/uploads/users/chrissie1/basic4android/basic4android4.png?mtime=1326568124" width="996" height="900" /></a>
</div>

Yes of course it does.

Now the question is, would I ever use this&#8230;. No. But it&#8217;s fun to see what people think of.