---
title: Creating A XSD Schema From A Table In SQL Server With FOR XML Syntax
author: SQLDenis
type: post
date: 2009-05-11T17:21:31+00:00
url: /index.php/datamgmt/datadesign/creating-a-xsd-schema-from-a-table-in-sq/
views:
  - 26576
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql server 2005
  - sql server 2008
  - xml
  - xsd

---
You have a table and you would like to create a XSD schema based on that table. What is the easiest way to do that in SQL Server? The easiest way to do that would be to use FOR XML syntax with AUTO, ELEMENTS and XMLSCHEMA. If your table is named test and you want your schema to be named TestXsdSchema then you would do the following

<pre>SELECT * FROM Test FOR XML AUTO, ELEMENTS, XMLSCHEMA('TestXsdSchema')</pre>

Let&#8217;s look at a complete example. First create the table below

<pre>create table Test(id int identity,
SomeName varchar(53) not null,
SomeValue decimal(20,10) not null,
SomeGuid uniqueidentifier not null default newsequentialid())</pre>

Now execute the following block of code

<pre>DECLARE @XsdSchema xml
SET @XsdSchema = (SELECT * FROM Test FOR XML AUTO, ELEMENTS, XMLSCHEMA('TestXsdSchema'))
SELECT @XsdSchema</pre>

This is the schema that gets generated

<pre><xsd:schema targetNamespace="TestXsdSchema" elementFormDefault="qualified">
 <xsd:import namespace="http://schemas.microsoft.com/sqlserver/2004/sqltypes" schemaLocation="http://schemas.microsoft.com/sqlserver/2004/sqltypes/sqltypes.xsd"/>
<xsd:element name="Test">
<xsd:complexType>
<xsd:sequence>
 <xsd:element name="id" type="sqltypes:int"/>
<xsd:element name="SomeName">
<xsd:simpleType>
<xsd:restriction base="sqltypes:varchar" sqltypes:localeId="1033" sqltypes:sqlCompareOptions="IgnoreCase IgnoreKanaType IgnoreWidth" sqltypes:sqlSortId="52">
 <xsd:maxLength value="53"/>
 </xsd:restriction>
 </xsd:simpleType>
 </xsd:element>
<xsd:element name="SomeValue">
<xsd:simpleType>
<xsd:restriction base="sqltypes:decimal">
 <xsd:totalDigits value="20"/>
 <xsd:fractionDigits value="10"/>
 </xsd:restriction>
 </xsd:simpleType>
 </xsd:element>
 <xsd:element name="SomeGuid" type="sqltypes:uniqueidentifier"/>
 </xsd:sequence>
 </xsd:complexType>
 </xsd:element>
 </xsd:schema></pre>

See, that was pretty simple wasn&#8217;t it?

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22