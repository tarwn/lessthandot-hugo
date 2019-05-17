---
title: 'Virtual Lab: Setting up Database Mail on SQL Server 2008 R2'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-07-06T10:02:26+00:00
ID: 834
excerpt: After our installation of SQL Server 2008 R2 a few weeks ago, we mentioned that we still had some additional setup tasks before we could consider the server to be done. This article covers setting up DatabaseMail, the internal mail client that will allow us to use built-in alerts (among other things).
url: /index.php/datamgmt/datadesign/virtual-lab-setting-up-database-mail-on/
views:
  - 13303
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - databasemail
  - sqlserver 2008 r2
  - virtual lab

---
<div class="acc_header">
  After our installation of SQL Server 2008 R2 <a href="/index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/virtual-lab-creating-a-basic-sql-2008-r2" alt="Click here to catch up on that article if you missed it" target="_blank">a few weeks ago</a>, I mentioned that we still had some additional setup tasks before we could consider the server to be done. This article covers setting up DatabaseMail, the internal mail client/management system that will allow us to use built-in alerts (among other things).<br /> <br /> <label>Technical Area:</label> Accidental Database Administrator<br /> <label class="diff">Level of Difficulty: </label><img src="http://www.tiernok.com/LTDBlog/dr_basic.png" alt="Basic Difficulty" /><br /> <label>Additional Articles:</label><a href="http://wiki.lessthandot.com/index.php/Virtual_Lab" title="View the wiki entry">Virtual Lab entry on the LTD Wiki</a>
</div>

Database Mail is a subsystem that acts like a [SMTP][1] client, allowing us to send emails from SQL Server processes and scripts. It replaces SQL Mail and doesn't require a local installation of additional software (like Outlook, the quick fix) to function. 

## Account, Profile, Service Broker, what?

Database Mail expands on the concept of sending mail to include all of the features we wanted in SQLMail. This grown up version of mail uses SMTP to communicate with mail servers, no longer requiring a MAPI component to communicate messages (the reason we used to install products like Outlook to make SQLMail work). Behind the scenes, Database Mail uses Service Broker to manage email in queues, rather than trying an immediate send. Profiles allow us to define a chain of fail-over accounts to send from, so that messages are never left undelivered due to a primary email server being unavailable. And accounts are exactly what they sound like, individual, unique accounts that we can setup to use a variety of authentication and SMTP options.

## From the GUI

Database Mail is something we want to setup immediately on creating a new server. When we are working with a relatively small number of servers, the GUI is not a bad option for setting up some accounts.

We'll start by setting up an administrator profile with then intent to send critical notifications and alerts to this profile.

In SSMS we can right click the Database Mail entry under Management to begin the wizard.

<div class="screenshot">
  <a href="http://www.tiernok.com/LTDBlog/DatabaseMail/orig/01_config.png" title="View Fullsize" target="_blank"><img src="http://www.tiernok.com/LTDBlog/DatabaseMail/01_config.png" alt="SSMS DatabaseMail" /></a><br /> DatabaseMail Menu Item in SSMS
</div>

The Intro screen is fairly boring, so lets press "Next" and head to the main configuration page. This being the first time we are configuring DatabaseMail on our new server, we want to leave the first option selected and continue.

<div class="screenshot">
  <a href="http://www.tiernok.com/LTDBlog/DatabaseMail/orig/02_config.png" title="View Fullsize" target="_blank"><img src="http://www.tiernok.com/LTDBlog/DatabaseMail/02_config.png" alt="SSMS DatabaseMail Wizard" /></a><br /> DatabaseMail Wizard in SSMS
</div>

A quick check by the wizard and it determines that components for Database Mail aren't available on the server yet, so it asks us if we want to go ahead and install them (yes).

<div class="hint">
  How did the server know Database Mail wasn't configured, and what did it do when we pressed 'Yes'? <br /> There is an advanced option in the system configurations called 'Database Mail XPs'. When the option is set to '0' (which is the default), then the Database Mail process doesn't run. So the dialog simply checked the current value and, when we selected 'Yes', updated the configuration to a value of '1'.
</div>

Profiles are used to represent a set of email addresses which allows us to represent a single person (or system) with multiple fail-over accounts. If an error occurs when the system is attempting to send mail from the first account in a profile, it fails to the next and retries, continuing until it either runs out of accounts or successfully sends it's message.

Lets enter a Profile Name of "The Accidental Admin".

<div class="screenshot">
  <a href="http://www.tiernok.com/LTDBlog/DatabaseMail/orig/03_config.png" title="View Fullsize" target="_blank"><img src="http://www.tiernok.com/LTDBlog/DatabaseMail/02_config.png" alt="SSMS DatabaseMail Wizard" /></a><br /> DatabaseMail Wizard in SSMS
