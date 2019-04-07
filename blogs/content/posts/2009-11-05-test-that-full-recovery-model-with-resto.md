---
title: Test your Full Recovery model by restoring backups
author: Ted Krueger (onpnt)
type: post
date: 2009-11-05T12:29:30+00:00
ID: 611
url: /index.php/datamgmt/datadesign/test-that-full-recovery-model-with-resto/
views:
  - 7257
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
A major point of failure in a full recovery model is not testing your backups. You should not limit this to your FULL backups either. If you have full recovery model on any of your databases there is more than likely (or should be) log backups being run based on either DR strategies or transaction log management. If you also have differential backups to take full advantage of the quickest path to restore, then all of these stages must be tested out.

### _**Key points**_

  1. Test restoring full backups
  2. Test applying differential and transaction log backups
  3. Check header information on all backups. To do this you can submit to a table for historic purposes the results from RESTORE WITH HEADERONLY, &#8220;RESTORE HEADERONLY FROM DISK = BackupFile.bak'&#8221;
  4. Push testing further than one time restore. Automate your RESTORE tests either daily, weekly or what your resources permit. Remember, just because you go on vacation does not mean your methods change and your duties go away.
  5. Document the results and let management know of those results with either simple reporting results of utilizing SSRS for subscription automation.

Testing your backups will expose problems that will save you when your backup strategies are put to the test in a real life disaster that your normal DR strategy fails to recover from. The last thing any DBA wants is to tell them the restore doesn’t work. As DBAs, securing the data is the number one priority in your list of duties and responsibilities. Backups are the fundamental method for doing just that.

Remember, if you don&#8217;t test your backups regularly then you will end up testing them when their failure could hurt you the most.