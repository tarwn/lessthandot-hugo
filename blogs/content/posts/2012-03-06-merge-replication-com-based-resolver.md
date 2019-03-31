---
title: Merge Replication – COM Based Resolver throws Buffer too small Error
author: Ted Krueger (onpnt)
type: post
date: 2012-03-06T10:20:00+00:00
excerpt: |
  Troubleshooting Methodolgy
  One of my favorite books is, “Troubleshooting SQL Server – A Guide for the Accidental DBA”.  I may have helped author the book but I also rely on the book and the other authors to remind me just how important troubleshooting&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/merge-replication-com-based-resolver/
views:
  - 17872
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
**Troubleshooting Methodolgy**

One of my favorite books is, “[Troubleshooting SQL Server – A Guide for the Accidental DBA][1]”.  I may have helped author the book but I also rely on the book and the other authors to remind me just how important troubleshooting as a distinct set of tasks with parameters and means to deductions is.  The book has a great deal of content in terms of SQL Server performance paths that start from a problem andend in a resolution.  All of this content is absolutely invaluable to both beginning and seasoned DBAs.  One of my favorite chapters in the book, Chapter 1: A Performance Troubleshooting Methodology, truly grasps a concept that can be followed, not only by DBAs, but by any professional working on a specific problem.

 

This article demonstrates a firm grasp of the concepts that Jonathan goes over in Chapter 1.  The article will also show that, even though Chapter 1 discusses SQL Server and may direct the reader to think it pertains only to SQL Server, using the methodology Jonathan explains and walks through can be used in any troubleshooting situation to help find a clear and precise resolution in a timely manner.

**COM Based Resolver Failure – Buffer Too Small**

Recently a bug in Merge Replication on a specific version and build was found.  The bug revolves around the COM based interface for resolving conflicts with an article in a merge replication publication.   The exact symptoms occurred when Service Pack 3 was applied to SQL Server 2008. Packaged within SP3 for SQL Server 2008 was the COM interface for Merge Replication file, replrec.dll.  This file is the interface that is utilized when the conflict resolution event occurs.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-118.png?mtime=1331005008"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-118.png?mtime=1331005008" width="624" height="65" /></a>
</div>

COM based conflict resolver errors

**Error messages:**

##### Buffer too small. (Exception from HRESULT: 0x80028016 (TYPE\_E\_BUFFERTOOSMALL)) (Source: MSSQL\_REPL, Error number: MSSQL\_REPL-2147319786)</p> 

ec.dll file was not originally under scrutiny.  Several troubleshooting steps were taken prior to looking further into the replrec.dll.  Those steps are not as important in regards to showing the solution provided in this article, but the steps are valid in the methodology of troubleshooting.  Remember to follow some basic rules of troubleshooting

  1. Change one attribute of the system at a time
  2. Fully test each attribute change before concluding if it worked or not
  3. Document each attribute change
  4. Revert or decide if the attribute should be left for further changes

If this practice is followed, troubleshooting becoming a problem itself will be limited.  Imagine if you change three things at once, test the system, and  the fixes work.  Which one actually was the fix to the problem?  How would you document this and report it to management or even to Microsoft Connect as a bug?

The problem of the buffer error is a direct result of SP3 being applied.  This has been identified and tested thoroughly in virtual environments.  After the troubleshooting steps, following the 4 step process, were performed to a point, the replrec.dll file was reviewed itself.  Once the version of the file was reviewed, the change to a newer version was identified.

**Looking into the replrec.dll**

In order to review the replrec.dll, the follwing steps must be performed to build an il file that can be used to review the code itself from the DLL.  This is done by using the tlbimp command run from the Visual Studio command prompt (or naviagting to the VC folder in the exact version you are running of Visual Studio).  Running the tlbimp exports the file to another DLL that can then be used to run the ildasm command to generate the actual il file.

> This can be seen in great detail by [Doug Arterbum in an article][2] showing how to resolve buffer parameter differences in the file itself</p>
For further verification that the replrec.dll was indeed being utilized fully for the interface and the conflict resolution, Process Monitor was used while loading steps from the resolver and the backend replication processing in replmerg.exe.  The following results were observed and recorded.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-119.png?mtime=1331005010"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-119.png?mtime=1331005010" width="897" height="610" /></a>
</div>

The SQLConflictResolver.dll is the conflict resolver itself but we can see the replrec.dll it referrred to by replmerg.exe to complete the interface process.

