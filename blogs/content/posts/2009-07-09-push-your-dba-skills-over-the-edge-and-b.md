---
title: Push your DBA skills over the edge by becoming a better developer
author: Ted Krueger (onpnt)
type: post
date: 2009-07-09T12:47:22+00:00
ID: 500
url: /index.php/datamgmt/dbprogramming/push-your-dba-skills-over-the-edge-and-b/
views:
  - 5234
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
A few days ago I had a situation in which I needed to get backups for one of my DBs off to the hot site for disaster/recovery purposes. The primary goal was for me to again remove the need to rely on tape backups from my recovery plans. I already have Quests Litespeed in place and with that have the ability to retain a month of backups on a disk array attached to the instance that this DB resides on. That alone has me feeling comfortable but as most DBAs will agree, we never really get into a comfort zone and always feel the need to better protect out data from disasters. So compressed my backups for this DB are running around 15GB and the Differentials are around 6GB. Of course you can&#8217;t just log in daily and drag&#8217;n&#8217;drop these files over the WAN. First the file may not get over there by the time you leave and you end up cancelling it. Second if you have something like RDP or VNC, the chances of the copy failing are 5050 and then the file is useless for restore on the other side. This leads me to my theory of DBAs and the need for them to have basic development skills. I don&#8217;t mean for DBAs to run out and become hard core developers. I&#8217;m talking about having the ability to go above and beyond the simplistic batch files and windows task manager. In the scenario I was faced with this time, I felt a windows service was idea to get the job done. This way I can use my backup schedules to my advantage and do copy tasks during those off peak hours. Basically my thought process was to grab the file once it was available in the backup directory. Once it was found based on the naming conventions of the files, I then start the copy over to the DR site. This happens through the night and then once the copy succeeds or fails use the event log to capture the events. Now I can simply browse the event viewer on the server the windows service is installed to see how my backups copied over. I can also test my backups by restoring them on that offsite instance either manually or through jobs. Testing your backups by restoring them is of course the best way to know if they will actually save you. 

I&#8217;ll be the first to admit that over the years I have lost some of my development skills. It&#8217;s like anything else in technology really. If you don&#8217;t use it for awhile, you need to work a bit harder to get the job done when you want to jump back on it. Being a DBA, development tasks are nowhere near a daily job. I do utilize SQLCLR and my knowledge of programming often but you can only use those skills to work as a DBA so often while retaining the, &#8220;use the right object for the task at hand&#8221; mentality. With that I thought posting the service and how to install it would be helpful to others. I&#8217;m still going through some additional error handling with the code and adding more logging but this is the shell of things. 

First warning of this entire process is to find a server that you won&#8217;t directly affect by the task. File operations cause high OS paging and a number of other issues with the servers resources. So don&#8217;t go throwing this onto your primary database servers unless you want to have issues during copy times. I recommend to all DBAs to fight for a job server. Everyone should have an instance set aside and configured for jobs that perform tasks that put pressure on the database servers. Last thing you want to do is affect performance by trying to better secure your data. No matter what that task is.

The code for this type of task is pretty simple. In the OnStart of your windows service you initiate the timer and the call to the method in which you perform your tasks.

```CSHARP
protected override void OnStart(string[] args)
        {
            TimerCallback tmrCallBack = new TimerCallback(MoveBackup);
            oTimer = new Timer(tmrCallBack);
            oTimer.Change(new TimeSpan(0, 0, 1), new TimeSpan(0, 1, 0));
        }
```
Then in our main method we need to first grab some config values. In this case we need the source and destination directory and we want this to be easily changed so hence the app.config usage. Once we have those values we grab all the files in the directories and then start running through them finding the files that are designated Full and Diff. In most cases log shipping will be going on or some other means so the log backups are unwanted for this operation. The last step in validation is to make sure we don&#8217;t try copying files we already have on the destination. You do that by simply searching the array we filled up with file names. And of course after all this is done we simply copy it over. To better this you can throttle it up by chopping the file up and sending it across in small pieces similar to bringing down large files from HTTP sources. For this case I have the schedules for backups on off peak hours and a few hours of time in which I can do the copy without concern to flooding the pipe. The network is also setup to throw the copy to the back of network traffic so it will not take priority over other things.

So here is that method

```CSHARP
private void MoveBackup(object state)
        {
            string source = ConfigurationSettings.AppSettings["dirSource"];
            string destination = ConfigurationSettings.AppSettings["dirDestination"];
            string sSource;
            string sLog;
  

            sSource = "Full Backup Copy Service";
            sLog = "Application";

            try
            {
                oTimer.Change(Timeout.Infinite, Timeout.Infinite);

                string[] files = Directory.GetFiles(source);
                string[] destfiles = Directory.GetFiles(destination);

                for (int i = 0; i < files.Length; i++)
                {
                    FileInfo f = new FileInfo(files[i]);


                    if (((f.ToString().IndexOf("Full") > 0) || (f.ToString().IndexOf("Diff") > 0)) 
                                    && (SearchArr(destfiles, f.Name.ToString()) == false)
                                    && (f.CreationTime >= System.DateTime.Now.AddDays(-7)))
                    {
                        File.Copy(files[i], destination + f.Name);
                        if (!EventLog.SourceExists(sSource))
                            EventLog.CreateEventSource(sSource, sLog);

                        EventLog.WriteEntry(sSource, "Full backup file copy as " + destination + f.Name + " was successful at: " + System.DateTime.Now.ToString());
                    }
                }
                oTimer.Change(new TimeSpan(0, 0, 1), new TimeSpan(0, 5, 0));
            }
            catch (Exception e)
            {
                if (!EventLog.SourceExists(sSource))
                    EventLog.CreateEventSource(sSource, sLog);

                    EventLog.WriteEntry(sSource, "Full backup file copy failure: " + e.Message.ToString(),EventLogEntryType.Warning, 234);
                    oTimer.Change(new TimeSpan(0, 0, 1), new TimeSpan(0, 1, 0));
            }
            
        }
```
And my supporting methods for searching arrays and grabbing the file names are

```CSHARP
private bool SearchArr(string[] arr, string search_string)
        {
            bool match = false;

            for (int i = 0; i < arr.Length; i++)
            {
                if (GetFileName(arr[i].ToString()) == search_string)
                {
                    match = true;
                }
            }
            return match;
        }

        private string GetFileName(string filepath)
        {
            Regex r = new Regex(@"w+[.]w+$+");
            return r.Match(filepath).Value;
        }
```
These are nothing special and can be found online in hundreds of different forms. 

After you have that all written this is how you install it if you have never done so.

Copy the debug or release folder form your project over to the server. Open command prompt and navigate to the .net framework version you have. in my case that is v2.0 so
  
C:WINDOWSMicrosoft.NETFrameworkv2.0.50727

Using the InstallUtil.exe you install the service by simply calling the executable with the parm of your executable.
  
InstallUtil &#8220;C:BackupCopyCopyService.exe&#8221;

The installation is controlled by your installer you added to the project. The settings like user logon, start mode etc&#8230; are all set there. If you have a hard time with this I would use sites like [code project][1] and such to get you going on the fundamentals of writing windows services. They really are simple to get going. They are a pain to debug, but simple in comparison to other development tasks.

So there is my plea to all the DBAs to go out and have the motivation to learn some development skills. It can really make you an exceptional DBA and push you over that level of just another DBA monitoring index fragmentation. 🙂

 [1]: http://www.codeproject.com/KB/system/WindowsService.aspx