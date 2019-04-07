---
title: We ain’t afraid of no errors!
author: SQLology ~ Kim Tessereau
type: post
date: 2011-04-03T02:28:00+00:00
ID: 1096
excerpt: "Good old fashion error handling..........This is something that is often skipped over when we code because we don't stop and take the time out to plan it out.  I've put together a presentation on this topic and I'll be submitting it to the next SQL Satu&hellip;"
url: /index.php/datamgmt/dbprogramming/we-ain-t-afraid-of-1/
views:
  - 2279
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
<span style="font-family: &amp;amp; font-size: 10pt;">Good old fashion error handling&#8230;&#8230;&#8230;.This is something that is often skipped over when we code because we don&#8217;t stop and take the time out to plan it out.  I&#8217;ve put together a presentation on this topic and I&#8217;ll be submitting it to the next SQL Saturday that I attend, but would like to share the main points here.</span><span style="font-size: small;"><span style="font-family: Times New Roman;"><span style="mso-spacerun: yes;">  </span>Obviously, there is a lot I left out here, don’t want to spoil the presentation!</span></span>

<p style="line-height: 14.25pt; text-indent: -0.25in; padding-left: 30px; margin-left: 0.25in; mso-list: l0 level1 lfo2;">
  <span style="font-family: Symbol; font-size: 10pt; mso-fareast-font-family: Symbol; mso-bidi-font-family: Symbol;"><span style="mso-list: Ignore;">·<span style="font: 7pt &amp;amp;">         </span></span></span><strong><span style="font-family: arial,helvetica,sans-serif;"><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;">Why do we need error handling?</span></span></strong>
</p>

<p style="padding-left: 30px;">
   <span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;"><span style="color: #000000;"><span style="language: en-US; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-ascii-theme-font: minor-latin; mso-fareast-theme-font: minor-fareast; mso-bidi-theme-font: minor-bidi; mso-color-index: 3; mso-font-kerning: 12.0pt; mso-style-textfill-type: solid; mso-style-textfill-fill-themecolor: text2; mso-style-textfill-fill-color: #675D59; mso-style-textfill-fill-alpha: 100.0%;">One of the first things that comes to mind is that the calling process needs to be informed if a stored procedure/script has failed or was successful.</span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt;"> </span><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;"> It is also used to</span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt;"> </span><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;">control the processing of transactions whether they are implicit or explicit.</span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt;"> </span><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;"> The user needs to be alerted if there is a problem too.</span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt;"> </span><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;"> Error handling allows us to be able to code for unexpected results and helps to provide data consistency.</span></span></span></span></span>
</p>

  * <div style="line-height: 14.25pt; text-indent: -0.25in; margin-left: 0.25in; mso-list: l1 level1 lfo1;">
      <span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="font-family: Symbol; font-size: 10pt; mso-fareast-font-family: Symbol; mso-bidi-font-family: Symbol;"><span style="color: #000000;"><span style="font-family: arial,helvetica,sans-serif;"><span><strong><span style="mso-special-format: bullet;">·         </span>@@ERROR Global Variable</strong></span></span></span></span></span></span>
    </div>

