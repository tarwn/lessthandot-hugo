---
title: What makes a successful SQL Server Reporting Services implementation?
author: Ted Krueger (onpnt)
type: post
date: 2009-04-20T20:18:54+00:00
ID: 392
url: /index.php/datamgmt/datadesign/what-makes-a-good-sql-server-reporting-s/
views:
  - 5763
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server Admin

---
End of the day and I thought I'd offer this up to read and think about.

What makes a good SQL Server Reporting Services implementation?

I think to answer this you have to ask yourself what would make a bad implementation. First and foremost, if the report rendering process is slow, then it has failed. Second, can you gain access securely and without compromising the internal and external systems? Last, can the implementation allow for rapid report generation in order to supply the company with reporting abilities required to function smoothly?

Once you ask these questions you can identify issues that must be avoided.

Report rendering performance:

As much as possible, all data should be located with the SSRS installation. This is a hard task to meet but if obtained, the trips once taken off the server to other data stores will increase performance by taking network latency out of the picture. Most reports are going to generate a large return of data. Make sure you don't allow this to hurt your performance. That does not remove the server to client aspect but limits the latency to that alone. Security is a problem once you create your data storage for reporting. Are you compromising data that is sensitive? Working in the pharmaceutical area this may be more of a critical step in the process to take. It should always be considered though. Next, what are the clients using to access your report server? Is your client environment controlled so you know what to develop for?

Security:

Not only should security be important to the data you may replicate, snapshot, ETL, log ship or whatever you so choose to use to build your warehouse, but it is even more critical to the other database servers that you have no choice but to access remotely. In no way should you install SSRS on your production OLTP driven database servers. So that means if you are running your ERP solution on one database server and the SSRS instance is rendering across the LAN, you have to access it either through linked servers, data extracts or some other form of collection. This connection is critical to secure. If I had a dollar for every time I saw "sa" in a linked server entry I could have retired months before writing this.

Rapid Development of Report:

OK, you've gone through months of planning and implementing the solution. You've composed hardware that will outperform even the best of your other production database servers. But have you? Once you design your hardware, data stores for reporting on, security (user, roles and schemas), take a minute and go to the next step. Create a report off your new solution. While you are thinking about the report creation steps ask yourself

  * Was creating the procedures to get my data a hardship? Was it secure and was I allowed to do only what I needed?
  * Was the data even there?
  * What do I do with my report now? How do I present it to the users?
  * Should I de-normalize? If you have to write the same fifteen join statement, union all to the same thing for history, then you really haven't created a reporting solution have you? Well you did, but it sucks. It may suck but at least you're not causing blocking on the production server, right? Not really. Take the time to work on it. It will pay off.

Ending note, please configure your disk for OLAP services. Disk costs you a lot. Also, don't take SQL Server Reporting Service for granted as a childish reporting toy. It has more power than you think and it will bite back if you use it incorrectly. The SQL Server group has given us plenty of control over how SSRS will function and how you need it to function for your particular installation. This goes hand-in-hand with configuring SQL Server itself. Remember, there are databases that need to be maintained for SSRS. Backup, maintain objects and keep your reporting available so you can work on your next big plan to take over the world.