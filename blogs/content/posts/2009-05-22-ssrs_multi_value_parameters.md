---
title: Multi-Value Parameters in SSRS
author: Erik
type: post
date: 2009-05-22T17:22:45+00:00
ID: 443
excerpt: "Here's a quick rundown of how to modify an SSRS report and a stored procedure to accept and use a multi-valued parameter."
url: /index.php/datamgmt/datadesign/ssrs_multi_value_parameters/
views:
  - 58150
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
tags:
  - escape commas
  - multi-value
  - parameter
  - split
  - sql server
  - ssrs

---
# Background

I have a report that returns specific documentation from the database with one parameter, a CustomerID. The report is bound to a stored procedure which has hard-coded in it the names of the specific documents that should be included. This solution is problematic because when the list of documents needs to change, the SP has to be updated, which means mistakes can be (and have been) made.

The good step of converting the SP to use a table doesn't satisfy me fully, because these reports are executed from a special reporting system that also has its own list of document names, so that it knows when to kick off a particular report—for example, if none of the documents exist, no report will be generated. I didn't like the idea of depending on having the same data updates made in two places: I should be able to just update the reporting system with the new document names and then trust that the reports will contain the correct data. In the past, we had a mistake where the reporting system received a new document name that didn't get included in the SP properly, so after finally fixing the SP, I had to do a recovery and resend some reports.

I realized that what could solve the problem once and for all was to provide the exact list of document names to the SSRS report as a parameter, and have the report pass the document names into the SP, so that even if the SP is not updated (or its source table is not updated), the correct content will still be pulled. The way to do this is with a “multi-value” parameter.

# Starting Points

* A quick test will show that the way SSRS passes multi-valued parameters to a stored procedure is simply by taking all the values selected and packing them into a comma-delimited string. If any of the items in the list contain a comma, no escaping or special handling is done—so manual escaping is required.

* In case you're really new to all this, all the report development is being done in Visual Studio 2005, with a project of type Business Intelligence Projects – Report Server Project. I'm sure you can find tutorials elsewhere for how to set up a basic report. Once you have a working report, you're ready to add a multi-value parameter.

* In this article, the multi-value parameter I am working with is a list of document names. So wherever I say document names, you can insert the thing that you are concerned with. If you can guarantee that there will never be a comma in the list, you can skip or edit the parts of my explanation that have special handling for commas.

# Adding the Multi-Value Parameter

What I learned while doing this is probably useful to someone, so here's a quick rundown of how to modify an SSRS report and a stored procedure to accept and use a multi-valued parameter.

## Modify the Stored Procedure

1. Add the new parameter. I copied the old SP to a new name so that I couldn't possibly mess up the original, and so going forward no one else who works with these things will get confused about which does what.</p> 

```sql
CREATE PROCEDURE dbo.MyReport_DocParam
    @CustomerID int,
    @DocumentNameParam varchar(Max) = ''
    -- Please note that any document names with commas must be put inside single quotes.
```

The default value of an empty string allows the SP to be run with just a CustomerID and it will still work. It may not have all the document names, but for testing and development of the SP that may not matter most of the time. I put the comment shown right into the SP to help prevent problems with other people trying to use the SP when I am not around.

I chose to make the new parameter varchar(Max) because it won't hurt anything, and on the theory that I don't like my code breaking, and despite knowing that the list will almost surely never exceed 8000 characters, I'd rather be safe. In SQL 2000 you can probably get by with varchar(8000) or even the text data type.</li> 

* Write a value-splitting function. 
_Note_: I have not experimented with other data types such as Integer. If you have done this and know how it works in SSRS differently from using strings, please post a comment. I suspect that whatever the actual data type of your items, they will have to be passed as a comma-separated string anyway. If your items are fixed-length or easily convert to relatively short fixed-length strings, padding them so the comma delimiters added by SSRS appear at exactly known positions will make your task of splitting much easier. You can just join to a special Numbers table and split at each position. For example, if you can make your inputs always 10 characters long then you can just break the string at every 11th character—the comma.

Here's how I decided to escape commas:

* If the value contains a comma, enclose it in single quotes and double up any single quotes in that value.

* If the value does not contain a comma, don't escape it in any way even if it has a single quote.

