---
title: Exploring Reactive Extensions – Adding a Dash of Linq
author: Alex Ullrich
type: post
date: 2010-11-30T12:27:00+00:00
url: /index.php/desktopdev/mstech/exploring-reactive-extensions-adding-a-d/
views:
  - 13505
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies

---
I think the reactive extensions library starts to look even better when you start taking into consideration all the cool linq stuff it lets you do with sequences of events. In order to use this we need to add another reference, this time to System.Reactive. It&#8217;s interesting that the stuff we are importing with these references becomes available anyplace the System namespace is available &#8211; there&#8217;s no need to add using directives to our code files. I guess this is because it&#8217;s intended to become a first class feature in .net some time.

The first example here will be a form that listens for mouse movements. We want to update a label to show current coordinates (and buttons pushed) when either the X or Y coordinate is a multiple of 10, OR the left and right buttons are both pushed. Normally this could be done with a bit of conditional logic in our event handler. Consider the following:

<pre>public partial class MouseWatcherForm : Form {
	public MouseWatcherForm() {
		InitializeComponent();
		this.MouseMove += UpdateLabel;
	}

	public void UpdateLabel(object sender, MouseEventArgs e) {
		if(( e.X % 10 == 0 || e.Y % 10 == 0)
							|| ((e.Button & MouseButtons.Left) == MouseButtons.Left 
								&& (e.Button & MouseButtons.Right) == MouseButtons.Right))
		label1.Text = string.Format("X: {0}, Y: {1}, Button: {2}", e.X, e.Y, e.Button);
	}
}</pre>

Then, if we wanted to do the same thing in a reactive way:

<pre>public partial class ReactiveMouseWatcherForm : Form {
	public ReactiveMouseWatcherForm() {
		InitializeComponent();
		var mouseMoveEvent = Observable.FromEvent&lt;MouseEventArgs&gt;(this, "MouseMove");

		var selectedMovementEvents = from pos in mouseMoveEvent
					 .Where(x =&gt; (x.EventArgs.X % 10 == 0 || x.EventArgs.Y % 10 == 0)
							|| ((x.EventArgs.Button & MouseButtons.Left) == MouseButtons.Left
								&& (x.EventArgs.Button & MouseButtons.Right) == MouseButtons.Right))
					 .Repeat()
					select pos.EventArgs;

		selectedMovementEvents.Subscribe(p =&gt; {
			label1.Text = string.Format("X: {0}, Y: {1}, Button: {2}", p.X, p.Y, p.Button);
		});
	}
}</pre>

In this case the savings aren&#8217;t that significant, though it is interesting to move from a scenario where we&#8217;re always reacting to the event, and handling the conditional logic in our handler to a scenario where we are actually filtering the stream of events to limit what we need to react to in the first place. The latter definitely sounds like a more sane way to do things from where I&#8217;m sitting, and can open up some interesting possibilities. Take, for example, the idea of letting a user draw a line on a form by holding down the left mouse button and moving the mouse. Because you always need the current AND previous location, it can get ugly trying to do this with traditional events.

<pre>public partial class DrawingForm : Form {

	MouseEventHandler handler;
	Point? previousMousePosition;

	public DrawingForm() {
		InitializeComponent();

		handler = (sender, e) =&gt; {
			if (previousMousePosition.HasValue) {
				using (var gfx = this.CreateGraphics())
				using (var pen = new Pen(Color.Black, 5F)) {
					gfx.DrawLine(pen, previousMousePosition.Value, e.Location);
				}
			}
			previousMousePosition = e.Location;
		};

		this.MouseDown += DrawingForm_MouseDown;
		this.MouseUp += DrawingForm_MouseUp;
	}

	void DrawingForm_MouseUp(object sender, MouseEventArgs e) {
		previousMousePosition = null;
		this.MouseMove -= handler;
	}

	void DrawingForm_MouseDown(object sender, MouseEventArgs e) {
		this.MouseMove += handler;
	}
}</pre>

I&#8217;m not sure this is the best way to solve the problem at hand, but this isn&#8217;t tremendously bad. It&#8217;s easy to see how this could get out of hand as more complexities are introduced though. This approach can also be difficult to follow, as the interplay between different event handlers isn&#8217;t always immediately apparent. We can tell that for some reason the mouse&#8217;s previous position is important, and that we have event handlers set up for wiring up (and de-wiring) another event handler. This is not very intuitive, and to make matters worse the bit of saved state leaves us open to the various side effects if we need to access the field from other methods in the future.

However, because we can apply linq queries to streams of events using Rx, something like this becomes a bit easier.

<pre>public partial class ReactiveDrawingForm : Form {
	public ReactiveDrawingForm() {
		InitializeComponent();
		var mouseMove = Observable.FromEvent&lt;MouseEventArgs&gt;(this, "MouseMove");
		var mouseDown = Observable.FromEvent&lt;MouseEventArgs&gt;(this, "MouseDown");
		var mouseUp = Observable.FromEvent&lt;MouseEventArgs&gt;(this, "MouseUp");

		var selectedMovementEvents = from pair in mouseMove
					 .SkipUntil(mouseDown)
					 .TakeUntil(mouseUp)
					 .Let(mvmnt =&gt; mvmnt.Zip(mvmnt.Skip(1),
						(previous, current) =&gt; new {
							current = current.EventArgs.Location,
							previous = previous.EventArgs.Location
						}))
					.Repeat()
			select pair;

		selectedMovementEvents.Subscribe(p =&gt; {
			using (var gfx = this.CreateGraphics())
			using (var pen = new Pen(Color.Black, 5F)) {
				gfx.DrawLine(pen, p.previous, p.current);
			}
		});
	}
}</pre>

The .SkipUntil and .TakeUntil calls limit our sequence to only the mouse move events that occur between the mouse button being pressed and released. From there, the combination of calls to .Let, .Zip and .Skip allow us to yield a series of **pairs** of locations. This makes drawing the line between the last two locations a piece of cake, and prevents us from needing to store any kind of state globally, which in turn will make the function behave more predictably. The new code is free of the side effects seen in the other example resulting from the global state, and once you get your head around the syntax for working with the sequence it is much better about self-documenting.

The biggest improvement in the reactive code for me is how much more intuitive it is. It may be because I spent a few years doing only web UI&#8217;s, but it often feels like black magic to me working with the event mechanisms in .NET. They just tend to make code challenging for me to follow. While the new method is not much shorter, and a bit more dense, I like having everything we need in one place, and with less dependencies that could bite us later. I&#8217;m interested to look at what effects (if any) a change like this can have on testability as well, but this has gotten kinda long so that can wait for another post.

_*****Source code for this post can be found over at [github][1], with these examples in the EventsVsReactive project._

 [1]: https://github.com/lessthandot/ExploringRx