<p style="line-height: 14.25pt; margin-left: 0.25in;">
  <span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;"> </span></span><span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="color: #000000;"><span style="language: en-US; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-ascii-theme-font: minor-latin; mso-fareast-theme-font: minor-fareast; mso-bidi-theme-font: minor-bidi; mso-color-index: 3; mso-font-kerning: 12.0pt; mso-style-textfill-type: solid; mso-style-textfill-fill-themecolor: text2; mso-style-textfill-fill-color: #675D59; mso-style-textfill-fill-alpha: 100.0%;"><span style="color: #000000;"><span style="font-family: Times New Roman; font-size: small;">@@ERROR is known as a system variable to SQL Server.</span></span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt;"> </span><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;"> It is read-only and contains the value of the error number that was generated</span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt;"> </span><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;">in the previous transact SQL statement<strong><span style="font-family: &amp;amp; mso-bidi-font-family: +mn-cs; mso-hansi-font-family: Arial;"> that just processed</span></strong>.</span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt;"> </span><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;"> Every statement is evaluated to determine whether or not it was successful.</span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt;"> </span><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;"> So for example:</span></span></span></span></span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">USE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">master</span> <span style="mso-tab-count: 5;">                            </span><span style="color: green;">&#8211;@@ERROR contains 0</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 1;">   </span><span style="color: blue;">SELECT</span> <span style="color: gray;">*</span> <span style="color: blue;">FROM</span> <span style="color: green;">sysobjects</span> <span style="mso-tab-count: 1;">     </span><span style="mso-tab-count: 1;">      </span><span style="color: green;">&#8211;@@ERROR contains 0</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">USE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> model <span style="mso-tab-count: 5;">                             </span><span style="color: green;">&#8211;@@ERROR contains 0</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 1;">   </span><span style="color: blue;">SELECT</span> <span style="color: gray;">*</span> <span style="color: blue;">FROM</span> <span style="color: green;">sysdatabases</span> <span style="mso-tab-count: 1;">   </span><span style="mso-tab-count: 1;">      </span><span style="color: green;">&#8211;@@ERROR contains 208</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">IF</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: gray;">EXISTS</span><span style="color: blue;"> </span><span style="color: gray;">(</span><span style="color: blue;">SELECT</span> <span style="color: gray;">*</span> <span style="color: blue;">FROM</span> <span style="color: green;">sysservers</span><span style="color: gray;">)</span> <span style="mso-tab-count: 1;">  </span><span style="color: green;">&#8211;@@ERROR contains 208</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 1;">   </span><span style="color: blue;">SELECT</span> 1<span style="color: gray;">=</span>1<span style="mso-tab-count: 2;">        </span><span style="mso-tab-count: 3;">                 </span><span style="color: green;">&#8211;@@ERROR contains 102</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">IF</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: fuchsia;">@@ERROR</span> <span style="color: gray;">></span> 0 <span style="mso-tab-count: 4;">                        </span><span style="color: green;">&#8211;@@ERROR contains 0 </span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 1;">   </span><span style="color: blue;">GOTO</span> somewhere <span style="mso-tab-count: 4;">                     </span><span style="color: green;">&#8211;@@ERROR contains 0</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">somewhere:</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="mso-tab-count: 5;">                            </span><span style="color: green;">&#8211;@@ERROR contains 0</span></span>
</p>

<p style="line-height: 14.25pt; text-indent: -0.25in; margin-left: 0.25in; mso-list: l2 level1 lfo3;">
  <span style="font-family: Symbol; font-size: 10pt; mso-fareast-font-family: Symbol; mso-bidi-font-family: Symbol;"><span style="mso-list: Ignore;">·<span style="font: 7pt &amp;amp;">         </span></span></span><strong style="mso-bidi-font-weight: normal;"><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;">RETURN Statement</span></strong><strong style="mso-bidi-font-weight: normal;"></strong>
</p>

<p style="line-height: 14.25pt; margin-left: 0.25in;">
  <span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;"> </span></span><span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="color: #000000;"><span style="language: en-US; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-ascii-theme-font: minor-latin; mso-fareast-theme-font: minor-fareast; mso-bidi-theme-font: minor-bidi; mso-color-index: 3; mso-font-kerning: 12.0pt; mso-style-textfill-type: solid; mso-style-textfill-fill-themecolor: text2; mso-style-textfill-fill-color: #675D59; mso-style-textfill-fill-alpha: 100.0%;"><span style="color: #000000;"><span style="font-size: small;"><span style="font-family: Times New Roman;">The RETURN statement when executed returns control to the call process unconditionally.<span style="mso-spacerun: yes;">  </span>It returns an integer value.<span style="mso-spacerun: yes;">  </span>A warning will be issued it the value is NULL.<span style="mso-spacerun: yes;">  </span></span></span></span></span></span></span></span></span>
</p>

