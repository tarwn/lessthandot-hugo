---
title: Communicating with KBay, Design Pattern for Web Services
author: ThatRickGuy
type: post
date: 2009-12-07T19:06:46+00:00
url: /index.php/webdev/webdesigngraphicsstyling/comms-for-kbay/
views:
  - 2884
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling

---
Hey everyone, 

<p style="text-indent: 30pt;">
  Sorry this post has been so delayed. I was wiped out last week and a half with a nasty cold that I am still recovering from. I missed my entire Thanksgiving break, curled up under blankets and coughing my lungs up. Anyway, I&#8217;m finally back on my feet, albeit completely buried under catch up work and a new project. But as promised, nuts and bolts from KBay!
</p>

### The Power of Communication

<p style="text-indent: 30pt;">
  One the best parts of KBay is, IMO, the communication design. Every web method in the service takes a single object parameter and returns a single parameter. These two objects are always called the Request and the Response.
</p>

<p style="text-indent: 30pt;">
  Some of the awesomeness behind this pattern includes:<br /> 1) Client side, you can hang on to a request and populated it as needed. No more piles of variables to pass in, just set up the single request object, it&#8217;s all nice and self contained.<br /> 2) Super easy logging/debugging. At any point in time we can serialize the request/response objects to see what&#8217;s in them. Even using a tool like Fiddler you can see a nicely formatted data layout of your parameters.<br /> 3) Server side additions will not break functionality. So long as the server side checks the value it wants to use before using it, the client side will continue to function. This was great for us as we had 2+ developers on the project the whole time. If Adam was adding more parameters and functionality server side, I could continue hitting the dev server with out having to rebind or implement any changes.
</p>

### The Simplest Web Methods Ever

<p style="text-indent: 30pt;">
  First off, the actual code behind on the web services:
</p>

<pre>&lt;WebMethod(Description:="Returns a set of data required at application startup.")&gt; _
    Public Function GetInit(ByVal Request As GetInitRequest) As GetInitResponse
        Return Request.FillRequest()
    End Function

    &lt;WebMethod(Description:="Returns a list of Alert Data based on the filter criteria set in the request.")&gt; _
    Public Function GetAlertData(ByVal Request As GetAlertDataRequest) As GetAlertDataResponse
        Return Request.FillRequest()
    End Function</pre>

<p style="text-indent: 30pt;">
  This code is actually for a different project, but it&#8217;s what I had handy. All of the functions follow the same pattern as the ones you can see here. They take a custom Request object and return a custom Response object (both named after the method). Inside the method, they call the Request.FillRequest method.
</p>

### Where&#8217;s the beef?

<p style="text-indent: 30pt;">
  So let&#8217;s keep digging. Here is the GetInit.vb file that contains the functionality for the GetInit method:
</p>

<pre>Namespace Implementation
    &lt;ServiceManager.AutoCreateSessionId()&gt; _
    Public Class GetInitRequest
        Inherits ServiceManager.BaseRequest(Of GetInitRequest, GetInitResponse)

        Public Overrides Function innerFillRequest() As GetInitResponse
            Dim output As New GetInitResponse

            Dim env = Utility.GetEnvironement("Alerts|Development")
            Dim Statuses As String = Utility.GetEnvironmentSetting("Alerts|Development", "StartupMessage").ToString
            If Statuses &lt;&gt; String.Empty Then
                output.Statuses.AddRange(Statuses.Split(","c))
            End If

            Return output
        End Function


        Protected Overrides Function GetLoggingReplacement(ByVal XMLElement As System.Xml.Linq.XElement) As String
            Return Nothing
        End Function
    End Class



    Public Class GetInitResponse
        Inherits ServiceManager.BaseResponse

        Private _Statuses As New List(Of String)
        Public Property Statuses() As List(Of String)
            Get
                Return _Statuses
            End Get
            Set(ByVal value As List(Of String))
                _Statuses = value
            End Set
        End Property
    End Class

End Namespace</pre>

<p style="text-indent: 30pt;">
  A couple of things to notice here. First, there are two classes in this file, both the request and the response. This is just for organizational purposes. You&#8217;ll also want to note the &#8220;servicemanager.AutoCreateSessionId()&#8221; attribute on the Request class. We&#8217;ll use this again later.
</p>

