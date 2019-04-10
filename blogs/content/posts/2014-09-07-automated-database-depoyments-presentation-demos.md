---
title: Automated Database Deployments – Presentation Demos
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-09-07T14:20:46+00:00
ID: 2925
url: /index.php/datamgmt/dbadmin/automated-database-depoyments-presentation-demos/
views:
  - 6270
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
tags:
  - continuous delivery
  - continuous integration

---
This is an outline of the tools and scripts I used while demonstrating conversion of a sample “we make all our changes in production” database into a basic pipeline to verify and deploy changes automatically, as well as verify restores on a nightly basis.

This post contains all of the steps, software, and scripts that I used during the “Automated Deployments” talk at SQL Saturday 320.

There aren't any pictures, if I did that than what would be the point of presenting it? 🙂

## Software Choices

When I started the demo, I had some previously installed software already in place to help the demos move quickly. Everything I used was free or covered under an MSDN subscription.

**Database VM** – “PLC026-DB01” – The demo database “server” was running Windows 2012 R2 and SQL Server 2012 developer edition. The database was a sample database from a ASP.Net Music Store tutorial that I happened to have sitting around; the backup I used during the demo is available [here][1] (press the “Raw” button to download).

**Build Server VM** – “PLC026-Build01” – The “build server” was running Windows 2012 R2 and included a free git server named [Bonobo Git Server][2] and the build server [TeamCity][3]. I also installed the SQL Management Objects for Powershell following the instructions [here][4].

**Laptop** – on my “local” system I was using SQL Server 2012, Powershell 3, Chrome, and [git][5]. I was using a GUI for git named [SmartGit][6], which was the only-non-free/non-MSDN tool in the mix.

## Server Setup

The Database VM was a basic next-next-next installation of SQL, though I did use mixed authentication so I could easily supply credentials from the build scripts.