<p style="line-height: 14.25pt; text-indent: -0.25in; margin-left: 0.25in; mso-list: l2 level1 lfo3;">
  <span style="font-family: Symbol; font-size: 10pt; mso-fareast-font-family: Symbol; mso-bidi-font-family: Symbol;"><span style="mso-list: Ignore;">·<span style="font: 7pt &amp;amp;">         </span></span></span><strong style="mso-bidi-font-weight: normal;"><span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-font-kerning: 12.0pt; mso-hansi-font-family: Arial;">User-Defined Error Messages</span></strong><strong style="mso-bidi-font-weight: normal;"></strong>
</p>

<p style="line-height: 14.25pt; margin-left: 0.25in;">
  <span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-theme-font: minor-latin; mso-bidi-theme-font: minor-latin; mso-hansi-theme-font: minor-latin;"> </span></span><span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="color: #000000;"><span style="language: en-US; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-ascii-theme-font: minor-latin; mso-fareast-theme-font: minor-fareast; mso-bidi-theme-font: minor-bidi; mso-color-index: 3; mso-font-kerning: 12.0pt; mso-style-textfill-type: solid; mso-style-textfill-fill-themecolor: text2; mso-style-textfill-fill-color: #675D59; mso-style-textfill-fill-alpha: 100.0%;"><span style="color: #000000;"><span style="font-size: small;"><span style="font-family: Times New Roman;">The user-defined error messages are saved in master in sys.messages table.<span style="mso-spacerun: yes;">  </span>They can be used across databases.<span style="mso-spacerun: yes;">  </span>When a user-defined error message is called if can write to the Windows application log.<span style="mso-spacerun: yes;">  </span>The message can be informational or error related, depends on severity level.<span style="mso-spacerun: yes;">  </span>Severity level 20 thru 25 are fatal errors – processing has stopped.</span></span></span></span></span></span></span></span>
</p>

<p style="line-height: 14.25pt; text-indent: -0.25in; margin-left: 0.25in; mso-list: l2 level1 lfo3;">
  <span style="font-family: Symbol; font-size: 10pt; mso-fareast-font-family: Symbol; mso-bidi-font-family: Symbol;"><span style="mso-list: Ignore;">·<span style="font: 7pt &amp;amp;">         </span></span></span><strong style="mso-bidi-font-weight: normal;"><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-theme-font: minor-latin; mso-bidi-theme-font: minor-latin; mso-hansi-theme-font: minor-latin;">RAISERROR Statement</span></strong>
</p>

<p style="line-height: 14.25pt; margin-left: 0.25in;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-theme-font: minor-latin; mso-bidi-theme-font: minor-latin; mso-hansi-theme-font: minor-latin;">The RAISERROR statement is used to raise user-defined error messages.<span style="mso-spacerun: yes;">  </span>It can be used to raise dynamically built messages.<span style="mso-spacerun: yes;">  </span>The message is returned as a server message.<span style="mso-spacerun: yes;">  </span>It can also raise messages to a Catch block and insert them into the error and application log.<span style="mso-spacerun: yes;">  </span>Here is an example of a RAISERROR statement.</span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">DECLARE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> @oldname <span style="color: blue;">varchar</span><span style="color: gray;">(</span>15<span style="color: gray;">),</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 1;">   </span><span style="mso-tab-count: 1;">      </span>@newname <span style="color: blue;">varchar</span><span style="color: gray;">(</span>15<span style="color: gray;">)</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 2;">         </span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">SET</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> @oldname <span style="color: gray;">=</span> <span style="color: red;">&#8216;Cato&#8217;</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">SET</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> @newname <span style="color: gray;">=</span> <span style="color: red;">&#8216;Tessereau&#8217;</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: red; font-size: 10pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">RAISERROR </span><span style="font-family: &amp;amp; color: gray; font-size: 10pt; mso-no-proof: yes;">(</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;">50001<span style="color: gray;">,</span><span style="mso-spacerun: yes;">  </span><span style="color: green;">&#8211;user-defined error message number</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 3;">               </span>10<span style="color: gray;">, </span><span style="color: green;">&#8211;severity level</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 3;">               </span>1<span style="color: gray;">,<span style="mso-spacerun: yes;">  </span></span><span style="color: green;">&#8211;error state</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 3;">               </span>@oldname,</span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt 0.25in; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 2;">         </span><span style="mso-tab-count: 1;">      </span>@newname<span style="color: gray;">)</span> <span style="color: blue;">WITH</span> <span style="color: fuchsia;">LOG</span><span style="color: gray;">;</span></span>
</p>

