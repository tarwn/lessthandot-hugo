---
title: Using WNetGetConnectionA to get the UNCPath
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-19T10:29:00+00:00
ID: 1181
excerpt: |
  Introduction
  
  From time to time you want to let your users browse for a certain file and save that path somewhere. The users will usually go to their mapped folder and select a file from there. Then the filedialog will give you the path on that mapped&hellip;
url: /index.php/desktopdev/mstech/using-wnetgetconnectiona-to-get-the/
views:
  - 3023
categories:
  - Microsoft Technologies
  - VB.NET

---
## Introduction

From time to time you want to let your users browse for a certain file and save that path somewhere. The users will usually go to their mapped folder and select a file from there. Then the filedialog will give you the path on that mapped drive. But that mapping might not exists on another drive so we should better save the UNC path since we then no longer depend on the mapping to be there when we want to get the file the next time.

## The code

You can use a PInvoke to do this.

Here is the code I use for the moment and that seems to always work ;-). 

```vbnet
Private Declare Ansi Function WNetGetConnection Lib "mpr.dll" Alias "WNetGetConnectionA" (ByVal LocalName As String, ByVal RemoteName As System.Text.StringBuilder, ByRef Size As Int32) As Int32

		Private Function PathtoUnc(ByVal Path As String) As String
			Dim UNCName As New System.Text.StringBuilder(500)
			
			If Not Path Is Nothing AndAlso Path.Length &gt; 3 Then
				If Path.Substring(0, 1).ToUpper &gt; "E" And Path.Substring(0, 1).ToUpper &lt;= "Z" Then
					Dim ErrorCode = WNetGetConnection(Path.Substring(0, 2), UNCName, UNCName.Capacity)
					If ErrorCode = 0 AndAlso Not String.IsNullOrEmpty(UNCName.ToString.TrimStart) Then
						Path = System.IO.Path.Combine(UNCName.ToString.TrimStart, Path.Substring(3)).ToString()
					End If
				End If
			End If
			Return Path
		End Function```
It will search for the UNC when possible and if not it will just return what you gave it.

I used WNetGetConnection for this. WNetGetConnection accepts three parameters. [According to MSDN][1].

> Parameters
> 
> lpLocalName [in]
> 
> Pointer to a constant null-terminated string that specifies the name of the local device to get the network name for.
  
> lpRemoteName [out]
> 
> Pointer to a null-terminated string that receives the remote name used to make the connection.
  
> lpnLength [in, out]
> 
> Pointer to a variable that specifies the size of the buffer pointed to by the lpRemoteName parameter, in characters. If the function fails because the buffer is not large enough, this parameter returns the required buffer size. 

And should return NO_ERROR or 0 when all goes well and I don&#8217;t care for any other errors so there. You can find all the errorcodes you want [here][2]. 

And yes ERROR_SUCCES means that you had no error, which is weird.

> ERROR_SUCCESS 0 (0x0) = The operation completed successfully.

## Conclusion

Uhm, it works.

 [1]: http://msdn.microsoft.com/en-us/library/aa385453%28v=vs.85%29.aspx
 [2]: http://msdn.microsoft.com/en-us/library/ms681382%28v=vs.85%29.aspx