---
title: “Connect” an Excel 2010 Slicer to an unrelated object.
author: joshuafennessy
type: post
date: 2012-07-24T01:15:00+00:00
ID: 1679
excerpt: 'Slicers are super cool!  By extending the power of filtering to apply to multiple PivotTables (or PivotCharts) they have really increas ed the reporting and data delivery power of Excel 2010.  Not only do they look cool and are fun to interact with, the&hellip;'
url: /index.php/datamgmt/datadesign/connect-a-slicer-to-an/
views:
  - 40401
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Excel Reporting

---
<p class="MsoNormal">
  <span style="font-family: tahoma,arial,helvetica,sans-serif;">Slicers are sup</span><span style="font-family: tahoma,arial,helvetica,sans-serif;"><a href="/media/users/joshuafennessy/Slicer.png?mtime=1343099573"><img style="float: left;" src="/wp-content/uploads/users/joshuafennessy/Slicer.png?mtime=1343099573" alt="" width="167" height="223" /></a></span><span style="font-family: tahoma,arial,helvetica,sans-serif;">er cool!  By extending the power of filtering to apply to multiple PivotTables (or PivotCharts) they have really increas</span><span style="font-family: tahoma,arial,helvetica,sans-serif;"> </span><span style="font-family: tahoma,arial,helvetica,sans-serif;">ed the reporting and data delivery power of Excel 2010.  Not only do they look cool and are fun to interact with, t</span><span style="font-family: tahoma,arial,helvetica,sans-serif;"></span><span style="font-family: tahoma,arial,helvetica,sans-serif;">hey are also extremely functional.  The fact that you can indeed apply a slicer value to multiple PivotTable objects was a big step forward for Excel based dashboarding and reporting.  I’ve used them in nearly every Excel 2010 report that I’ve written since the product release.</span>
</p>

<span style="font-family: tahoma,arial,helvetica,sans-serif;">One thing that they are not so good at doing</span> <span style="font-family: tahoma,arial,helvetica,sans-serif;">is interacting with objects that are based on a different data source.  I know, you’re going to say “But THAT’S how they are designed!”  Sure, it’s true that they are, but as data developers we are often asked to do the impossible (or the improbable)</span>

<p class="MsoNormal">
  <span style="font-family: tahoma,arial,helvetica,sans-serif;">Often, I’ve had to tell users that “this isn’t possible”.  Connecting a slicer to an object created from a different data source was one of those things.  I finally ran into a situation that I couldn’t just brush off as “impossible”.  I was working on some Excel reporting that used PivotTables based on a TSQL query.  I needed to create a slicer based on one of the data sets and have it interact with the other. </span>
</p>

<p class="MsoNormal">
  <span style="font-family: tahoma,arial,helvetica,sans-serif;">I decided that I was going to solve this problem, and solve it I did.  It DOES require the use of VBA, which I normally try to avoid, but in this case, it was the right solution.  Let’s take a look at the anatomy of this solution.</span>
</p>

<p class="MsoNormal">
  <span style="font-family: tahoma,arial,helvetica,sans-serif;"><strong>The Players</strong></span>
</p>

<p class="MsoNormal">
  <span style="font-family: tahoma,arial,helvetica,sans-serif;">First, we have to take note of some of the objects that we are going to be interacting with.</span>
</p>

  * <span style="font-family: tahoma,arial,helvetica,sans-serif;"><span style="font-style: normal; font-variant: normal; font-weight: normal; font-size: 7pt; line-height: normal; font-size-adjust: none; font-stretch: normal;"> </span>ActiveWorkbook – The currently open Excel workbook.</span>
  * <span style="font-family: tahoma,arial,helvetica,sans-serif;">Slicer – A slicer object embedded in the worksheet.  It will have a name (typically <em>SlicerN</em> where <em>N</em> is a number designating which order it was placed)</span>
  * <span style="font-family: tahoma,arial,helvetica,sans-serif;"><span style="font-style: normal; font-variant: normal; font-weight: normal; font-size: 7pt; line-height: normal; font-size-adjust: none; font-stretch: normal;"> </span>PivotTable –A PivotTable object embedded in a report (see naming above)</span>
  * <span style="font-family: tahoma,arial,helvetica,sans-serif;">PivotField – A specific field in the PivotTable</span>

<p class="MsoNormal">
  <span style="font-family: tahoma,arial,helvetica,sans-serif;"><strong>The Order of Operations</strong></span>
</p>

<p class="MsoNormal">
  <span style="font-family: tahoma,arial,helvetica,sans-serif;">In order to complete this solution, some understand of what needs to be done is required.  Here is the basic order of operations required to make this happen:</span>
</p>

  1. <span style="font-family: tahoma,arial,helvetica,sans-serif;">Identify all of the correct objects in play<br /></span>
  2. <span style="font-family: tahoma,arial,helvetica,sans-serif;">Collect a list of selected values in the slicer object</span>
  3. <span style="font-family: tahoma,arial,helvetica,sans-serif;">Find the correct field to filter on in the PivotTable.  This field NEEDS to be what’s known as a PAGE type field.  Most commonly, this is a field in the FILTERS section of the PivotTable.</span>
  4. <span style="font-family: tahoma,arial,helvetica,sans-serif;">Pass the list of selected values to the PivotTable object.  <strong> </strong></span>

<p class="MsoNormal">
  <span style="font-family: tahoma,arial,helvetica,sans-serif;"><strong>Only a few Limitations:</strong></span>