<p style="line-height: 14.25pt; text-indent: -0.25in; margin-left: 0.25in; mso-list: l2 level1 lfo3;">
  <span style="font-family: Symbol; font-size: 10pt; mso-fareast-font-family: Symbol; mso-bidi-font-family: Symbol;"><span style="mso-list: Ignore;">·<span style="font: 7pt &amp;amp;">         </span></span></span><strong style="mso-bidi-font-weight: normal;"><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-theme-font: minor-latin; mso-bidi-theme-font: minor-latin; mso-hansi-theme-font: minor-latin;">TRY/CATCH Blocks</span></strong>
</p>

<p style="line-height: 14.25pt;">
  <span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-theme-font: minor-latin; mso-bidi-theme-font: minor-latin; mso-hansi-theme-font: minor-latin;"> </span></span><span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="color: #000000;"><span style="language: en-US; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-ascii-theme-font: minor-latin; mso-fareast-theme-font: minor-fareast; mso-bidi-theme-font: minor-bidi; mso-color-index: 3; mso-font-kerning: 12.0pt; mso-style-textfill-type: solid; mso-style-textfill-fill-themecolor: text2; mso-style-textfill-fill-color: #675D59; mso-style-textfill-fill-alpha: 100.0%;"><span style="color: #000000;"><span style="font-size: small;"><span style="font-family: Times New Roman;">These consist of two parts a TRY block and a CATCH block.<span style="mso-spacerun: yes;">  </span>Logic control is passed to the CATCH block from the TRY block when an error is encountered.<span style="mso-spacerun: yes;">  </span>They can be nested, but be careful when doing this because the logic can get very confusing the deeper you nest.<span style="mso-spacerun: yes;">  </span>Severity 20 or higher not handled by TRY/CATCH because connection is closed.<span style="mso-spacerun: yes;">  </span>Severity 10 or less not handled, these are informational only.<span style="mso-spacerun: yes;">  </span>Here is an example of a simple TRY/CATCH block.</span></span></span></span></span></span></span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">CREATE</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">PROC</span> usp_myproc</span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">AS</span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">  </span><span style="color: green;">&#8211;just a silly little proc</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">  </span><span style="color: blue;">SELECT</span> <span style="color: gray;">*</span> <span style="color: blue;">FROM</span> <span style="color: gray;"><</span><span style="color: blue;">table</span> that does <span style="color: gray;">not</span> exist<span style="color: gray;">>;</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">GO</span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">BEGIN</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">TRY</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 1;">      </span><span style="color: blue;">EXEC</span> usp_myproc</span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">END</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">TRY</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">BEGIN</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">CATCH</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 1;">      </span><span style="color: blue;">SELECT</span> </span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 2;">            </span><span style="color: fuchsia;">ERROR_NUMBER</span><span style="color: gray;">()</span> <span style="color: blue;">as</span> ErrNum<span style="color: gray;">,</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 2;">            </span><span style="color: fuchsia;">ERROR_MESSAGE</span><span style="color: gray;">()</span> <span style="color: blue;">as</span> ErrMsg<span style="color: gray;">;</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">END</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">CATCH</span></span>
</p>

<p class="MsoNormal" style="line-height: normal; margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">GO</span>
</p>

<p style="line-height: 14.25pt;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-tab-count: 1;">      </span></span>
</p>

<p style="line-height: 14.25pt; text-indent: -0.25in; margin: 0in 0in 0pt; mso-list: l2 level1 lfo3;">
  <span style="font-family: Symbol; font-size: 10pt; mso-fareast-font-family: Symbol; mso-bidi-font-family: Symbol;"><span style="mso-list: Ignore;">·<span style="font: 7pt &amp;amp;">         </span></span></span><strong style="mso-bidi-font-weight: normal;"><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;">XACT_ABORT statement</span></strong><strong style="mso-bidi-font-weight: normal;"></strong>
