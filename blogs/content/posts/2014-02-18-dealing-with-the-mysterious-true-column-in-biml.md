---
title: Dealing with the mysterious "True" column in BIML
author: Koen Verbeeck
type: post
date: 2014-02-18T14:41:34+00:00
ID: 2423
url: /index.php/datamgmt/ssis/dealing-with-the-mysterious-true-column-in-biml/
views:
  - 14890
rp4wp_auto_linked:
  - 1
categories:
  - SSIS
tags:
  - bids helper
  - biml
  - bug
  - issue
  - sql server
  - ssis

---
<p style="text-align: justify">
  Recently I ran into the following error when compiling BIML into SSIS packages:
</p>

<p style="text-align: justify">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/02/BIMLissue.png"><img class="alignnone size-full wp-image-2425" alt="BIMLissue" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/02/BIMLissue.png" width="1200" height="92" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/02/BIMLissue.png 1200w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/02/BIMLissue-300x23.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/02/BIMLissue-1024x78.png 1024w" sizes="(max-width: 1200px) 100vw, 1200px" /></a>
</p>

<p style="text-align: justify">
  The message itself is not very revealing:
</p>

<p style="text-align: justify">
  <i>The data type for input buffer column 'True' on the ScriptComponent input buffer 'Input0' does not match the mapped output column from the previous transformation.</i>
</p>

<p style="text-align: justify">
  You might have guessed it already; there is no column called <b>True</b> in the data flow. A quick Google search brought back this topic on the Varigence forums: <a href="http://www.varigence.com/Forums?threadID=289">Error: Script Transformation</a>. However, uninstalling BIDS Helper and installing an older version (and possible lose other functionality) is not an option. Besides, the BIML code worked when I generated the package for another table, so BIDS Helper was not to blame.
</p>

<p style="text-align: justify">
  After a quick debug using Messageboxes – so old school and <a href="http://www.bimlgeek.com/1/post/2013/08/using-windows-forms-messageboxshow-to-debug-bimlscript.html">it is possible in BIMLScript by the way</a> – I confirmed it had nothing to do with the C# code generating the BIML.
</p>

<p style="text-align: justify">
  So let's take another good look at the error message. It mentions something about data types not being correct. All the data types were simple varchars, numerics and datetimes and I was sure I mapped them to the corresponding <a href="http://msdn.microsoft.com/en-us/library/system.data.dbtype(v=vs.110).aspx">DBType</a>. After all, the code works for other tables.
</p>

<p style="text-align: justify">
  And then it hit me: the destination table had some columns with a greater length than the source column. For example varchar(250) instead of varchar(213). I enlarged them to deal with the risk of other sources being added with larger columns. In the SSIS data flow, the metadata of the columns is derived from the columns returned by the source query. However, in the script component I specified the metadata of the destination columns, all having another length. I changed the lengths of the destination columns, lo and behold, the BIML script compiled successfully. Apparently SSIS (or BIML) has some serious issues of the metadata of an input column doesn't match 100% the metadata of the output column of the previous component.
</p>

<p style="text-align: justify">
  If now only one of the kind BIML developers would substitute <i>true</i> with the actual column name, life would be much easier.
</p>