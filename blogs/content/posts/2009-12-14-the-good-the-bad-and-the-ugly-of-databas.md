---
title: The Good, the Bad and the Ugly of Database Design
author: Ted Krueger (onpnt)
type: post
date: 2009-12-14T13:25:14+00:00
ID: 651
url: /index.php/datamgmt/datadesign/the-good-the-bad-and-the-ugly-of-databas/
views:
  - 27015
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
## _It really can get bad..._

Recently I had the pleasure of working on a new database introduced to the environment. This database brought with it the need for some critical reports to be developed in order to properly allow the new business plan to flow. Typically any database and software piece added to the business flow will cause the same two tasks to be deemed critical to the success of the implementation of the system. In these tasks, reporting will force you as either a DBA or DB Developer to become intimate with the database and how it is storing data. During that process of getting to know other database designs and storage engines all together, you will find bad and good practices. In my career I have seen the ugly and then the really ugly but I found on this particular implementation it could get even uglier.

## _Deal with it?_

The sad truth to the matter is, third party injections into our stable environments is something you cannot prevent. Business entities buy software by the payload and will never stop doing that. It's important to understand these entities are simply doing their part to make the business run better and make money in the long run. Of course this all means we end up dealing with the good, the bad and the ugly of software and database design.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//goodbadugly.gif" alt="" title="" width="364" height="463" />
</div>

So now that we all accept the fact we have no choice of dealing with these issues we can start to write about them. This give hope that we can help the database community and prevent the recreation of the practices. My own hopes from what I found in this one database are it will never cross your mind as a good idea. This design flaw truly was beyond ugly and bordering just under the hammer of the gun on bad.

## _The design uncovered..._

After hours of searching the database to find critical data for reporting I realized there was little I could accomplish without contacting the vendor with my questions. The reply I received was nothing short of jaw dropping to where the data I needed really was being stored.

It's important to note that this database sat on one of the top 3 enterprise database servers out there on the market. The database server is respected by any database professional at being able to handle enterprise level needs without question. In this case that only proved to help the situation by helping hide horror hiding under the covers. 

So the data I was after was related to customer billing, item long descriptions, creation date and customer number itself. These should be few key data mining steps that should take minutes to determine locations and relations in any database. I'm going to limit this blog to those key columns but the design flowed into the entire database and tables. I took the time to populate my own fake table with data to show the issues.

Let's take a look at my create table statement

```sql
CREATE TABLE FINDME
(
KEYS Char(500)
,STRINGS Char(3000)
,TXT1 Char(150)
,TXT2 Char(100)
,DATE1 DATETIME
,DATE2 DATETIME
,DATE3 DATETIME
,DATE4 DATETIME
,DATE5 DATETIME
)

INSERT INTO FINDME 
VALUES(
		'9999999933333ABC94ORT               9432          CUST5     2SOP'
		,'MY CUSTOMER 5  ADDRESS1 NYYNTRUE    54324532'
		,'THIS IS MY PRODUCT DESCRIPTION THAT IS REALLY LONG'
		,' SO I NEED LOTS OF COLUMNS'
		,GETDATE()
		,GETDATE()-1
		,GETDATE()-10
		,GETDATE()+1
		,GETDATE()-2
	  )


SELECT * FROM FINDME
```

We can already see that the first issue is the CHAR data types. That isn't really the disturbing part though. Well, CHAR does scare me when I see it but let's move beyond that for now. The problem is, you can immediately see the storage of dozens of unrelated and unique chunks of data held within the same columns. My career started off in development so I'm no stranger to using fixed field strings in my code. It's a great way to pass parameters between logic in order to make passing those objects simplified and quickly maintained with a brief key so others know the fixed format and can extract the data as needed. Now put that into a database and we have a serious problem. There is no means to even the first form of normalization and you start to manipulate data not in result sets but case steps that cause performance issues. 

Notice the CUST5 string. This CUST5 related back to CUSTOMER which holds other customer data. How are you going to join these tables? Given a normal primary to foreign relationship you would compose a query such as

```sql
SELECT
	a.CUSTID
	,b.CUSTID
FROM
FINDME a
JOIN CUSTOMER b ON a.CUSTID = b.CUSTID
```

Let's do it with the data as it is in our FINDME table now though. We'll assume for this exercise that CUSTID is actually stored in its own column in CUSTOMER

```sql
SELECT
	SUBSTRING(a.KEYS,51,5)
	,b.CUSTID
FROM
FINDME a
JOIN CUSTOMER b ON SUBSTRING(a.KEYS,51,5) = b.CUSTID
```

First query will obtain index seeks given the supporting indexes and even on high count tables will perform in milliseconds for you. Second has no hope whatsoever of being a query that will perform well. It is nonsargable, gives no patter for proper indexing and storage will become an issue across the table. So you are already struggling to just get your data out of the database.

Second problem is the storage of the description in multiple fields. By nature concatenation is slow. For that matter, any string manipulation in any development plan is slower than not. In order to get your long description out of this table you will be forced into the following

```sql
SELECT 
	TXT1 + TXT2
FROM 
FINDME
```

But wait, how do we know there is a leading space to form this string correctly? You don't and you'll have to test for it if you are a good developer.

Now there is a need to find the create date. So which is it? OK, yes. An ERD would give you the answer to that question and any good software company (Hint if that's you) will provide the diagrams for you. What happens if the ERD is not available at audit time and the company you purchased this from is belly up? You are forced to start testing in the application to figure what data is going where. You will be entering data while watching profiler and viewing the tables to see the value you need. This will give you the best guess assessment of how to write your query. Who reading this has made those, "best guess" choices? I have and know they never work so please, don't use those unless you fall into just that predicament. 

## _Conclusion? Not really..._

I only went over a few issues so far just with that one table. If you have designed databases before I'm sure you see about a dozen issues. I want to save the others for some follow up blogs as they are much bigger issues and showing the affects they have on the database server should be keyed in on. So by no means is this a conclusion on the "bad design" issues just found in this one instance. I do hope that from this and future blogs to come from me, that you don't make these mistakes in your own designs.