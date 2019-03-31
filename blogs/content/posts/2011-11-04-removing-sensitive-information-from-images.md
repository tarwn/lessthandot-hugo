---
title: Blogging Tip – Removing Sensitive Information from Images
author: Ted Krueger (onpnt)
type: post
date: 2011-11-04T11:04:00+00:00
excerpt: 'One of the things that is fairly important when writing articles, blogs, whitepapers or even authoring books is ensuring you do not share any personal information in your screen captures.  This is even more important when you are using company property&hellip;'
url: /index.php/itprofessionals/policystandards/removing-sensitive-information-from-images/
views:
  - 6897
rp4wp_auto_linked:
  - 1
categories:
  - Policy and Standards
  - Professional Development
  - Project Management

---
One of the things that is fairly important when writing articles, blogs, whitepapers or even authoring books is ensuring you do not share any personal information in your screen captures.  This is even more important when you are using company property to make your screen captures.

Now, on some occasions, leaving connections to SQL Server or Security context is ok.  For example, I’ve used Fred and God commonly as SQL Authenticated Logins to my instances.  Those two accounts are something I picked and kept to over the years.  This can be done with named instances also.  You can install and name a SQL Server something comical, meaningful or encrypted.  _“Connect to your instance named, SomeCrasyMessedUpMachineHolySmokesSQLIsCool”._

But getting back to topic; you really want to make sure you never leave a trace of anything that you do not purposely intend to use, like above.

Here is how I remove these potentially, incriminating names.

Download [PAINT.NET][1].  Paint.net is a free image editor that is extremely valuable.  For years I used Photo Shop and for years my wallet was empty due to it.  Now, Photo Shop does have a lot of very advanced options that Paint.net doesn’t but at my level for writing, those options will never be touched.  So Paint.net filled a hole where cost and needed abilities were completely met.

After installing Paint.net, I recommend installing the [Megalo Effects Plugin Pack][2]. You get dozens of really cool effects and options for editing in more advanced ways.  For this tip, those will not be used but again, highly recommended.

Using Windows 7 Snipping Tool, take a screen capture of a login properties window opened from SSMS.  (Note: I marked out the real values in Server and Connection still. You can do that with Paint.net too!)

<div class="image_block">
  <a href="/wp-content/uploads/blogs/ITProfessionals/-3.png?mtime=1320411554"><img alt="" src="/wp-content/uploads/blogs/ITProfessionals/-3.png?mtime=1320411554" width="318" height="284" /></a>
</div>

We’re going to edit Fred in this case.  The properties that we would not want to share to the public are Server and Connection.  These properties will show domain logins and SQL Server Instance names.  You would be surprised how many Server values I’ve seen that were full IP addresses that I could ping and I could actual connect to.  Those instances were placed online and exposed extremely poorly and a massive security risk to networks and even, home machines.

**Removal with Paint.net**

To remove these values, click the cropping icon in the Tools box in Paint.net.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/ITProfessionals/-4.png?mtime=1320411554"><img alt="" src="/wp-content/uploads/blogs/ITProfessionals/-4.png?mtime=1320411554" width="67" height="97" /></a>
</div>

Now, select the area you need to remove and hit Delete on your keyboard.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/ITProfessionals/-5.png?mtime=1320411554"><img alt="" src="/wp-content/uploads/blogs/ITProfessionals/-5.png?mtime=1320411554" width="276" height="171" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/ITProfessionals/-6.png?mtime=1320411554"><img alt="" src="/wp-content/uploads/blogs/ITProfessionals/-6.png?mtime=1320411554" width="235" height="145" /></a>
</div>

We need to make this look better because the world is going to see it.  To do this we want to first, sample the color area that the value we just removed was in and then using the paint bucket, fill that area with the selected color.

Select the Color Picker icon in the toolbox

<div class="image_block">
  <a href="/wp-content/uploads/blogs/ITProfessionals/-7.png?mtime=1320411554"><img alt="" src="/wp-content/uploads/blogs/ITProfessionals/-7.png?mtime=1320411554" width="91" height="99" /></a>
</div>

Move the color picker over the gray area next to Server: and click the area.  This selects the color exactly the way it is in the image.

Next, select the Paint Bucket icon in the toolbox.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/ITProfessionals/-8.png?mtime=1320411610"><img alt="" src="/wp-content/uploads/blogs/ITProfessionals/-8.png?mtime=1320411610" width="77" height="98" /></a>
</div>

Move the paint bucket icon over the areas that were cropped and click the area.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/ITProfessionals/-9.png?mtime=1320411611"><img alt="" src="/wp-content/uploads/blogs/ITProfessionals/-9.png?mtime=1320411611" width="245" height="133" /></a>
</div>

Perfect!

At this point you have a clean image that is not distracting to what you are trying to write about and looks professional.

Happy writing!

 [1]: http://paint.net/
 [2]: http://paint.net.amihotornot.com.au/Download/PluginsPack/