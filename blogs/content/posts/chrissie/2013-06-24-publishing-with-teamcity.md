---
title: Publishing with teamcity
author: Christiaan Baes (chrissie1)
type: post
date: 2013-06-24T07:57:00+00:00
ID: 2115
excerpt: Publishing your website using teamcity and the publish feature of Visual studio.
url: /index.php/webdev/serverprogramming/publishing-with-teamcity/
views:
  - 6851
categories:
  - ASP.NET
  - Server Programming
tags:
  - publish
  - teamcity

---
I guess most of you know that feature of Visual studio where you can easily publish your site, either via MS Deploy or just to a shared folder. You can even have different profiles, one for the server, one for QA and one for Dev. Or you can just deploy straight to production and leave out all the fiddly bits.

I have been doing this webdev thing for a few months now and have been using the publish method to Deploy to Dev and then I would copy that code to prod (technically it is not yet in production), making sure I did not copy/paste the config files over or the log files. I also had a build configuration on [teamcity][1] to build and run the tests. I gathered the copy/paste to prod step was kind of unprofessional and I should make it better. 

Using the publish feature and teamcity it is fairly easy to make it work. 

There is two ways to make this work in teamcity.

But first we need to have the name of our profile we made in VS (you did make a profile, right?)

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/publish/Publish001.png?mtime=1372059527"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/publish/Publish001.png?mtime=1372059527" width="720" height="560" /></a>
</div>

And now we can create our MSBuild step and start telling it to publish. 

We can do this by creating Build parameters. We need three.

Configuration, DeployOnBuild and PublishProfile. These are all System properties.

You can add them in your configuration via the buid parameters page.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/publish/Publish005.png?mtime=1372060237"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/publish/Publish005.png?mtime=1372060237" width="266" height="481" /></a>
</div>

Click on the Add new parameter button and fill in this little form.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/publish/Publish003.png?mtime=1372059859"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/publish/Publish003.png?mtime=1372059859" width="480" height="364" /></a>
</div>

After you did all three you should see this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/publish/Publish004.png?mtime=1372060226"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/publish/Publish004.png?mtime=1372060226" width="877" height="334" /></a>
</div>

The problem with the above method is that the build parameters will work for all the build steps in your configuration and that is not always desirable. 

I for one like to build a debug version, run the tests on the debug version and the do a release build that gets published. If you use the build parameters way, that won&#8217;t work.

So go ahead and delete the build parameters. And add them to the commandline of your MSBuild. Like so.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/publish/Publish002.png?mtime=1372059702"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/publish/Publish002.png?mtime=1372059702" width="699" height="550" /></a>
</div>

The Platform is optional for the publish. 

So now that that works I just push to master and the website gets published after running all the tests. Happy Chris.

 [1]: http://www.jetbrains.com/teamcity/