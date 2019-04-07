---
title: How Does Between Work With Dates In SQL Server?
author: SQLDenis
type: post
date: 2008-08-01T12:35:14+00:00
ID: 98
url: /index.php/datamgmt/datadesign/how-does-between-work-with-dates-in-sql/
views:
  - 14833
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Do you use between to return data that has dates? Do you know that between will get everything since midnight from the first criteria and up to midnight exactly from the second criteria. If you do BETWEEN 2006-10-01 AND 2006-10-02 then all the values that are greater or equal than 2006-10-01 and less or equal to 2006-10-02 will be returned. So no values after 2006-10-02 midnight will be returned. 

Let&#8217;s test this out, first let&#8217;s create this table 

```sql
CREATE TABLE SomeDates (DateColumn DATETIME)
```

Insert 2 values 

```sql
INSERT INTO SomeDates VALUES('2006-10-02 00:00:00.000') 
INSERT INTO SomeDates VALUES('2006-10-01 00:00:00.000')
```

Return everything between &#8216;2006-10-01&#8217; and &#8216;2006-10-02&#8217; 

```sql
SELECT * 
FROM SomeDates 
WHERE DateColumn BETWEEN '20061001' AND '20061002' 
ORDER BY DateColumn 
```

This works without a problem 

Let&#8217;s add some more dates including the time portion 

```sql
INSERT INTO SomeDates VALUES('2006-10-02 00:01:00.000') 
INSERT INTO SomeDates VALUES('2006-10-02 00:00:59.000') 
INSERT INTO SomeDates VALUES('2006-10-02 00:00:01.000') 
INSERT INTO SomeDates VALUES('2006-10-01 00:01:00.000') 
INSERT INTO SomeDates VALUES('2006-10-01 00:12:00.000') 
INSERT INTO SomeDates VALUES('2006-10-01 23:00:00.000') 
```

Return everything between &#8216;2006-10-01&#8217; and &#8216;2006-10-02&#8217; 

```sql
SELECT * 
FROM SomeDates 
WHERE DateColumn BETWEEN '20061001' AND '20061002' 
ORDER BY DateColumn 
```

Here is where it goes wrong; for 2006-10-02 only the midnight value is returned the other ones are ignored 

Now if we change 2006-10-02 to 2006-10-03 we get what we want 

```sql
SELECT * 
FROM SomeDates 
WHERE DateColumn BETWEEN '20061001' AND '20061003' 
ORDER BY DateColumn 
```

Now insert a value for 2006-10-03 (midnight) 

```sql
INSERT INTO SomeDates VALUES('2006-10-03 00:00:00.000') 
```

Run the query again 

```sql
SELECT * 
FROM SomeDates 
WHERE DateColumn BETWEEN '20061001' AND '20061003' 
ORDER BY DateColumn 
```

We get back 2006-10-03 00:00:00.000, between will return the date if it is exactly midnight 

If you use >= and < then you get exactly what you need 

```sql
SELECT * 
FROM SomeDates 
WHERE DateColumn >= '20061001' AND DateColumn < '20061003' 
ORDER BY DateColumn 
```

```sql
--Clean up 
DROP TABLE SomeDates 
```

So be careful when using between because you might get back rows that you did not expect to get back and it might mess up your reporting if you do counts or sums