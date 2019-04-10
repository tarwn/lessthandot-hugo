---
title: Removing OS Files Through a Stored Procedure
author: SQLology ~ Kim Tessereau
type: post
date: 2011-05-07T13:00:00+00:00
ID: 1162
excerpt: |
  Deleting OS files from a stored procedure:  
   
  Here is one way to remove old backup files from the OS through a stored procedure.  The procedure can be compiled in any database.  Once compiled you can run the stored procedure from a scheduled job to r&hellip;
url: /index.php/datamgmt/datadesign/removing-os-files-through-a/
views:
  - 4280
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
**Deleting OS files from a stored procedure: ** 

 Here is one way to remove old backup files from the OS through a stored procedure.  The procedure can be compiled in any database.  Once compiled you can run the stored procedure from a scheduled job to remove old backup files from the OS.

 

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">IF</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: gray;">EXISTS</span><span style="color: blue;"> </span><span style="color: gray;">(</span><span style="color: blue;">SELECT</span> [name] <span style="color: blue;">FROM</span> dbo<span style="color: gray;">.</span><span style="color: green;">sysobjects</span> <span style="color: blue;">WHERE</span> [name] <span style="color: gray;">=</span> <span style="color: red;">'usp_Delete_OS_Files_By_Date'</span> <span style="color: gray;">AND</span> <span style="color: blue;">TYPE</span> <span style="color: gray;">=</span> <span style="color: red;">'P'</span><span style="color: gray;">)</span><span style="mso-spacerun: yes;">  </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">DROP</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">PROCEDURE</span> dbo<span style="color: gray;">.</span>usp_Delete_OS_Files_By_Date</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">GO</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">CREATE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">PROCEDURE</span> dbo<span style="color: gray;">.</span>usp_Delete_OS_Files_By_Date<span style="color: blue;"> </span><span style="color: gray;">(</span>@SourceDir <span style="color: blue;">varchar</span><span style="color: gray;">(</span>255<span style="color: gray;">),</span> @SourceFile <span style="color: blue;">varchar</span><span style="color: gray;">(</span>255<span style="color: gray;">),</span> @DaysToKeep <span style="color: blue;">int</span><span style="color: gray;">)</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">AS</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">BEGIN</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;">/***********************************************************************************<span style="mso-spacerun: yes;">     </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;">Description:<span style="mso-spacerun: yes;">  </span>Delete operating system files older than n days<span style="mso-spacerun: yes;">                   </span>****<span style="mso-spacerun: yes;">       </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;">Prototype:</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;">EXEC dbo.usp_Delete_OS_Files_By_Date<span style="mso-spacerun: yes;">                                      </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">                                      </span>@SourceDir = '\smpprod01BackupsSGC'</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">                                    </span>, @SourceFile = 'SGC_Backup_*'</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">              </span><span style="mso-spacerun: yes;">                      </span>, @DaysToKeep = 3<span style="mso-spacerun: yes;">                  </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">                                                                                </span>**** </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;">History<span style="mso-spacerun: yes;">                                                                         </span>****</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;">05/06/2011</span><span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;"> Kim Tessereau <span style="mso-tab-count: 1;">     </span>Created <span style="mso-spacerun: yes;">       </span><span style="mso-spacerun: yes;">                                   </span>****</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;">************************************************************************************/</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">SET</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">NOCOUNT</span> <span style="color: blue;">ON</span><span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">DECLARE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> @CurrentFileDate <span style="color: blue;">char</span><span style="color: gray;">(</span>10<span style="color: gray;">),</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;">@OldFileDate <span style="color: blue;">char</span><span style="color: gray;">(</span>10<span style="color: gray;">),</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;">@SourceDirFOR <span style="color: blue;">varchar</span><span style="color: gray;">(</span>255<span style="color: gray;">),</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;">@FileName <span style="color: blue;">varchar</span><span style="color: gray;">(</span>255<span style="color: gray;">),</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;">@DelCommand <span style="color: blue;">varchar</span><span style="color: gray;">(</span>255<span style="color: gray;">);</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">SET</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> @CurrentFileDate <span style="color: gray;">=</span> <span style="color: fuchsia;">CONVERT</span><span style="color: gray;">(</span><span style="color: blue;">char</span><span style="color: gray;">(</span>10<span style="color: gray;">),</span><span style="color: fuchsia;">getdate</span><span style="color: gray;">(),</span>121<span style="color: gray;">);</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">SET</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> @OldFileDate <span style="color: gray;">=</span> <span style="color: fuchsia;">CONVERT</span><span style="color: gray;">(</span><span style="color: blue;">char</span><span style="color: gray;">(</span>10<span style="color: gray;">),</span><span style="color: fuchsia;">DATEADD</span><span style="color: gray;">(</span>dd<span style="color: gray;">,-</span>@DaysToKeep<span style="color: gray;">,</span>@CurrentFileDate<span style="color: gray;">),</span>121<span style="color: gray;">);</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">SET</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> @SourceDirFOR <span style="color: gray;">=</span> <span style="color: red;">'FOR %I IN (“'</span> <span style="color: gray;">+</span> @SourceDir <span style="color: gray;">+</span> @SourceFile <span style="color: gray;">+</span> <span style="color: red;">'”) DO @ECHO %~nxtI'</span><span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;">— Get OS File information from the OS</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">CREATE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">TABLE</span> #Temp_01<span style="color: blue;"><span style="mso-spacerun: yes;">      </span></span><span style="color: gray;">(</span> [lineorder] <span style="color: blue;">int</span> <span style="color: blue;">IDENTITY</span><span style="color: gray;">(</span>1<span style="color: gray;">,</span>1<span style="color: gray;">)</span><span style="mso-spacerun: yes;">   </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">                           </span><span style="color: gray;">,</span> [OSInfo] <span style="color: blue;">varchar</span><span style="color: gray;">(</span>255<span style="color: gray;">)</span> <span style="color: gray;">);</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">INSERT</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">INTO</span> #Temp_01</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">EXEC</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">master</span><span style="color: gray;">..</span><span style="color: maroon;">xp_cmdshell</span><span style="color: blue;"> </span>@SourceDirFOR<span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;">— Put the OS File info in date order</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">CREATE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">TABLE</span> #Temp_02<span style="color: blue;"><span style="mso-spacerun: yes;">      </span></span><span style="color: gray;">(</span> [lineorder] <span style="color: blue;">int</span><span style="mso-spacerun: yes;">    </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">                           </span><span style="color: gray;">,</span> [TimeStamp] <span style="color: blue;">datetime</span> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">                           </span><span style="color: gray;">,</span> [FileName] <span style="color: blue;">varchar</span><span style="color: gray;">(</span>255<span style="color: gray;">)</span> <span style="color: gray;">);</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">INSERT</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">INTO</span> #Temp_02<span style="color: blue;"> </span><span style="color: gray;">(</span>[lineorder]<span style="color: gray;">,</span> [Timestamp]<span style="color: gray;">,</span> [FileName]<span style="color: gray;">)</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">SELECT</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> [lineorder]</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">      </span><span style="color: gray;">,</span> <span style="color: fuchsia;">CONVERT</span><span style="color: blue;"> </span><span style="color: gray;">(</span><span style="color: blue;">char</span><span style="color: gray;">(</span>10<span style="color: gray;">),</span><span style="color: fuchsia;">SUBSTRING</span><span style="color: gray;">(</span>[OSInfo]<span style="color: gray;">,</span>1<span style="color: gray;">,</span>10<span style="color: gray;">),</span> 121<span style="color: gray;">)</span> <span style="color: blue;">AS</span> [TimeStamp]<span style="mso-spacerun: yes;">                      </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">      </span><span style="color: gray;">,</span> <span style="color: fuchsia;">SUBSTRING</span><span style="color: gray;">(</span>[OSInfo]<span style="color: gray;">,</span>21<span style="color: gray;">,</span>255<span style="color: gray;">)</span> <span style="color: blue;">AS</span> [FileName]<span style="mso-spacerun: yes;">                       </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">FROM</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> #Temp_01<span style="mso-spacerun: yes;">                   </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">WHERE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> [OSInfo] <span style="color: gray;">IS</span> <span style="color: gray;">NOT</span> <span style="color: gray;">NULL</span><span style="mso-spacerun: yes;">            </span><span style="mso-spacerun: yes;">   </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">ORDER</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">BY</span> [lineorder]<span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: green; font-size: 10pt; mso-no-proof: yes;">— Loop through OS Information and create delete command and execute </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">DECLARE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> DeleteCursor <span style="color: blue;">CURSOR</span> <span style="color: blue;">READ_ONLY</span> <span style="color: blue;">FOR</span><span style="mso-spacerun: yes;">  </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">SELECT</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> [FileName]<span style="mso-spacerun: yes;">      </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">FROM</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> #Temp_02 </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">WHERE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> [TimeStamp] <span style="color: gray;"><=</span> @OldFileDate<span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">OPEN</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> DeleteCursor<span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">FETCH</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">NEXT</span> <span style="color: blue;">FROM</span> DeleteCursor <span style="color: blue;">INTO</span> @FileName<span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">WHILE </span><span style="font-family: &amp;amp; color: gray; font-size: 10pt; mso-no-proof: yes;">(</span><span style="font-family: &amp;amp; color: fuchsia; font-size: 10pt; mso-no-proof: yes;">@@fetch_status</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: gray;"><></span> <span style="color: gray;">–</span>1<span style="color: gray;">)</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">BEGIN</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">      </span><span style="color: blue;">IF </span><span style="color: gray;">(</span><span style="color: fuchsia;">@@fetch_status</span> <span style="color: gray;"><></span> <span style="color: gray;">–</span>2<span style="color: gray;">)</span><span style="mso-spacerun: yes;">    </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">            </span><span style="color: blue;">BEGIN</span><span style="mso-spacerun: yes;">      </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">            </span><span style="color: blue;">SET</span> @DelCommand <span style="color: gray;">=</span> <span style="color: red;">'</span></span><span style="font-family: &amp;amp; color: red; font-size: 10pt; mso-no-proof: yes;">DEL</span><span style="font-family: &amp;amp; color: red; font-size: 10pt; mso-no-proof: yes;"> /Q “'</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: gray;">+</span> @SourceDir <span style="color: gray;">+</span> @FileName <span style="color: gray;">+</span> <span style="color: red;">'”'</span><span style="color: gray;">;</span><span style="mso-spacerun: yes;">     </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">            </span><span style="color: blue;">EXEC</span> <span style="color: blue;">master</span><span style="color: gray;">..</span><span style="color: maroon;">xp_cmdshell</span><span style="color: blue;"> </span>@DelCommand<span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 3;">                  </span><span style="color: blue;">END</span><span style="mso-spacerun: yes;">  </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">FETCH</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">NEXT</span> <span style="color: blue;">FROM</span> DeleteCursor <span style="color: blue;">INTO</span> @FileName<span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">END</span><span style="font-family: &amp;amp; color: gray; font-size: 10pt; mso-no-proof: yes;">;</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;"> </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">CLOSE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> DeleteCursor<span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">DEALLOCATE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> DeleteCursor<span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: gray; font-size: 10pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">END</span><span style="font-family: &amp;amp; color: gray; font-size: 10pt; mso-no-proof: yes;">;</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">GO</span>
</p>

 