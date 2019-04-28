---
title: Resharper File template for Nunit testing with Structuremap support for VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2008-05-17T05:59:28+00:00
ID: 28
url: /index.php/desktopdev/mstech/vbnet/resharper-file-template-for-nunit-testin/
views:
  - 2033
categories:
  - VB.NET

---
So I also made the a similar template with structuremap support included.

Which looks something like this

```
Imports Nunit.FrameWork
Imports StructureMap

Namespace $NAMESPACE$
    ''' &lt;summary&gt;
     ''' A TestClass
      ''' &lt;/summary&gt;
       ''' &lt;remarks&gt;&lt;/remarks&gt;
     &lt;TestFixture()&gt; _
     Public Class $CLASSNAME$
    
#Region " Setup and TearDown "
        ''' &lt;summary&gt;
         ''' Sets up the Tests
          ''' &lt;/summary&gt;
           ''' &lt;remarks&gt;&lt;/remarks&gt;
         &lt;Setup()&gt; _
          Public Sub Setup()
                       StructureMapConfiguration.UseDefaultStructureMapConfigFile = False
                   StructureMapConfiguration.ScanAssemblies.IncludeTheCallingAssembly()
            End Sub

        ''' &lt;summary&gt;
         ''' Tears down the test. Is executed after the Test is Completed
          ''' &lt;/summary&gt;
           ''' &lt;remarks&gt;&lt;/remarks&gt;
         &lt;TearDown()&gt; _
          Public Sub TearDown()
                ObjectFactory.ResetDefaults()        
             End Sub      
#End Region     

#Region " Tests "
                   ''' &lt;summary&gt;
         ''' A Test
          ''' &lt;/summary&gt;
           ''' &lt;remarks&gt;&lt;/remarks&gt;
             &lt;Test()&gt; _
              Public Sub $Test_Name$()
           
               End Sub
#End Region

     End Class
End Namespace```
And of course I also made an XmlFile so you can easily import it.

[NunitTestFixtureWithStructureMapSupport.xml][1]

 [1]: http://blog.baesonline.com/content/binary/NunitTestFixtureWithStructureMapSupport.xml