<p style="text-indent: 30pt;">
  One of the cool things about this system is that it uses generics to move data UP the hierarchy chain. In this case, you can see that we are inheriting from BaseRequest, which allows us to specify the types of the generic.
</p>

<p style="text-indent: 30pt;">
  The &#8220;innerFillRequest&#8221; method is where the actual functionality of this web method takes place. In the case of GetInit, it grabs a string from the Environment Provider, splits it, and adds it to a member on the Response object.
</p>

<p style="text-indent: 30pt;">
  Below that is the GetInitResponse class, which in this case is pretty simple and just contains a property that exposes a list of strings. The same property that is populated in the Request object&#8217;s innerFillRequest method.
</p>

### Strange Child, Who is your Parent?

<p style="text-indent: 30pt;">
  While these derived classes show us what the method does, it doesn&#8217;t show us how they are called. The web methods called Request.FillRequest, not InnerFillRequest. So lets look at the class that the request inherits from, BaseRequest:
</p>

<pre>Namespace ServiceManager
    Public MustInherit Class BaseRequest(Of Req As {New, iRequest}, Res As {New, BaseResponse})
        Implements iRequest

        Private mRequestToken As String
        Public Property RequestToken() As String Implements iRequest.RequestToken
            Get
                Return mRequestToken
            End Get
            Set(ByVal value As String)
                mRequestToken = value
            End Set
        End Property
        Private mSessionId As Long
        Public Property SessionId() As Long Implements iRequest.SessionId
            Get
                Return mSessionId
            End Get
            Set(ByVal value As Long)
                mSessionId = value
            End Set
        End Property


        ''' &lt;summary&gt;
        ''' This procedure forms a response object and deals with any errors that take place while
        ''' that response is being created.
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="Req"&gt;&lt;/typeparam&gt;
        ''' &lt;typeparam name="Res"&gt;&lt;/typeparam&gt;
        ''' &lt;param name="Request"&gt;&lt;/param&gt;
        ''' &lt;param name="RunAction"&gt;&lt;/param&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Function FillRequest() As Res
            '// create a place holder for the output response object
            Dim Output As Res = Nothing
            '// note the time we started this service call.
            Dim sw As New Stopwatch : sw.Start()

            Try
                '// if this request type implements the AutoCreateSessionIdAttribute AND the user has
                '// not given a session ID then create a session ID.
                Dim Atts = Me.GetType.GetCustomAttributes(GetType(AutoCreateSessionIdAttribute), True)
                If Atts IsNot Nothing AndAlso Atts.Count &gt; 0 Then
                    If Me.SessionId &lt;= 0 Then
                        Me.SessionId = Utility.LogApplicationStartup()
                    End If
                End If

                '// if at this point there is no session ID, throw an exception
                If Me.SessionId &lt;= 0 Then
                    Throw New ApplicationException("Invalid session ID given. Ensure you pass the session ID to every service call.")
                End If

                '// set a GUID token for this request
                If String.IsNullOrEmpty(Me.RequestToken) Then
                    Me.RequestToken = System.Guid.NewGuid.ToString
                End If


                '// log the request XML
                Utility.WriteLogEntry(Me.SessionId, KerryAuditLoggingServices.LogTypes.Verbose, CreateXML(Me))

                '// actually run the request and build a response object.
                Output = innerFillRequest()

                '// note the session ID back to the response object
                Output.SessionId = Me.SessionId
                '// note the request token
                Output.RequestToken = Me.RequestToken


                '// log the response object about to be sent back.
                Utility.WriteLogEntry(Output.SessionId, KerryAuditLoggingServices.LogTypes.Verbose, CreateXML(Output))
            Catch ex As Exception
                '// create a new empty response object to hold the error that was thrown
                Output = New Res()
                Output.Error = ex.ToString

                '// note the session ID if the request isnot empty (the caller may have passed a sessionId in)
                If Me IsNot Nothing Then
                    Output.SessionId = Me.SessionId
                    Output.RequestToken = Me.RequestToken
                End If

                '// log the error
                Utility.WriteLogEntry(Me.SessionId, KerryAuditLoggingServices.LogTypes.HandledException, CreateXML(Output))
            Finally
                If Output IsNot Nothing Then
                    '// note the amount of time it took to complete this request.
                    Output.CompletedInSeconds = sw.Elapsed.TotalSeconds
                End If
            End Try

            Return Output
        End Function
        Public MustOverride Function innerFillRequest() As Res


        ''' &lt;summary&gt;
        ''' This procedure will take the given object and create an XML string representing the
        ''' public properties of the given object.
        ''' &lt;/summary&gt;
        ''' &lt;param name="Obj"&gt;&lt;/param&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Function CreateXML(ByVal Obj As Object) As String
            Dim XML As String


            Dim xmlSer = New System.Xml.Serialization.XmlSerializer(Obj.GetType)
            Using ms As New System.IO.MemoryStream(1000)
                xmlSer.Serialize(ms, Obj)
                ms.Seek(0, IO.SeekOrigin.Begin)
                Dim reader As New System.IO.StreamReader(ms)
                XML = reader.ReadToEnd
            End Using

            Dim XMLElements = XElement.Parse(XML)
            ReplaceInvalidTags(XMLElements)


            Return XMLElements.ToString
        End Function

        ''' &lt;summary&gt;
        ''' This procedure should be modified to remove any tags you do not want to send to the log. 
        ''' Tags that hold byte arrays are good candidates for removal.
        ''' &lt;/summary&gt;
        ''' &lt;param name="Source"&gt;&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Sub ReplaceInvalidTags(ByVal Source As XElement)
            Dim Nodes = (From e In Source.Elements Select e).ToArray
            For Each n In Nodes
                Dim LoggingReplacement As String = GetLoggingReplacement(n)
                If String.IsNullOrEmpty(LoggingReplacement) Then
                    ReplaceInvalidTags(n)
                Else
                    n.Value = LoggingReplacement
                End If
            Next
        End Sub

        Protected Overridable Function GetLoggingReplacement(ByVal XMLElement As XElement) As String
            If XMLElement.Name.ToString = "SFDCSessionId" Then
                Return "[SFDC Session ID Hidden]"
            Else
                Return String.Empty
            End If
        End Function
    End Class
