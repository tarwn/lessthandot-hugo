---
title: 'T-SQL Tuesday #63: How do you manage security'
author: Koen Verbeeck
type: post
date: 2015-02-10T08:11:04+00:00
url: /index.php/datamgmt/ssis/t-sql-tuesday-63-how-do-you-manage-security/
views:
  - 4999
rp4wp_auto_linked:
  - 1
categories:
  - SSIS
tags:
  - security
  - ssis
  - syndicated
  - t-sql tuesday

---
<p style="text-align: justify">
  <a href="http://sqlstudies.com/2015/02/03/tsql-tuesday-63-how-do-you-manage-security/"><img style="float: left;margin: 0px 10px 0px 10px" src="http://blogs.ltd.local/wp-content/uploads/2014/01/TSQL2sday.png" alt="TSQL2sday" width="133" height="134" /></a>This month’s T-SQL Tuesday is hosted by Kenneth Fisher (<a href="http://sqlstudies.com/">blog </a>| <a href="https://twitter.com/sqlstudent144">twitter</a>) and its subject is about security.
</p>

<p style="text-align: justify">
  <em>Security is one of those subjects that most DBAs have to deal with regardless of specialty. So as something we all have to work with at some point or another what are some tips you’d like to share? What’s the best security design? You’ve picked up a legacy system and the security is awful, how do you fix it? Any great tools out there you’d like to share? Hate it or love it I’m betting we all have something to say.</em>
</p>

<p style="text-align: justify">
  Now, I’m not exactly a DBA but even ETL developers have to deal with security from time to time. In this blog post, I’d like to talk about the built-in security of the SSIS packages. You see, SSIS packages can store sensitive data inside them, such as the passwords for the connection managers for example. In order to protect that information, SSIS uses <a href="https://msdn.microsoft.com/en-us/library/ms141747.aspx">protection levels</a> which is a package-level property. These are the available protection levels:
</p>

<ul style="text-align: justify">
  <li>
    <strong>DontSaveSensitive</strong>, which is my personal favorite as it forces you to use either configurations or parameters.
  </li>
  <li>
    <strong>EncryptSensitiveWithPassword</strong>. Other people can still open the package if they don&#8217;t have the password,  but the passwords in the connection managers are lost and need to be re-entered..
  </li>
  <li>
    <strong>EncryptAllWithPassword</strong>, for the paranoia people.
  </li>
  <li>
    <strong>EncryptSensitiveWithUserKey</strong>, which is the default. And as usual, they choose the absolute worst choice as the default. This setting basically means only you can access the sensitive data. Real peachy when you are in a team with multiple developers. Other developers can still open the package (then ignore a few warnings) but they have to re-enter all the passwords in the connection managers.
  </li>
  <li>
    <strong>EncryptAllWithUserKey</strong>, when you are really possessive over your packages.
  </li>
  <li>
    <strong>ServerStorage</strong>. When you deploy a package to the server, you trust SQL Server in protecting the sensitive data. This is default when you deploy packages to the SSIS catalog with the project deployment model.
  </li>
</ul>

<p style="text-align: justify">
  Protection levels were really important before SSIS 2012, as they specified how sensitive data was protected. However, the default of EncryptSensitiveWithUserKey is a really crappy one. If you schedule a package with this protection level using SQL Server Agent, the package can still run if there is no actual sensitive data in the package, but a lot of unnecessary warnings are generated. It&#8217;s best to avoid this protection level at all costs.
</p>

<p style="text-align: justify">
  Protecting the package with a password is a good option. However, when deploying the package with the package deployment model (or every version of SSIS before 2012), you had to enter the password when you were scheduling the package with Agent. This means though that the password is stored in MSDB, so it isn&#8217;t that secure. My advice is to use DontSaveSensitive and store sensitive information inside a SQL Server configuration table.
</p>

<p style="text-align: justify">
  With the project deployment model however, configurations are gone. They are replaced with parameters and environments. Since parameters are configured inside the package or project, this means the sensitive data is stored inside the package again. In this case it makes sense to protect the package with a password if needed. There is no issue on the server side, since the protection level is automatically converted to ServerStorage during deployment. Keep in mind that all the packages and the project itself need to have the same protection level, otherwise you get an error when the .ispac file is built.
</p>

<p style="text-align: justify">
  Conclusion:
</p>

<ul style="text-align: justify">
  <li>
    when using earlier version of SSIS or when using the package deployment: the DontSaveSensitive protection level in combination with a configuration table is the most secure option.
  </li>
  <li>
    when using the project deployment model: use parameters and environments to replace the configurations. Protect the package with a password if you want to secure those parameters.
  </li>
</ul>

<p style="text-align: justify">
  Note:<br /> when using Windows Authentication for all sources and destinations, there is almost nothing to be secured in a package. Obviously, this is even more secure.
</p>