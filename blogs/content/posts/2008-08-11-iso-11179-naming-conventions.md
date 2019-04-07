---
title: ISO-11179 Naming Conventions
author: SQLDenis
type: post
date: 2008-08-11T14:43:02+00:00
ID: 105
url: /index.php/datamgmt/datadesign/iso-11179-naming-conventions/
views:
  - 23987
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - best practices
  - database
  - iso
  - naming conventions
  - programming
  - sql server

---
Straight from the man himself comes this statement posted in the [microsoft.public.sqlserver.programming forum][1]: &#8220;_You need to read ISO-11179 so you use proper data element names. You
  
actually had &#8220;tbl-&#8220;on the table names! Sometimes &#8220;id&#8221; id a
  
prefix and sometimes it is a postfix._&#8221;

Of course you know who I am talking about? No? Joe Celko of course. So what is ISO-11179?

The 11179 standard is a multipart standard that includes the following parts:

[11179-1][2]
  
**Part 1: Framework**, introduces and discusses fundamental ideas of data elements, value domains, data element concepts, conceptual domains, and classification schemes essential to the understanding of this set of standards and provides the context for associating the individual parts of ISO/IEC 11179.

[11179-2][3]
  
**Part 2: Classification**, provides a conceptual model for managing classification schemes. There are many structures used to organize classification schemes and there are many subject matter areas that classification schemes describe. So, this Part also provides a two-faceted classification for classification schemes themselves.

[11179-3][4]
  
**Part 3: Registry Metamodel and Basic Attributes**, specifies a conceptual model for a metadata registry. It is limited to a set of basic attributes for data elements, data element concepts, value domains, conceptual domains, classification schemes, and other related classes, called administered items. The basic attributes specified for data elements in ISO/IEC 11179-3:1994 are provided in this revision.

[11179-4][5]
  
**Part 4: Formulation of Data Definitions,** provides guidance on how to develop unambiguous data definitions. A number of specific rules and guidelines are presented in ISO/IEC 11179-4 that specify exactly how a data definition should be formed. A precise, well-formed definition is one of the most critical requirements for shared understanding of an administered item; well-formed definitions are imperative for the exchange of information. Only if every user has a common and exact understanding of the data item can it be exchanged trouble-free.

[11179-5][6]
  
**Part 5: Naming and Identification Principles**, provides guidance for the identification of administered items. Identification is a broad term for designating, or identifying, a particular data item. Identification can be accomplished in various ways, depending upon the use of the identifier. Identification includes the assignment of numerical identifiers that have no inherent meanings to humans; icons (graphic symbols to which meaning has been assigned); and names with embedded meaning, usually for human understanding, that are associated with the data item&#8217;s definition and value domain.

[11179-6][7]
  
**Part 6: Registration**, provides instruction on how a registration applicant may register a data item with a central Registration Authority and the allocation of unique identifiers for each data item. Maintenance of administered items already registered is also specified in this document.

The one that deals with naming conventions is [11179-5][6] The link will point to a zip file which has a pdf file in it. The TOC of this pdf file is below

Contents
  
Foreword
  
1 Scope
  
2 Normative references
  
3 Terms and definitions
  
4 Data Identifiers within a registry
  
5 Identification
  
6 Names
  
6.1 Names in a registry
  
6.2 Naming conventions
  
7 Development of naming conventions
  
7.1 Introduction
  
7.2 Scope principle
  
7.3 Authority principle
  
7.4 Semantic principle
  
7.5 Syntactic principle
  
7.6 Lexical principle
  
7.7 Uniqueness principle
  
Annex A (informative) Example naming conventions for names within an MDR registry
  
Annex B (informative) Example naming conventions for Asian languages

So check it out and hopefully you and your team can adapt a common naming conventions instead of having things named like employee_address, EmployeeAddress, employeeAddress and tblEmployeeAddress.

 [1]: http://groups.google.com/group/microsoft.public.sqlserver.programming/browse_thread/thread/b29389050253df78/87d7c1b73e60e41d#87d7c1b73e60e41d
 [2]: http://metadata-standards.org/11179/#11179-1
 [3]: http://metadata-standards.org/11179/#11179-2
 [4]: http://metadata-standards.org/11179/#11179-3
 [5]: http://metadata-standards.org/11179/#11179-4
 [6]: http://metadata-standards.org/11179/#11179-5
 [7]: http://metadata-standards.org/11179/#11179-6