</div>

Now we'll add the first two accounts to this profile, each on a different network. Many admins will choose a non-local account with an external provider as their primary to reduce the chances that emails will not get through when other issues are happening. As many of you know, when a server room overheats the exchange server is always the first to go, so we will start with an external provider and fail-over to an internal one. Keep in mind that these are the accounts the database will send emails _from_ when using the specified profile, not to.

Creating an account is fairly straightforward, simply enter the details for where you would like the email to be sent and any authentication options that are necessary. In this case we want to create a minimum of two accounts so the failover can work properly.

<div class="screenshot">
  <a href="http://www.tiernok.com/LTDBlog/DatabaseMail/orig/04_config.png" title="View Fullsize" target="_blank"><img src="http://www.tiernok.com/LTDBlog/DatabaseMail/04_config.png" alt="SSMS DatabaseMail Wizard" /></a><br /> DatabaseMail Wizard in SSMS – Account Entry
</div>

Now that we have two accounts, we can move on.

<div class="screenshot">
  <a href="http://www.tiernok.com/LTDBlog/DatabaseMail/orig/05_config.png" title="View Fullsize" target="_blank"><img src="http://www.tiernok.com/LTDBlog/DatabaseMail/05_config.png" alt="SSMS DatabaseMail Wizard" /></a><br /> DatabaseMail Wizard in SSMS – Account View
</div>

The next step is to decide whether our profile is going to be public or private. A public profile will be accessible to other people working on the system, whereas a private one will not. In this case we will make the profile public but be aware that this means anyone will be able to us the database server to send mail from this particular profile.

<div class="screenshot">
  <a href="http://www.tiernok.com/LTDBlog/DatabaseMail/orig/06_config.png" title="View Fullsize" target="_blank"><img src="http://www.tiernok.com/LTDBlog/DatabaseMail/06_config.png" alt="SSMS DatabaseMail Wizard" /></a><br /> DatabaseMail Wizard in SSMS – Profile Security
</div>

The last interactive step is to confirm or modify some additional system parameters. This step is not specific to configuring an email profile and accounts, it is displayed as the final step of setting up DatabaseMail (when setting up for the first time). I suggest reviewing the settings to ensure your comfortable with each of them.

<div class="screenshot">
  <a href="http://www.tiernok.com/LTDBlog/DatabaseMail/orig/07_config.png" title="View Fullsize" target="_blank"><img src="http://www.tiernok.com/LTDBlog/DatabaseMail/07_config.png" alt="SSMS DatabaseMail Wizard" /></a><br /> DatabaseMail Wizard in SSMS – System Parameters
</div>

After a final review screen, press the 'Finish' button to implement the changes.

<div class="screenshot">
  <a href="http://www.tiernok.com/LTDBlog/DatabaseMail/orig/08_config.png" title="View Fullsize" target="_blank"><img src="http://www.tiernok.com/LTDBlog/DatabaseMail/08_config.png" alt="SSMS DatabaseMail Wizard" /></a><br /> DatabaseMail Wizard in SSMS – Finished
</div>

The last step is to test our new setup. Right-click the Database Mail menu option in SSMS and select "Send Test Email...".

<div class="screenshot">
  <a href="http://www.tiernok.com/LTDBlog/DatabaseMail/orig/09_config.png" title="View Fullsize" target="_blank"><img src="http://www.tiernok.com/LTDBlog/DatabaseMail/09_config.png" alt="SSMS DatabaseMail - Test Email" /></a><br /> SSMS Menu – Sending a Test Email
</div>

After entering an email address for our test message, we can hit the "Send" button and wait for confirmation of our ability to hit "Next, Next, Finish".

<div class="screenshot">
  <a href="http://www.tiernok.com/LTDBlog/DatabaseMail/orig/10_config.png" title="View Fullsize" target="_blank"><img src="http://www.tiernok.com/LTDBlog/DatabaseMail/10_config.png" alt="SSMS DatabaseMail - Test Email Dialog" /></a><br /> SSMS DatabaseMail – Test Email Dialog
</div>

## or From a Script

Doing this on one server is pretty quick. Banging through the dialog on 20 (or with multiple profiles) is not only going to be a bit slower but probably guarantees at least one bad entry along the way. Luckily there is nothing magical about what SSMS is doing to configure these accounts and profiles, just a wizard driving system procedures behind the scenes. We can follow the same process programmatically and create a setup script. Once we have a setup script, of course, we can then use it against multiple systems and if we save it then we not only can set up future servers the same way, but we have detailed documentation on exactly how our servers were setup when we need it 3 years later.

