---
title: Silverlight Spinner
author: ThatRickGuy
type: post
date: 2010-03-19T14:27:48+00:00
ID: 731
excerpt: Every week in my shop we do cross training. Someone brings in something they are working on, some system that people are unfamiliar with, some new technology, basically anything related to any of our jobs as developers and maintainers of applications. Often enough I do presentations on Silverlight and Blend, introducing my coworkers to different tools and options that we have available to us. This blog is a write up of one such training presentation. I give you, the Silverlight Spinner.
url: /index.php/webdev/webdesigngraphicsstyling/silverlight-spinner/
views:
  - 7899
rp4wp_auto_linked:
  - 1
categories:
  - UI Development
  - Web Design, Graphics and Styling

---
### Introduction

Every week in my shop we do cross training. Someone brings in something they are working on, some system that people are unfamiliar with, some new technology, basically anything related to any of our jobs as developers and maintainers of applications. Often enough I do presentations on Silverlight and Blend, introducing my coworkers to different tools and options that we have available to us. This blog is a write up of one such training presentation. I give you, the _Silverlight Spinner_.

<!--more-->


          


<center>
  </p> 
  
  <div style="height:150px; background-color: #000000; width: 75%; text-align:center;">
    <br /> <iframe id="_sl_historyFrame" style="visibility:hidden;height:0px;width:0px;border:0px"></iframe>
  </div>
  
  <p>
    </center>
  </p>
  
  <h3>
    Getting Started
  </h3>
  
  <p>
    Start up a new Silverlight application project as usual. If you want to reuse the spinner you may want to make a Silverlight control library project, but for now it's easier to make a standard application.
  </p>
  
  <h3>
    The Basic Layout
  </h3>
  
  <p>
    The spinner control starts out just like any other control. We have a layout root control, a grid. In the layout root we add a ViewBox. The view box handles dynamic scaling so that we don't have to worry about rendering the spinner at different sizes. The view box only accepts one child though, so we need another grid inside of it. Since the ViewBox can scale to any size, we need to set a fixed size for the grid so that we can work with it. I went with 128 ×128, you can use any size you like, the larger, the more accurate things will be.
  </p>
  
  <h3>
    The Background
  </h3>
  
  <p>
    In side the grid we're going to have a whole lot of rectangles. Starting off with the background, set the background rectangle to fill horizontally and vertically, then set the x and y corner radius to 64. This will cause the rectangle to appear like a circle. Next set the margins to 2 pixels. This will ensure that the background doesn't stick out from behind the mask we will make later.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner1.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner1.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    The Windows
  </h3>
  
  <p>
    Next up are the windows. Start off with a top-left justified rectangle. We want it's width to be a bit less than a third the width of the control, 45px out of 128px in this case. And we want it to be about three times as long as it is thick, which puts its height at 14px. Again we want it to be rounded, so set the x and y corner radius to 7px. Set the left margin to 3px and the top margin to 57px (128/2 – 14/2), putting it just inside of the background. Make a copy of this rectangle and switch it to right justified and change the right margin to 3px.<br /> Copy and paste twice more, but this time switch the width and the height and horizontal and vertical offsets, this should create the vertical windows.<br /> Copy all four of these rectangles and paste them below the others. Select the 4 new rectangles and from the Transforms panel use the Rotate transform to turn them all 45 degrees. You'll need to adjust the margins a bit on each one to get them to line up correctly.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner2.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner2.PNG" border=0 /></a>
    </center>
  </p>
  
  <p>
    ** Low Tech Tip: Hold a piece of paper up to the screen and mark on it the gap between the outer edge of the circle and the outer edge of one of the Windows that is positioned where you want it. Use that measurement to ensure that all of the other controls are positioned correctly. It may be a low tech solution, but it's easier than trying to zoom in and count pixels.
  </p>
  
  <h3>
    The Mask pt1
  </h3>
  
  <p>
    Once we have the windows in place, we need the mask layer. Just like the background, we want a rectangle that fills the control and looks like a circle. The only difference is that we have a margin of 0 on this rectangle.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner3.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner3.PNG" border=0 /></a>
    </center>
  </p>
  
  <p>
    ** Special thanks to Adam for introducing me to the combine functionality!
  </p>
  
  <h3>
    The Mask pt2
  </h3>
  
  <p>
    Select all of the window rectangles, right click and select “Combine” and “Unite”. This will turn all of those separate rectangles into a single path.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner5.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner5.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    The Mask pt3
  </h3>
  
  <p>
    Next, ensure that the Mask rectangle is lower in the list (higher in z-order) than the new path. Select both the mask rectangle and the new path, right click and select “Combine” and “Subtract”. This will merge the two into a single path, but it will cut the old path out of the rectangle.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner6.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner6.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    Voilà!
  </h3>
  
  <p>
    At this point, you should see something like this. Where the Window rectangles use to be there should now be holes through the mask, and you should be able to see the background.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner7.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner7.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    Bright Side of the Glass
  </h3>
  
  <p>
    Next, we're going to need a bunch more rectangles. 16 all together, but start with 2. What we're going to do is create a quick and dirty beveled glass effect. The first rectangle we will give a light gray stroke and a radial gradient. The gradient you'll want to toy around with, the goal is to get translucent white along the left and top edges that fades into full opacity. Use the Gradient Tool to manipulate how the gradient is applied.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner8.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner8.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    Dark Side of the Glass
  </h3>
  
  <p>
    On the second rectangle we don't need the stroke, but just like the first rectangle we're adding to the beveled glass effect. This time with a translucent black radial gradient set to the right and bottom.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner9.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner9.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    Making Quick Work of it pt1
  </h3>
  
  <p>
    Manually recreating each of these is going to be a bit of a pain, so to speed things up we can take a short cut. The first 4 are pretty easy, copy & past the two new rectangles and change the justification/margins/height/width appropriately. The next four are a bit more intense. Copy and paste the two original rectangles. Select them both and in the Transform pane switch to the Center Point tab. Set the X value to 1.35 and the Y value to 0.5.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner10.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner10.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    Making Quick Work of it pt2
  </h3>
  
  <p>
    Once you've changed the center point, switch to the Rotate tab and rotate the control 45 degrees. They should line up, almost perfectly, with the mask window. You may have to increase the length and width of these rectangles by 1 pixel and tweak the margins, but once you touch up the first one, the rest are pretty easy.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner11.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner11.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    More Gradients!
  </h3>
  
  <p>
    And on to the spinner graphic! We need another rectangle, identical to the background rectangle (2px margin). This one we're going to fill with a radial gradient. The one below is not the same one from the working demo. In most cases you'll want a strong leading edge with a gradual fade behind it, but this is an easy part to play with. Use the gradient tool and the Brushes pane to make an interesting pattern.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner12.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner12.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    Looking Good!
  </h3>
  
  <p>
    If you turn on the mask and glass layers, you should see something like this:<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner13.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner13.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    Let's Get Moving
  </h3>
  
  <p>
    Next, we want to animate the spinner. Click the “+”, or the down arrow and select New, above the control tree to create a new story board.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner14.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner14.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    Name it
  </h3>
  
  <p>
    Name the storyboard so that we can access it from code.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner15.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner15.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    Setting Up the Animation
  </h3>
  
  <p>
    Use the “Record Keyframe” button to set a starting position at the 0 second mark on the Spinner rectangle line.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner16.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner16.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    Spin Me Round
  </h3>
  
  <p>
    Drag the yellow bar out to 1 second, then on the Rotate tab of the Transform Pane, set the angle to 360. You should now be able to hit the play button on the animation control to see your spinner spin<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner17.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner17.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    And Around, And Around, And Around…
  </h3>
  
  <p>
    Before we get into the code, we need to change the looping behavior of the spin animation. Select the animation by clicking on it above the control tree. In the top right corner you should see the “Common Properties” pane. Set the RepeatBehavior to “Forever”.<br /> 
    
    <center>
      <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/Spinner18.PNG"><img src="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/thSpinner18.PNG" border=0 /></a>
    </center>
  </p>
  
  <h3>
    On To the Code
  </h3>
  
  <p>
    The code for the spinner is really simple:
  </p>
  
  <pre>
			Public Sub New()
				' Required to initialize variables
				InitializeComponent()
				StopSpinner
			End Sub

			public sub StartSpinner
				me.recSpinner.Visibility=Windows.Visibility.Visible
				me.Spin.begin
			end sub

			public sub StopSpinner
				me.recSpinner.Visibility=Windows.Visibility.Collapsed
				me.Spin.Stop
			end sub
		</pre></p> 
  
  <h3>
    And So On
  </h3>
  
  <p>
    By calling StopSpinner in the constructor, we can be sure that when the control becomes visible it will be displaying the background, not a stationary spinner. And the Start and Stop methods will handle switching to the appropriate rectangle for us. If you plan on reusing this control, it is also a great idea to expose the brushes for your Background, Spinner, and Mask. This allows future users to change your controls colors to match their applications.
  </p>
  
  <h3>
    Download
  </h3>
  
  <p>
    You can grab the VS2008 project and code from <a href="http://ringdev.com.web10.reliabledomainspace.com/SpinnerTutorial/SilverlightApplication1.zip">here</a>
  </p>
  
  <p>
    -Rick
  </p>