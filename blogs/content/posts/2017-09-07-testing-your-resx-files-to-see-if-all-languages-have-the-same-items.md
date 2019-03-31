---
title: Testing your resx files to see if all languages have the same items.
author: Christiaan Baes (chrissie1)
type: post
date: 2017-09-07T12:48:58+00:00
url: /index.php/uncategorized/testing-your-resx-files-to-see-if-all-languages-have-the-same-items/
rp4wp_auto_linked:
  - 1
views:
  - 1661
categories:
  - Uncategorized

---
It&#8217;s one of those things you need to when writing multilingual applications. Create resource file (resx) for each language you want to support. And then you add an item and forget to add it to one of language files and then, oops empty label. 

We don&#8217;t want that. 

And we write tests, so why not write a test for that. 

And on [Stackoverflow][1] the user [TiltonJH][2] was so kind to provide me with the answer. 

I translated it to VB.Net code and changed a small thing (the resourcemanager didn&#8217;t find the resourcesets but the resourcemaager from the type did, so I pass that in. 

<pre>Imports System.Globalization
Imports System.Reflection
Imports System.Resources
Imports System.Text
Imports Nancy.Testing

Namespace Resources

    Public Class ResourceTester

        Public Shared Sub TestResxForInconsistencies(type As Type, resourceManager As ResourceManager)
            If type Is Nothing Then Throw New ArgumentNullException(NameOf(type))
            Dim cultureResourceDictionaries = GetResxDictionaries(type, resourceManager)
            Dim emptyEntries = GetEmpty(cultureResourceDictionaries)
            Dim neutralLanguage = ExtractNeutralLanguage(cultureResourceDictionaries, type)
            Dim missingEntries = GetMissing(cultureResourceDictionaries, neutralLanguage)
            Dim dispensableEntries = GetDispensable(cultureResourceDictionaries, neutralLanguage)
            If (emptyEntries.Count &gt; 0 OrElse missingEntries.Count &gt; 0 OrElse dispensableEntries.Count &gt; 0) Then
                Dim message = New StringBuilder()
                message.AppendFormat("Found resx errors in ""{0}"":", type)
                message.AppendLine()
                message.AppendLine()
                Append(message, emptyEntries, " Empty Entries ", "Entries which do not have a value.")
                Append(message, missingEntries, " Missing Entries ", "Entries which are specified in the neutral language but are missing in the specified language.")
                Append(message, dispensableEntries, " Dispensable Entries ", "Entries which are not specified in the neutral language but are present in the specified language and should be removed.")
                Throw New Nunit.Framework.AssertionException(message.ToString())
            End If
        End Sub

        Private Shared Sub Append(message As StringBuilder, entries As Dictionary(Of String, List(Of String)), headline As String, description As String)
            If entries.Count &gt; 0 Then
                message.AppendLine(headline)
                message.AppendLine(New String("=", headline.Length))
                message.Append("(")
                message.Append(description)
                message.AppendLine(")")
                For Each pair In entries
                    Dim languageName = pair.Key
                    If String.IsNullOrEmpty(languageName) Then
                        languageName = "&lt;neutral language&gt;"
                    End If
                    Dim line = String.Format("  Language: {0}  ", languageName)
                    message.AppendLine(line)
                    message.AppendLine(New String("-", line.Length))
                    For Each key In pair.Value
                        message.Append("\t")
                        message.AppendLine(key)
                    Next
                Next
                message.AppendLine()
            End If
        End Sub

        Private Shared Function ExtractNeutralLanguage(resxs As Dictionary(Of String, Dictionary(Of String, Object)), type As Type) As Dictionary(Of String, Object)
            Dim neutralLanguage = New Dictionary(Of String, Object)
            If Not resxs.TryGetValue(String.Empty, neutralLanguage) Then Throw New AssertException(String.Format("The neutral language is not specified in ""{0}"".", type))
            resxs.Remove(String.Empty)
            Return neutralLanguage
        End Function

        Private Shared Function GetAvailableResxCultureInfos(assembly As Assembly) As CultureInfo()
            Dim assemblyResxCultures = New HashSet(Of CultureInfo)()
            assemblyResxCultures.Add(CultureInfo.InvariantCulture)
            Dim names = assembly.GetManifestResourceNames()
            If names IsNot Nothing AndAlso names.Length &gt; 0 Then
                Dim allCultures = CultureInfo.GetCultures(CultureTypes.AllCultures)
                Const resourcesEnding As String = ".resources"
                For i = 0 To names.Length - 1
                    Dim name = names(i)
                    If String.IsNullOrWhiteSpace(name) OrElse name.Length &lt;= resourcesEnding.Length OrElse Not name.EndsWith(resourcesEnding, StringComparison.InvariantCultureIgnoreCase) Then
                        Continue For
                    End If
                    name = name.Remove(name.Length - resourcesEnding.Length, resourcesEnding.Length)
                    If (String.IsNullOrWhiteSpace(name)) Then
                        Continue For
                    End If
                    Dim resourceManager = New ResourceManager(name, assembly)
                    For j = 0 To allCultures.Length - 1
                        Dim culture = allCultures(j)
                        Try
                            If (culture.Equals(CultureInfo.InvariantCulture)) Then
                                Continue For
                            End If
                            Using resourceSet = resourceManager.GetResourceSet(culture, True, False)
                                If (resourceSet IsNot Nothing) Then
                                    assemblyResxCultures.Add(culture)
                                End If
                            End Using
                        Catch ex As CultureNotFoundException

                        End Try
                    Next
                Next
            End If
            Return assemblyResxCultures.ToArray()
        End Function

        Private Shared Function GetResxDictionaries(type As Type, resourceManager As resourceManager) As Dictionary(Of String, Dictionary(Of String, Object))
            Dim availableResxsCultureInfos = GetAvailableResxCultureInfos(type.Assembly)
            Dim resxDictionaries = New Dictionary(Of String, Dictionary(Of String, Object))()
            For i = 0 To availableResxsCultureInfos.Length - 1
                Dim cultureInfo = availableResxsCultureInfos(i)
                Try
                    Dim resourceSet = resourceManager.GetResourceSet(cultureInfo, True, True)
                    If resourceSet IsNot Nothing Then
                        Dim dict = New Dictionary(Of String, Object)()
                        For Each item In resourceSet
                            Dim key = item.Key.ToString()
                            Dim value = item.Value
                            dict.Add(key, value)
                        Next
                        resxDictionaries.Add(cultureInfo.Name, dict)
                    End If
                Catch ex As Exception

                End Try
               
            Next
            Return resxDictionaries
        End Function

        Private Shared Function GetDispensable(resxDictionaries As Dictionary(Of String, Dictionary(Of String, Object)), neutralLanguage As Dictionary(Of String, Object)) As Dictionary(Of String, List(Of String))
            Dim dispensable = New Dictionary(Of String, List(Of String))()
            For Each pair In resxDictionaries
                Dim resxs = pair.Value
                Dim list = New List(Of String)()
                For Each key In resxs.Keys
                    If Not neutralLanguage.ContainsKey(key) Then
                        list.Add(key)
                    End If
                Next
                If (list.Count &gt; 0) Then
                    dispensable.Add(pair.Key, list)
                End If
            Next
            Return dispensable
        End Function

        Private Shared Function GetEmpty(resxDictionaries As Dictionary(Of String, Dictionary(Of String, Object))) As Dictionary(Of String, List(Of String))
            Dim empty = New Dictionary(Of String, List(Of String))()
            For Each pair In resxDictionaries
                Dim resxs = pair.Value
                Dim list = New List(Of String)()
                For Each entrie In resxs
                    If entrie.Value Is Nothing Then
                        list.Add(entrie.Key)
                    End If
                    Dim stringValue = entrie.Value
                    If (String.IsNullOrWhiteSpace(stringValue)) Then
                        list.Add(entrie.Key)
                    End If
                Next
                If (list.Count &gt; 0) Then
                    empty.Add(pair.Key, list)
                End If
            Next
            Return empty
        End Function

        Private Shared Function GetMissing(resxDictionaries As Dictionary(Of String, Dictionary(Of String, Object)), neutralLanguage As Dictionary(Of String, Object)) As Dictionary(Of String, List(Of String))
            Dim missing = New Dictionary(Of String, List(Of String))()
            For Each pair In resxDictionaries
                Dim resxs = pair.Value
                Dim list = New List(Of String)()
                For Each key In neutralLanguage.Keys
                    If Not resxs.ContainsKey(key) Then
                        list.Add(key)
                    End If
                Next
                If list.Count &gt; 0 Then missing.Add(pair.Key, list)
            Next
            Return missing
        End Function

    End Class
End Namespace</pre>

ANd now my test looks like this. 

<pre>Imports NUnit.Framework                 

Namespace Resources
    Public Class TestReportResource

        &lt;Test&gt;
        Public Sub TestResx
            ResourceTester.TestResxForInconsistencies(Gettype(BI.My.Resources.Report), BI.My.Resources.Report.ResourceManager)
        End Sub
        
    End Class
End NameSpace</pre>

And when it failes it looks like this. 

<img src="http://blogs.ltd.local/wp-content/uploads/2017/09/resx.png" alt="resx" width="850" height="400" class="alignnone size-full wp-image-8801" srcset="http://blogs.ltd.local/wp-content/uploads/2017/09/resx.png 850w, http://blogs.ltd.local/wp-content/uploads/2017/09/resx-300x141.png 300w, http://blogs.ltd.local/wp-content/uploads/2017/09/resx-768x361.png 768w" sizes="(max-width: 850px) 100vw, 850px" />

So there you go, simple.

 [1]: https://stackoverflow.com/a/41760659
 [2]: https://stackoverflow.com/users/3733965/tiltonjh