Here are the commands we will be using:

<a href="http://msdn.microsoft.com/en-us/library/ms189631.aspx" onclick="_blank" title="Read more at MSDN">sp_configure</a>
:   This procedure is used to change SQL Server configurations

<a href="http://msdn.microsoft.com/en-us/library/ms188058.aspx" onclick="_blank" title="Read more at MSDN">sysmail_add_profile_sp</a>
:   Used to create a Database Mail Profile

<a href="http://msdn.microsoft.com/en-us/library/ms182804.aspx" onclick="_blank" title="Read more at MSDN">sysmail_add_account_sp</a>
:   Used to create a Database Mail Account

<a href="http://msdn.microsoft.com/en-us/library/ms186258.aspx" onclick="_blank" title="Read more at MSDN">sysmail_add_profileaccount_sp</a>
:   Used to tie a Database Mail Account to a Profile (Tab A in Slot B)

<a href="http://msdn.microsoft.com/en-us/library/ms187911.aspx" onclick="_blank" title="Read more at MSDN">sysmail_add_principalprofile_sp</a>
:   Assign access rights to a Database Mail profile

<a href="http://msdn.microsoft.com/en-us/library/ms190307.aspx" onclick="_blank" title="Read more at MSDN">sp_send_dbmail</a>
:   And sending an email, of course

And if we put it all together then we can duplicate the wizardy approach above like so:

```sql
---- Enable the XPs for Database Mail
EXECUTE sp_configure 'show advanced', 1;
RECONFIGURE;
EXECUTE sp_configure 'Database Mail XPs',1;
RECONFIGURE;
EXECUTE sp_configure 'show advanced', 0;
RECONFIGURE;
GO

---- Create our Default Profile
DECLARE @newProfileId int;
EXECUTE msdb.dbo.sysmail_add_profile_sp
	@profile_name = 'The Accidental Admin v2',
	@description = 'A second version of the DB Admin email profile',
	@profile_id = @newProfileId OUTPUT;
print 'Profile Created';

---- Create our accounts (You did plan on a minimum of two, right?)
DECLARE @newPriAccountId int,
	@newSecAccountId int;
DECLARE @sender varchar(50);
SELECT @sender = 'DB SERVER - ' + @@SERVERNAME;

-- External account using username/password
EXECUTE msdb.dbo.sysmail_add_account_sp  
	@account_name = 'External DBAdmin',
	@email_address = 'AnAccount@ExternalService.com',
	@display_name = @sender,
	@description = 'Primary account for DB Admin profile',
	@mailserver_name = 'mail.ExternalService.com',
	@username = 'username',
	@password = 'password',
	@account_id = @newPriAccountId OUTPUT;

-- Internal account using credentials from the account DB server is running under
EXECUTE msdb.dbo.sysmail_add_account_sp  
	@account_name = 'Internal DBAdmin',
	@email_address = 'YourAccount@YourDomain.com',
	@display_name = @sender,
	@description = 'Secondary account for DB Admin profile',
	@mailserver_name = 'mail.YourDomain.com',
	@use_default_credentials = 1,
	@account_id = @newSecAccountId OUTPUT;

print 'Accounts Created: ' + @sender;

    
---- Add Accounts to Profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
	@profile_id = @newProfileId,
	@account_id = @newPriAccountId,
	@sequence_number = 1;

EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
	@profile_id = @newProfileId,
	@account_id = @newSecAccountId,
	@sequence_number = 2;

print 'Accounts assigned to Profile';

Go

---- And Then We Test
-- Make sure you update the profile name with the value from above
EXECUTE msdb.dbo.sp_send_dbmail
	@profile_name = 'The Accidental Admin v2',
	@subject = 'Test Database Mail Message',
	@recipients = 'You@YourDomain.Com',
	@body = 'Test Message';
print 'Test Email Away!';

GO	
```
Running this script creates a setup similar to what we created in the first section, though some of the names have been changed (and I have removed my email address from the list so I don't get server updates when someone comes along and forgets to swap out the addresses).

## And Now, Uses

So now that we have Database Mail setup, what are we going to use it for? Well, besides creating our own monitoring and email scripts, the built in Alerts component in SQL Server uses Database mail to tell us when critical parts of our server have decided to misbehave. SQL Agent jobs have the option to notify us when they fail and they do so using, yep, Database Mail. Setting up Database Mail and using it in each of these situations won't prevent problems from occurring, but it is the difference between finding out when the problem is occurring and finding out after the server has melted down. 

Next week will be something interesting, but I haven't determined what that will be yet, so we'll figure it out as we go.

 [1]: http://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol "Simple Mail Transport Protocol"