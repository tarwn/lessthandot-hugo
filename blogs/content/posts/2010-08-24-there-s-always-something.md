---
title: There’s Always Something
author: David Forck (thirster42)
type: post
date: 2010-08-24T12:50:16+00:00
ID: 884
url: /index.php/webdev/serverprogramming/there-s-always-something/
views:
  - 2959
rp4wp_auto_linked:
  - 1
categories:
  - Server Programming

---
It&#8217;s always amazing to go back and look at code that you wrote just over a year ago. As I&#8217;m sure it common with most people, I was surprised, and a little embarassed, at how bad some of my code was back then. It&#8217;s amazing how much you learn and grow in a year, especially when your as relatively new to the industry as I am. So let me show you what I found that made me reflect on the last year.

I recently got a request to make a rehash of a report I did a year ago, except that the drill down order is different. Cool. Copy, pase, drag, change a couple things, done. I deployed the new report to the dev server and off it goes. I then decided to go ahead and look at the stored procedure running the report to see exactly what it was doing, since I haven&#8217;t looked at it in about a year. Wow, I was a bit surprised when I opened it. Here&#8217;s what I found.

sql
ALTER PROCEDURE [rp].[CompanyMoodList] 
AS
BEGIN
	SET NOCOUNT ON;

declare @maxdate table (WASSN_ID int, CreationDate datetime primary key (WASSN_ID))

declare @projects table 
(
	WASSN_ID int,
	WPROJ_ID int,
	TaskName varchar(50),
	BusinessUnit varchar(10),
	Description varchar(max),
	Director varchar(50),
	OTAG bit,
	OTAB bit,
	Priority int,
	CreationDate datetime,
	Light varchar(5),
	CompanyName nvarchar(50)
)


insert into @maxdate (WASSN_ID, CreationDate)
select distinct case when WASSN_ID is null then '' else WASSN_ID end as WASSN_ID, max(CreationDate) as CreationDate
from dbo.tblENTERPRISE_ISSUES
group by WASSN_ID
order by WASSN_ID, CreationDate

insert into @projects
(
	WASSN_ID,
	WPROJ_ID,
	TaskName,
	BusinessUnit,
	Description,
	Director,
	OTAG,
	OTAB,
	Priority,
	CreationDate,
	Light,
	CompanyName
)
select 
	a.WASSN_ID, 
	c.WPROJ_ID, 
	replace(c.TASK_NAME,'.Published','') as TASK_NAME, 
	d.prBusinessUnit, 
	a.Description, 
	a.Director, 
	a.OTAG, 
	a.OTAB, 
	a.Priority, 
	a.CreationDate, 
	case a.Priority when 3 then 'G' when 2 then 'Y' when 1 then 'R' else 'N' end as Light,
	d.prCompanyName
from dbo.tblENTERPRISE_ISSUES a
	inner join @maxdate b
		on a.WASSN_ID=b.WASSN_ID and a.CreationDate=b.CreationDate
	inner join appdb.DataWarehouse.dw.MSP_WEB_ASSIGNMENTS c
		on a.WASSN_ID=c.WASSN_ID
	inner join appdb.DataWarehouse.cube.Projects d
		on c.WPROJ_ID=d.PK_prID
where prProjectStatus='Active'

select * from @projects
where Light<>'N'
order by CompanyName, BusinessUnit, TaskName





END
```
I decided to clean that up somewhat, since i definately don&#8217;t need those temp tables. Here&#8217;s what I ended up with (note that this is just in a query window on the prod server, I&#8217;m not actually modifying live code, that&#8217;s a No-No!)

sql
select 
	a.WASSN_ID, 
	c.WPROJ_ID, 
	replace(c.TASK_NAME,'.Published','') as TaskName, 
	d.prBusinessUnit as BusinessUnit, 
	a.Description, 
	a.Director, 
	a.OTAG, 
	a.OTAB, 
	a.Priority, 
	a.CreationDate, 
	case a.Priority when 3 then 'G' when 2 then 'Y' when 1 then 'R' else 'N' end as Light,
	d.prCompanyName as CompanyName
from dbo.tblENTERPRISE_ISSUES a
	inner join 
	(
		select
			coalesce(WASSN_ID,'') as WASSN_ID,
			MAX(CreationDate) as CreationDate
		from dbo.tblENTERPRISE_ISSUES
		group by WASSN_ID
	) b
		on a.WASSN_ID=b.WASSN_ID and a.CreationDate=b.CreationDate
	inner join appdb.DataWarehouse.dw.MSP_WEB_ASSIGNMENTS c
		on a.WASSN_ID=c.WASSN_ID
	inner join appdb.DataWarehouse.cube.PWAProjects d
		on c.WPROJ_ID=d.prWPROJ_ID
where prProjectStatus='Active'
	and a.Priority>=1
```
So I hit execute, and it works fine, except that it&#8217;s now taking 6 seconds to execute, instead of 1 second like the previous version. I thought to myself &#8220;WTF?! This is better! This should be even faster!&#8221; I looked at the execution plan and see some clustered index scans that are pretty high, so I make a couple of indexes. No dice. 

Now I&#8217;m thinking that there must be something in the new query that I&#8217;m over looking. It then struck me that the main thing I changed was the subquery for the max date. I then take the coalesce statement out of the subquery because there&#8217;s no nulls in that column (I had just carried it over from the last one). I then rerun the query and voila! It ran in about 1 second with the exact same dataset as before.

I now understand what was happening, or at least sort of. Before the subquery could do the group by on the wassnid sql server had to go and check if all the wasnid&#8217;s were null, and if so change them to &#8221;. Removing the coalesce removed that extra step.

So now I&#8217;ve got some updated code and I learned something new (or learned something to keep in mind in sub-queries). I&#8217;d call today a good day!