---
title: A New Solution for Monitoring SQL Server
author: Jes Borland
type: post
date: 2013-12-30T14:22:00+00:00
ID: 2215
excerpt: Monitoring a SQL Server environment is one of the many tasks a DBA should be performing on a regular basis.
url: /index.php/datamgmt/dbadmin/a-new-solution-for-monitoring/
views:
  - 18486
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
Monitoring a SQL Server environment is one of the many tasks a DBA should be performing on a regular basis. However, you need to know more than “Is my SQL Server still running? Did my backups complete?” You should know what your average performance is over time &#8211; baselines. You should be able to get notifications if there is a long-running query, or if a disk is running low on space, or if there is blocking.

There are a lot of data points on any one SQL Server to monitor, and writing the tools to do so is time-consuming, tedious &#8211; and unnecessary. Lots of companies have done the work for you &#8211; no need to reinvent the wheel.

Now, it's no secret I'm a fan of [Red Gate][1] tools &#8211; I've even been lucky enough to be a Friend of Red Gate for the last two years. Red Gate makes a fantastic tool, [SQL Monitor][2], which keeps you up to date on the status of the servers you choose to monitor. It has dashboards, alerts, baselines, and custom metrics. It also requires that you install the tool on a server in your environment. In some cases, you cantt get clearance to do that. For example, as a consultant, I don't always get &#8211; or want! &#8211; full-time access to a client's servers. But I'd still like to monitor some of the metrics as I need them. How can you and I take advantage of this monitoring tool?

Red Gate is rolling out SQL Monitor Hosted! It's a new service in which you install a relay service on the server you wish to monitor (or another server that can see the machines you want to monitor and has access to the internet), it collects information, and sends it to a Red Gate server. When you want to view your metrics, you log in to the website, pick which of your server's metrics you want to view, and go to it.

I'll give you a quick overview of setup and use.

The first thing you'll need to do is go to <https://sqlmonitor.red-gate.com/> and create an account.

Second, you'll download and install the relay service &#8211; once for each server you wish to monitor.

<img style="vertical-align: middle;" src="/wp-content/uploads/users/grrlgeek/smh 1.JPG?mtime=1388175665" alt="" width="991" height="320" />

The server you're installing it on needs to be able to communicate with a Red Gate IP, which is given to you in the setup process, in case you need to add that to your firewall.

After installation, you can go to the SQL Monitor Hosted page and log in. Then, you add the names of the server(s) you wish to monitor.

Then you begin monitoring! You get all the functionality of SQL Monitor that is installed on a local server &#8211; without having to install the application and database on your server. Here's a screenshot of my overview:

<img style="vertical-align: middle;" src="/wp-content/uploads/users/grrlgeek/smh 3.JPG?mtime=1388175665" alt="" width="1375" height="376" />

I can see the number of alerts that have been raised, the priority, and which servers need the most attention. I can drill into that more by going to the Alerts tab. Clicking on an alert will bring up further information, which can help me determine the cause and take action to fix it (if necessary).

<img style="vertical-align: middle;" src="/wp-content/uploads/users/grrlgeek/smh 2.JPG?mtime=1388175666" alt="" width="1299" height="590" />

The Analysis tab lets you view a history of various metrics on the system. I can view CPU usage, memory usage, disk usage, and more. One of my favorite features here is the ability to compare it to a baseline. I can choose to compare the last hour of data with the hour prior to that, for example.

<img style="vertical-align: middle;" src="/wp-content/uploads/users/grrlgeek/smh 4.JPG?mtime=1388175666" alt="" width="1372" height="618" />

Security shouldn't be a concern &#8211; everything sent between your server and Red Gate's server is SSL-encrypted, and the credentials are stored on your server, not Red Gate's.

If you're looking for a monitoring solution that will be full-featured but lightweight in your environment, give SQL Monitor Hosted a try, and give Red Gate feedback &#8211; they’d love to hear more about it!

 [1]: http://www.red-gate.com/
 [2]: http://www.red-gate.com/products/dba/sql-monitor/