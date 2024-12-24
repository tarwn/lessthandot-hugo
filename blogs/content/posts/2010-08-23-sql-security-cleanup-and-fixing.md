---
title: SQL Security Cleanup and Fixing
author: David Forck (thirster42)
type: post
date: 2010-08-23T13:11:19+00:00
ID: 883
url: /index.php/webdev/serveradmin/sql-security-cleanup-and-fixing/
views:
  - 5415
rp4wp_auto_linked:
  - 1
categories:
  - Server Admin

---
So I've been working on cleaning up the security for my SQL Servers the last few months. The brunt of the work has been documenting what's currently in place, what needs to go away immediately, and the long term overall plan. In this whole mess I've learned a few things, and I've set some new standards at work (where there weren't any before). These standards, however, are always open for improvement, which is very nice because once I learn something useful I can start using it. I'd like to share what we're currently doing, and where we came from. I'd also like for others to explain how their security works.

**Where We Came From**

Where we came from was a pretty big mess. The mess wasn't just on the database servers though, it was also in active directory. When I arrived at my company active directory was a disaster. Security groups weren't being managed except on requests to be put in one, there were duplicates everywhere, no naming standards, and there was just no overall organization. One of our new IS Director's goals is to clean up active directory, set some standards, and get it looking like the Org chart. This push is making it a lot easier to work on database security.

Now, on the database side, things weren't a lot of fun either. Generally the old rule of thumb (before I started) was to add domain users to the database and the application would handle the security (sometimes by name, sometimes by security group). Any security groups used were named so randomly that I am still not sure exactly what application uses which security group. Or, in some other cases, add a sql login to the database and the application would log in as the login.

We're making patches and updating security for older applications where and when we can. We've already updated two applications completely onto our new standards. Do this has improved the quality of the older applications tremendously, and has reduced the time and effort needed for when something needs to change.

**What We're Doing Now**

We generally start off by defining roles for an application and/or database. The most generic version of roles we have here is to have an Admin Role and a Users Role. Most of the time though on the database side there isn't a real separation between the Admin role and the Users role so generally we make a Users database role with the appropriate permissions. We separate our databases out into several schemas: an application schema (where all the tables for the core application are), a cg schema (code generated sp's for CRUD), and an sp schema (usually stuff we don't want users to have access to).

The Users database role is generally assigned to the application schema with insert, update, select, and execute permissions (we don't let users actually delete data). The role is also assigned to the cg schema with execute permissions.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/thirster42/UsersRole.jpg" alt="" title="" width="446" height="257" />
</div>

Once our roles in the database are created we need to create an AD security group for each database role. Generally, if we only have one database role, the only security group made would be SQL.(Application Abbreviation). We use SQL at the beginning to make it easy to find and understand what it is. The application abbreviation tells us exactly which application it's for. If there are multiple roles the roles would be named SQL.(Application Abbreviation).(Role Name). We then go into sql server, add the security group to the server, and assign it to the role. We're now done with SQL Server!

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/thirster42/DatabaseSecurityGroups.jpg" alt="" title="" width="354" height="240" />
</div>

Now that the database role security groups have been created the User Security groups need to be made. These are the security groups that the application will actually check to determine permissions in the application. These could be numerous, but for the sake of simplicity and this blog we'll keep it down to two.

For this example, we would need two user security groups, an Admin group and a users group. In AD we have the groups (Application Abbreviation)-Users and (Application Abbreviation)-Admins (notice the –'s, that means that actual people will be in this security group). This is where the clean-up of AD comes in handy, because depending on our requirements we can just drop a security group (or groups) in each and be done.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/thirster42/UserSecurityGroups.jpg" alt="" title="" width="425" height="162" />
</div>

The next thing to do after the user security groups are made is to tie it all together. To do this, we simply just drop the UserSecurityGroups into their proper Database Security Groups (ie, their specific roles).

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/thirster42/OverallSecurity.jpg" alt="" title="" width="436" height="489" />
</div>

**What We Get Out of This**

We get a lot of good things out of this process. The first being that once it's set up, we should rarely have to make any changes to the security in Active Directory (if other security groups are used). Another good thing is that, unless requirements change and we need to make new roles or redefine roles, we don't have to touch security on the database side at all. It is also very easy to trace back and see who all has access to a specific application.

**Discussion**

My methodology probably isn't the best, but it's working for us so far. I'm interested to hear your comments, suggestions, and anything else you have to say. I'm curious as to how others set up their security.