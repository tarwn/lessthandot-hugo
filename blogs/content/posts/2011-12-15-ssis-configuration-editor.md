---
title: SQL Server Integration Services tools to have – Configuration File Editor
author: Ted Krueger (onpnt)
type: post
date: 2011-12-15T11:33:00+00:00
ID: 1435
excerpt: |
  In a previous post, SQL
  Server Integration Services tools to have – Expression Editor &amp; Tester,
  the expression editor value was looked at.&nbsp;
  That tool is extremely useful for preparing and maintaining
  expressions.&nbsp; Expressions are extre&hellip;
url: /index.php/datamgmt/dbprogramming/ssis-configuration-editor/
views:
  - 3609
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSIS

---
<p id="__mce">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>
</p>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<span style="font-family: verdana,geneva;"><span style="font-size: small;">In a previous post, <a href="/index.php/DataMgmt/ssis/sql-server-integration-services-tools">SQL Server Integration Services tools to have – Expression Editor & Tester</a>, I discussed a tool that is extremely useful for preparing and maintaining expressions. Expressions are extremely common now to enforce dynamic and mobile SSIS packages. Tools like the editor make that task more efficient. Another tool that has recently been provided on Codeplex is the <a href="http://ssisconfigeditor.codeplex.com/">Configuration File Editor</a>.</span></span>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;"><strong style="mso-bidi-font-weight: normal;">Overview</strong></span></span>
</p>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;">Given an SSIS package that consists of two packages, Package.dtsx and Package1.dtsx, the packages have a configuration file that allows a setting in Packge.dtsx for the execute package task. The value that can be altered is the package name property. This makes the task of the initiating package to be more dynamic on what package it can call. </span></span>
</p>

<div class="image_block">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;"><a href="/media/blogs/DataMgmt/-93.png?mtime=1323611029"><img src="/wp-content/uploads/blogs/DataMgmt/-93.png?mtime=1323611029" alt="" width="179" height="97" /></a></span></span>
</div>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;"><!--?xml:namespace prefix = v ns = "urn:schemas-microsoft-com:vml" /--></span></span>
</p>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<span style="font-family: verdana,geneva;"><span style="font-size: small;">In a production setting and more commonly in my own practices, Business Intelligence Studio or any version of Visual Studio is not allowed as a standard installation. This is to prevent unwanted use of resources on the server. Servers should be built and maintained for their distinct purpose. A database server’s purpose is not to be a development ready machine. SSIS Configuration Editor assists in not having those IDEs on the servers by allowing a thin and simple method to configure configuration files.</span></span>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<span style="font-family: verdana,geneva;"><span style="font-size: small;">In the initial screens of the editor, the server and the package folder are shown. The package source is also available for both a SQL Server and File System store.</span></span>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"><br /></span></span>

<div class="image_block">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;"><a href="/media/blogs/DataMgmt/-94.png?mtime=1323611029"><img src="/wp-content/uploads/blogs/DataMgmt/-94.png?mtime=1323611029" alt="" width="624" height="144" /></a></span></span>
</div>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>
</p>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;">The first nice feature is the evaluation of the configuration file. Notice the red font in the above image. This indicates the configuration file is missing or there are permission issues getting to the file. Even if you do not intend to edit the files, this feature can prove valuable to validate the configuration files on a scheduled basis. </span></span>
</p>

<div class="image_block">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;"><a href="/media/blogs/DataMgmt/-95.png?mtime=1323611030"><img src="/wp-content/uploads/blogs/DataMgmt/-95.png?mtime=1323611030" alt="" width="624" height="134" /></a></span></span>
</div>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>
</p>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: verdana,geneva;"><span style="font-size: small;">Once the issue with the file is resolve, the font returns to black. Now, the editor cannot edit the internals of the configuration files. The purpose is to have multiple configuration files and a quick method for you to alter the configuration file that is used per that package. In a transport or mobile package setting, this is extremely useful.</span></span>
</p>

<span style="font-family: verdana,geneva;"><span style="font-size: small;"> </span></span>