---
title: The shaky bug in SSIS
author: Koen Verbeeck
type: post
date: 2015-03-24T11:52:20+00:00
ID: 3312
url: /index.php/datamgmt/ssis/the-shaky-bug-in-ssis/
views:
  - 13222
rp4wp_auto_linked:
  - 1
categories:
  - SSIS
tags:
  - bug
  - jitter
  - shake
  - ssis
  - syndicated

---
<p style="text-align: justify">
  You may or may not have noticed it after installing SQL Server 2012: the designer interface in Visual Studio 2010/2012 has had a make-over. Nothing too drastic, but at least the undo button works. However, sometimes when you drag or move tasks/components on the canvas, they start to shake. A lot. And very annoyingly as well. (Quite impossible to take a screenshot of that, so sorry). This behaviour emerged after applying CU6. There are Connect items logged about this issue:
</p>

  * [When dragging SSIS objects on canvas the objects shake/jiggle/jitter][1]
  * [SSIS 2012 Control Flow moving objects is unusable][2]

<p style="text-align: justify">
  Luckily Microsoft patched this bug in CU7. You can download the hotfix <a href="https://support.microsoft.com/en-us/kb/2883424/en-us?wa=wsignin1.0">here</a>. If your environment is up-to-date with the latest patches, most likely you won&#8217;t experience this bug.
</p>

<p style="text-align: justify">
  I patched the Visual Studio 2010 environment at the client and the issue went away. But at home I use Visual Studio 2012 and the issue was still there, even after applying the fix. Fortunately, Twitter was there with the answer:
</p>

<blockquote class="twitter-tweet" lang="en">
  <p>
    <a href="https://twitter.com/Ko_Ver">@Ko_Ver</a> Ok, because the CU that fixes that only updates the DLL&#8217;s for VS2010, you need to manually overwrite the assemblies in VS2012 folder
  </p>
  
  <p>
    — Jan Van humbeek (@janvanhumbeek) <a href="https://twitter.com/janvanhumbeek/status/517569881672544256">October 2, 2014</a>
  </p>
</blockquote>

If you go back to the [first Connect item][1], these steps are described in the work arounds and they work like a charm.

The issue has also been [reported for SSIS 2014][3], and while the item has been closed as fixed, there&#8217;s no explanation on how it is fixed. Maybe in an upcoming CU for SQL Server 2014? It&#8217;s possible the same work around works for SSIS 2014, but I haven&#8217;t tried it (I don&#8217;t have the bug in Visual Studio 2013) so if you do try it, it&#8217;s at your own risk.

 [1]: https://connect.microsoft.com/SQLServer/feedback/details/790470/when-dragging-ssis-objects-on-canvas-the-objects-shake-jiggle-jitter
 [2]: https://connect.microsoft.com/SQLServer/feedback/details/776664/ssis-2012-control-flow-moving-objects-is-unusable
 [3]: https://connect.microsoft.com/SQLServer/feedback/details/1013088/ssis-2014-control-flow-moving-objects-is-unusable