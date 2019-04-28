---
title: Making an interface for plugwise with nancy
author: Christiaan Baes (chrissie1)
type: post
date: 2013-01-06T11:44:00+00:00
ID: 1900
excerpt: Controlling my plugwise plugs using Nancy.
url: /index.php/webdev/serverprogramming/making-an-interface-for-plugwise/
views:
  - 6321
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - plugwise

---
## Introduction

People who follow me might have heard me complain about [plugwise][1] and their poor service and late delivery. So I have at least some mixed feelings about plugwise, to say the least.

But their product is awesome and works.

So what does plugwise do I hear you ask. Well, you plug them in to an electricity socket and then you plug your device into the plugwise. You can now see how much electricity the device or devices are using and you can turn them on or off from a distance or via a schedule. This opens lots of opportunities. The bad part, their software only works on windows and you need a Zigbee compatible USB-stick to communicate with them. Their are some efforts to make them work on linux and I even read that you can control them via the Raspberry Pi. This opens lots of opportunities. One being that I can build my own webserver to control the devices and then log in to that server from my phone, tablet or other computers that has a browser. And so today I got it working. 

You can read all on the plugwise website.

## Source

I will use the builtin webserver from the source software to communicate with. For this you need to open the source software.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/plugwise/plugwise1.png?mtime=1357478168"><img alt="" src="/wp-content/uploads/users/chrissie1/plugwise/plugwise1.png?mtime=1357478168" width="990" height="816" /></a>
</div>

Then click on Program and Enable the webserver.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/plugwise/plugwise2.png?mtime=1357478180"><img alt="" src="/wp-content/uploads/users/chrissie1/plugwise/plugwise2.png?mtime=1357478180" width="1003" height="807" /></a>
</div>

you can now just browse to http://localhost:8080 and see your modules and their energy consumption and you can turn them on or off.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/plugwise/plugwise3.png?mtime=1357478474"><img alt="" src="/wp-content/uploads/users/chrissie1/plugwise/plugwise3.png?mtime=1357478474" width="1041" height="293" /></a>
</div>

I could now just adapt those files. But it will be easier if I just create a second webapplication that talks to that server. This way I can program it like I want.

## Nancy

So how do talk to that server and get information. Easy. Well not really. Anyway. Go to you program files (x86) folder search for plugwise then Plugwise Source then www where the webpage hides and then the xml folder. In this folder you will find two files. appliances.xml and blocks.xml.

If you open the aplliances.xml file it will look like this.

```xml
&lt;% include '/xml/blocks.xml' %&gt;
&lt;PlugwiseSource Version="1.0"&gt;
  &lt;Appliances&gt;
&lt;%
  foreach Plugwise.Appliances
    Write System.Blocks['xml_Appliance']
  /foreach
%&gt;  
  &lt;/Appliances&gt;
&lt;Modules&gt;
&lt;%
  foreach Plugwise.Modules
    Write System.Blocks['xml_Module']
  /foreach
%&gt;
  &lt;/Modules&gt;
&lt;/PlugwiseSource&gt;```
That is weird. That looks like a scripting language. And it is. 

If you go to http://localhost:8080/xml/appliances.xml you will see this.