</p>

<p style="line-height: 14.25pt; margin: 0in 0in 0pt;">
  <span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-theme-font: minor-latin; mso-bidi-theme-font: minor-latin; mso-hansi-theme-font: minor-latin;"> </span></span><span style="font-family: arial,helvetica,sans-serif;"><span style="font-size: x-small;"><span style="color: #000000;"><span style="language: en-US; mso-ascii-font-family: Calibri; mso-fareast-font-family: +mn-ea; mso-bidi-font-family: +mn-cs; mso-ascii-theme-font: minor-latin; mso-fareast-theme-font: minor-fareast; mso-bidi-theme-font: minor-bidi; mso-color-index: 3; mso-font-kerning: 12.0pt; mso-style-textfill-type: solid; mso-style-textfill-fill-themecolor: text2; mso-style-textfill-fill-color: #675D59; mso-style-textfill-fill-alpha: 100.0%;"><span style="color: #000000;"><span style="font-size: small;"><span style="font-family: Times New Roman;">OFF is the default setting.<span style="mso-spacerun: yes;">  </span>Compile errors are not affected by XACT_ABORT.</span></span></span></span></span></span></span></span>
</p>

<p style="line-height: 14.25pt; margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-theme-font: minor-latin; mso-bidi-theme-font: minor-latin; mso-hansi-theme-font: minor-latin;">When set to ON</span>
</p>

<p style="line-height: 14.25pt; text-indent: -0.25in; margin: 0in 0in 0pt 0.25in; mso-list: l2 level1 lfo3;">
  <span style="font-family: Symbol; font-size: 10pt; mso-fareast-font-family: Symbol; mso-bidi-font-family: Symbol;"><span style="mso-list: Ignore;">·<span style="font: 7pt &amp;amp;">         </span></span></span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-theme-font: minor-latin; mso-bidi-theme-font: minor-latin; mso-hansi-theme-font: minor-latin;">Processing stops when an error is encountered</span>
</p>

<p style="line-height: 14.25pt; text-indent: -0.25in; margin: 0in 0in 0pt 0.25in; mso-list: l2 level1 lfo3;">
  <span style="font-family: Symbol; font-size: 10pt; mso-fareast-font-family: Symbol; mso-bidi-font-family: Symbol;"><span style="mso-list: Ignore;">·<span style="font: 7pt &amp;amp;">         </span></span></span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-theme-font: minor-latin; mso-bidi-theme-font: minor-latin; mso-hansi-theme-font: minor-latin;">When run-time error is raised, entire transaction is rolled back automatically, no need for ROLLBACK statement.</span>
</p>

<p style="line-height: 14.25pt; text-indent: -0.25in; margin: 0in 0in 0pt 0.25in; mso-list: l2 level1 lfo3;">
  <span style="font-family: Symbol; font-size: 10pt; mso-fareast-font-family: Symbol; mso-bidi-font-family: Symbol;"><span style="mso-list: Ignore;">·<span style="font: 7pt &amp;amp;">         </span></span></span><span style="font-family: &amp;amp; font-size: 10pt; mso-ascii-theme-font: minor-latin; mso-bidi-theme-font: minor-latin; mso-hansi-theme-font: minor-latin;">When combined with a TRY/CATCH block you get unexpected behavior</span>
</p>

<p style="line-height: 14.25pt; text-indent: -0.25in; margin: 0in 0in 0pt 0.75in; mso-list: l2 level2 lfo3;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-fareast-font-family: 'Courier New';"><span style="mso-list: Ignore;">o<span style="font: 7pt &amp;amp;">    </span></span></span><span style="font-size: 10pt; mso-bidi-font-family: Calibri; mso-bidi-theme-font: minor-latin;"><span style="font-family: Times New Roman;">SQL Server treats statement level errors as batch level errors</span></span>
</p>