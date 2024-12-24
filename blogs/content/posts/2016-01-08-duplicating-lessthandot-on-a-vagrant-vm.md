---
title: Duplicating LessThanDot on a Vagrant VM
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-01-08T14:43:49+00:00
ID: 4297
url: /index.php/webdev/duplicating-lessthandot-on-a-vagrant-vm/
featured_image: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/01/Vagrant.png
views:
  - 2165
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - Vagrant

---
Setting up a development environment for LessThanDot is kind of tricky, where "tricky" is defined somewhere between "I have PHP on my Windows box" and "at least I'm running it in a version of Apache". Recently I realized I had misplaced my local development environment somewhere and needed a way to make some changes.

Have a problem once? Solve it. Have a problem a second time? Automate it.

So, using [Vagrant][1], I created a series of configurations and scripts that can create a duplicate of our production environment, running a local VM that answers to http://lessthandot.local instead of the real URL.

## What it takes to launch a server

LessThanDot is probably not that dissimilar from other LAMP blogs. We have wired together several external packages that we run on a very specific version of PHP and MySQL that doesn't make them angry (ish). The site lives on a set of sub-domains and includes some tricky bits, like using soft-linked media folders to prevent the content from being wiped out when we deploy new versions (or having to be included in source control). 

**Folder structure:**

  * / – top-level git repository folder 
      * configs/ – configurations used on our live serve for Apache, PHP, etc
      * trunk/ – the source code for the site
      * deploy/ – the deployment scripts (not relevant to this story)
      * vagrant_data/ – (.gitignore'd) created by vagrant script for copies of backups and such
      * vagrant_setup/ – scripts for vagrant like bootstrap.sh and mysql-secure.sh
      * Vagrantfile – the main Vagrant provisioning script
      * vagrant.config – (.gitignore'd) the configuration file consumed by vagrant
      * vagrant.config.sample – a sample file to create a vagrant.config from

The real configuration file and vagrant_data folder are listed in the gitignore to ensure real configurations and media content don't make it into git (more soon on where that media content even came from).

**Type "vagrant up" on the host:**

  * VM is created with matching version of Centos and booted
  * The local configuration file is loaded with values for: 
      * Root MySQL password
      * Site MySQL User and password
      * Prefix to use for the MySQL databases
      * VM IP Address and fake domain name (lessthandot.local)
      * Address, credentials, and filenames for our backups (not on our public server)
  * A private network is setup using the configured IP Address
  * The VM's hostname is set using the domain name above
  * The /etc/hosts file is updated with aliases for the configured domain and necessary subdomains (ex: blogs.lessthandot.local)
  * The host folder is linked to a new /vagrant folder on the VM (content in these folders is synced)
  * The bootstrap.sh is called to continue setup on the VM (passing in the configs args)

**Bootstrap.sh on the VM (runs from the Vagrantfile above):**

  * Creates a local /vagrant/vagrant_data folder if it doesn't exist (this is synced with the host)
  * If there isn't a database backup available in /vagrant/vagrant_data: 
      * Connect to the backup server and download the most recent database backup
      * Since this is synced with the host, I can choose to leave this file for subsequent "vagrant up" runs or delete it to get a fresh one
  * If there isn't a media backup available in /vagrant/vagrant_data: 
      * Connect to the backup server and download the most recent media backup
  * Install PHP 
      * I had to add a new package source to get the specific version I wanted: rpm -Uvh https:/a.completely.different.source/
      * Then `yum -y install php-blah` the list of PHP packages we use 
  * Install MySQL 
      * `yum -y install mysql-server`
      * `service mysqld start`
  * Secure MySQL – run the "secure-mysql.sh" script (below), passing the configured root password from the conf file
  * Create the website database user from passed configs
  * Restore MySQL Backups 
      * unzip the database backup from /vagrant/vagrant_data to the local user directory
      * for each database (passing in the root password $SETTINGS\_MYSQL\_ROOTPASS and prefix $SETTINGS\_MYSQL\_DBPREFIX) 
          * `mysql -u root -p"$SETTINGS_MYSQL_ROOTPASS" -e "CREATE DATABASE "$SETTINGS_MYSQL_DBPREFIX"_sampledb COLLATE=utf8_general_ci"`
          * `mysql -u root -p"$SETTINGS_MYSQL_ROOTPASS" "$SETTINGS_MYSQL_DBPREFIX"_sampledb < mysql/prod_sampledb.sql`
      * GRANT a narrow set of read/write rights to the web user for these new databases
  * Install Apache 
      * yum -y install httpd
  * Configure All The Things (site-setup.sh) 
      * Create /etc/httpd/conf.d/z_lessthandot.httpd.conf with IP Address and definitions for all of the subdomains to match production site
      * Overwrite /etc/httpd/conf/httpd.conf with production version
      * Overwrite php.ini with production version
      * Create main site config.php using the configured domain name, MySQL credentials, etc
      * Update hostname in the WordPress database (wiki) in a couple places
      * Extract media files from the backups and put them in the appropriate places (already gitignored, these are replaced with soft links in production)
      * restart apache, cross fingers

At this point I can open up my local browser of choice, type in http://lessthandot.local, and the site loads with up to date data. All of the links throughout the site link to appropriate sub-domains as you move from sub-site to sub-site, and everything runs smoothly. If I screw something up, I'm working in a local git folder and can back it out and, if it's bad enough, destroy and recreate my environment in minutes.

## Details (aka, Places Things Got Hinky)

Here are some of the details and sticky spots, without revealing anything I shouldn't behind the scenes on our server.

### 64-bit OS on Windows

initially I couldn't get the VirtualBox VMs to start. vagrant would get stuck after setting the private key for SSH. I pulled up the VritualBox GUI and tried to view the screen and it warned me that I couldn't run a 64-bit VM. I rebooted and turned on the Intel virtualization option in my BIOS, which didn't fix it. Then I removed the Hyper-V feature from Windows, which did the trick.

At this point, I could successfully launch a Centos VM from the command-line. I could SSH into the system with "vagrant ssh" and it automatically linked my host directory with "/vagrant" on the VM, so files copied in either would sync back to the first".

### Local Configuration File, not in git, passed through to VM

The local configuration file works like this.

1) Added a vagrant.config file using YAML that looked like this:

```text
mysql.rootpass:    qwertyissupersafe
mysql.webuser:     yourusername
mysql.webpass:     yourpassword
mysql.dbprefix:    ltd
vagrant.ipaddress: 192.168.1.1
vagrant.hostname:  lessthandot.com
backups.address:   192.168.1.2
backups.username:  FTPUserNameHere
backups.password:  PasswordHere
backups.conf:      lotsostuff.tar.gz
backups.data:      thedatas.tar.gz
```
2) Added logic in the Vagrantfile to parse the config file and later pass values to the bootstrap as args:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'yaml'

