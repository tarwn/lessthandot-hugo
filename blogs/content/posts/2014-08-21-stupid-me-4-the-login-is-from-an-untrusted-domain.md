---
title: 'Stupid me #4 – The login is from an untrusted domain'
author: Koen Verbeeck
type: post
date: 2014-08-21T12:37:37+00:00
ID: 2911
url: /index.php/datamgmt/ssis/stupid-me-4-the-login-is-from-an-untrusted-domain/
views:
  - 22818
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSIS
tags:
  - sql server
  - ssis
  - syndicated

---
<p style="text-align: justify">
  After quite a while it has finally returned: another Stupid Me™®©! A reminder:
</p>

<p style="text-align: justify">
  <em>Every time I do something “stupid”, which happens from time to time, I'll do a little blog post on what happened and how I solved it. The reason for this is twofold: I'll have a solution online I can consult if it happens again and other people can benefit from my mistakes as well. Because remember the ancient Chinese proverb</em>: <em>“It's only stupid if you don't turn it into a learning experience”. </em><em>Okay, I might have made that last one up...</em>
</p>

<p style="text-align: justify">
  <strong>The problem</strong>
</p>

<p style="text-align: justify">
  At a client, the test environment has the same daily SQL Server Agent job that runs the ETL just like in production.  On a rainy morning, the job suddenly returned the following error:
</p>

<p style="text-align: justify">
  <em>Login Failed. The login is from an untrusted domain and cannot be used with Windows authentication.</em>
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/08/error.png"><img class="alignnone size-full wp-image-2913" src="/wp-content/uploads/2014/08/error.png" alt="error" width="297" height="120" /></a>
</p>

<p style="text-align: justify">
  Important to know is that each environment – Development, Test and Production – has its own Active Directory domain for security reasons. I immediately thought of an issue within Active Directory, but the AD admin assured me nothing had changed. The login itself – a regular domain account used as an execution account – was active and its password had not expired.
</p>

<p style="text-align: justify">
  So where did this error come from?
</p>

<p style="text-align: justify">
  <strong>The solution</strong>
</p>

<p style="text-align: justify">
  Several packages of the same SSIS project had the same issues, but packages from another project did not. Time to investigate further, so I checked the versions of that particular project in the SSIS catalog.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/08/versions.png"><img class="alignnone size-full wp-image-2916" src="/wp-content/uploads/2014/08/versions.png" alt="versions" width="286" height="327" srcset="/wp-content/uploads/2014/08/versions.png 286w, /wp-content/uploads/2014/08/versions-262x300.png 262w" sizes="(max-width: 286px) 100vw, 286px" /></a>
</p>

<p style="text-align: justify">
  Turned out I did a deployment of that project the day before the SQL Agent job started failing. The plot thickens. It means there was some change in the project that caused this strange error. But what?
</p>

<p style="text-align: justify">
  I looked into the connection manager that was failing to connect. Connection managers in the project are project connection managers and they are configured using two parameters: one to set the database and one to set the server. Configuring project connection managers is easy: this can be done through any package in the project. Simply go to the properties of the connection manager and configure the expressions. These expressions will immediately apply in every package.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/08/expression.png"><img class="alignnone size-full wp-image-2914" src="/wp-content/uploads/2014/08/expression.png" alt="expression" width="568" height="501" srcset="/wp-content/uploads/2014/08/expression.png 568w, /wp-content/uploads/2014/08/expression-300x264.png 300w" sizes="(max-width: 568px) 100vw, 568px" /></a>
</p>

<p style="text-align: justify">
  When the package is deployed to the SSIS catalog of another environment, the parameters there will have the corresponding values of that environment.
</p>

<p style="text-align: justify">
  I took a look at the version control history of the project connection manager. And apparently I did make a change to the connection manager.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/08/history.png"><img class="alignnone size-full wp-image-2915" src="/wp-content/uploads/2014/08/history.png" alt="history" width="484" height="123" srcset="/wp-content/uploads/2014/08/history.png 484w, /wp-content/uploads/2014/08/history-300x76.png 300w" sizes="(max-width: 484px) 100vw, 484px" /></a>
</p>

<p style="text-align: justify">
  However, I did not recall making any change to the projection manager. And then it hit me. I generated all of the packages using BIML. In the BIML script, I also generate the connection managers as they are needed during compile time (as far as I know BIML doesn't realize they are actually already there in the project). I didn't include the expressions in the BIML script as I do not actually generate them. When the BIML script is finished, it asks you if you want to overwrite existing items. I simply deselect the checkboxes for the connection managers and everything is fine in the world.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/08/bimloverwrite.png"><img class="alignnone size-full wp-image-2917" src="/wp-content/uploads/2014/08/bimloverwrite.png" alt="bimloverwrite" width="652" height="139" srcset="/wp-content/uploads/2014/08/bimloverwrite.png 652w, /wp-content/uploads/2014/08/bimloverwrite-300x63.png 300w" sizes="(max-width: 652px) 100vw, 652px" /></a>
</p>

<p style="text-align: justify">
  Except when you forget to deselect the checkbox. In that case, the connection manager is overwritten and the expressions are gone. Which means that when you deploy the project to another server, the package tries to connect to the development server (which is on another domain). Hence the error. Whoops.
</p>

<p style="text-align: justify">
  <strong>Conclusion</strong>
</p>

<p style="text-align: justify">
  The obvious solution is to not forget to deselect the checkboxes for the connection managers, but a more durable solution is to expand my BIML script to include the expressions, just to make sure. Debugging this issue was made easy because of the automatic versioning in the SSIS catalog, but also because the SSIS projects in Visual Studio are checked into version control, making it straight forward to find any changes.
</p>

<p style="text-align: justify">
  Related posts:
</p>

<li style="text-align: justify">
  <a href="/index.php/datamgmt/dbprogramming/mssqlserver/stupid-me-1-locking-myself/">Stupid me #1 – Locking myself out of SQL Server</a>
</li>
<li style="text-align: justify">
  <a href="/index.php/datamgmt/dbprogramming/mssqlserver/stupid-me-2/">Stupid me #2 – Where is my SSIS package executed?</a>
</li>
<li style="text-align: justify">
  <a href="/index.php/datamgmt/ssis/stupid-me-3-building-the/">Stupid me #3 – Building the script component</a>
</li>