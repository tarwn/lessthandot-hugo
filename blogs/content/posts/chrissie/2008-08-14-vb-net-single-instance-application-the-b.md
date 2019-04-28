---
title: 'VB.Net: Single Instance application the better way'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-08-14T08:31:25+00:00
ID: 107
url: /index.php/desktopdev/mstech/vb-net-single-instance-application-the-b/
views:
  - 15511
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - application
  - instance
  - single
  - singleton
  - vb.net

---
Based on [Scott Hanselman&#8217; s][1] blogpost named [The Weekly Source Code 31- Single Instance WinForms and Microsoft.VisualBasic.dll][2]

We all know about this little setting, right? At least now you do ;-).

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/SingleInstance.jpg" alt="" title="" width="313" height="322" />
</div>

<div class="image_block">
  <p>
    For you C# lovers don&#8217;t search for it, it&#8217;s only there in VB.Net.
  </p>
  
  <p>
    And this little nasty side effect.
  </p>
  
  <p>
    <img src="/wp-content/uploads/blogs/DesktopDev/Singleinstancewitherror.jpg" alt="" title="" width="783" height="422" /></div> 
    
    <p>
      That meant you had to have all your startupcode in the constructor or loadevent of that form (:'()
    </p>
    
    <p>
      So Scott gave me the inspiration to do this.
    </p>
    
    <p>
      First of all go back to the properties of your project and uncheck the &#8220;enable application framework&#8221; thing and never check it again :>. Then we can just have a sub main in a module.
    </p>
    
    <pre lang="vbnet">Module StartupModule

#Region " Public methods "
    ''' &lt;summary&gt;
    ''' Beginning of the application
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    Public Sub Main()
        Dim _StartUp As New Startup.StartUp
        _StartUp.Start()
    End Sub
#End Region

End Module
</pre>
    
    <p>
      And this is the Startup class
    </p>
    
    <pre lang="vbnet">Namespace Startup
    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    Public Class StartUp
        Implements Interfaces.IStartUp

#Region " private members "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _Log As TDB2007.Utils.Logging.Logging.Interfaces.ILog
#End Region

#Region " Constructors "
        ''' &lt;summary&gt;
        '''  
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New()

        End Sub
#End Region

#Region " public method "
        ''' &lt;summary&gt;
        ''' The start method for eassier unit testing
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub Start() Implements Interfaces.IStartUp.Start
            CommonFunctions.IoC.IoCUtil.InsertConfigOfContainingType(Of View.Menu.IoC.ConfigureStructureMap)()
            log4net.Config.XmlConfigurator.Configure()
            Application.EnableVisualStyles()
            Dim controller As View.Menu.Startup.Interfaces.ISingleInstanceController = CommonFunctions.IoC.Container.Resolve(Of View.Menu.Startup.Interfaces.ISingleInstanceController)()
            CommonFunctions.IoC.Container.Resolve(Of Controller.Interfaces.IStart).Start()
            Try
                controller.Run(Environment.GetCommandLineArgs())
            Catch Ex As Exception
                _Log = CommonFunctions.IoC.Container.Resolve(Of Utils.Logging.Logging.Interfaces.ILog)()
                _Log.LogWarning("Unhandled Exception - " & Ex.Message)
                _Log.LogWarning("Stacktrace : " & Ex.StackTrace)
            End Try
        End Sub
#End Region

    End Class
End Namespace</pre>
    
    <p>
      Which calls our singleinstancecontroller.
    </p>
    
    <pre lang="vbnet">Imports Microsoft.VisualBasic.ApplicationServices

Namespace Startup
    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    Public Class SingleInstanceController
        Inherits WindowsFormsApplicationBase
        Implements Interfaces.ISingleInstanceController

#Region " Constructors "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New()
            IsSingleInstance = True
        End Sub
#End Region

#Region " Evenhandlers "
        ''' &lt;summary&gt;
        ''' When the user tries to open our application again then menu will just become visible and be pushed to the front.
        ''' &lt;/summary&gt;
        ''' &lt;param name="sender"&gt;&lt;/param&gt;
        ''' &lt;param name="e"&gt;&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Sub this_StartupNextInstance(ByVal sender As Object, ByVal e As StartupNextInstanceEventArgs) Handles MyBase.StartupNextInstance
            Me.MainForm.Show()
            Me.MainForm.BringToFront()
        End Sub
#End Region

#Region " public methods "
        ''' &lt;summary&gt;
        ''' When the application is run the first time this method is called.
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;We make a new instance of the Menu and it will be shown&lt;/remarks&gt;
        Protected Overrides Sub OnCreateMainForm()
            MainForm = CType(CommonFunctions.IoC.Container.Resolve(Of Forms.Interfaces.IMenu)(), System.Windows.Forms.Form)
        End Sub

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;param name="args"&gt;&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Shadows Sub Run(ByVal args As String()) Implements Interfaces.ISingleInstanceController.Run
            MyBase.Run(args)
        End Sub
#End Region

    End Class
End Namespace</pre>
    
    <p>
      A working project/solution to download.
    </p>
    
    <p>
      <a href="/wp-content/uploads/blogs/DesktopDev/SingleInstance.zip" title="">SingleInstance.zip</a>
    </p>

 [1]: http://www.hanselman.com/blog/
 [2]: http://www.hanselman.com/blog/TheWeeklySourceCode31SingleInstanceWinFormsAndMicrosoftVisualBasicdll.aspx