Vagrant.configure(2) do |config|

  # load settings from file
  custom_vagrantfile = 'vagrant.local'
  if File.exist?(custom_vagrantfile)
    settings = YAML.load_file custom_vagrantfile
  else
    raise(Exception, "Settings file '#{custom_vagrantfile}' has not been created. Please copy the sample file and fill in the necessary passwords (don't worry, it's excluded from git)")
  end

  # ... more stuff ###

  settings_array = [
    settings["mysql.rootpass"], 
    settings["mysql.webuser"], 
    settings["mysql.webpass"], 
    settings["mysql.dbprefix"], 
    settings["vagrant.ipaddress"], 
    settings["vagrant.hostname"], 
    settings["backups.address"], 
    settings["backups.username"], 
    settings["backups.password"], 
    settings["backups.conf"], 
    settings["backups.data"]
  ]
  config.vm.provision :shell, :path => "vagrant_setup/bootstrap.sh", :args => settings_array
end
```
I was explicit with the settings array so it would be more obvious to me if I mismatched an arg between the array and the bootstrap script.

3) The bootstrap file then starts like so:

```bash
#!/usr/bin/env bash

# Settings from Vagrantfile
SETTINGS_MYSQL_ROOTPASS=$1
SETTINGS_MYSQL_WEBUSER=$2
SETTINGS_MYSQL_WEBPASS=$3
SETTINGS_MYSQL_DBPREFIX=$4
SETTINGS_VAGRANT_IPADDRESS=$5
SETTINGS_VAGRANT_HOSTNAME=$6
SETTINGS_BACKUPS_ADDRESS=$7
SETTINGS_BACKUPS_USERNAME=$8
SETTINGS_BACKUPS_PASSWORD=$9
SETTINGS_BACKUPS_CONF=${10}
SETTINGS_BACKUPS_DATA=${11}
```
4) To prevent the config file from committing, I added it to the gitignore and created a sample file with dummy files that would be added to the git repository.

### Custom Domains / Hosts file

To have the host names resolve, I used a plugin named "hostsupdater". It doesn't support windows UAC yet, so for the short term I had to make my hosts file editable by my user account (ick). I chose this plugin because it allowed me to specify additional aliases rather than assuming I only wanted the hostname of the VM added.

To install it:
  
`vagrant plugin install vagrant-hostsupdater`

In my Vagrantfile:

```ruby
config.vm.hostname = settings["vagrant.hostname"]
config.hostsupdater.aliases = ["sqlcop.#{config.vm.hostname}","blogs.#{config.vm.hostname}","wiki.#{config.vm.hostname}","forum.#{config.vm.hostname}","cooking.#{config.vm.hostname}","admin.#{config.vm.hostname}"]
```
The plugin, hostsupdater, takes care of adding and removing entries from the hosts files when I "vagrant up".

### Copying files that already exist

This will get stuck at an "are you sure" prompt: 

```bash
cp /vagrant/configs/httpd.conf /etc/httpd/conf/httpd.conf
```

This will charge ahead without any prompting at all: 

```bash
\cp /vagrant/configs/httpd.conf /etc/httpd/conf/httpd.conf
```

Do the second. 🙂

### Securing MySQL – mysql-secure.sh

When you install MySQL, it will suggest you run a command to secure your installation. This script performs the same steps:

```bash
#!/usr/bin/env bash

