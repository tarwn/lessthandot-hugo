---
title: Using T-SQL OUTPUT and MERGE To Link Old and New Keys
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-09-13T11:44:00+00:00
ID: 1312
excerpt: "You don't catch me doing SQL posts that often, probably because I spend far more time working in a development world that seems to be moving further and further away from SQL. That being said, the database server is optimized towards handling large sets of data and data manipulations, so rather than second-guessing developers that know far more (and have far larger budgets), I like to take advantage of database-side solutions when I can. The time saved not dragging data back and forth on the network is an extra bonus."
url: /index.php/datamgmt/datadesign/using-t-sql-output-and-merge/
views:
  - 14983
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
tags:
  - merge
  - output
  - t-sql

---
You don't catch me doing SQL posts that often, probably because I spend far more time working in a development world that seems to be moving further and further away from SQL. That being said, the database server is optimized towards handling large sets of data and data manipulations, so rather than second-guessing developers that know far more (and have far larger budgets), I like to take advantage of database-side solutions when I can. The time saved not dragging data back and forth on the network is an extra bonus.

Recently I needed the ability to transfer records (and control of the data) between two data structures on demand. The original data structure has grown up over the course of 10+ years as part of a larger system, while the newer, purpose-built data structures exist specifically to support this data and support newer capabilities. The tricky part is that the end user gets to pick and chose which records will be managed by the newer system and which will not, on demand.

## Sample Table Schemas

To get us started, I've created some simplified, substitute table for the post. Using simpler tables that are far fewer in count will let us focus more on the problem at hand and less on my data definition skills.

```sql
use SampleStuff;
Go

-- "Original" tables with columns for new keys

CREATE TABLE dbo.Orig_User(
	Orig_User_Key	int IDENTITY(1,1) NOT NULL,
	Username		varchar(20) NOT NULL,
	New_User_Key	int NULL
);

CREATE TABLE dbo.Orig_Work_Phone(
	Orig_Work_Phone_Key int IDENTITY(1,1) NOT NULL,
	Orig_User_Key		int NOT NULL,
	Phone				varchar(20),
	New_Phone_Key		int NULL
);

CREATE TABLE dbo.Orig_Home_Phone(
	Orig_Home_Phone_Key int IDENTITY(1,1) NOT NULL,
	Orig_User_Key		int NOT NULL,
	Phone				varchar(20),
	New_Phone_Key		int NULL	
);

-- And the "New" tables
CREATE TABLE dbo.New_User(
	New_User_Key	int IDENTITY(1,1) NOT NULL,
	Username		varchar(20) NOT NULL
)

CREATE TABLE dbo.New_Phone(
	New_Phone_Key	int IDENTITY(1,1) NOT NULL,
	New_User_Key	int NOT NULL,
	Phone			varchar(20) NOT NULL,
	Is_Work			bit NOT NULL
);
```
_Note: For the sake of shorter examples, I'm going to provide examples on the User tables. We don't actually need anything fancier than SCOPE_IDENTITY() until we get to the step where we transfer Phone records since we are doing one user at a time, but that's why examples are so much easier than the real world._

The tricky part of this transform is the ownership of the data. Due to some internal restrictions, the original data structure is going to be the master. This means the original record is responsible for holding both its own key and the key in the newer system. The transform needs to get the record from the original system, push it into the newer system (generating the new key), then update the new key back into the original record.

If this was a one-shot ETL load, I would probably add a temporary column to the target tables to track the source keys, then update the new keys back into the source table and drop the temporary column. As you can imagine, this is an even less graceful solution when we put control into the users hands and operate at the individual record level (c'mon, it's only a few hundred thousand table locks during peak hours).

## The OUTPUT Statement

Added in SQL Server 2005 (6 years ago already?), OUTPUT can be used to return data from inside INSERT, UPDATE, DELETE, and MERGE statements. This solves the first part, giving us access to the new key when it's generated. Well, almost.

```sql
CREATE TABLE #ID_Transfer(new_key int, old_key int);

INSERT INTO dbo.New_User(Username)
OUTPUT Inserted.New_User_Key, ??? INTO #ID_Transfer
SELECT OU.Username FROM dbo.Orig_User OU
WHERE OU.Orig_User_Key = @TargetUserKey;
```
While we can easily create a temporary table to capture the resulting New\_User\_Key that is generated on insert, we don't have access to the table the data is coming from, so we don't have the original key to populate next to that new key.

Which brings us to MERGE.

## The MERGE Statement

MERGE was added in SQL 2008 and allows us to define a source dataset, target data set, and rules to insert or update data from that source to the target. While we don't really need the rules and update capability, what it also provides is simultaneous access to both datasets AND support for OUTPUT. Aha, I hear you saying.

By forcing the merge to always perform an INSERT, we will satisfy our needs for inserting the data into the “New” tables as well as have the capability to capture both the original and new key in a single OUTPUT statement.

