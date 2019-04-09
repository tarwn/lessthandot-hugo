---
title: 'X# a domain specific language designed to quickly create Web applications and services'
author: SQLDenis
type: post
date: 2009-01-21T22:31:10+00:00
ID: 294
url: /index.php/webdev/webdesigngraphicsstyling/x-a-domain-specific-language-designed-to/
views:
  - 7039
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - 'x#'
  - x-sharp

---
Just saw the X# language website, what do you think of X#?

X# (pronounced X-sharp) is a domain specific language designed to quickly create Web applications and services. In X# everything is represented as a hierarchical structure or tree and instead of using functions to manipulate information or perform actions, all possible operations you can think of are done by adding, removing or changing nodes from this tree. Since there are no functions to learn and everything is done intuitively, even inexperienced developers can create complex Web applications and services in record time.

X# is …

Unlike other similar DSLs which require developers to learn hundreds of libraries and thousands of functions, X# is simple, intuitive, and easy to learn. It only uses 30 statements and four data types (node, string, number, and boolean) to create any application or service you can think of.

Here is an example

Retrieve all RSS Feeds from NY Times and save those entries containing the word oil in a MySQL database titled oil_news:

```xml
<xsp:append-child target="document('xdbc:mysql://192.168.1.27:3306/maindb')/oil_news">  
       <xsp:for-each select="(document('http://www.nytimes.com/services/xml/rss/nyt/HomePage.xml')/text() >> /library/xml/pi('import'))/channel/item[contains(title,'oil')]">  
            <row>  
                <title><xsp:text value="{title}"/></title>  
               <description><xsp:text value="{description}"/></description>  
                <link><xsp:text value="{link}"/></link>  
            </row>  
        </xsp:for-each>  
    </xsp:append-child>  
```

Some samples are here http://www.xsharp.org/samples/ and the main site is here: http://www.xsharp.org/