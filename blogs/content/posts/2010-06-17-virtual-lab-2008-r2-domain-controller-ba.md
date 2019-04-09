---
title: 'Virtual Lab: 2008 R2 Domain Controller – Basic Tasks'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-06-17T09:51:38+00:00
ID: 797
excerpt: 'In the previous article we covered the creation of a basic Domain Controller for our Virtual Lab. Using this basic domain controller and a basic SQL Server machine, we will look at the creation of a basic user account, creating a service account, and ad&hellip;'
url: /index.php/sysadmins/security/virtual-lab-2008-r2-domain-controller-ba/
views:
  - 12321
rp4wp_auto_linked:
  - 1
categories:
  - 2008 Server
  - Security
  - Windows

---
<div class="acc_header">
  In the <a href="/index.php/SysAdmins/OS/Windows/2008Server/virtual-lab-creating-a-2008-r2-domain-co" target="_blank" title="Virtual Lab: Creating a 2008 R2 Domain Controller">previous article</a> we covered the creation of a basic Domain Controller for our Virtual Lab. Using this basic domain controller and a basic SQL Server machine, we will look at the creation of a basic user account, creating a service account, and addition of a system to the domain.<br /> <br /> <label>Technical Area:</label> Accidental Systems Administrator<br /> <label class="diff">Level of Difficulty: </label><img src="http://tiernok.com/LTDBlog/dr_basic.png" alt="Basic Difficulty" /><br /> <label>Additional Articles:</label><a href="http://wiki.ltd.local/index.php/Virtual_Lab" title="View the wiki entry">Virtual Lab entry on the LTD Wiki</a>
</div>

This post picks up where the [Virtual Lab: Creating a 2008 R2 Domain Controller][1] post left off. Now that we have a new domain up and running we need to add some systems and users.

## Creating Accounts

After installing the Active Directory Directory Services role, there are several new tools available from the Administrative Tools section of the Start menu. Throughout the remainder of the article we will be working through the “Active Directory Users and Computers” tool.

### Domain Administrator Account

The first account we are going to create on the new domain is a Domain Administrator account. Even though this is a virtual lab environment, we don't want to get in the habit of using the built-in administrator account. 

Open the “Active Directory User and Computers” screen and explore the default groups that have been created. As this is our first visit, there are no custom folders in our tree.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/0_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/0_config.png" alt="AD Users and Computers" /></a><br /> AD &#8211; Users and Computers
</div>

To add a new user to the default “Users” folder we will right-click on the folder, select “New”, and select “User”. This provides us with a dialog to enter the basic user information.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/1_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/1_config.png" alt="AD Adding a User" /></a><br /> AD &#8211; Adding a User
</div>

After entering the general user information and pressing next, we are presented with a request for the user's password and password options. As we have not yet configured the password policy, the <a href="http://technet.microsoft.com/en-us/library/cc264456.aspx" title="Windows 2008 Password Policy Settings" target="_blank">default policy</a> is still in place.

Completing the wizard gives us a brand new member with basic Domain User permissions.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/2_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/2_config.png" alt="AD Adding a User" /></a><br /> AD &#8211; Adding a User
</div>

To elevate the permissions of the user across the domain, we will assign them membership to the Domain Admins group. Open the user's properties panel (right-clicking the user and select “Properties”) and select the “Member Of” tab.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/3_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/3_config.png" alt="AD Configuring a User" /></a><br /> AD &#8211; Configuring a User
</div>

<div class="hint">
  There are several default groups created when you install AD DS with default levels of permissions across the domain. There is a pretty good reference on the default groups available <a href="http://ss64.com/nt/syntax-security_groups.html" title="AD Security Groups" target="_blank">here</a>.
</div>

Pressing the “Add” button brings up the search dialog. Enter “Domain Admins” and press “Check Names” to quickly find and select the Domain Admins group.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/4_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/4_config.png" alt="AD Configuring a User" /></a><br /> AD &#8211; Configuring a User
</div>

Press Ok twice and the user is now part of the Domain Admins group.

### Service Accounts

Service accounts are an important part of a network. Many companies will rely on common accounts, like the built-in Administrator account, when they are setting up services on the Domain that require domain permissions. There are numerous reasons this is a bad idea: 

  * Greater Impact &#8211; a change to a single user account suddenly has a much wider impact then is intended, potentially causing service outages in disparate places acros the network
  * Greater Impact, Frequently &#8211; how about password changes? Using one accuont for a wide range of services requires a wider set of work when a single password has to change
  * Documentation &#8211; using a single account for multiple services requires either good documentation, good guessing, or a lot of overtime
  * Security &#8211; providing an account with too much security is a potential security hole, a virus that uses a single application as an entry point suddenly has rights to everything on the network

In order to make management of service accounts (and services) clearer, I suggest each service account only have the level of permission on the domain it absolutely needs and that it only be used for a single service or application. This requires more work up front, as you have to not only create individual accounts for each application and service, but more importantly determine what “minimum requirements” means for each one. On the other hand, minimizing planned and unplanned system outages in the future is worth a little extra time.

