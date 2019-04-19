---
title: Raise your hand if you have seen code that sends email from within a trigger in SQL Server
author: SQLDenis
type: post
date: 2011-05-11T11:19:00+00:00
ID: 1170
excerpt: |
  Please leave me a comment if you have written or have seen a trigger that is written in such a way that it will send an email when a value changes in a table.
  
  I am looking at the following question: Email trigger when data is changed
  
  ALTER TRIGGER&hellip;
url: /index.php/datamgmt/datadesign/raise-your-hand-if-you/
views:
  - 8367
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - mail
  - pitfalls
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - triggers

---
Please leave me a comment if you have written or have seen a trigger that is written in such a way that it will send an email when a value changes in a table.

I am looking at the following question: [Email trigger when data is changed][1]

```sql
ALTER TRIGGER [dbo].[RVABestellingenAantalWijzigenTrigger]
ON [dbo].[RVA_Bestellingen]  
AFTER UPDATE
AS
--Vars
DECLARE @body varchar(500)
DECLARE @BestellingID int
DECLARE @CategorieID int
DECLARE @SubCategorieID int
DECLARE @AantalOrigineel int
DECLARE @AantalNieuw int
DECLARE @LocatieNaam varchar(255)
DECLARE @ComponentNaam varchar(255)
DECLARE @CategorieNaam varchar(255)
DECLARE @SubCategorieNaam varchar(255)
DECLARE @Datum datetime

if update(Aantal) /*and (SELECT Datum FROM inserted) = cast(floor(cast(dateadd(day,1,getdate()) as float)) as datetime)  */ and   (convert(varchar,getdate(),108)>'11:00')
begin

    --Zetten aantallen
    SET @AantalOrigineel            = (SELECT Aantal FROM deleted)
    SET @AantalNieuw                = (SELECT Aantal FROM inserted) 
    SET @BestellingID               = (SELECT BestellingID FROM inserted)
    SET @CategorieID                = (SELECT CategorieID FROM inserted)    
    SET @SubCategorieID             = (SELECT SubCategorieID FROM inserted) 

    --Zetten locatienaam en componentnaam
    SELECT @LocatieNaam = ('RVA Aanpassingen Locatie: '+LocatieNaam), @ComponentNaam=OfficieleNaam, @Datum=Datum
    FROM RVA_Bestellingen r
    LEFT OUTER JOIN Locaties l on l.LocatieID = r.LocatieID
    LEFT OUTER JOIN Componenten c on c.ComponentID = r.ComponentID
    WHERE r.BestellingID = @BestellingID    


    SELECT @CategorieNaam = Categorie
    FROM RVA_HoofdCategorie
    WHERE HoofdCategorieID = @CategorieID   

    SELECT @SubCategorieNaam = Categorie
    FROM dbo.RVA_SubCategorie
    WHERE SubCategorieID = @SubCategorieID      

    --Zet boyd
    SET @body = (
                    SELECT 
                        'HoofdCategorie: ' + @CategorieNaam+ char(10)+char(13)
                        +'SubCategorie: ' + @SubCategorieNaam+ char(10)+char(13)
                        + 'Componentnaam: '
                        + @ComponentNaam + char(10)+char(13)
                        + 'Origineel aantal: '
                        + CAST(@AantalOrigineel as varchar(50) ) + char(10)+char(13)
                        + 'Nieuw aantal: '
                        + CAST(@AantalNieuw as varchar(50) ) + char(10)+char(13)
                        + 'Leverdatum: ' +
                        + convert(varchar(50),@Datum,105)                       
                )

    --Mailen naar Adeline
     EXEC master..xp_sendmail 
            @recipients = 'fake@fake.fake', 
            @message    = @body, 
            @subject    = @LocatieNaam
end
```

And I am just shaking my head, for one it doesn't take into account muliple rows being updated, see [Best Practice: Coding SQL Server triggers for multi-row operations][2] for more on that topic

The second bad thing is of course that you want your trigger to complete as fast as possible, you don't want it to email a bunch of people. What if the email blows up?
  
Ideally you would write a query inside the trigger that dumps the desired results into another table, then you would have a job that checks that table every minute or so and does the emailing.

Now I admit, I have written a trigger in the past that emailed when something got inserted, this was a table that would interact with Great <del>Pains</del> Plains. Once we decided to insert nine thousand rows as a stress test from a batch, and yes we brought down the whole exchange server, the email went out to I believe 20 people, this was the time of 3 MB inboxes 🙂

Lastly this person is on SQL Server 2008 and is still using xp\_sendmail and not sp\_send_dbmail

Enough ranting, leave me a comment if you do send emails from withing triggers. If you do so, did you ever have any problems with it?

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://stackoverflow.com/questions/5964495/email-trigger-when-data-is-changed
 [2]: /index.php/DataMgmt/DBProgramming/MSSQLServer/best-practice-coding-sql-server-triggers
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22