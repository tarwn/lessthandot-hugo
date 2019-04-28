---
title: Intellisense for custom XML in Visual Studio
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-05-06T13:44:00+00:00
ID: 2080
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

So let's assume for the moment that you have the following sample files:

**AwesomeFile.xml**

```xml
<?xml version="1.0" encoding="utf-8" ?>
<AwesomeList>
	<AwesomeItem AwesomenessFactor="11">
		<AwesomeType>Is Awesome</AwesomeType>
	</AwesomeItem>
</AwesomeList>
```
**Awesome.xsd**

```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema id="MyAwesomeSchema"
    xmlns:xs="http://www.w3.org/2001/XMLSchema">

	<xs:simpleType name="AwesomeLevel">
		<xs:restriction base="xs:integer">
			<xs:enumeration value="1"></xs:enumeration>
			<xs:enumeration value="5"></xs:enumeration>
			<xs:enumeration value="10"></xs:enumeration>
			<xs:enumeration value="11"></xs:enumeration>
		</xs:restriction>
	</xs:simpleType>

	<xs:complexType name="AwesomeElement">
		<xs:sequence>
			<xs:element name="AwesomeType" type="xs:string"></xs:element>
		</xs:sequence>
		<xs:attribute name="AwesomenessFactor" type="AwesomeLevel" use="required"></xs:attribute>
	</xs:complexType>

	<xs:element name="AwesomeList">
		<xs:complexType>
			<xs:sequence>
				<xs:element name="AwesomeItem" type="AwesomeElement" minOccurs="0" maxOccurs="unbounded">
				</xs:element>
			</xs:sequence>
		</xs:complexType>
	</xs:element>
	
</xs:schema>
```
Visual Studio gives us handy intellisense suggestions and warnings when we're writing the schema because we have specified a namespace it knows, but how do we get that usefulness when we're adding more content to our awesome XML file?

## More Cowbell

Turns out, adding this functionality is pretty easy. Visual Studio is smart enough to use know schemas, so all we have to do is provide the information that makes our schema known and relevant for our XML file. 

For this example I am assuming that the two files are in the same folder. I've also made the file complex enough that the additions we make should work for far more complex setups also.

Here's the steps:

  1. Define a target namespace on the schema
  2. Define the empty namespace of the schema as this namespace
  3. set attributeFormDefault to unqualified so attributes in our XML file won't require namespace declarations
  4. Add the namespace declaration to the XML file

Updating our files (and adding comments to reflect the list above), we have:

**AwesomeFile.xml**

```xml
<?xml version="1.0" encoding="utf-8" ?>
<AwesomeList xmlns="my://awesomeness"> <!-- (4) -->
	<AwesomeItem AwesomenessFactor="11">
		<AwesomeType>Is Awesome</AwesomeType>
	</AwesomeItem>
</AwesomeList>
```
**Awesome.xsd**

```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema id="MyAwesomeSchema"
    xmlns:xs="http://www.w3.org/2001/XMLSchema"
		targetNamespace="my://awesomeness" <!-- (1) -->
		xmlns="my://awesomeness" <!-- (2) -->
		attributeFormDefault="unqualified" <!-- (3) -->
		elementFormDefault="qualified">

	<xs:simpleType name="AwesomeLevel">
		<xs:restriction base="xs:integer">
			<xs:enumeration value="1"></xs:enumeration>
			<xs:enumeration value="5"></xs:enumeration>
			<xs:enumeration value="10"></xs:enumeration>
			<xs:enumeration value="11"></xs:enumeration>
		</xs:restriction>
	</xs:simpleType>

	<xs:complexType name="AwesomeElement">
		<xs:sequence>
			<xs:element name="AwesomeType" type="xs:string"></xs:element>
		</xs:sequence>
		<xs:attribute name="AwesomenessFactor" type="AwesomeLevel" use="required"></xs:attribute>
	</xs:complexType>

	<xs:element name="AwesomeList">
		<xs:complexType>
			<xs:sequence>
				<xs:element name="AwesomeItem" type="AwesomeElement" minOccurs="0" maxOccurs="unbounded">
				</xs:element>
			</xs:sequence>
		</xs:complexType>
	</xs:element>

</xs:schema>
```
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

_Hopefully the custom color scheme isn't confusing, was feeling too lazy to switch it and switch it back_