<div class="hint">
  Sometimes multiple services need access to a common resource, or a common service needs a few different permission profiles based on the use of the system (for instance, qa versus production SQL Server instances). Rather than add these permissions to each individual service account, consider creating a group that outlines the permissions and then give the service accounts membership in the group. Our goal is to minimize the number of configuration changes we will have to make when that configuration needs to change in the future.
</div>

#### Creating the Service User Account

Our guinea pig in this section will be the basic SQL install from a [previous article][1]. We are going to create a service account for the database server that has limited domain permissions but local administrative permissions. We will be using a middle-of-the-road configuration to create a single user account for all SQL Server services on our domain to run unser, what I would consider to be the minimum level of implementation.

<div class="hint">
  Larger networks may want to create groups to handle permissions and then create accounts for individual servers and assign them the correct group membership. This second route reduces risk but heightens the level of work necessary to manage the accounts. In this situation, I would suggest using Managed Service accounts rather than standard user accounts, as they can be assigned the same types of group permissions but reduce a portion of the overhead in administering the accounts.
</div>

On the AD DS server open the “Active Directory User and Computers” screen again. While there is a “Users” container already available in the interface, we're going to create a new Organizational Unit (OU) named “Service Accounts” to store these accounts.

To create the new OU, we right-click the Domain (in my case _avl.local_) and select New -> Organizational Unit. In the dialog we enter the name, in my case “Service Accounts”, and press Ok.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/5_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/5_config.png" alt="Creating a new OU" /></a><br /> AD &#8211; Creating a new OU
</div>

Right clicking the new “Service Accounts” container and selecting New > User opens the new user dialog.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/6_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/6_config.png" alt="Creating a new Service User" /></a><br /> AD &#8211; Creating a new Service User
</div>

As this is the domain account for our SQL Server services, I have chosen the name “SQL Server Service Account” and a username of “sqladmin”.

On the next step we will also ensure that our password won't expire in the middle of the night by verifying the checked defaults and modifying a few.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/7_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/7_config.png" alt="Creating a new Service User" /></a><br /> AD &#8211; Creating a new Service User
</div>

After viewing the summary in the final step, we press Ok and create the new user. As the user is not going to have any special, domain-wide permissions we do not need to modify their group memberships or other settings. In fact we are done with the account creation until we bring our database server onto the domain and set up the local permissions for the user on that server.

## Joining the Domain

Joining a server to the domain is a fairly straightforward process. In this case we will join our database server from <a href="http://wiki.ltd.local/index.php/Virtual_Lab" title="See the list of prior articles" target="_blank">prior articles</a> and then we will configure our domain account from the previous section to serve as the administrative account for the database services.

The first step will be to change the network properties of the virtual server. When we first created it we used DHCP and that means that it received a dynamic address and DNS entries other than our new controller. 

<div class="mylab">
  In my personal lab I am still serving DHCP requests from my core switch and have not configured it to direct DNS requests at my virtual DC because there is no guarantee that the DC will be on and later on it will be moved behind a virtual firewall and will be inaccessible to the other physical systems on my network.
</div>

On the SQL Server machine we open the network settings for our adapter, select the “Internet Protocol Version 4 (TCP/IPv4)” value, and press “Properties”. 

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/8_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/8_config.png" alt="SQL Server VM - Network Settings" /></a><br /> SQL Server VM &#8211; Network Settings
</div>

Assign the server a static IP address on the network and point the DNS settings to the DC. At this point it would also be a good idea to check the “Validate” button to let Windows test the settings when you press Ok.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/9_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/9_config.png" alt="SQL Server VM - Network Settings" /></a><br /> SQL Server VM &#8211; Network Settings
</div>

In my server the validation resulted in a message indicating that Windows couldn't identify the problem, this translates to “Yes It Works”.

Next we will open the System Properties. 

<div class="hint">
  One of the 90 ways you can get to the System Properties panel is to press the “Server Manager” icon that is pinned to the toolbar, then in the console highlight “Server manager” in the left side to display the high level dashboard on the right, followed by selecting “Change System Properties” on the right side of that dash.
</div>

Now we want to press the “Change” button on the “Computer Name” tab. Select the “Domain” radio button and type in the name of the domain from your domain controller.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/10_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/10_config.png" alt="SQL Server VM - Join Domain" /></a><br /> SQL Server VM &#8211; Join Domain
</div>

On pressing “Ok” we are asked to enter domain credentials with permission to join the network. 

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/11_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/11_config.png" alt="SQL Server VM - Join Domain" /></a><br /> SQL Server VM &#8211; Join Domain
</div>

Once we enter our credentials and press “Ok” the system is accepted to the Domain and provides us with the customary reboot request.

