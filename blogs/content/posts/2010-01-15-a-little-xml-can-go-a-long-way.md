---
title: A Little XML Can Go a Long Way
author: Alex Ullrich
type: post
date: 2010-01-15T16:35:01+00:00
ID: 577
url: /index.php/datamgmt/datadesign/a-little-xml-can-go-a-long-way/
views:
  - 3654
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
Never really had much need for XML in SQL Server, but I'd played around with it a bit in the past. I do my best to learn the bare minimum about things like this, just in case. Just in case happened a little while ago. We got a request to add customer-defined data fields to our application, with only a week of development time before our next release. And of course, this functionality \*HAD\* to go into said release.

I suppose a little background on our application is in order. It was originally developed by a consulting firm using COM+ (VB6) and XSL transforms, but we've been replacing a great number of the pages with ASP.net web forms. Typically, for a request like this, we would simply redo the page using ASP.net / C# in the process of adding the new feature. But this was needed on one of the most complicated screens in our entire application, and there was no way we were getting it rewritten and tested in time for deployment. After confirming that it was completely impossible to get this request pushed to the next deployment cycle so we could recreate the page, I started wading into the mess of XSL and VB6 code that I'd need to use to make this happen.

Because I'm not too familiar with XSL, the first thing I wanted to figure out was how to display this stuff. I'm comfortable enough modifying an existing template, but not so much with the creation of a new one. After a quick talk with [ChaosPandion][1] I knew the basic structure for the template, and building on it I came up with something like this:

```xml
<xsl:template match="CustomFieldList">
		<xsl:if test="count(CustomerFields/Field) > 0">
			<div class="SectionTitle" style="margin-bottom:20px;">
				Customer Defined Fields
			</div>
		</xsl:if>
		<xsl:for-each select="CustomerFields">
			<xsl:if test="count(Field) > 0">
				<p>
					<xsl:value-of select="concat(@name, ' (', @associationtypename, ')')"/>
				</p>
				<xsl:for-each select="field">
					<label>
						<xsl:value-of select="@name"/>
					</label>
					<br/>
					<xsl:choose>
						<xsl:when test="@type = 'Decimal'">
							<input id="{@name}" name="udf_{@typeid}_{../@custid}_{@id}" size="30" type="text" value="{@value}" title="{@name}" onblur="ValidateDecimalInput(this)" />
						</xsl:when>
						<xsl:when test="@type = 'Integer'">
							<input id="{@name}" name="udf_{@typeid}_{../@custid}_{@id}" size="30" type="text" value="{@value}" title="{@name}" onblur="ValidateIntegerInput(this)" />
						</xsl:when>
						<!-- Other Types -->
						<xsl:otherwise>
							<input id="{@name}" name="udf_{@typeid}_{../@custid}_{@id}" size="30" type="text" value="{@value}" />
						</xsl:otherwise>
					</xsl:choose>
					<br/>
				</xsl:for-each>
			</xsl:if>
		</xsl:for-each>
	</xsl:template>
```

Basically, we needed the customer-defined fields shown on the page grouped by the relationship said customer had with the case in question (there are 6 different relationship types, I won't bore you with the details). Once I had the template, I knew what the data I had to provide it looked like, and it wasn't too complicated.

```xml
<CustomFieldList>
  <CustomerFields custid="NULL" name="LessThanDot" associationtypeid="1" associationtypename="Awesome Blogs">
    <Field id="16" name="TextOne" type="Text" typeid="1" value="different value" />
  </CustomerFields>
  <CustomerFields custid="NULL" name="alexcuse.com" associationtypeid="2" associationtypename="Crummy Web Page">
    <Field id="4" name="TextOne" type="Text" typeid="1" value="chchchchchanges" />
    <Field id="13" name="Field3" type="Decimal" typeid="3" />
    <Field id="18" name="Integer Test" type="Integer" typeid="4" value="15" />
  </CustomerFields>
</CustomFieldList>
```

In the framework that was developed to support the older bits of our application (bites tongue), there are at least two ways (probably more) to dump a normal ADO recordset into a node of the XML document used to render the page, but that wouldn't help because I needed a bit of structure to the data (I suppose that I could have made everything completely flat, but being as unskilled as I am with XSL I wanted to minimize the room for me to mess something up). Coding the transformation in VB6 wasn't really a valid option either because it would require untangling the rat's nest of existing VB6 code, and there just wasn't any time. Enter “For Xml”.

I'd never used For Xml to generate a nested xml output like this before, and the syntax proved to be just a little awkward for me. What I needed to do first was get the list of customers associated with the business entity being displayed, and then get the list of Customer Fields associated with each of them (along with any data that had already been entered for those fields). The actual query to retrieve it ended up being kind of nasty, so I won't hurt anyone's eyes with that. For the sake of the example, lets assume that everything for the inner bits of xml (the “Field” elements) comes from one table.

sql
declare @caseid int

Select CustomerTable.Id as "@custid",
	CustomerTable.Name as "@name",
	CustomFieldsTable.Id as "@associationtypeid",
	CustomFieldsTable.Name as "@associationtypename",
	(
		Select Id as "@id"
			, Name as "@name" 
			, TypeName as "@type"
			, TypeId as "@typeid"
			, Coalesce(
				Case TypeId
					when 1 Then TextValue
					when 2 Then dbo.DateFormat(DateValue, 0)
					When 3 Then Cast(DecimalValue as VarChar(20))
					When 4 Then Cast(NumericValue as VarChar(20))
				  End, '')  as "@value"
		From CustomFieldsTable cft
		Where CaseId = @CaseId
			and cft.SomeColumn = CustomFieldsTable.SomeColumn
		For Xml Path('Field'), Type
	)
From CustomFieldsTable 
	Inner Join CaseTable on CustomFieldsTable.AssociationTargetIdValue = 
		Case CustomFieldAssociation.AssociationTypeId 
			When 1 then CaseTable.BillTo
			When 2 then CaseTable.Employer  --etc...
		End
	Inner Join CustomerTable on AssociationTargetIdValue = CustomerTable.Id
Where CaseTable.Id = @CaseId 
Group By CustomerTable.Id, CustomerTable.Name, CustomFieldsTable.Id
Order By TypeId
For Xml Path('CustomerFields'), Type, Root('CustomFieldList')
```
What I found awkward here was that nested select – it's written much like a correlated subquery, but it returns multiple rows / columns, and relies on the “For Xml” magic to to associate the resulting list with the correct outer node.

It took a bit of mental gymnastics to adjust to the way this feature works, but it **really** saved the day for me in this situation. The feature ended up finished on time, and an internal client that I had once thought was impossible to please was actually pleased (if only for a fleeting moment).

 [1]: http://forum.ltd.local/memberlist.php?mode=viewprofile&u=330