```xml
This XML file does not appear to have any style information associated with it. The document tree is shown below.
&lt;PlugwiseSource Version="1.0"&gt;
&lt;Appliances&gt;
&lt;Appliance Id="1" Name="Computer" Type="computer_desktop" PowerUsage="14,2000" TotalUsage="0,86" PowerState="on" Module="3" Image="/sysimg/32/computer_desktop_on.png"/&gt;
&lt;Appliance Id="3" Name="Dishwasher" Type="dishwasher" PowerUsage="0,0000" TotalUsage="0,08" PowerState="on" Module="10" Image="/sysimg/32/dishwasher_on.png"/&gt;
&lt;Appliance Id="7" Name="Microwave oven" Type="oven_microwave" PowerUsage="2,5000" TotalUsage="0,05" PowerState="on" Module="7" Image="/sysimg/32/oven_microwave_on.png"/&gt;
&lt;Appliance Id="9" Name="Nas and switch" Type="computer_desktop" PowerUsage="4,9000" TotalUsage="0,62" PowerState="on" Module="9" Image="/sysimg/32/computer_desktop_on.png"/&gt;
&lt;Appliance Id="8" Name="Refrigerator" Type="refrigerator" PowerUsage="0,0000" TotalUsage="0,55" PowerState="on" Module="8" Image="/sysimg/32/refrigerator_on.png"/&gt;
&lt;Appliance Id="2" Name="TV" Type="tv" PowerUsage="-0,2000" TotalUsage="0,57" PowerState="off" Module="4" Image="/sysimg/32/tv_off.png"/&gt;
&lt;Appliance Id="6" Name="TV 2" Type="tv" PowerUsage="204,8000" TotalUsage="3,08" PowerState="on" Module="2" Image="/sysimg/32/tv_on.png"/&gt;
&lt;Appliance Id="4" Name="Water heater vessel" Type="water_heater_vessel" PowerUsage="0,0000" TotalUsage="1,11" PowerState="on" Module="11" Image="/sysimg/32/water_heater_vessel_on.png"/&gt;
&lt;Appliance Id="5" Name="Water heater vessel 2" Type="water_heater_vessel" PowerUsage="0,0000" TotalUsage="6,71" PowerState="off" Module="5" Image="/sysimg/32/water_heater_vessel_off.png"/&gt;
&lt;/Appliances&gt;
&lt;Modules&gt;
&lt;Module Id="6" Name="278C42A" MAC="000D6F000278C42A" Type="-1"/&gt;
&lt;Module Id="1" Name="278683C" MAC="000D6F000278683C" Type="0"/&gt;
&lt;Module Id="3" Name="2786B62" MAC="000D6F0002786B62" Type="2" PowerUsage="14,1655" RelayState="closed" Appliance="1"/&gt;
&lt;Module Id="4" Name="2786D98" MAC="000D6F0002786D98" Type="2" PowerUsage="-0,1812" RelayState="open" Appliance="2"/&gt;
&lt;Module Id="7" Name="2786DA0" MAC="000D6F0002786DA0" Type="2" PowerUsage="2,4894" RelayState="closed" Appliance="7"/&gt;
&lt;Module Id="10" Name="2786F1D" MAC="000D6F0002786F1D" Type="2" PowerUsage="0,0000" RelayState="closed" Appliance="3"/&gt;
&lt;Module Id="8" Name="278B571" MAC="000D6F000278B571" Type="2" PowerUsage="0,0000" RelayState="closed" Appliance="8"/&gt;
&lt;Module Id="11" Name="278B5C5" MAC="000D6F000278B5C5" Type="2" PowerUsage="0,0000" RelayState="closed" Appliance="4"/&gt;
&lt;Module Id="9" Name="278B834" MAC="000D6F000278B834" Type="2" PowerUsage="4,8539" RelayState="closed" Appliance="9"/&gt;
&lt;Module Id="5" Name="278C37A" MAC="000D6F000278C37A" Type="2" PowerUsage="0,0000" RelayState="open" Appliance="5"/&gt;
&lt;Module Id="2" Name="D35C4B" MAC="000D6F0000D35C4B" Type="1" PowerUsage="204,7996" RelayState="closed" Appliance="6"/&gt;
&lt;/Modules&gt;
&lt;/PlugwiseSource&gt;```
Yep that is all the information we need to make a website.

We can now create our nancy website, which you can [find on github][2].

I guess you all know how to make modules and views and the rest to get it to work. 

And here is a service to read the data.

```csharp
IList&lt;PlugwiseApplianceModel&gt; FindAll()
        {
            var xml = XDocument.Load("http://localhost:8080/xml/appliances.xml");

            var q = from b in xml.Descendants("Appliance")
            select new PlugwiseApplianceModel 
            {
                Name = b.Attribute("Name")==null?"":b.Attribute("Name").Value,
                Id = b.Attribute("Id") == null ? "" : b.Attribute("Id").Value,
                TotalUsage = b.Attribute("TotalUsage") == null ? "" : b.Attribute("TotalUsage").Value,
                Type = b.Attribute("Type") == null ? "" : b.Attribute("Type").Value,
                PowerUsage = b.Attribute("PowerUsage") == null ? "" : b.Attribute("PowerUsage").Value,
                PowerState = b.Attribute("PowerState") == null ? "" : b.Attribute("PowerState").Value,
                Image = b.Attribute("Image") == null ? "" : b.Attribute("Image").Value,
                Module = b.Attribute("Module") == null ? "" : b.Attribute("Module").Value                   
            };
            return q.ToList();
        }```
