---
title: 'Me and NHProf: the beginning'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-07T05:58:28+00:00
ID: 413
url: /index.php/desktopdev/mstech/me-and-nhprof-the-beginning/
views:
  - 3251
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - nhprof
  - vb.net

---
As a user of nHibernate it was about time I got to check out [NHProf][1]. NHProf is a profiler for nHibernate applications. In other words you can see what SQL nHinernate is producing and it also gives alerts for parts that have suspect code in it.

First of course I had to download the package you can have a 30 day trail to go so you can test it on your application. I just bought it. Let&#8217;s not forget that this is still in Beta so because of that you get a discount for when the full version comes out. 

Then I had to set it up to work with my application. But remember you are not supposed to ship the binaries with your application (what would be the point anyway?)

First you add a reference to the HibernatingRhinos.NHibernate.Profiler.Appender.dll and then you add this line of code to your startup routine.

```vbnet
HibernatingRhinos.NHibernate.Profiler.Appender.NHibernateProfiler.Initialize()
            ```
of course you could just add that line anywhere you want NHProf to start profiling.

There is also another way of initializing NHProf. You can add an XML config file.

That looks like this

```xml
&lt;?xml version="1.0" encoding="utf-8" ?&gt;
&lt;log4net&gt;
  &lt;appender name="NHibernate.Profiler"
      type="HibernatingRhinos.NHibernate.Profiler.Appender.NHibernateProfilerAppender, 
		HibernatingRhinos.NHibernate.Profiler.Appender" &gt;
    &lt;sink value="tcp://127.0.0.1:22897/NHibernateAppenderLoggingSink" /&gt;
  &lt;/appender&gt;
  &lt;logger name="NHibernate.Transaction.AdoTransaction"&gt;
    &lt;level value="DEBUG"/&gt;
    &lt;appender-ref ref="NHibernate.Profiler"/&gt;
  &lt;/logger&gt;
  &lt;logger name="NHibernate.Loader.Loader"&gt;
    &lt;level value="DEBUG"/&gt;
    &lt;appender-ref ref="NHibernate.Profiler"/&gt;
  &lt;/logger&gt;
  &lt;logger name="NHibernate.Event.Default.DefaultLoadEventListener"&gt;
    &lt;level value="DEBUG"/&gt;
    &lt;appender-ref ref="NHibernate.Profiler"/&gt;
  &lt;/logger&gt;
  &lt;logger name="NHibernate.Impl.AbstractSessionImpl"&gt;
    &lt;level value="DEBUG"/&gt;
    &lt;appender-ref ref="NHibernate.Profiler"/&gt;
  &lt;/logger&gt;
  &lt;logger name="NHibernate.SQL"&gt;
    &lt;level value="DEBUG"/&gt;
    &lt;appender-ref ref="NHibernate.Profiler"/&gt;
  &lt;/logger&gt;
  &lt;logger name="NHibernate.Impl.SessionImpl"&gt;
    &lt;level value="DEBUG"/&gt;
    &lt;appender-ref ref="NHibernate.Profiler"/&gt;
  &lt;/logger&gt;
  &lt;logger name="NHibernate.Persister.Entity.AbstractEntityPersister"&gt;
    &lt;level value="DEBUG"/&gt;
    &lt;appender-ref ref="NHibernate.Profiler"/&gt;
  &lt;/logger&gt;
&lt;/log4net&gt;
```
but then you need to initialize NHProf with this line of code.

```vbnet
log4net.Config.XmlConfigurator.Configure(New FileInfo("log4net.config"))```
<span class="MT_red">Problem number 1</span>

The first problem I had was that I have the sourcecode of log4net and that I therefor use a custom version. Allthough both versions have the same versionnumber I had to change over to the binary NHProf uses. No big problem since I use a wrapper assembly and I only had one reference to log4net that I had to change. I mailed the problem to Ayende and I got an imediate reply.
  