I chose this because it is similar to how SQL Server accepts string literals already and should be more familiar to anyone working with my code than other methods (such as backslash-escaping for example). I also considered replacing commas with a character that I know won't be in the document names such as character 0 or 255 and then restoring it after splitting values apart, but rejected that because it makes it much more difficult to understand and makes it much harder to provide the list of items if the SP is being run from a query window such as Query Analyzer or Management Studio.

<div style="border:1px solid black;background-color:#444;color:white;margin:0 20px;padding:0 5px 0 5px;">
  <span class="MT_larger"><em><strong>But I Don't Wanna Escape The Comma</strong></em></span></p> 

  If you don't think you need to escape commas, please ensure that commas <strong>can't</strong> be in your data ever, not that they don't exist now or “we'll tell people not to put commas in.” An utterly basic and beginner's rule of programming is to never trust user input. Bad Things happen when you do. How do you know that in 5 years when you may be gone or on vacation that some new person who never heard about the comma rule won't enter a comma?
</div>

Here's my function to do the unescaping and splitting work:

```sql
CREATE FUNCTION SplitQuotedString (@String varchar(max))
RETURNS @Array TABLE (Item varchar(8000))
AS
BEGIN
   DECLARE
      @Pos int,
      @End int,
      @TextLength int,
      @Item varchar(8000),
      @Fragment varchar(8000),
      @InQuotes bit
   SET @TextLength = DataLength(@String)

   IF @TextLength = 0 RETURN

   SET @Pos = 1
   SET @InQuotes = 0
   SET @String = @String + ','
   SET @Item = ''
   WHILE 1 = 1 BEGIN
      SET @End = CharIndex(',', @String, @Pos)
      IF @End = 0 BREAK
      SET @Fragment = Substring(@String, @Pos, @End - @Pos)
      IF @InQuotes = 1 BEGIN
         --InQuotes becomes false if there are an odd number of single quotes at the end of this fragment
         SET @InQuotes = PatIndex('%[^'']%', Reverse(@Fragment) + 'x') % 2
         SET @Item = @Item + ',' + Replace(Left(@Fragment, DataLength(@Fragment) - 1 + @InQuotes), '''''', '''')
      END
      ELSE BEGIN
         IF @Fragment LIKE '''%' BEGIN
            SET @InQuotes = 1
            SET @Item = Replace(Substring(@Fragment, 1 + @InQuotes, 8000), '''''', '''')
         END
         ELSE BEGIN
            SET @Item = @Fragment
         END
      END
      IF @InQuotes = 0 BEGIN
         INSERT @Array (Item) VALUES (@Item)
         SET @Item = ''
      END
      SET @Pos = @End + 1
   END

   RETURN
END
```

You're welcome to use it, or write your own. You may not agree with how I chose to do it, and if you have any seriously big improvements do let me know. But I was not overly concerned with extracting that last tiny bit of performance, as my strings will never be very long and will never have more than a dozen or so items. I could not use a Numbers table solution since I am doubling up single quotes and in the case of encountering input such as “””” it would incorrectly match an extra time at the second quote mark. Preventing a false match requires knowing if the current quote mark is an even or an odd one, which would need to look backward in the string, which complicated it so much that I just went for a chunk-by-chunk method that looks at each string separated by commas and determines if it begins a quoted string. If so, then each fragment is added to the item until the closing quote mark is encountered, and finally the complete item is added to the output table. If the item is not quoted, it is simply added to the output table.

_Note_: In this function I assumed that no individual value would exceed 8000 characters. I also assumed that an item will not be quoted without containing a comma character. If items are quoted that don't contain commas, this code will break. See the escaping logic I used in the query for the value list producing Dataset on the SSRS report.

If your data will never have commas in it or you can use the the fixed-length method, then should use your own plain-vanilla split function rather than my unescaping version.

Split and use the multi-value string. 

Here's the code I used, which should be enough of an example to let you develop your own version that suits your needs:

```sql
DECLARE @DocumentNames TABLE (
	DocumentName varchar(60)
)
IF Coalesce(@DocumentNameParam, '') = '' BEGIN
   INSERT @DocumentNames SELECT Item FROM dbo.SplitQuotedString(@DocumentNameParam)
END
ELSE BEGIN
   INSERT @DocumentNames
   SELECT 'Document Name 1'
   UNION ALL SELECT 'Document Name 2'
   UNION ALL SELECT 'Document Name 3, Special'
END
```

