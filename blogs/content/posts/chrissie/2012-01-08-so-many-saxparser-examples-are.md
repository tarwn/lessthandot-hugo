---
title: So many saxparser examples are just wrong and here is why.
author: Christiaan Baes (chrissie1)
type: post
date: 2012-01-08T05:32:00+00:00
ID: 1481
excerpt: |
  If you go on Google and look for an example on how to parse xml in java you will find plenty of examples using the saxparser. And you will see them doing something like this.
  
  package be.baes.thisDevelopersLifePlayer.rss;
  
  import android.util.Log;&hellip;
url: /index.php/desktopdev/suntech/java/so-many-saxparser-examples-are/
views:
  - 6514
categories:
  - Java
tags:
  - java
  - sax
  - xml

---
If you go on Google and look for an example on how to parse xml in java you will find plenty of examples using the saxparser. And you will see them doing something like this.

```java
package be.baes.thisDevelopersLifePlayer.rss;

import android.util.Log;
import be.baes.thisDevelopersLifePlayer.Constants;
import org.xml.sax.helpers.DefaultHandler;
import org.xml.sax.*;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RSSHandler extends DefaultHandler 
{
	
	RSSFeed _feed;
	RSSItem _item;
	final int RSS_TITLE = 1;
	
        int depth = 0;
	int currentstate = 0;
    
    /*
      * Constructor 
      */
	public RSSHandler()
	{
	}
	
	/*
	 * getFeed - this returns our feed when all of the parsing is complete
	 */
	public RSSFeed getFeed()
	{
		return _feed;
	}
	
	
	public void startDocument() throws SAXException
	{
		// initialize our RSSFeed object - this will hold our parsed contents
		_feed = new RSSFeed();
		// initialize the RSSItem object - we will use this as a crutch to grab the info from the channel
		// because the channel and items have very similar entries..
		_item = new RSSItem();

	}
	public void endDocument() throws SAXException
	{
	}
	
	public void startElement(String namespaceURI, String localName,String qName, Attributes atts) throws SAXException
	{
		depth++;
		if (localName.equals("item"))
		{
			// create a new item
			_item = new RSSItem();
			return;
		}
		if (localName.equals("title"))
		{
			currentstate = RSS_TITLE;
			return;
		}
		currentstate = 0;
	}
	
	public void endElement(String namespaceURI, String localName, String qName) throws SAXException
	{
    		depth--;
		if (localName.equals("item"))
		{
			_feed.addItem(_item);
		}
	}

    	public void characters(char ch[], int start, int length)
	{
	  Sring result = new String(ch,start,length));
          switch (currentstate)
          {
            case RSS_TITLE:
              _item.setTitle(result);
              currentstate = 0;
              break;
          }
        }
    
}
```
The biggest problem in the above code is that they assume that the characters method will only be called once per element. But that&#8217;s not true. It can and will be called multiple times per element, especially if you have encoded html tags in there. So be careful and use the below method instead.

Here we use a StringBuffer to concatenate the entire value of the element and then on endelement we write it to our new rssitem object.

```java
package be.baes.thisDevelopersLifePlayer.rss;

import android.util.Log;
import be.baes.thisDevelopersLifePlayer.Constants;
import org.xml.sax.helpers.DefaultHandler;
import org.xml.sax.*;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RSSHandler extends DefaultHandler 
{
	
	RSSFeed _feed;
	RSSItem _item;
	final int RSS_TITLE = 1;
	
        int depth = 0;
	int currentstate = 0;
    
    /*
      * Constructor 
      */
	public RSSHandler()
	{
	}
	
	/*
	 * getFeed - this returns our feed when all of the parsing is complete
	 */
	public RSSFeed getFeed()
	{
		return _feed;
	}
	
	
	public void startDocument() throws SAXException
	{
		// initialize our RSSFeed object - this will hold our parsed contents
		_feed = new RSSFeed();
		// initialize the RSSItem object - we will use this as a crutch to grab the info from the channel
		// because the channel and items have very similar entries..
		_item = new RSSItem();

	}
	public void endDocument() throws SAXException
	{
	}
	
	public void startElement(String namespaceURI, String localName,String qName, Attributes atts) throws SAXException
	{
		depth++;
		if (localName.equals("item"))
		{
			// create a new item
			_item = new RSSItem();
			return;
		}
		if (localName.equals("title"))
		{
			currentstate = RSS_TITLE;
			return;
		}
		currentstate = 0;
	}
	
	public void endElement(String namespaceURI, String localName, String qName) throws SAXException
	{
        switch (currentstate)
        {
            case RSS_TITLE:
                _item.setTitle(buffer.toString());
                currentstate = 0;
                break;
        }
        buffer = new StringBuffer();
		depth--;
		if (localName.equals("item"))
		{
			_feed.addItem(_item);
		}
	}

    private StringBuffer buffer = new StringBuffer();
                             
	public void characters(char ch[], int start, int length)
	{
		buffer.append(new String(ch,start,length));
	}
    
}
```