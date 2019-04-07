---
title: Setting a standard DateFormat for SQL Server
author: George Mastros (gmmastros)
type: post
date: 2008-11-20T16:58:53+00:00
ID: 213
url: /index.php/datamgmt/datadesign/setting-a-standard-dateformat-for-sql-se/
views:
  - 61456
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Each SQL Server has a default language. You can see what the default language is by executing these commands (in a query window).

sql
sp_configure 'default language'
```

This will tell you what the default language is (sort of). It actually returns a config_value with an integer that represents the language id.

You can then run&#8230;

sql
sp_helplanguage
```

You will see a list of languages that SQL Server supports. 

The odd thing here is that the server&#8217;s language setting will not solve your problem. This setting configures the default language for NEW users. By changing this setting, existing users will continue to have their original language. This is where it gets interesting because it&#8217;s the login&#8217;s language setting that determines SQL Server&#8217;s date format.

For example, if user A has a default language of us_english, then a date of 4/6/2006 will be interpreted as April 6, 2006. If user B has a default language of &#8216;British&#8217;, then the date will be interpreted as June 4, 2006.

So far, I&#8217;m just presenting some background information and haven&#8217;t really solved the problem. 

The good news is that you can change the default language for a user so that subsequent logins will exhibit the correct interpretation of dates. Here&#8217;s how:

You can set the default language for a user by issueing the following command.

sql
sp_defaultlanguage @loginame = 'LoginName', @language = 'Language'
```

After running this command, you will need to logout and back in to the database in order for the change to take affect. The good news is that the language setting only needs to be done once (for each user in the database).

There is an alternative method, but it only works for the current session. You can set the language in your query (much the same way the Set DateFormat works). When you disconnect from the database, the language setting is NOT saved. Set Language differs from Set DateFormat regarding weekday names and month names, for example:

sql
set language 'us_english'

Select Convert(DateTime, '4/6/2006'), 
       DateName(weekday, '4/6/2006'),
       DateName(Month, '4/6/2006')

set language 'Italian'

Select Convert(DateTime, '4/6/2006'), 
       DateName(Weekday, '4/6/2006'),
       DateName(Month, '4/6/2006')
```
_**Summary**_
  
You can set the default language for new logins by configuring the server&#8217;s default language by using sp_configure.

You can change a user&#8217;s default language by using sp_defaultlanguage.

You can temporarily change the language for a query by using Set Language

I hope you find this useful.