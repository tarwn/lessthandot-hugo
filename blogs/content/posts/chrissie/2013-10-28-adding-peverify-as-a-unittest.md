---
title: Adding PEVerify as a unittest
author: Christiaan Baes (chrissie1)
type: post
date: 2013-10-28T08:34:00+00:00
ID: 2187
excerpt: Run PEVerify as a unittest.
url: /index.php/desktopdev/mstech/adding-peverify-as-a-unittest/
views:
  - 9106
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - anotar
  - peverify

---
## Introduction

Last week I found a little bug caused by [Anotar][1]. Anotar adds IL code to my assembly. The problem with this bug is that it only manifests itself at runtime or when covered by a test or when doing a PEVerify. Since I don&#8217;t claim to have 100% test coverage. I need a way to run PEVErify on my assemblies to make sure I catch any Anotar bugs (So I can report them and Simon can fix them). 

When I mentioned this on twitter, the always brilliant Simon Cropp pointed me to his [Verifier class][2] that you can find in the sourcecode of Anotar. 

## The code

And here is my current, slightly reworked version of his code.

```vbnet
Imports System
Imports System.Diagnostics
Imports System.IO
Imports NUnit.Framework

Namespace PeVerify
    Public Class Verifier

        Public Shared Sub Verify(assemblyPath As String, Optional ByVal errorCodesToIgnore As String = "")
            Dim before = Validate(assemblyPath, errorCodesToIgnore)
            Assert.IsTrue(File.Exists(assemblyPath), "Assembly not found: " & assemblyPath)
            Assert.IsFalse(before.Contains("Error"), before)
        End Sub

        Private Shared Function Validate(assemblyPath As String, Optional ByVal errorCodesToIgnore As String = "") As String
            Dim exePath = GetPathToPeVerify()
            If (Not File.Exists(exePath)) Then
                Return String.Empty
            End If
            Dim arguments = String.Format("""{0}""", assemblyPath)
            If Not String.IsNullOrEmpty(errorCodesToIgnore) Then
                arguments = String.Format("{0} /ignore={1}", arguments, errorCodesToIgnore)
            End If
            Dim process1 = Process.Start(New ProcessStartInfo(exePath, arguments) With {.RedirectStandardOutput = True, .UseShellExecute = False, .CreateNoWindow = True})
            process1.WaitForExit(10000)
            Return process1.StandardOutput.ReadToEnd().Trim().Replace(assemblyPath, "")
        End Function

        Private Shared Function GetPathToPeVerify() As String
            Dim exePath = Environment.ExpandEnvironmentVariables("%programfiles(x86)%Microsoft SDKsWindowsv7.0ABinNETFX 4.0 ToolsPEVerify.exe")
            If (Not File.Exists(exePath)) Then
                exePath = Environment.ExpandEnvironmentVariables("%programfiles(x86)%Microsoft SDKsWindowsv8.0ABinNETFX 4.0 ToolsPEVerify.exe")
            End If
            Return exePath
        End Function
        
    End Class
End Namespace```
This does not work on either the ncrunch or resharper testrunner since those use their ownversion of the assembly. But it works on Teamcity, which is the important part for me since I don&#8217;t want to let bugs out in the wild.

You can use it like this.

```vbnet
&lt;Test&gt;
        Public Sub PeVerifyAssembly()
            Dim filePath = Assembly.GetAssembly(GetType(SomeTypeInYourAssembly)).Location
            Verifier.Verify(filePath)
        End Sub```
Or in one case where I had an error like this you can just ignore the cannot find file error. 

<span class="MT_red">[IL]: Error: [ : TexDatabases2012.Menu.Facade.MessagesFacade::set__newtransfers] [HRESULT 0x80070002] &#8211; The system cannot find the file specified. [</span>

```vbnet
&lt;Test&gt;
        Public Sub PeVerifyAssembly()
            Dim filePath = Assembly.GetAssembly(GetType(SomeTypeInYourAssembly)).Location
            Verifier.Verify(filePath, "0x80070002")
        End Sub```
## Conclusion

Adding a PEVerify step when working with software like Fody seems to be a good thing.

And I would also like to thank Simon for his awesome support.

 [1]: https://github.com/Fody/Anotar
 [2]: https://github.com/Fody/Anotar/blob/master/Common/Verifier.cs