</p>

  * <span style="font-family: tahoma,arial,helvetica,sans-serif;">OLAP data sources can accept multiple slicer values.  Non-OLAP data sources can only accept ONE slicer value</span>
  * <span style="font-family: tahoma,arial,helvetica,sans-serif;">As this solution works with strings, the values in the slicer NEED to match the values in the PivotObject  Creating them based on the same field should ensure this to be the case.</span>
  * <span style="font-family: tahoma,arial,helvetica,sans-serif;">This does require a macro-enable workbook, which can cause an extra layer of security to be used in collaboration scenarios.</span>
  * <span style="font-family: tahoma,arial,helvetica,sans-serif;">Slicers with extremely large number of filter options may cause performance issues.</span>

<p class="MsoNormal">
  <span style="font-family: tahoma,arial,helvetica,sans-serif;"><strong>The code</strong></span>
</p>

<p class="MsoNormal">
  <span style="font-family: tahoma,arial,helvetica,sans-serif;">Ok, this is where we get a little technical.  Here is the basic code needed to pass a <strong>SINGLE</strong> slicer value to a PivotTable.  Modification would need to be made — and are shown in comments – to allow for multiple values to be passed.  <strong>REMEMBER.  THIS IS IMPORTANT!</strong> Multiple values can ONLY be passed if the data source is from an OLAP database.  This is from the Excel Object Model documentation.  If you try to pass multiple values to a non-OLAP source object, a run-time error will occur.</span>
</p>

 

<pre>' Author:     Josh Fennessy (http://www.joshuafennessy.com)</pre>

<pre>'</pre>

<pre>' This short method will allow a value from a selected slicer to be</pre>

<pre>' passed to a PivotObject that is connected to a different data source</pre>

<pre>' Since this is based on string matching, data would need to be indentical</pre>

<pre>' from the two sources.</pre>

<pre>'</pre>

<pre>' Use cases for this could be PivotObject based on different T-SQL queries or</pre>

<pre>' cubes/perspectives in the same SSAS database.</pre>

<pre><br />' We want this code to fire with an object connected to our slicer</pre>

<pre>' is updated.  This means that the slicer was likely interacted with.</pre>

<pre>Private Sub Worksheet_PivotTableUpdate(ByVal Target As PivotTable)</pre>

<pre>    </pre>

<pre>    'find the right object in the workbook. AKA: The Source</pre>

<pre>    If Target.Name = "PivotTable0" Then</pre>

<pre>    </pre>

<pre>        Dim objSlicerItem As SlicerItem</pre>

<pre>        Dim ptSummary As PivotTable</pre>

<pre>        Dim ptField As PivotField</pre>

<pre>        Dim objField As PivotField</pre>

<pre>        Dim strValues() As String</pre>

<pre>        'Dim intIndex as Integer</pre>

<pre>        </pre>

<pre>        'get a reference to the PivotTable to be filtered  AKA: The Target</pre>

<pre>        Set ptSummary = Worksheets("Sheet1").PivotTables("Sheet1")</pre>

<pre>                </pre>

<pre>        'locate the correct field (by name) to be filtered</pre>

<pre>        'this field MUST be a "page" type -- on a normal PivotTable this</pre>

<pre>        'is the FILTERS section</pre>

<pre>        For Each ptField In ptSummary.PageFields</pre>

<pre>            If ptField.Name = "Field1" Then</pre>

<pre>                 Set objField = ptField</pre>

<pre>            End If</pre>

<pre>        Next</pre>

<pre>                        </pre>

<pre>        'intIndex = 1</pre>

<pre>                </pre>

<pre>        'loop through each item in the slicer and check to see if it's selected</pre>

<pre>        'if so, add it to the current pages property of the field selected above</pre>

<pre>        For Each objSlicerItem In ActiveWorkbook.SlicerCaches.Item(1).SlicerItems</pre>

<pre>            </pre>

<pre>            If objSlicerItem.Selected = True Then</pre>

<pre>                ''NOTE: The following commented code is for use with PivotTables connected to an OLAP source</pre>

<pre>                </pre>

<pre>                'ReDim Preserve strValues(1 To intIndex)</pre>

<pre>                'strValues(intIndex) = objSlicerItem.Value</pre>

<pre>                'intIndex = intIndex + 1</pre>

<pre>                </pre>

<pre>                'this is for setting a single filter value -- required for non-olap sources</pre>

<pre>                objField.CurrentPage = objSlicerItem.Value</pre>

<pre>            End If</pre>

<pre>        Next</pre>

<pre>        </pre>

<pre>        ''NOTE: If working with a PivotTable connected to an OLAP source</pre>

<pre>        ''      You can set the CurrentPageList property to a string array</pre>

<pre>        ''      of values.  The EnableMultiplePageItems property also</pre>

<pre>        ''      needs to be set to TRUE.</pre>

<pre>        </pre>

<pre>        ''      This means that when working with non-OLAP sources, only single</pre>

<pre>        ''      select filters are enabled.  Important to note when working with</pre>

<pre>        ''      slicers.</pre>

<pre>        </pre>

<pre>        'objField.EnableMultiplePageItems = True</pre>

<pre>        </pre>

<pre>        ''use the following command to clear all filters before setting new values</pre>

<pre>        ''important when setting multi-value filters.</pre>

<pre>        </pre>

<pre>        'objField.ClearAllFilters</pre>

<pre>        'objField.CurrentPageList = strValues()</pre>

<pre><br />    End If</pre>

<pre>    </pre>

<pre>End Sub</pre>

 

<p class="MsoNormal">
  <p>
  </p>