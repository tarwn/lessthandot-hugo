---
title: Resharper Template Nunit TestFixture for VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2008-05-17T06:00:52+00:00
ID: 29
url: /index.php/desktopdev/mstech/resharper-template-nunit-testfixture-for/
views:
  - 1311
categories:
  - Microsoft Technologies

---
I made a file template for resharper to make it easier to create a TestClass.

It looks like this.

```
Imports Nunit.FrameWork

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
    
            End Sub

        ''' &lt;summary&gt;
         ''' Tears down the test. Is executed after the Test is Completed
          ''' &lt;/summary&gt;
           ''' &lt;remarks&gt;&lt;/remarks&gt;
         &lt;TearDown()&gt; _
          Public Sub TearDown()
            
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
I also made an xmlFile so you can easily import it into Resharper.

[NunitTestFixture.xml][1]

 [1]: http://blog.baesonline.com/content/binary/NunitTestFixture.xml