<span class="MT_green"><br /> Problem solved.</span>

<span class="MT_red">Problem number 2</span>

The second problem I faced was this.

> I have worked around it 
> 
> I use this piece of code for making my windowsforms application singleinstance. It&#8217;s in VB.Net but there&#8217;s not much code. In essence it inherits from WindowsFormsApplicationBase which is the problem it seems.
> 
> ```vbnet
Imports Microsoft.VisualBasic.ApplicationServices
> 
>  
> Namespace Startup
>     ''' &lt;summary&gt;
>     ''' 
>     ''' &lt;/summary&gt;
>     ''' &lt;remarks&gt;&lt;/remarks&gt;
>     Public Class SingleInstanceController
>         Inherits WindowsFormsApplicationBase
>         Implements Interfaces.ISingleInstanceController
> 
> #Region " Private members "
>         ''' &lt;summary&gt;
>         ''' 
>         ''' &lt;/summary&gt;
>         ''' &lt;remarks&gt;&lt;/remarks&gt;
>         Private _Menu As Forms.Interfaces.IMenu
> #End Region
> 
> #Region " Constructors "
>         ''' &lt;summary&gt;
>         ''' 
>         ''' &lt;/summary&gt;
>         ''' &lt;remarks&gt;&lt;/remarks&gt;
>         Public Sub New(ByVal Menu As Forms.Interfaces.IMenu)
>             _Menu = Menu
>             IsSingleInstance = True
>         End Sub
> #End Region
> 
> #Region " Evenhandlers "
>         ''' &lt;summary&gt;
>         ''' When the user tries to open our application again then menu will just become visible and be pushed to the front.
>         ''' &lt;/summary&gt;
>         ''' &lt;param name="sender"&gt;&lt;/param&gt;
>         ''' &lt;param name="e"&gt;&lt;/param&gt;
>         ''' &lt;remarks&gt;&lt;/remarks&gt;
>         Private Sub this_StartupNextInstance(ByVal sender As Object, ByVal e As StartupNextInstanceEventArgs) Handles MyBase.StartupNextInstance
>             Me.MainForm.Show()
>             Me.MainForm.BringToFront()
>         End Sub
> #End Region
> 
> #Region " public methods "
>         ''' &lt;summary&gt;
>         ''' When the application is run the first time this method is called.
>         ''' &lt;/summary&gt;
>         ''' &lt;remarks&gt;We make a new instance of the Menu and it will be shown&lt;/remarks&gt;
>         Protected Overrides Sub OnCreateMainForm()
>            MainForm = CType(_Menu, System.Windows.Forms.Form)
>         End Sub
> 
>         ''' &lt;summary&gt;
>         ''' 
>         ''' &lt;/summary&gt;
>         ''' &lt;param name="args"&gt;&lt;/param&gt;
>         ''' &lt;remarks&gt;&lt;/remarks&gt;
>         Public Shadows Sub Run(ByVal args As String()) Implements Interfaces.ISingleInstanceController.Run
>             MyBase.Run(args)
>         End Sub
> #End Region
> 
>     End Class
> End Namespace```
> The exception is tcp channel is already registered (freely translated) and it&#8217;s a remotingexception. NHProf is registered before that and All the nhibernate code before that gets nicely caught by NHProf (the little there is). So I&#8217;m guessing NHProf and it use the same channel ;-). I haven&#8217;t looked further into it. 

Also got a swift reply.

> Christian,
  
> Yes, that is a known problem, and it will be fixed shortly, we change the way that we communicate and that would solve the issue for you.
  
> The release data for this is a few days 

I&#8217;ll forgive him for misspelling my name.

<span class="MT_green"><br /> Problem solved.</span>

So now I have a working NHProf. 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/nhprof.jpg" alt="" title="" width="614" height="461" />
</div>

And now I have something else to talk too ;-).

 [1]: http://nhprof.com/