To my dismay, I was not allowed to make this SP read from a table. But I am still quite happy because now I don't care if the SP gets updated with new document names: when the reporting system creates a report, it will pass in document names and always be correct. I have insulated the reporting system against exactly the thing I was trying to avoid: mistakes when updating the SP. I'll even throw in that I was the one who made the mistake updating the SP last time—I didn't know the hard-coded document list was in <strong>three</strong> places. Now, at least, the list is in one place.

And finally, using the document names:

sql
  INNER JOIN @DocumentNames N on D.DocumentName = N.DocumentName
```

## Modify The SSRS Report

1. Update the Dataset. On the Data tab, either create a new Dataset or update an existing Dataset so that it has the correct SP name and finds the new parameter you added. To edit a Dataset, click the “…” button to the right of the Dataset name dropdown.

2. Decide whether the report's default list of available document names will be hard-coded in the report or supplied by a query. I chose to make it data-driven so that if a new document is added, it will show up in the report without any changes to it, or with only a change to a shared data source, rather than having to change the SSRS report itself. I always prefer to make changes to data or shared objects than to code or types of things that have to be tested and deployed. 

  <strong>From query:</strong> Create a new DataSet that pulls the document names, one per row. Here is the query I used:

  
```
sql
    SELECT
      DocNameString =
          CASE
          WHEN DocName LIKE '%,%' THEN '''' + Replace(DocName, '''', '''''') + ''''
          ELSE DocName
          END,
      DocName
    FROM ReportDocName
    WHERE ReportID = 62 -- my particular report that has multiple document names
    ORDER BY DocName
  
```


  This is the logic that I wrote the SplitQuotedString to match. If you change how you escape commas, you'll need to write your own splitting/unescaping function. Run the query to make sure it's working correctly. It should have two columns, one with commas escaped, and one with them unescaped. If you don't have to escape commas in your data, then one column is okay.

  <strong>Non-queried:</strong> Run a query manually to extract the list you want or locate the values wherever they are. Keep this handy to paste in later.

3. Select Report->Report Parameters. If there isn't already a new parameter automatically added by the fact that your SP now has a new parameter, then Add a new parameter.<br /> • Name: Give it the same name that you called the parameter in your SP. For me: DocumentNameParam.<br /> • Data type: String<br /> • Prompt: Document Names<br /> • Check the “Multi-value” checkbox. I also checked “Allow blank value” because if no names are specified, I want the SP's list of document names to take over. You cannot choose “Allow null value” with a Multi-value parameter (at least, I couldn't select it and I think that is the reason).<br /> • Available values:<br /> – For “Non-queried” paste in each option, one at a time, from the list of options you built earlier.<br /> – For “From query” choose the Dataset you created that displays the possible list. For “Value field” select the column that has commas escaped, and for “Label field” select the one that is plain. If your Dataset only has one column, use it in both places.<br /> • Default values:<br /> – For “Non-queried” paste in each option that you want to be selected by default.<br /> – For “From query” choose your list Dataset, or perhaps select a special default Dataset you created that is different from the list of all possible options. I used the same Dataset since I want all values to be selected by default.</p> <p class="text-align:center;">
<img src="/wp-content/uploads/blogs/DataMgmt/ssrs_multi_value_parameters/MultiValueReportParameters.PNG" alt="SSRS Report Parameters" title="Creating a Multi-value Parameter" />
</p>
</li>

<li>
If you had to add the multi-value report parameter manually, edit the source Dataset and make sure it is mapped properly. Click on the Data tab, select the correct Dataset, click the “…” edit button, select Parameters, and make sure that the Name is mapped to the Value. <p>
<img src="/wp-content/uploads/blogs/DataMgmt/ssrs_multi_value_parameters/MultiValueDataSet.PNG" alt="SSRS Dataset Parameters" title="Mapping A Dataset Parameter to a Report Parameter" /></li> </ol> 

<h1>
Happily Use Your Shiny New Report
</h1>

<p>
I hope that this article has been useful to you, and helps you get a Multi-value parameter added to your SSRS report in no time!
</p>

<p>
Erik<br /> <br /> <span class="MT_smaller"><strong>Afterthoughts</strong></p> 

<p>
  • You <em>could</em> use a query instead of a stored procedure.<br /> • Think carefully before using a multi-valued parameter to solve problems that should be solved by database normalization. A multi-value column in a database is a BAD idea if you ever have to work with the individual items. Don't try to adapt this solution to such a column. Fix the database design instead.</span>
</p>