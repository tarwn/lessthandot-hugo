---
title: Nancy razorview to PDF
author: Christiaan Baes (chrissie1)
type: post
date: 2014-06-23T06:49:32+00:00
ID: 2777
excerpt: 'Nancy razorviews to pdf using ITextSharp and HtlmAgilityPack. '
url: /index.php/uncategorized/nancy-razorview-to-pdf/
views:
  - 3135
categories:
  - Uncategorized

---
Sometimes one comes up with the most silly of requirements. This time an external consultant wanted the documentation for my <a href="http://nancyfx.org/" target="_blank">nancy </a>services. I have that documentation as webpages made with razorviews in nancy. Only a very small part is dynamic, most of it is static. The consultant does not have acces to those pages so he needed them in a portable format. I could have used a webscraper, but those sites are blocked here at work. So I decided I could perhaps make them into a PDF file. And look mommy , I did it.

> Of course this method is not limited to just any old razorview, this method could be used for all the views nancy makes.

It&#8217;s actually very easy to turn a razorview into a pdf as shown by <a href="http://shanemitchell.ca/prerendering-a-view/" title="Prerendering a view." target="_blank">Shane Mitchell on his blog</a>. 

In essence it is this part that does the rendering and it uses <a href="http://sourceforge.net/projects/itextsharp/" target="_blank">ITextsharp</a>. <pre lang=vbnet>Dim response = New StreamResponse(Function() Dim pdfOutput = New MemoryStream() Dim document = New Document(PageSize.A4, 30, 30, 30, 30) Dim writer = PdfWriter.GetInstance(document, pdfOutput) writer.CloseStream = False If Not document.IsOpen() Then document.Open() For Each c In content XMLWorkerHelper.GetInstance.ParseXHtml(writer, document, New StringReader(c)) Next document.Close() pdfOutput.Seek(0, SeekOrigin.Begin) Return pdfOutput End If Return Nothing End Function, "application/pdf")</pre> 

The thing is that ITextsharp and the XMLWorker support CSS but it is very limited. It for one does not allow you to set an element to display none, and I really needed to eliminate the menus and headers and footer. But for that I can use the <a href="http://htmlagilitypack.codeplex.com/" target="_blank">HtmlAgilityPack</a>.

Here is the complete code. It reads all the razorviews in the documentation folder, renders it. Removes a few elements, mainly the header and the menu. And then throws them all together in a pdf. And now I can give that to all my little friends. <pre lang=vbnet> Public Sub New(viewlocator As IViewLocator) MyBase.Get("pdf") = Function(parameters) Dim viewLocactionContext = New ViewLocationContext() With {.Context = Context, .ModuleName = "Test", .ModulePath = ""} Dim razorViews = viewlocator.GetAllCurrentlyDiscoveredViews().Where(Function(x) x.Location = "Views/documentation").Select(Function(x) x.Location.Replace("Views/", "") & "/" & x.Name) Dim content = New List(Of String) Try For Each razorView In razorViews Dim precontent = "" Dim rendered = ViewFactory.RenderView(razorView, Nothing, viewLocactionContext) Using ms = New MemoryStream() rendered.Contents.Invoke(ms) ms.Seek(0, SeekOrigin.Begin) Using reader = New StreamReader(ms) precontent = reader.ReadToEnd End Using End Using Dim doc = New HtmlDocument() doc.LoadHtml(precontent) doc.OptionOutputAsXml = True If doc.DocumentNode.SelectNodes("//header") IsNot Nothing Then For Each link In doc.DocumentNode.SelectNodes("//header").ToList link.Remove() Next End If If doc.DocumentNode.SelectNodes("//div[@id='menu']") IsNot Nothing Then For Each link In doc.DocumentNode.SelectNodes("//div[@id='menu']").ToList link.Remove() Next End If doc.OptionStopperNodeName = "" If doc.DocumentNode.SelectSingleNode("//div[@id='wrapper']") IsNot Nothing Then content.Add(doc.DocumentNode.SelectSingleNode("//div[@id='wrapper']").InnerHtml) End If Next Catch ex As Exception Return ex.Message & ControlChars.CrLf & ex.StackTrace End Try Dim response = New StreamResponse(Function() Dim pdfOutput = New MemoryStream() Dim document = New Document(PageSize.A4, 30, 30, 30, 30) Dim writer = PdfWriter.GetInstance(document, pdfOutput) writer.CloseStream = False If Not document.IsOpen() Then document.Open() For Each c In content XMLWorkerHelper.GetInstance.ParseXHtml(writer, document, New StringReader(c)) Next document.Close() pdfOutput.Seek(0, SeekOrigin.Begin) Return pdfOutput End If Return Nothing End Function, "application/pdf") Return response End Function End Sub</pre> 

ANd yes this is an extremely quick and dirty solution. 

But it has a test.<pre lang=vbnet> Public Class TestDocumentationModule Private _browser As Browser <SetUp()> Public Sub FixtureSetup() Dim configuration = A.Fake(Of IRazorConfiguration)() Dim textResource = A.Fake(Of ITextResource)() Dim bootstrapper = New ConfigurableBootstrapper(Sub(config) config.Module(Of BecareServices.Modules.DocumentationModule)() config.RootPathProvider(Of RootPathProvider)() config.Dependency(Of ITextResource)(textResource) config.ViewEngine(New RazorViewEngine(configuration)) End Sub) _browser = New Browser(bootstrapper) End Sub <Test> Public Sub IfDocumentationAsPdfIsCreated() Dim result = _browser.Get("/documentation/pdf", Sub(x) x.HttpRequest() End Sub) Assert.AreEqual(result.ContentType, "application/pdf") End Sub End Class</pre> 

Tests always make everything better.