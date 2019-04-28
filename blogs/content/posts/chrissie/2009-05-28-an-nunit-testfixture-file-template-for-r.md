---
title: An nUnit testfixture file template for resharper that also conforms to stylecop laws
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-28T07:35:45+00:00
ID: 448
url: /index.php/desktopdev/mstech/an-nunit-testfixture-file-template-for-r/
views:
  - 7294
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'
  - nunit
  - resharper

---
Like the title says An nUnit testfixture file template for resharper that also conforms to stylecop laws. Here it is. 

```csharp
// --------------------------------------------------------------------------------------------------------------------
// &lt;copyright file="$FileName$" company="$Company$"&gt;
//   $Author$
// &lt;/copyright&gt;
// &lt;summary&gt;
//   Tests the $FIXTURE$ type.
// &lt;/summary&gt;
// --------------------------------------------------------------------------------------------------------------------

namespace $NAMESPACE$
{
    using NUnit.Framework;

    /// &lt;summary&gt;
    /// Test for $FIXTURE$
    /// &lt;/summary&gt;
    [TestFixture]
    public class Test$FIXTURE$
    {
        /// &lt;summary&gt;
        /// Test $TestName$
        /// &lt;/summary&gt;
        [Test]
        public void $TestName$()
        {
        }
    }
}```
That&#8217;s the way I like them and no more stylecop warnings. Since Stylecop doesn&#8217;t exist in VB.Net I don&#8217;t have the equivalent.

This is the xml. Just copy paste this in a file and then import it in resharper file templates.

```xml
&lt;TemplatesExport family="File Templates"&gt;
  &lt;Template uid="3a8692a1-37f3-4d44-9310-639548a1d876" shortcut="" description="NUnit Test Fixture" text="// --------------------------------------------------------------------------------------------------------------------&#xD;&#xA;// &lt;copyright file=&quot;$FileName$&quot; company=&quot;$Company$&quot;&gt;&#xD;&#xA;//   $Author$&#xD;&#xA;// &lt;/copyright&gt;&#xD;&#xA;// &lt;summary&gt;&#xD;&#xA;//   Tests the $FIXTURE$ type.&#xD;&#xA;// &lt;/summary&gt;&#xD;&#xA;// --------------------------------------------------------------------------------------------------------------------&#xD;&#xA;&#xD;&#xA;namespace $NAMESPACE$&#xD;&#xA;{&#xD;&#xA;    using NUnit.Framework;&#xD;&#xA;&#xD;&#xA;    /// &lt;summary&gt;&#xD;&#xA;    /// Test for $FIXTURE$&#xD;&#xA;    /// &lt;/summary&gt;&#xD;&#xA;    [TestFixture]&#xD;&#xA;    public class Test$FIXTURE$&#xD;&#xA;    {&#xD;&#xA;        /// &lt;summary&gt;&#xD;&#xA;        /// Test $TestName$&#xD;&#xA;        /// &lt;/summary&gt;&#xD;&#xA;        [Test]&#xD;&#xA;        public void $TestName$()&#xD;&#xA;        {&#xD;&#xA;        }&#xD;&#xA;    }&#xD;&#xA;}" reformat="True" shortenQualifiedReferences="True"&gt;
    &lt;Context&gt;
      &lt;ProjectLanguageContext language="CSharp" /&gt;
    &lt;/Context&gt;
    &lt;Categories /&gt;
    &lt;Variables&gt;
      &lt;Variable name="NAMESPACE" expression="fileDefaultNamespace()" initialRange="-1" /&gt;
      &lt;Variable name="FileName" expression="getFileName()" initialRange="0" /&gt;
      &lt;Variable name="Company" expression="constant(&quot;NICC&quot;)" initialRange="0" /&gt;
      &lt;Variable name="FIXTURE" expression="getFileNameWithoutExtension()" initialRange="-1" /&gt;
      &lt;Variable name="Author" expression="getFullUserName()" initialRange="0" /&gt;
      &lt;Variable name="TestName" expression="" initialRange="0" /&gt;
    &lt;/Variables&gt;
    &lt;CustomProperties&gt;
      &lt;Property key="FileName" value="Test" /&gt;
      &lt;Property key="Extension" value="cs" /&gt;
      &lt;Property key="ValidateFileName" value="False" /&gt;
    &lt;/CustomProperties&gt;
  &lt;/Template&gt;
&lt;/TemplatesExport&gt;```