End Namespace</pre>

<p style="text-indent: 30pt;">
  The BaseRequest class is abstract, it must be inherited. But even cooler, it is a generic class. We can define it&#8217;s type dynamically. It implements iRequest, which just guarantees it some basic members:
</p>

<pre>Namespace ServiceManager
    Public Interface iRequest
        Property SessionId() As Long
        Property RequestToken() As String
    End Interface
End Namespace</pre>

<p style="text-indent: 30pt;">
  The &#8220;FillRequest&#8221; method is the conductor of this train. This is the method that the web services are actually calling. Right off the bat, if starts up a stop watch, so we can get performance indicators off of every web service call.
</p>

<p style="text-indent: 30pt;">
  If you remember back to that GetInitRequest class, we set the AutoCreateSessionIDAttribute. And this is where it gets used. If that attribute is set, then we know that this is an acceptable application start method. So we Log the application start up, which returns the Session ID. The Session ID, from this point on, should always be passed back and forth between the client and server, so that all interactions with this user for this execution of the application are under one ID. If for any reason, this Session ID is not set, we kill the process with an exception. This prevents applications from jumping into web service calls with out following the appropriate chain of processes.
</p>

<p style="text-indent: 30pt;">
  The RequestToken is a feature used for logging. It allows us to tie multiple log entries (say like a request, some server side process logs, and the response) into a single log entity. This is really only of use when viewing the log, so you can collapse multiple log entries into a single interaction.
</p>

<p style="text-indent: 30pt;">
  The &#8220;GetLoggingReplacement&#8221; is a block we use to replace chunks of text we don&#8217;t want to log. For instance, if the request contains large binary blocks (like uploading files) or sensitive information, we can set up string manipulations here to remove those items from the serialized request before it gets logged. This sub is usually overridden by the derived classes to add more method specific replacements.
</p>

### Until Next Time

<p style="text-indent: 30pt;">
  The projects continue to pile up. Everyone is trying to wrap stuff up before the holidays and new year, so I&#8217;ve got a lot on my plate. As I get more time, I&#8217;ll try to put up more on some of the other technical tricks we used inside of Silverlight. But this communication system, in conjunction with our Logging system, Unit tests, and having 2 developers on each project has dramatically helped us cut down on development time, reduce released bugs, and improved user acceptance of the applications.
</p>

-Rick