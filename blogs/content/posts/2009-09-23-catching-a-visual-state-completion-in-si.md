---
title: Catching a Visual State Completion in Silverlight
author: ThatRickGuy
type: post
date: 2009-09-23T11:41:24+00:00
ID: 565
excerpt: Catching the completion of a visual state change in Silverlight 3
url: /index.php/webdev/webdesigngraphicsstyling/catching-a-visual-state-completion-in-si/
views:
  - 5245
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - bug
  - silverlight
  - storyboard
  - visual state

---
Just a quickie from the Silverlight front today. 

Visual States and the VSM are awesome tools to handling most of your application's animations. One annoyance though, Visual States do not have a .Completed event!

Luckily for us, Visual States are composed of Story Boards. Story Boards DO have .Completed events 🙂

To get it to work, it's really simple. First, create your event handling method:

```VB.Net
Private Sub SomeStoryBoard_Completed(ByVal sender As Object, ByVal e As System.EventArgs) 
     MessageBox.Show("StoryBoard Completed!")
End Sub
```

Next, in your LayoutRoot_Loaded method, add a handler:

```VB.Net
AddHandler Me.MyVisualState.Storyboard.Completed, AddressOf SomeStoryBoard_Completed
```

Where “MyVisualState” is the name of the visual state you are trying to work with.

Nothing too fancy there. You just have to know that a VisualState contains a story board and that you can wire up that story board's completed event. 

There is another way, that is even more simple, but it appears to not be working as intended. If you have any idea's why the following doesn't work, I'd love to hear about it.

Start by going into the XAML and finding the Storyboard in the Visual State you are trying to work with. Once you've found it, give the Storyboard a name (x:Name=”sbSearchOpen” on line 11 in the following example)

```XAML
<VisualStateManager.VisualStateGroups>
	<VisualStateGroup x:Name="SearchBox">
		<VisualStateGroup.Transitions>
			<VisualTransition GeneratedDuration="00:00:00.6000000">
				<VisualTransition.GeneratedEasingFunction>
					<CubicEase EasingMode="EaseOut"/>
				</VisualTransition.GeneratedEasingFunction>
			</VisualTransition>
		</VisualStateGroup.Transitions>
		<VisualState x:Name="SearchOpen">
			<Storyboard x:Name="sbSearchOpen">
				<DoubleAnimationUsingKeyFrames BeginTime="00:00:00" Duration="00:00:00.0010000" Storyboard.TargetName="grdSearch" Storyboard.TargetProperty="(FrameworkElement.Height)">
					<EasingDoubleKeyFrame KeyTime="00:00:00" Value="85"/>
				</DoubleAnimationUsingKeyFrames>
				<ObjectAnimationUsingKeyFrames BeginTime="00:00:00" Duration="00:00:00.0010000" Storyboard.TargetName="grdNavSearch" Storyboard.TargetProperty="(FrameworkElement.Margin)">
					<DiscreteObjectKeyFrame KeyTime="00:00:00">
						<DiscreteObjectKeyFrame.Value>
							<Thickness>0,0,0,85</Thickness>
						</DiscreteObjectKeyFrame.Value>
					</DiscreteObjectKeyFrame>
				</ObjectAnimationUsingKeyFrames>
			</Storyboard>
		</VisualState>
	</VisualStateGroup>
</VisualStateManager.VisualStateGroups>
```
Then we can switch to the code behind and in the object list select the sbSearchOpen story board, and in the event list select Completed. This will generate the sbSearchOpen_Completed event:

```VB.Net
Private Sub sbSearchOpen_Completed(ByVal sender As Object, ByVal e As System.EventArgs) Handles sbSearchOpen.Completed
        MessageBox.Show("Search Opened!")
    End Sub
```

Totally an easy and intuitive way of catching the end of a story board. Unfortunately, it doesn't work. The IDE acts like it expects it to work, but for some reason, at run time, the sbSearchOpen_Completed method will never fire.

-Rick