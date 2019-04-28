---
title: Exceptions can send you on the wrong path
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-13T07:00:43+00:00
ID: 427
url: /index.php/desktopdev/mstech/exceptions-can-send-you-on-the-wrong-pat/
views:
  - 4433
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - .net
  - tests

---
This morning I got this exception when running my build on the build server.

> <code class="codespan">Command Line: "f:Source TexdatabasesProjectsTDB2007.Dal.DataAccessLayer.TestbinDebugTDB2007.Dal.DataAccessLayer.Test.dll" /config="f:Visual Studio 2005ProjectsTDB2007ProjectsMenubinDebugDataAccessLayer.dll.config" /nologo&lt;br />
Unhandled Exception:&lt;br />
System.IO.FileLoadException: Could not load file or assembly 'nunit.core, Version=2.4.6.0, Culture=neutral, PublicKeyToken=96d09a1eb7f44a77' or one of its dependencies. Exception from HRESULT: 0x80131902&lt;br />
File name: 'nunit.core, Version=2.4.6.0, Culture=neutral, PublicKeyToken=96d09a1eb7f44a77' ---&gt; System.Configuration.ConfigurationErrorsException: Configuration system failed to initialize ---&gt; System.Configuration.ConfigurationErrorsException: 'NUnitCategories.Categories.nHibernate' is an unexpected token. The expected token is '"' or '''. Line 31, position 18. (f:Source TexdatabasesProjectsTDB2007.Dal.DataAccessLayer.TestbinDebugTDB2007.Dal.DataAccessLayer.Test.dll.config line 31) ---&gt; System.Xml.XmlException: 'NUnitCategories.Categories.nHibernate' is an unexpected token. The expected token is '"' or '''. Line 31, position 18.&lt;br />
   at System.Xml.XmlTextReaderImpl.Throw(Exception e)&lt;br />
   at System.Xml.XmlTextReaderImpl.Throw(String res, String[] args)&lt;br />
   at System.Xml.XmlTextReaderImpl.ThrowUnexpectedToken(String expectedToken1, String expectedToken2)&lt;br />
   at System.Xml.XmlTextReaderImpl.ParseAttributes()&lt;br />
   at System.Xml.XmlTextReaderImpl.ParseElement()&lt;br />
   at System.Xml.XmlTextReaderImpl.ParseElementContent()&lt;br />
   at System.Xml.XmlTextReaderImpl.Read()&lt;br />
   at System.Xml.XmlTextReader.Read()&lt;br />
   at System.Xml.XmlTextReaderImpl.Skip()&lt;br />
   at System.Xml.XmlTextReader.Skip()&lt;br />
   at System.Configuration.XmlUtil.StrictSkipToNextElement(ExceptionAction action)&lt;br />
   at System.Configuration.BaseConfigurationRecord.ScanSectionsRecursive(XmlUtil xmlUtil, String parentConfigKey, Boolean inLocation, String locationSubPath, OverrideModeSetting overrideMode, Boolean skipInChildApps)&lt;br />
   at System.Configuration.BaseConfigurationRecord.ScanSections(XmlUtil xmlUtil)&lt;br />
   at System.Configuration.BaseConfigurationRecord.InitConfigFromFile()&lt;br />
   --- End of inner exception stack trace ---&lt;br />
   at System.Configuration.ConfigurationSchemaErrors.ThrowIfErrors(Boolean ignoreLocal)&lt;br />
   at System.Configuration.BaseConfigurationRecord.ThrowIfParseErrors(ConfigurationSchemaErrors schemaErrors)&lt;br />
   at System.Configuration.BaseConfigurationRecord.ThrowIfInitErrors()&lt;br />
   at System.Configuration.ClientConfigurationSystem.EnsureInit(String configKey)&lt;br />
   --- End of inner exception stack trace ---&lt;br />
   at System.Configuration.ClientConfigurationSystem.EnsureInit(String configKey)&lt;br />
   at System.Configuration.ClientConfigurationSystem.System.Configuration.Internal.IInternalConfigSystem.GetSection(String sectionName)&lt;br />
   at System.Configuration.ConfigurationManager.GetSection(String sectionName)&lt;br />
   at System.Configuration.PrivilegedConfigurationManager.GetSection(String sectionName)&lt;br />
   at System.Diagnostics.DiagnosticsConfiguration.GetConfigSection()&lt;br />
   at System.Diagnostics.DiagnosticsConfiguration.Initialize()&lt;br />
   at System.Diagnostics.DiagnosticsConfiguration.get_IndentSize()&lt;br />
   at System.Diagnostics.TraceInternal.InitializeSettings()&lt;br />
   at System.Diagnostics.TraceInternal.WriteLine(String message, String category)&lt;br />
   at System.Diagnostics.Trace.WriteLine(String message, String category)&lt;br />
   at NUnit.Core.AssemblyResolver.CurrentDomain_AssemblyResolve(Object sender, ResolveEventArgs args)&lt;br />
   at System.AppDomain.OnAssemblyResolveEvent(String assemblyFullName)&lt;br />
   at System.Reflection.Assembly._nLoad(AssemblyName fileName, String codeBase, Evidence assemblySecurity, Assembly locationHint, StackCrawlMark& stackMark, Boolean throwOnFileNotFound, Boolean forIntrospection)&lt;br />
   at System.Reflection.Assembly.nLoad(AssemblyName fileName, String codeBase, Evidence assemblySecurity, Assembly locationHint, StackCrawlMark& stackMark, Boolean throwOnFileNotFound, Boolean forIntrospection)&lt;br />
   at System.Reflection.Assembly.InternalLoad(AssemblyName assemblyRef, Evidence assemblySecurity, StackCrawlMark& stackMark, Boolean forIntrospection)&lt;br />
   at System.Reflection.Assembly.InternalLoad(String assemblyString, Evidence assemblySecurity, StackCrawlMark& stackMark, Boolean forIntrospection)&lt;br />
   at System.Activator.CreateInstance(String assemblyName, String typeName, Boolean ignoreCase, BindingFlags bindingAttr, Binder binder, Object[] args, CultureInfo culture, Object[] activationAttributes, Evidence securityInfo, StackCrawlMark& stackMark)&lt;br />
   at System.Activator.CreateInstance(String assemblyName, String typeName, Boolean ignoreCase, BindingFlags bindingAttr, Binder binder, Object[] args, CultureInfo culture, Object[] activationAttributes, Evidence securityInfo)&lt;br />
   at System.AppDomain.CreateInstance(String assemblyName, String typeName, Boolean ignoreCase, BindingFlags bindingAttr, Binder binder, Object[] args, CultureInfo culture, Object[] activationAttributes, Evidence securityAttributes)&lt;br />
   at System.AppDomain.CreateInstanceAndUnwrap(String assemblyName, String typeName, Boolean ignoreCase, BindingFlags bindingAttr, Binder binder, Object[] args, CultureInfo culture, Object[] activationAttributes, Evidence securityAttributes)&lt;br />
   at System.AppDomain.CreateInstanceAndUnwrap(String assemblyName, String typeName, Boolean ignoreCase, BindingFlags bindingAttr, Binder binder, Object[] args, CultureInfo culture, Object[] activationAttributes, Evidence securityAttributes)&lt;br />
   at NUnit.Util.TestDomain.MakeRemoteTestRunner(AppDomain runnerDomain)&lt;br />
   at NUnit.Util.TestDomain.Load(TestPackage package)&lt;br />
   at NUnit.ConsoleRunner.ConsoleUi.MakeRunnerFromCommandLine(ConsoleOptions options)&lt;br />
   at NUnit.ConsoleRunner.ConsoleUi.Execute(ConsoleOptions options)&lt;br />
   at NUnit.ConsoleRunner.Runner.Main(String[] args)&lt;br />
