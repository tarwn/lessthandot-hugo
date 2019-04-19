---
title: How to get data out of Microsoft Dynamics Great Plains
author: David Forck (thirster42)
type: post
date: 2009-07-10T12:23:37+00:00
ID: 502
url: /index.php/datamgmt/dbprogramming/mssqlserver/how-to-get-data-out-of-microsoft-dynamic/
views:
  - 1043
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
Well as anyone who has had the luxury of working with Great Plains and its database, you know it's close to impossible to find data in the database. Well fear not, for there is a way to navigate this labyrinth of a database.

The Great Plains developers, as kind as they are, created a tool that allows you to see their documentation on the database and application. If you know the name of the form you want to get data from, you can get it. Here's how to do it.

First you have to log into the actual Great Plains application. If you don't have permission to do this, it's probably a good idea to get set up, especially if you know that you're going to be working with the data a lot. Now, once logged in, go to Tools – Resource Descriptions – Windows.

The easiest way that I've figured out how to use this is to click the find button and type in the name of the window you're trying to use, then click find. If you're lucky the list below will automatically go to the name you typed in, if not, then you've got a bit more searching to do. You can search by Product and by Series. I believe that only certain products have a certain set of series, but I don't know the correlations. But it's easiest to go though one by one, clicking the find button until you find what you're looking for.

Once you find your window name, click on it. At the bottom of the window the two fields should populate, Window Fields and Tables. Now you have the option to try and find data by field, but it's much easier to go by table. Go through the table list and write down all that don't end with SETP or TEMP. MSTR (or master) tables tend to have the main information you'll be looking for. Once you have the list, we're going to go hunting again.

This time, click Tools – Resource Descriptions – Tables. Click the button with the three dots. The list of table names that we got earlier are the table technical names, so click the find button, type in the first table name, click the drop down and select technical name, and click find. Once again, if you're lucky you'll find it, if not, you have to go hunting. Once you find the table click on it and click ok.

The window you are taken to gives you overall information on the table and a list of fields. You can click on each field and click the Field Info button to get data on that field. Note the Physical Name. This is the actual name of the table in the Great Plains database.

Now with the knowledge of this tool, you should be able to more easily find the data you're looking for in Great Plains.