SETTINGS_MYSQL_ROOTPASS=$1
mysqladmin -uroot password "$SETTINGS_MYSQL_ROOTPASS"
mysql -u root -p"$SETTINGS_MYSQL_ROOTPASS" -e "UPDATE mysql.user SET Password=PASSWORD('$SETTINGS_MYSQL_ROOTPASS') WHERE User='root'"
mysql -u root -p"$SETTINGS_MYSQL_ROOTPASS" -e "DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1')"
mysql -u root -p"$SETTINGS_MYSQL_ROOTPASS" -e "DELETE FROM mysql.user WHERE User=''"
mysql -u root -p"$SETTINGS_MYSQL_ROOTPASS" -e "DELETE FROM mysql.db WHERE Db='test' OR Db='test\_%'"
mysql -u root -p"$SETTINGS_MYSQL_ROOTPASS" -e "FLUSH PRIVILEGES"
```
The single parameter for this script is the value from the configuration that I intended the root password to be set to. This is also passed to my later scripts for applying backups and creating a new user.

### Template for httpd.conf

The z_lessthandot.httpd.conf template is just a copy of the production conf with tokens in all the spots that the IP Address or domain name show up, like so:

```text
NameVirtualHost %%SETTINGS_VAGRANT_IPADDRESS%%:80 

#### Production Sites ####
##########################

<VirtualHost %%SETTINGS_VAGRANT_IPADDRESS%%:80>
	DocumentRoot /vagrant/trunk/www/
	ServerName %%SETTINGS_VAGRANT_HOSTNAME%%
	# ... etc ...
</VirtualHost>

<VirtualHost %%SETTINGS_VAGRANT_IPADDRESS%%:80>
	DocumentRoot /vagrant/trunk/forum/
	ServerName forum.%%SETTINGS_VAGRANT_HOSTNAME%%
	# ...etc...
</VirtualHost>
```
To replace the tokens, I use sed and the args passed into the script (originally form the config file):

```bash
sed "s/%%SETTINGS_VAGRANT_IPADDRESS%%/$SETTINGS_VAGRANT_IPADDRESS/g;s/%%SETTINGS_VAGRANT_HOSTNAME%%/$SETTINGS_VAGRANT_HOSTNAME/g" /vagrant/vagrant_setup/z_lessthandot.httpd.conf > /etc/httpd/conf.d/z_lessthandot.httpd.conf
```
And there we go, one customized site httpd.conf.

## Instant Server, Just Add Water

Having done this once, I know the next one I have to build will be even easier. This provides me the freedom of working with the tools of my choice while also running the code exactly like it will on the production environment, without any quirks from my OS, personal PHP setup, and so on getting in my way. If the worst happens, I can destroy the machine and recreate it in minutes.

 [1]: https://www.vagrantup.com/