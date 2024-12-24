---
title: Teamcitysharp v0.1 released
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-24T08:12:00+00:00
ID: 1395
excerpt: The always amazing Paul Stack has released his very first edition of teamcitysharp and I though it a good idea to try it.
url: /index.php/desktopdev/mstech/teamcitysharp-v0-1-released/
views:
  - 5711
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - teamcity
  - teamcitysharp

---
The always amazing [Paul Stack][1] has released his very first edition of [teamcitysharp][2] and I though it a good idea to try it.

If you don&#8217;t know what [teamcity][3] is then you are missing out. No need to hold back either since it is free for personal use and some other uses.

For people that use Teamcity it is always useful if you can spread your stats around and show the managers what you are doing and how good you are doing. Managers love shiny things. So I made this shiny thing in less then ten minutes so the managers can now see how well I&#8217;m doing. 

You would also like to keep it simple for your manager.

My statusboard looks like this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/teamcitysharp/teamcitysharp.png?mtime=1322128878"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/teamcitysharp/teamcitysharp.png?mtime=1322128878" width="307" height="519" /></a>
</div>

And yes I made that just now in less than ten minutes.

Here is how I connected tot the teamcity server

```csharp
var client = new TeamCityClient("url:port");
client.Connect("username", "password");
```
the url is the url to the server but without the http:// in front of it.

This is how I got all the information you see in that status.

```csharp
var projects = client.AllProjects();
RtfUtils.RtfRedSizeplus3(Text, "Builds");
foreach(var project in projects)
{
  RtfUtils.RtfBlackBold(Text, project.Name);
  foreach(var config in client.BuildConfigsByProjectId(project.Id))
  {
    var lastBuild = client.LastBuildByBuildConfigId(config.Id);
    RtfUtils.RtfBlackNormal(Text, config.Name);
    if (lastBuild.Status == "SUCCESS")
    {
      RtfUtils.RtfGreenNormal(Text, " last built on " + DateTime.ParseExact(lastBuild.StartDate, "yyyyMMddTHHmmsszzzzz", System.Globalization.CultureInfo.InvariantCulture).ToString("dd/MM/yyyy HH:mm"));   
    }
    else
    {
      RtfUtils.RtfRedNormal(Text, " last built on " + DateTime.ParseExact(lastBuild.StartDate, "yyyyMMddTHHmmsszzzzz", System.Globalization.CultureInfo.InvariantCulture).ToString("dd/MM/yyyy HH:mm"));  
    }
  }
  RtfUtils.RtfBlackBold(Text, "");
}
```
The important things are the client.AllProjects() which gets you all the projects. The client.BuildConfigsByProjectId(project.Id) which gets you all the Buildconfigsfor that project. And client.LastBuildByBuildConfigId(config.Id) which will get you the latest build information of that build. On success I then color the text green and on failure I color it red. 

So there, simple enough.

But remember this is version 0.1 so a few features are still missing. But I&#8217;m sure Paul will add them all over the weekend ;-).

 [1]: http://www.paulstack.co.uk/blog/
 [2]: http://www.paulstack.co.uk/blog/post/introducing-teamcitysharp.aspx
 [3]: http://www.jetbrains.com/teamcity/