Easy as Pie.

The clue is in the blocks.xml file where they format the output.

```xml
&lt;%
 format 'Module.PowerUsage' as '{0:0.0000}'
 format 'Appliance.PowerUsage' as '{0:0.0000}'
 format 'Appliance.TotalUsage' as '{0:0.00}'
%&gt;
&lt;% block 'xml_Appliance'%&gt;
&lt;Appliance Id="&lt;%=.Id%&gt;" Name="&lt;%=.Name%&gt;" Type="&lt;%=.Type%&gt;" PowerUsage="&lt;%=.PowerUsage%&gt;" TotalUsage="&lt;%=.TotalUsage%&gt;" PowerState="&lt;%=.PowerState%&gt;" Module="&lt;%=.Module.Id%&gt;" Image="&lt;%$_Base%&gt;/sysimg/32/&lt;%=.StatusImageName%&gt;.png" /&gt;
&lt;%/block%&gt;
&lt;% block 'xml_Module'%&gt;
&lt;Module Id="&lt;%=.Id%&gt;" Name="&lt;%=.Name%&gt;" MAC="&lt;%=.MACAddress%&gt;" Type="&lt;%=.Type%&gt;" &lt;%if .Type&gt;0%&gt; PowerUsage="&lt;%=.PowerUsage%&gt;" RelayState="&lt;%=.RelayState%&gt;" Appliance="&lt;%=.Appliance.Id%&gt;" &lt;%/if%&gt; /&gt;
&lt;%/block%&gt;
&lt;% block 'xml_Group'%&gt;
&lt;Group Id="&lt;%=.Id%&gt;" Name="&lt;%=.Name%&gt;" /&gt;
&lt;%/block%&gt;
&lt;% block 'xml_Room'%&gt;
&lt;Room Id="&lt;%=.Id%&gt;" Name="&lt;%=.Name%&gt;" Appliances="&lt;%=.Appliances%&gt;" /&gt;
&lt;%/block%&gt;
```
As you can see I added the blocks for Group and Rooms. 

I for one made a modules.xml to get that data seperatly from the appliances and I made a rooms.xml to get the rooms data.

I also made a module.xml to get the data from a given module.

```xml
&lt;% include '/xml/blocks.xml' %&gt;
&lt;PlugwiseSource Version="1.0"&gt;
  &lt;Groups&gt;
&lt;%
  $param = Request.Get["moduleid"]
  $module = Module($param)
%&gt;
&lt;Module Id="&lt;%=$module.Id%&gt;" Name="&lt;%=$module.Name%&gt;" MAC="&lt;%=$module.MACAddress%&gt;" Type="&lt;%=$module.Type%&gt;" &lt;%if $module.Type&gt;0%&gt; PowerUsage="&lt;%=$module.PowerUsage%&gt;" RelayState="&lt;%=$module.RelayState%&gt;" Appliance="&lt;%=$module.Appliance.Id%&gt;" &lt;%/if%&gt; 
/&gt;
  &lt;/Groups&gt;
&lt;/PlugwiseSource&gt;
```
Which you can call like this http://localhost:8080/xml/module.xml?moduleid=6

So that is how this all works.

And this is what it looks like for now.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/plugwise/plugwise4.png?mtime=1357479769"><img alt="" src="/wp-content/uploads/users/chrissie1/plugwise/plugwise4.png?mtime=1357479769" width="896" height="421" /></a>
</div>

But I&#8217;m sure you can imagine where we are going with this. It will be easy to add a mobile version and so on. It was getting started that was difficult, since there is not to much documentation to learn from or examples. 

## Conclusion

It is fairly simple to get this to work. Now I just need to buy a cheap, low energy consuming PC that runs Windows and that can serve as webserver.
  
And yes the next step is to be able to turn on and off the modules. But that is simple if you [read the documentation][3].

 [1]: http://www.plugwise.com/idplugtype-e/
 [2]: https://github.com/chrissie1/NancyPlugWise
 [3]: http://www.bwired.nl/images/how/Plugwise_Source_Template_Engine.pdf