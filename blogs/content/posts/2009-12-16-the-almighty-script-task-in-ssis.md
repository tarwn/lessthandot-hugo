---
title: Script Task vs Flat File Connection Management
author: Ted Krueger (onpnt)
type: post
date: 2009-12-16T18:31:58+00:00
ID: 589
url: /index.php/datamgmt/datadesign/the-almighty-script-task-in-ssis/
views:
  - 8684
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
## _The Script Task_

The script task is pretty much the all mighty in terms of what you can do. When you sit there initially with the script task in front of you, you are going to be saying you can write your entire process in this thing. I mean in reality you could. You could open a connection to your source database and start hacking your way to the results, the power is there! It has the ability for you to write .NET code in its best form (or worst) and run that task to your SQL Agents heart’s content.

Alright, stop getting excited one task man, there is a place and a job for everything. SSIS has gifted abilities to give you options. I probably could have written the [6 ways to import][1] data completely from SSIS and still had the 6 completely different methods. That doesn’t mean I should do it any certain way just because I can. That has always been the foundation of multiple platforms, languages and frameworks. Each has the ability to perform certain jobs in certain situations better than the next. 

## _A real world scenario_

It is very common for the need to extract data from your databases in order to transform and import into flat files. Many database developers new so SSIS attach themselves to the script task to complete this type of process. System.IO is exposed as many namespaces in the script task so you can create the new file dynamically and then pump the data into the file while in the same context. The script task will be more than happy to do this for you and possibly in many cases efficiently. The problem is at this point you have just turned all of SSIS into a container around one or multiple simple executables. What you have in SSIS with the script task at this point is simply a container of the executable. A major reason I’m picking on the new flat file creation and export from a database source is I’ve seen it many times over. When I ask the developers why they do this, they usually explain to me how they selected a file for the flat-file connection and the OK button greyed out, leaving them unable to continue, so they figured it was broken or not capable of the task. “Don't stop there!”, I tell them, “You are on the right track”. 

The flat-file connection requires you to map your columns, but this is not obvious and is commonly missed. You can take the flat file connection and set the columns to the way they will be on export. This means the mappings will match up on the later setup of the transform from the source.

## _Let’s give it a try_

Right click Connection Managers and hit New Flat File Connection.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//watch_1.gif" alt="" title="" width="247" height="243" />
</div>

Enter in some funky name so you remember this was an example and you can go back to it when you need to refresh your memory. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//watch_2.gif" alt="" title="" width="445" height="250" />
</div>

Enter a path, any path. I like littering my C drive with files and in a month I’ll be wondering if I should or shouldn’t delete.

Now go into Advanced. Note: The OK is still grayed out. Click the New column button a few times 

Ah…I can save it now

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//watch_3.gif" alt="" title="" width="397" height="313" />
</div>

Let's try it out with a test data flow I created while helping someone out a few weeks ago. Change your connection manager property in a flat file destination to point to our new file connection.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/watch_4.gif" alt="" title="" width="469" height="143" />
</div>

Map some columns over

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/watch_5.gif" alt="" title="" width="478" height="208" />
</div>

And let’s execute to see the results.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/watch_6.gif" alt="" title="" width="438" height="263" />
</div>

🙂 It’s a beautiful thing we have. Checking our file that was created on the C drive we see the contents pumped in

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/watch_7.gif" alt="" title="" width="460" height="173" />
</div>

## _Comparing and wrapping up_

Now if you did this in a script task, you would first have to create variables to hold the file locations, then create the file in the location and then go about setting all the properties of the file connection along with the file destination.    
That would be one way.

The point I’m trying to get across is that the script task is nothing short of a powerful tool. I have used it to do things in the new ETL platform for SQL Server that I couldn’t dream of doing in DTS. Basically the lack of the script task and level of progamming in DTS would force me to start writing external code in a service or executable. This in some cases you can do with the script task alone. My career started in development so that was a way I turned quickly early on in my SSIS trials. I knew that wasn’t the greatest thing though. It brings up maintainability, knowledge transfer issues, documentation time and points of failure to name a few.

So the next time you go to perform a task in SSIS and that script task stares you in the face telling you he can do it

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/doitallnight.gif" alt="" title="" width="400" height="321" /><br />ALL NIGHT LONG!!!!!
</div>

You may want to think about it and look around to see if SSIS already has something that prevents the code from being rewritten allowing you to take advantage of SSIS and the abilities it brings with it.

 [1]: /index.php/DataMgmt/DBAdmin/title-12