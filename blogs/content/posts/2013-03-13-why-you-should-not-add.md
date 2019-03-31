---
title: Why you should not add formats to Microsoft Access tables
author: remou
type: post
date: 2013-03-13T18:11:00+00:00
excerpt: |
  Access Version: All
  
  One of the problems with MS Access is that it allows you to do a lot of things that you really should not do, for example adding formats to tables. Here is why adding formats to tables is a bad idea.
  
  1. Create a table
  
  This t&hellip;
url: /index.php/desktopdev/mstech/msaccess/accessvbajetsql/why-you-should-not-add/
views:
  - 8464
rp4wp_auto_linked:
  - 1
categories:
  - Access VBA and Jet SQL

---
Access Version: All

One of the problems with MS Access is that it allows you to do a lot of things that you really should not do, for example adding formats to tables. Here is why adding formats to tables is a bad idea.

#### 1. Create a table

This table has an ID and a Date duplicated to provide 4 fields, 2 of which have formats applied, as you can see below.

![create a table][1]

#### 2. Add three records

As you can see, the data added to the plain field and the formated field is exactly the same.

![Add three records][2]

#### 3. View the table

Well, this looks fine.

![View the table][3]

#### 4. Experience problems

You cannot run a query to get WF-2, it does not exist, the field contains 2, but the format means that anyone viewing the table will be confused about this.

![Experience problems query 1][4]

This query looks like it should work, but it will not, the field does not contain Date(), that is, 2013/03/13, it contains date and time. This is particularly confusing to the user, and will not help at all if you want to get rid of the time part.

![Experience problems query 2][5]

#### 5. Just to be sure

You can run code to see what the fields contain, and add more confusion when it does not match the view of the table.

![Experience problems query 2][6]

#### 6. Which is why

Adding formats to tables just disguises content and should be avoided. Adding formats to forms is a completely different story, and quite acceptable.

 [1]: http://ltd.remou.com/access/format/createtable.jpg ""
 [2]: http://ltd.remou.com/access/format/addrecords.jpg ""
 [3]: http://ltd.remou.com/access/format/viewtable.jpg ""
 [4]: http://ltd.remou.com/access/format/runquery.jpg ""
 [5]: http://ltd.remou.com/access/format/runquerydate.jpg ""
 [6]: http://ltd.remou.com/access/format/contents.jpg ""