Once the review of the il file was performed and the parameter changes from Doug’s article were changed, the buffer errors were not resolved.  The next stages were to revert any parameter changes (following the 4-step troubleshooting process) and then compare the actual replrec.dll file itself to the previous version prior to the file that SP3 installed.

A copy of replrec.dll was obtained prior to SP3 being applied and a replrec.dll after SP3 was applied.  Running the following commands genearted the il files for each.

c:Program Files (x86)Microsoft Visual Studio 10.0VC>tlbimp &#8220;C:VMSetupback date replrecreplrec.dll&#8221; /OUT:SQLCOM_Backdate.dll

c:Program Files (x86)Microsoft Visual Studio 10.0VC>tlbimp &#8220;C:VMSetupreplrec.dll&#8221; /OUT:SQLCOM_Current.dll

c:Program Files (x86)Microsoft Visual Studio 10.0VC>ildasm &#8220;SQLCOM\_backdate.dll&#8221; /OUT=SQLCOM\_backdate.il

c:Program Files (x86)Microsoft Visual Studio 10.0VC>ildasm &#8220;SQLCOM\_Current.dll&#8221; /OUT=SQLCOM\_current.il

Comparing the contents of the SQLCOM\_backdate.il and SQLCOM\_current.il files reveals the primary difference in the current replrec.dll and the post dated replrec.dll being the method SetColumnStatus. It did not exist in the current replrec.dll, but was in the backdated replrec.dll file.

The SetColumnStatus method is shown below.

<pre>.method public hidebysig newslot virtual 
          instance void  SetColumnStatus(uint32 ColumnId,
                                         uint32 dbStatus) runtime managed internalcall
  {
    .override SQLCOM_Current.IReplRowChange::SetColumnStatus
  } // end of method CLRBusinessLogicModuleClass::SetColumnStatus

  .method public hidebysig newslot virtual 
          instance void  UpdateRow() runtime managed internalcall</pre>

 

Using a text file comparison tool, the following shows the findings of the missing method in the SP3 replrec.dll and the previous replrec.dll

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-120.png?mtime=1331005012"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-120.png?mtime=1331005012" width="890" height="445" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-121.png?mtime=1331005012"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-121.png?mtime=1331005012" width="898" height="436" /></a>
</div>

This results in the call in the logical COM based resolver SQL ConclictResolver.DLL at

pRowChange.SetColumn2(intColumn, myBuffer.ToInt32, intBufferLenActual)

Which initiates the SetColumnStatus method, a failure point.

Given the monitoring from Process Monitor results and loading the dynamic link libraries on the system with normal SQL Server operations, replrec.dll was found to only be served when the COM interface was initiated due to the conflict resolver.  The resources utilized were also limited to the replrec.dll and SQLConflictResolver link libraries showing that no other supporting DLL or objects on the system require back-dating.

This concludes a stable and safe resolution of back-dating the replrec.dll in the COM folder for both the 32bit and 64bit folders in order to remove the buffer errors caused by the newer version contained in SQL Server 2008 SP3.  Again, the 4-step process outlined earlier in this article was used in the findings.

Testing the SQL Server by applying SP3 and then replacing the replrec.dll with the replrec.dll that existed prior to applying SP3, proved the replacement of the back-dated replrec.dll was the stable solution.

As shown below in steps where a subscription was synchronizing successfully, SP3 was applied, the subscription forced the Buffer too small error, the replrec.dll was back dated and then the error was resolved.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-122.png?mtime=1331005013"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-122.png?mtime=1331005013" width="624" height="136" /></a>
</div>

**Conclusion**

This article shows an exact method to resolve the case when COM interfaces are used with Merge Replication and the SP3 version of replrec.dll is applied to a SQL Server 2008 instance.  It also shows that the troubleshooting methodologies and 4-step process outlined are critical to coming to a sound and stable solution to any problem.  These steps and Chapter 1 from “[Troubleshooting SQL Server – A Guide for the Accidental DBA][1]” may be directed to other steps to resolve SQL Server problems, but they truly can be used with any administration event where a problem requires a step-by-stp process to find a resolution.  The resolution is then well documented and put in place to allow further transactions to continue as the business needs them.

 [1]: http://www.simple-talk.com/books/sql-books/troubleshooting-sql-server-a-guide-for-the-accidental-dba/
 [2]: http://www.replicationanswers.com/CustomResolver.asp