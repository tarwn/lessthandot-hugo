---
title: Database Mail Won't Deliver to Distribution Group
author: David Forck (thirster42)
type: post
date: 2010-05-07T16:30:50+00:00
ID: 783
url: /index.php/datamgmt/dbadmin/database-mail-won-t-deliver-to-distribut/
views:
  - 15125
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
So today I was working on setting up DB Mail on our new dev box (MS SQL 2008), but I ran into a little snag. Emails were sending fine to my email address, but all attempts to email our developers distribution group failed to be delivered. These was no sign on SQL Server that the email wasn't delivered, it just didn't appear in my inbox.

I got to talking with my network admin, and he said that unauthenticated emails are blocked on distributions groups in the company (spam reasons). This got me to thinking. I had set up the profile on the server to use anonymous authentication! 

![old profile settings][1]

I changed the settings to use the Database Engine Service Credential to authenticate the email. Still no dice.

![new profile settings][2]

I talked to the admin again, and he had an aha! moment. Apparently in our company distribution groups are set so that they only receive email from anyone in the all-company distribution group. He changed the settings to be From authenticated users only and From everyone. 

![new distribution group settings][3]

It worked! And he tested by sending an external email to the distribution group and the email was blocked. Success! Now I can email my distribution group internally and external emails are still blocked.

 [1]: /wp-content/uploads/blogs/DataMgmt/thirster42/DBMail/ProfileOld.JPG
 [2]: /wp-content/uploads/blogs/DataMgmt/thirster42/DBMail/ProfileNew.JPG
 [3]: /wp-content/uploads/blogs/DataMgmt/thirster42/DBMail/developers.JPG