Installation for the Build Server VM was a little more advanced. I had to install IIS to support Bonobo, which I downloaded and placed in the wwwroot folder as directed by their instructions. This places Bonobo on port 80, the default web port, so I could access it easily with a browser (http://PLC026-Build01/) and configure the admin account and my new account. I then downloaded an installed TeamCity, leaving all of the default options in place. I used the built-in database when asked, as this was a demo, but for a more permanent installation you would probably want to use SQL behind the scenes. TeamCity installs on port 8080 by default (http://PLC026-Build01:8080/).

The last step was to add a shared folder on the database server for the backups that could be accessed by the build server. The restore script uses a share named \\PLC026-DB01\Backups to access this folder, locally it was C:\Database\Backups on my DB VM.

The only additional setup I performed at this point was to restore a copy of my sample database as “PROD_DeployPresentation” to serve as the sample “production” database.

Demo Resources: All demo scripts are located here: [tarwn/AutomatedDBDeploymentTalk on github.com][7]

## Demo 1: Building the Initial Pipeline

The goal of the first demo is to:

  * Move the responsibility for writing <u>working</u> scripts back to the developers
  * Make it faster to detect and correct issues with the scripts
  * Automatically produce a production-like environment for any manual testing of changes
  * Add change tracking to the process to simplify troubleshooting + auditability

The first step is to start tracking your changes using version control.

1. We reviewed the script [00000_DayOfWeekSalesReport.sql][8] as one we would have previously applied manually

2. I created a folder locally, with a sub-folder named “Changes” and added the script to the “Changes” folder

3. I initialized the top folder as a git repository by right clicking in the folder and choosing “Git init repo” from the context menu

Note: There are other ways to initialize the repository, this option was available to me because I chose the “include cheetah” option when installing git, which provides some context menu items for git that wouldn't be available otherwise

4. I then opened smart git and created a new project from that repository so I could start using the GUI tool

5. We saw the script file listed as an non-committed change, so we committed this change to source control to start tracking it

6. We then switched over to our remote git server, Bonobo on the build server, and created a new remote repository

7. We copied the URL for the repository, switched back to our local git repository and added that URL as a Remote location we could Push to 

Note: In SmartGit this was the Remote folder, Push To, add the URL in the dialog, add credentials, a Push button, and then a dialog that we chose to Configure Tracking between that remote repository and our local one so we could track changes from our local “master” branch against the remote “master” branch. This can also be done via command line using the “git remote” command and if you are using a service like github.com or bitbucket.org they will show you the 2-3 commands you can paste and run via command-line to perform the same actions.

8. We reviewed the changes in the Remote repository to see they matched our local ones

9. We switched to TeamCity and selected the “Administration” link in the top right

10. I clicked the “+ Create Project” button to create the project our build configurations would live in

11. I clicked the “Create Build Configuration” and entered a name to start creating our “Continuous Integration” build configuration

12. I created a VCS setting by leaving the dropdown on “Guess from repository URL” and pasting in the same remote URL from the Bonobos git repository that we used locally and entering my username and password

Note: On a production system you would want to have separate credentials or use a private certificate for authentication. This is possible with TeamCity, I only left it out in the interest of time for the demo. You can also modify the time interval TeamCity will check for changes in the advanced settings for the VCS.

13. I then switched back to my local folder and added a “Tools” folder.

14. We reviewed the [CreateDatabaseUpdates.ps1][9] and the [ApplyDatabaseUpdates.ps1][10] powershell scripts

15. I copied these scripts into my tools folder, then used Smart git to Commit and Push those changes to the remote repository

16. Switching back to TeamCity build configuration, I selected the Build Steps menu and added a new Build Step for each script

<div style="margin: .5em 1em; padding: .5em; background-color: #eeeeee">
  <b>Create Batch Build Step</b></p> 
  
  <ul>
    <li>
      Runner Type: Powershell
    </li>
    <li>
      Step Name: Create Batch Script
    </li>
    <li>
      Execute Step: If all previous steps finished successfully
    </li>
    <li>
      Powershell Bitness: x64 – note: match this to the version of SQL Management Objects you installed on the server
    </li>
    <li>
      Error Output: error
    </li>
    <li>
      Working Directory: (leave it empty)
    </li>
    <li>
      Script: File
    </li>
    <li>
      Script file: Tools/CreateDatabaseUpdates.ps1
    </li>
    <li>
      Script execution mode: Execute *.ps1 with “-File” argument
    </li>
    <li>
      Script Arguments: -UpdatesFolder “Changes” -UpdatesFile “UpdatesBatch.sql”
    </li>
  </ul>
  
  <p>
    and Save
  </p>
</div>

<div style="margin: .5em 1em; padding: .5em; background-color: #eeeeee">
  <b>Apply Batch Build Step</b></p> 
  
  <ul>
    <li>
      Runner Type: Powershell
    </li>
    <li>
      Step Name: Apply Batch Script
    </li>
    <li>
      Execute Step: If all previous steps finished successfully
    </li>
    <li>
      Powershell Bitness: x64 – note: match this to the version of SQL Management Objects you installed on the server
    </li>
    <li>
      Error Output: error
    </li>
    <li>
      Working Directory: (leave it empty)
    </li>
    <li>
      Script: File
    </li>
    <li>
      Script file: Tools/ApplyDatabaseUpdates.ps1
    </li>
    <li>
      Script execution mode: Execute *.ps1 with “-File” argument
    </li>
    <li>
      Script Arguments: -UpdatesFile “UpdatesBatch.sql” -Server “PLC026-DB01” -Database “CI_DeployPresentation” -AdminUserName “USERNAME_HERE” -AdminPassword “PASSWORD_HERE”
    </li>
  </ul>
  
  <p>
    and Save
  </p>
</div>

Note: I also included a “-Trusted” option for the Apply script, if you specify this switch in the script arguments you can leave out the username/password and it will use a trusted connection instead.

17. I selected Triggers from the Build Configuration Settings menu and added a trigger of type “VCS trigger” (no options necessary). This ensured new changes that show up in the Version Control System would cause a new build to automatically trigger.

18. I selected Failure Conditions from the menu and checked the “an error message is logged by build runner” so that error messages from Powershell would cause the build to register as failed even if the script exited with a success exit code.

19. We went back to the top level (I click the logo as a shortcut) and pressed the run button for the build

20. When it was complete, we looked at the log (by clicking the success message) and when we expanded the steps we saw it had applied the “00000_DayOfWeekSalesReport” script

21. We reviewed the database and saw that the procedure from “00000_DayOfWeekSalesReport” was present, as well as the new UpdateTracking table

22. I edited the build configuration (“Edit Settings” from the dropdown next to the “Continuous Integration” build name on the dashboard)

23. In the “General” settings, I added UpdatesBatch.sql to the artifacts list as a file we wanted to keep after every build

24. We ran the build again from the dashboard, then reviewed the “UpdatesBatch.sql” file available from the Artifacts menu that showed up

25. I added two new scripts, [00001\_A\_SeparateUserTable_Schema.sql][11] and [00001\_B\_SeparateUserTable_Data.sql][12] to my local “Changes” folder and Commit and Pushed these

26. We ran the build and watched it fail, reading the error by clicking on it in the TeamCity dashboard

27. I corrected the aggregation error in the script locally and Commit and Pushed again

28. We ran the build again and verified it was successful

Achieved: At this point we had added version control and automated verification of changes to our process and talked about the benefit of it a bit

29. I returned to TeamCity and selected “Edit Settings” from the overall project so we could add a new production build configuration

30. I used the “+ Create Build Configuration” button to add a new Build Configuration, which I named “Apply to Production!!”

31. I chose the same VCS settings I had previously created in the Continuous Integration step

32. I jumped ahead to the Failure Conditions menu and checked the “an error message is logged by build runner”

33. I then selected the “Dependencies” menu option from the Build Configuration Stetings menu

34. I used the “+ Add new snapshot dependency” and selected my “Continuous Integration” step, to tie the Production step to run the same changes as the Continuous Integration step

35. I used the “+ Add new artifact dependency” button and selected the “Continuous Integration” step, selected to get artifacts from “Build from the same chain”, and typed in “UpdatesBatch.sql” so we could get the batch file that the corresponding CI build step had produced

36. I switched back to the Continuous Integration build configuration, went to Build Steps, and form the “More” menu for the “Apply” step, I chose to copy the step to the “Apply to Production” build configuration

37. I edited my new “Apply” build step in the “Apply to Production” build configuration and changed the target database in the powershell parameters from CI\_DeployPresentation to PROD\_DeployPresentation

38. We edited the “General” settings, added “UpdatesBatch,sql” to the artifacts and set the Version Number to the CI step's build number: %dep.DBDeployPres1_CiStep.build.number% (this may vary on your system depending on what you named your project and build configuration, type a % and look at the dropdown that appears)

39. We verified that the Production database still only had the original 7 tables

40. We ran the build in TeamCity, verified in the database that we had the new procedure, User Table, and UpdateTracking table

41. We looked at the build log and saw it had applied all the changes

Achieved: We reviewed the whole set of achievements at this point and then switched back to the slides briefly.

## Demo 2: Adding some checklist steps

We skipped this demo, but the purpose was to take that initial pipeline we had built and start adding in some of the checklist steps we need to perform before and after we run the change scripts.

This addition is going to add a MaintenanceMode table to the database and log before and after each attempted deployment. An application would then be able to look at the latest entry in this table and know whether we were online or in maintenance mode and deploying changes.

1. Copy the [SetMaintenanceMode.ps1][13] script into the local Tools folder, Copy the [00005_MaintenanceTable.sql][14] file into the “Changes” folder, Commit and Push them

2. Run the “Continuous Integration” and “Apply to Production” steps of the build to ensure the new table is in both systems

3. Edit the Settings for the “Continuous Integration” build configuration so we can add a new Build Step

4. Create a Build Step named “Start Maintenance Mode”

<div style="margin: .5em 1em; padding: .5em; background-color: #eeeeee">
  <b>Start Maintenance Mode Build Step</b></p> 
  
  <ul>
    <li>
      Runner Type: Powershell
    </li>
    <li>
      Step Name: Create Batch Script
    </li>
    <li>
      Execute Step: If all previous steps finished successfully
    </li>
    <li>
      Powershell Bitness: x64 – note: match this to the version of SQL Management Objects you installed on the server
    </li>
    <li>
      Error Output: error
    </li>
    <li>
      Working Directory: (leave it empty)
    </li>
    <li>
      Script: File
    </li>
    <li>
      Script file: Tools/SetMaintenanceMode.ps1
    </li>
    <li>
      Script execution mode: Execute *.ps1 with “-File” argument
    </li>
    <li>
      Script Arguments: -SetOffline -Notes “Applying Build %build.number%” -Server “PLC026-DB01” -Database “CI_DeployPresentation” -AdminUserName “USERNAME_HERE” -AdminPassword “PASSWORD_HERE”
    </li>
  </ul>
  
  <p>
    and Save
  </p>
</div>

5. Click the “Reorder Build Steps” button and drag this step above the “Apply” step so it runs before we apply the changes

6. Use the “More” button on the “Start Maintenance Mode” step to Copy the step (keep it in the same build Configuration)

7. Edit the copied step, changing the name to “End Maintenance Mode” and the script parameters to: -Notes “Done Build %build.number%” -Server “PLC026-DB01” -Database “CI\_DeployPresentation” -AdminUserName “USERNAME\_HERE” -AdminPassword “PASSWORD_HERE”

8. Also edit the “Execute Step” setting, selecting “Even if some of the previous steps failed” so that even if applying the script fails, we will still switch back out of maintenance mode. 

9. Save the build step and then use the “Reorder Build Steps” button and drag this new step to the last slot after the “Apply” step

10. Run the “Continuous Integration” build configuration form the dashboard

11. Look in the database and ensure that the new “MaintenanceMode” table has two new entries that correspond to the build you just ran (it will have the build number in the notes since we used that token in the scripts above)

12. Add a bad script, Commit and Push it, run CI, verify that we still had two maintenance mode entries even though the build failed

13. Open the “Continuous Integration” build configuration settings again, switch to the Build Steps, and use the “More” link on each of the Maintenance Mode rows to copy them over to the “Apply to Production” build configuration

14. Use the “Reorder” button to correct the order in the “Apply to Production” build

15. Edit each of the Maintenance Mode steps to switch from the CI\_DeployPresentation to the PROD\_DeployPresentation database

16. Run production and verify everything looks nice

Achieved: We have started converting our checklist of manual steps into something that will function automatically

## Demo 3: Nightly Restores

The last demo focused on the other side of our change deployment process, bringing fresh versions of the database down to test changes against (and co-incidentally testing our backups far more frequently then we do manually).

1. I added the [SqlRestore.ps1][15] script to my local “Tools” folder and Commit and Pushed it

2. I created a new Build Configuration in TeamCity named “Database Restore”

3. I attached the same VCS we used in the prior build configurations

4. I selected Triggers from the Build Configuration Settings menu and added a trigger of type “Schedule Trigger”, selected 2 AM, and unchecked the box “Trigger build only if there are pending changes” (wording differs depending on version of TeamCity – the intent is that we want to run every night even if there aren't new changes in VCS)

5. I added a Snapshot dependency just like the “Apply to Production” build configuration

6. I added an Artifact Dependency like the “Apply to Production” build configuration, but I chose to get Artifacts From “Last successful” build instead of the chain, as there may be a failing script in CI right now and we want to catch up with the last successful one that ran whether that was the most recent one or not

7. I selected Failure Conditions from the menu and checked the “an error message is logged by build runner” so that error messages from Powershell would cause the build to register as failed even if the script exited with a success exit code.

8. (optional) In the General settings, change the version number and add the batch script to Artifacts like we did in the “Apply to Production” build configuration

9. Add a Build Step named “Restore DB”

<div style="margin: .5em 1em; padding: .5em; background-color: #eeeeee">
  <b>Restore DB Build Step</b></p> 
  
  <ul>
    <li>
      Runner Type: Powershell
    </li>
    <li>
      Step Name: Create Batch Script
    </li>
    <li>
      Execute Step: If all previous steps finished successfully
    </li>
    <li>
      Powershell Bitness: x64 – note: match this to the version of SQL Management Objects you installed on the server
    </li>
    <li>
      Error Output: error
    </li>
    <li>
      Working Directory: (leave it empty)
    </li>
    <li>
      Script: File
    </li>
    <li>
      Script file: Tools/SqlRestore.ps1
    </li>
    <li>
      Script execution mode: Execute *.ps1 with “-File” argument
    </li>
    <li>
      Script Arguments: -Server “PLC026-DB01” -Database “CI_DeployPresentation” -AdminUserName “USERNAME_HERE” -AdminPassword “PASSWORD_HERE” -RemoteBackupsDir “\\PLC026-DB01\Backups” -LocalBackupsDir “C:\Database\Backups” -DbFileBasePath “C:\Database\DATA” -SourceDbBaseFileName “MvcMusicStoreGen”
    </li>
  </ul>
  
  <p>
    and Save
  </p>
</div>

Note: The “SourceDbBaseFileName” is actually the logical name that I will use in the Restore SQL script to rename the database and log file, I didn't rename this in my backups and the script parameter could have been named better.

8. Copy the “Apply DB Changes” from the “Continuous Integration” build configuration

9. We then disabled the “Apply” step and ran this once without it to verify that it restored the CI database back to where we started the session

10. I enabled the “Apply” step and re-ran the build and verified we were now matching the last successful CI UpdatesBatch.sql

Achieved: We now can freshen our CI database regularly and on demand. We have nightly verification of part of our DR plan (restores) and know that every single backups is in working condition (or receive an email the first time one fails). We also have the basis for producing sanitized versions of the database if needed.

## Thanks for a great session

I had a lot of fun with this session, even if it did go waaaaay over and we had to cut out the last slides. Hopefully you attended and are now taking all these notes and playing with some or all of these pieces on your own. Let me know if you run into any issues.

 [1]: https://github.com/tarwn/AutomatedDBDeploymentTalk/blob/master/BASE_DeployPresentation.bak
 [2]: http://bonobogitserver.com/
 [3]: http://www.jetbrains.com/teamcity/
 [4]: http://www.maxtblog.com/2012/09/create-powershell-smo-scripts-without-installing-sql-server/ "Create powershell SMO scripts without installing SQL Server"
 [5]: http://git-scm.com/
 [6]: http://www.syntevo.com/smartgit/
 [7]: https://github.com/tarwn/AutomatedDBDeploymentTalk
 [8]: https://github.com/tarwn/AutomatedDBDeploymentTalk/blob/master/Demo1/00000_DayOfWeekSalesReport.sql
 [9]: https://github.com/tarwn/AutomatedDBDeploymentTalk/blob/master/Demo1/CreateDatabaseUpdates.ps1
 [10]: https://github.com/tarwn/AutomatedDBDeploymentTalk/blob/master/Demo1/ApplyDatabaseUpdates.ps1
 [11]: https://github.com/tarwn/AutomatedDBDeploymentTalk/blob/master/Demo1/00001_A_SeparateUserTable_Schema.sql
 [12]: https://github.com/tarwn/AutomatedDBDeploymentTalk/blob/master/Demo1/00001_B_SeparateUserTable_Data.sql
 [13]: https://github.com/tarwn/AutomatedDBDeploymentTalk/blob/master/Demo2/SetMaintenanceMode.ps1
 [14]: https://github.com/tarwn/AutomatedDBDeploymentTalk/blob/master/Demo2/00005_MaintenanceTable.sql
 [15]: https://github.com/tarwn/AutomatedDBDeploymentTalk/blob/master/Demo3/SqlRestore.ps1