```sql
CREATE TABLE #ID_Transfer(new_key int, old_key int);

MERGE INTO dbo.New_User AS [Target]
USING (SELECT OU.Orig_User_Key, OU.Username FROM dbo.Orig_User OU WHERE OU.Orig_User_Key = @TargetUserKey) AS Source
ON 1 = 0
WHEN NOT MATCHED THEN
INSERT (Username)
VALUES(source.Username)
OUTPUT INSERTED.New_User_Key, source.Orig_USer_Key INTO #ID_Transfer;
```


## Full Roundtrip

With the critical pieces out of the way, it's easy now to create the full round-trip update.

```sql
CREATE TABLE #ID_Transfer(new_key int, old_key int);

DECLARE @TargetUserKey int;
SELECT @TargetUserKey = 1;

MERGE INTO dbo.New_User AS [Target]
USING (SELECT OU.Orig_User_Key, OU.Username FROM dbo.Orig_User OU WHERE OU.Orig_User_Key = @TargetUserKey) AS Source
ON 1 = 0
WHEN NOT MATCHED THEN
INSERT (Username)
VALUES(source.Username)
OUTPUT INSERTED.New_User_Key, source.Orig_USer_Key INTO #ID_Transfer;

-- and back-update original record with new key
UPDATE dbo.Orig_User
SET New_User_Key = IT.new_key
FROM dbo.Orig_User OU
	INNER JOIN #ID_Transfer IT ON IT.old_key = OU.Orig_User_Key;

DROP TABLE #ID_Transfer;
```
And we can see the updated values like so:

```sql
SELECT TOP 20 *
FROM dbo.Orig_User O
LEFT JOIN dbo.New_User N ON N.New_User_Key = O.New_User_Key
```
So that was the first step, next is the phone numbers.

## Homework/Practice

Given the sample tables above, it would now be fairly straightforward to apply this idea to the phone number tables, populating the data from the original two into the single new table and back-populating the new keys back into the original records. Rather than give you the final script, I'll instead give you the pieces needed to get this far. 

Setup script:

```sql
use SampleStuff;
Go

/*
DROP TABLE dbo.New_Phone;
DROP TABLE dbo.New_User;
DROP TABLE dbo.Orig_Home_Phone;
DROP TABLE dbo.Orig_Work_Phone;
DROP TABLE dbo.Orig_User;
DROP TABLE dbo.Number;
*/

CREATE TABLE dbo.Orig_User(
	Orig_User_Key	int IDENTITY(1,1) NOT NULL,
	Username		varchar(20) NOT NULL,
	New_User_Key	int NULL
);

CREATE TABLE dbo.Orig_Work_Phone(
	Orig_Work_Phone_Key int IDENTITY(1,1) NOT NULL,
	Orig_User_Key		int NOT NULL,
	Phone				varchar(20),
	New_Phone_Key		int NULL
);

CREATE TABLE dbo.Orig_Home_Phone(
	Orig_Home_Phone_Key int IDENTITY(1,1) NOT NULL,
	Orig_User_Key		int NOT NULL,
	Phone				varchar(20),
	New_Phone_Key		int NULL	
);


CREATE TABLE dbo.New_User(
	New_User_Key	int IDENTITY(1,1) NOT NULL,
	Username		varchar(20) NOT NULL
)

CREATE TABLE dbo.New_Phone(
	New_Phone_Key	int IDENTITY(1,1) NOT NULL,
	New_User_Key	int NOT NULL,
	Phone			varchar(20) NOT NULL,
	Is_Work			bit NOT NULL
);
Go

CREATE TABLE dbo.Number(num int IDENTITY(1,1) NOT NULL);
GO

SET NOCOUNT ON;

INSERT dbo.Number DEFAULT VALUES ;
WHILE SCOPE_IDENTITY() < 500
    INSERT dbo.Number DEFAULT VALUES ;
    
INSERT INTO dbo.Orig_User(Username)
SELECT 'User #' + CAST(num as varchar)
FROM dbo.Number;

INSERT INTO dbo.Orig_Work_Phone(Orig_User_Key, Phone)
SELECT Orig_User_Key, 'Phone ' + CAST(Orig_User_Key as varchar)
FROM dbo.Orig_User
WHERE Orig_User_Key % 2 = 0;

INSERT INTO dbo.Orig_Work_Phone(Orig_User_Key, Phone)
SELECT Orig_User_Key, 'Phone ' + CAST(Orig_User_Key as varchar)
FROM dbo.Orig_User
WHERE Orig_User_Key % 3 = 0;

INSERT INTO dbo.Orig_Home_Phone(Orig_User_Key, Phone)
SELECT Orig_User_Key, 'Phone ' + CAST(Orig_User_Key as varchar)
FROM dbo.Orig_User
WHERE Orig_User_Key % 3 = 0;

INSERT INTO dbo.Orig_Home_Phone(Orig_User_Key, Phone)
SELECT Orig_User_Key, 'Phone ' + CAST(Orig_User_Key as varchar)
FROM dbo.Orig_User
WHERE Orig_User_Key % 4 = 0;
```
Given those pieces above and some additional scripts, finishing the script should only take 5-10 minutes. Consider it a free practice problem 🙂