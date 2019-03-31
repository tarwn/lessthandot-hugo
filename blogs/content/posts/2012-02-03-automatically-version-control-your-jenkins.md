---
title: Automatically Version Control Your Jenkins Configuration
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-02-03T17:02:00+00:00
excerpt: As part of the Continuous Delivery project I embarked on late last year, I created 4 separate jobs in Jenkins to serve as steps in my pipeline. Some of these jobs are fairly complex and, while I could probably rebuild them from the information in my blog posts, I thought it would make more sense to make some backups.
url: /index.php/enterprisedev/application-lifecycle-management/automatically-version-control-your-jenkins/
views:
  - 12909
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
tags:
  - jenkins
  - mercurial

---
As part of the [Continuous Delivery project][1] I embarked on late last year, I created 4 separate jobs in Jenkins to serve as steps in my pipeline. Some of these jobs are fairly complex and, while I could probably rebuild them from the information in my blog posts, I thought it would make more sense to make some backups. 

But I hate doing backups.

And I hate digging through backups to find something.

What I really needed was a way to automatically push the configuration files into a mercurial repository. This would require no ongoing work from me, &#8216;backups&#8217; that are taken only when there are changes, and very easy browsing of differences between versions or over time. Sold.

## Mercurial Repository

First up is creating the local mercurial repo. I only want it to pick up the configuration files, but these are stored in the same folders that the Jenkins executables and my jobs are stored in. 

In order to only version my configuration files, I&#8217;ll create the repository, tell it to ignore all files in the folder, then explicitly add just the ones I want to track.

Creating the repository:

<pre>cd C:Program Files (x86)Jenkins
hg init
echo .*&gt;.hgignore</pre>

With the repository added and the hgignore set, we can now tell it exactly which files to add to the repository:

<pre>hg add .hgignore
	hg add config.xml
	hg add jobs/*/config.xml
	hg commit -m "Initial Commit of Configuration Files"</pre>

And then the last step is to create a remote repository, add the credentials to mercurial, and do our first push. 

In my case I created a new BitBucket repository and then cheated by opening the repository in TortoiseHg WorkBench to save the remote address and my credentials as &#8220;default&#8221;.

With the remote repository setup, now I can do the first push:

<pre>hg push default</pre>

And my configurations are safely whisked away to the cloud.

## Automagicalize It

One of the advantages I sold myself on was the hands off nature of the final solution. In order to achieve that I need to setup something to perform nightly commits and pushes for me. With Jenkins right here, I might as well use it to drive it&#8217;s own backups.

First up, I&#8217;ll create a new job named &#8220;Backup Configurations&#8221;.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/JenkinsBackups/1.png" title="General Job Settings" /><br /> General Job Settings
</div>

I want this to run every night, so I&#8217;ll setup a trigger to run at 1:30 every night by specifying &#8220;Build Periodically&#8221; with a setting of &#8220;30 1 \* \* *&#8221;.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/JenkinsBackups/2.png" title="Build Trigger Settings" /><br /> Build Trigger Settings
</div>

And then the last step is to add an &#8220;Execute Windows Batch Command&#8221; step to my job that executes a similar commit and push to the ones above. Because I am in a subfolder of the repository, I don&#8217;t have to add in any path commands or other trickery.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/JenkinsBackups/3.png" title="Build Step" /><br /> Build Step
</div>

Save the job and kick it off once to verify it does indeed work (after making a minor change to a config file, of course) and I can see I have a working, automated backup.

As a last step I went back and added the backup jobs config.xml file to the repository, so now it not only backs up my other configurations but also itself.

 [1]: http://wiki.ltd.local/index.php/Eli's_Continuous_Delivery_Project "See the wiki post on the project"