back on the Domain Controller in the “Users and Computers” console, the server has been automatically added under the “Computers” container. This being a simple setup, we have not created our own containers to store our servers in, so we will leave it in the default one (for now).

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/12_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/12_config.png" alt="SQL Server VM - Join Domain" /></a><br /> SQL Server VM &#8211; Join Domain
</div>

When the SQL Server has finished rebooting we will log in for the first time with our Domain credentials. In the “Server Manager” we now see that the full computer name and Domain entries reflect the name of the domain.

## Applying the SQL Service Account

The last step after adding a database server to the domain is to reconfigure it's services to use the domain service account we created above. This will allow us to later create domain resourcs that all of our SQL Server systems need to access without having to manage each one individually.

Currently our service account has no permissions on the server, other than as a basic Domain User (and for some of you, it may not even have that level). So the first step is to provide that domain account with local administrator permissions on our database server.

<div class="hint">
  Before continuing further make sure you log into the server once with the service account above. Several folders and files are created when the account logs in and not doing so (or not manually doing some trickery with the Users sub-directory and an NTLog file) appears to upset WMI when your assigning users to the services. Don't stay logged in as this user, as you will then end up battling UAC the rest of the day.
</div>

From the open “Server Manager” console, expand the “Configuration” and then “Local Users and Groups” containers, selecting the “Groups” folder. Right click the “Administartors” group on the right side and select “Properties”.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/13_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/13_config.png" alt="SQL Server VM - Service Account" /></a><br /> SQL Server VM &#8211; Service Account
</div>

We enter the name of the domain account (in this case AVLsqladmin) and press the “Check Names” button to ensure the account could be found.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/14_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/14_config.png" alt="SQL Server VM - Service Account" /></a><br /> SQL Server VM &#8211; Service Account
</div>

Press “Ok” and “Ok” and we are back in the Server Manager again.

The last step is to configure our services to use the Domain account, however this is done a little differently for SQL Server than you might expect.

<div class="hint">
  While it is technically possible to change the service accounts directly from the Windows “Services” console, there are a number of extra steps that have to be completed in order for it to work properly, as there are also various registry and application settings that have to be set outside of the Services console. Microsoft has published a <a href="http://support.microsoft.com/kb/283811" title="Read the KB article" target="_blank">knowledge base article</a> on this for 2005, I have not been able to locate a similar document for 2008.
</div>

Open the “SQL Server Configuration Manager” from the Start Menu (this would also be a good time to pin it to the taskbar).

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/15_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/15_config.png" alt="SQL Server VM - Service Account" /></a><br /> SQL Server VM &#8211; Service Account
</div>

<div class="hint">
  Ah, the wonders of UAC. Don't get me wrong, I actually like the concept, unfortunately the implementation leaves a little to be desired in Enterprise or Domain setups. Accounts in the Domain Admins (and a few others?) groups don't receive the level of rights you would expect and, in fact, it doesn't seem possible to grant them the abilities of the built-in administrator, which has strange and mystical powers that are turned on by default and can only be turned off in the policy settings, not applied to other accounts. You will receive a number of errors when you try to escalate with a variety of different settings, and all of the recommendations [I have found] end with either turning off UAC, using the Right-Click “Run As Administrator”, or appear to have been written by people logged in as a Local Administrator.<br /> To make matters worse, file access escalation (if it works) creates a mess of local folder permissions, so it's highly suggested that you read <a href="http://blog.akinstech.com/understanding-windows-7-and-2008-r2-uac-and-permissions" title="Read this post on making your own Local Admin group for file access" target="_blank">this workaround</a>.
</div>

In the [Basic SQL Server Virtual Lab post][2] we only installed a few services and configured SQL Server and SQL Agent to use a local account.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/16_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/16_config.png" alt="SQL Server VM - Service Account" /></a><br /> SQL Server VM &#8211; Server Configuration Manager
</div>

Right-click on each service and select Properties to open the properties panel. Enter the domain account we created above in the Service Accounts section and it's password, then press Apply. The system will then warn us that a restart to the services is required to continue.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/17_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/17_config.png" alt="SQL Server VM - Service Account" /></a><br /> SQL Server VM &#8211; Changing the Account
</div>

Follow the same instructions for each of the services and we son have all of our services running under the DOmain account.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/18_config.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/18_config.png" alt="SQL Server VM - Service Account" /></a><br /> SQL Server VM &#8211; Finished
</div>

## Wrapping Up

Now that we have the Domain Controller running, we've created some accounts, and we have joined our first database server to the domain, it's time to switch focus back to our database server for a while. Upcoming weeks will focus on some administrative tasks, such as configuring database mail and alerts and building out some scripts to monitor current state and state-over-time values for a few critical performance indicators.

<label>Additional Articles:</label>[Virtual Lab entry on the LTD Wiki][3]

 [1]: /index.php/All/?p=845
 [2]: /index.php/All/?p=840 "Review the Basic SQL Virtual Machine post"
 [3]: http://wiki.ltd.local/index.php/Virtual_Lab "View the wiki entry"