</code>

I was focussing on this <span class="MT_blue">&#8216;NUnitCategories.Categories.nHibernate&#8217; is an unexpected token. The expected token is &#8216;&#8221;&#8216; or &#8221;&#8217;.</span> which to me meant that the error was somewhere in the CategoryAttribute somewhere Like in this case <span class="MT_blue"><code class="codespan">&lt;Category(Categories.nHibernate)&gt; _</code></span>.

But it wasn&#8217;t when changing the categories to a more refacotr frienldy version, like I mentioned before, I did a wrong find and replace. I namely replace &#8220;nHibernate&#8221; with <span class="MT_blue">NUnitCategories.Categories.nHibernate</span> and I also seem to have this little nHibernate word in the app.config file I use in this project.

Which now was this 

```xml
&lt;logger name=NUnitCategories.Categories.nHibernate&gt;
      &lt;level value="Warn" /&gt;
      &lt;appender-ref ref="rollingFile"/&gt;
    &lt;/logger&gt;```
Instead of this

```
&lt;logger name="NHibernate"&gt;
      &lt;level value="Warn" /&gt;
      &lt;appender-ref ref="rollingFile"/&gt;
    &lt;/logger&gt;```
Of course that was the real cause of that exception.

The weird thing is that the resharper testrunner just doesn&#8217;t run the tests but doesn&#8217;t give an error either (no where visible anyway) but the Tesdriven.Net does give a suitable error.

> &#8212;&#8212; Test started: Assembly: TDB2007.Dal.DataAccessLayer.Test.dll &#8212;&#8212;
> 
> Check your &#8216;App.config&#8217; file.
  
> NUnitCategories.Categories.nHibernate is een onverwacht token. Het verwachte token is &#8221; of &#8216;. Regel 31, positie 18.

So it&#8217;s always good to have more then one testrunner to get the correct message so you can solve it quickly.