---
title: Intellisense for custom XML in Visual Studio
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-05-06T13:44:00+00:00
excerpt: This is one of those things that I only need once every several months and always forget how to do. Working with custom XML in Visual Studio is a whole lot easier with intellisense. Schema errors are highlighted, enumerated values are displayed, and the amount of typing goes from full tag names to just a few characters followed by tab to complete.
url: /index.php/desktopdev/mstech/vs2012/intellisense-for-custom-xml-in/
views:
  - 12137
rp4wp_auto_linked:
  - 1
categories:
  - Vistual Studio 2012
tags:
  - intellisense
  - schema
  - visual studio
  - xml

---
This is one of those things that I only need once every several months and always forget how to do. Working with custom XML in Visual Studio is a whole lot easier with intellisense. Schema errors are highlighted, enumerated values are displayed, and the amount of typing goes from full tag names to just a few characters followed by tab to complete.

There are a few ways to do this, but if you have a schema (XSD) for the file, then here are the steps to make the magic happen.

## Sample Files

So let&#8217;s assume for the moment that you have the following sample files:

**AwesomeFile.xml**

<pre><?xml version="1.0" encoding="utf-8" ?&gt;
<AwesomeList&gt;
	<AwesomeItem AwesomenessFactor="11"&gt;
		<AwesomeType&gt;Is Awesome</AwesomeType&gt;
	</AwesomeItem&gt;
</AwesomeList&gt;</pre>

**Awesome.xsd**

<pre><?xml version="1.0" encoding="utf-8"?&gt;
<xs:schema id="MyAwesomeSchema"
    xmlns:xs="http://www.w3.org/2001/XMLSchema"&gt;

	<xs:simpleType name="AwesomeLevel"&gt;
		<xs:restriction base="xs:integer"&gt;
			<xs:enumeration value="1"&gt;</xs:enumeration&gt;
			<xs:enumeration value="5"&gt;</xs:enumeration&gt;
			<xs:enumeration value="10"&gt;</xs:enumeration&gt;
			<xs:enumeration value="11"&gt;</xs:enumeration&gt;
		</xs:restriction&gt;
	</xs:simpleType&gt;

	<xs:complexType name="AwesomeElement"&gt;
		<xs:sequence&gt;
			<xs:element name="AwesomeType" type="xs:string"&gt;</xs:element&gt;
		</xs:sequence&gt;
		<xs:attribute name="AwesomenessFactor" type="AwesomeLevel" use="required"&gt;</xs:attribute&gt;
	</xs:complexType&gt;

	<xs:element name="AwesomeList"&gt;
		<xs:complexType&gt;
			<xs:sequence&gt;
				<xs:element name="AwesomeItem" type="AwesomeElement" minOccurs="0" maxOccurs="unbounded"&gt;
				</xs:element&gt;
			</xs:sequence&gt;
		</xs:complexType&gt;
	</xs:element&gt;
	
</xs:schema&gt;</pre>

Visual Studio gives us handy intellisense suggestions and warnings when we&#8217;re writing the schema because we have specified a namespace it knows, but how do we get that usefulness when we&#8217;re adding more content to our awesome XML file?

## More Cowbell

Turns out, adding this functionality is pretty easy. Visual Studio is smart enough to use know schemas, so all we have to do is provide the information that makes our schema known and relevant for our XML file. 

For this example I am assuming that the two files are in the same folder. I&#8217;ve also made the file complex enough that the additions we make should work for far more complex setups also.

Here&#8217;s the steps:

  1. Define a target namespace on the schema
  2. Define the empty namespace of the schema as this namespace
  3. set attributeFormDefault to unqualified so attributes in our XML file won&#8217;t require namespace declarations
  4. Add the namespace declaration to the XML file

Updating our files (and adding comments to reflect the list above), we have:

**AwesomeFile.xml**

<pre><?xml version="1.0" encoding="utf-8" ?&gt;
<AwesomeList xmlns="my://awesomeness"&gt; <!-- (4) --&gt;
	<AwesomeItem AwesomenessFactor="11"&gt;
		<AwesomeType&gt;Is Awesome</AwesomeType&gt;
	</AwesomeItem&gt;
</AwesomeList&gt;</pre>

**Awesome.xsd**

<pre><?xml version="1.0" encoding="utf-8"?&gt;
<xs:schema id="MyAwesomeSchema"
    xmlns:xs="http://www.w3.org/2001/XMLSchema"
		targetNamespace="my://awesomeness" <!-- (1) --&gt;
		xmlns="my://awesomeness" <!-- (2) --&gt;
		attributeFormDefault="unqualified" <!-- (3) --&gt;
		elementFormDefault="qualified"&gt;

	<xs:simpleType name="AwesomeLevel"&gt;
		<xs:restriction base="xs:integer"&gt;
			<xs:enumeration value="1"&gt;</xs:enumeration&gt;
			<xs:enumeration value="5"&gt;</xs:enumeration&gt;
			<xs:enumeration value="10"&gt;</xs:enumeration&gt;
			<xs:enumeration value="11"&gt;</xs:enumeration&gt;
		</xs:restriction&gt;
	</xs:simpleType&gt;

	<xs:complexType name="AwesomeElement"&gt;
		<xs:sequence&gt;
			<xs:element name="AwesomeType" type="xs:string"&gt;</xs:element&gt;
		</xs:sequence&gt;
		<xs:attribute name="AwesomenessFactor" type="AwesomeLevel" use="required"&gt;</xs:attribute&gt;
	</xs:complexType&gt;

	<xs:element name="AwesomeList"&gt;
		<xs:complexType&gt;
			<xs:sequence&gt;
				<xs:element name="AwesomeItem" type="AwesomeElement" minOccurs="0" maxOccurs="unbounded"&gt;
				</xs:element&gt;
			</xs:sequence&gt;
		</xs:complexType&gt;
	</xs:element&gt;

</xs:schema&gt;</pre>

And there we have it.

## Results

Now when we start typing in the XML file we will get intellisense suggestions/completion:

<div style="text-align:center; margin: .5em 0;">
  <img src="http://www.tiernok.com/LTDBlog/XmlSchemaIntellisense/Intellisense.png" alt="Intellisense suggestions" />
</div>

We also get warnings when we forget a required attribute:

<div style="text-align:center; margin: .5em 0;">
  <img src="http://www.tiernok.com/LTDBlog/XmlSchemaIntellisense/SchemaWarning.png" alt="Intellisense suggestions" />
</div>

And when we use the wrong type:

<div style="text-align:center; margin: .5em 0;">
  <img src="http://www.tiernok.com/LTDBlog/XmlSchemaIntellisense/WrongTypeWarning.png" alt="Intellisense suggestions" />
</div>

_Hopefully the custom color scheme isn&#8217;t confusing, was feeling too lazy to switch it and switch it back_