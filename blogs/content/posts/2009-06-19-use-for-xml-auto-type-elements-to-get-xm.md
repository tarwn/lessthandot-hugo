---
title: Use FOR XML AUTO,TYPE, ELEMENTS to get XML in the format you really want with SQL Server FOR XML Syntax
author: SQLDenis
type: post
date: 2009-06-19T14:13:41+00:00
ID: 477
url: /index.php/datamgmt/datadesign/use-for-xml-auto-type-elements-to-get-xm/
views:
  - 15328
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - how to
  - sql
  - xml

---
If you use the FOR XML syntax in SQL Server it can be a little tricky to get the XML formatted the way you really want it.
  
For every table that you have in a join it becomes a child of the table before. What if you have a 3 table join and you want one table to be the parent and the two tables that you are joining to be on the same level, I will show you how you can accomplish that.
  
First let's create some tables and insert some data

```sql
CREATE TABLE #tempCustomer (CustomerID INT)
CREATE TABLE #tempAddress (CustomerID INT,FullAddress varchar(100))
CREATE TABLE #tempPhone (CustomerID INT,PhoneNumber varchar(100))

INSERT #tempCustomer VALUES(1)
INSERT #tempAddress VALUES(1,'Some Address')
INSERT #tempPhone VALUES(1,'212-111-2222')

INSERT #tempCustomer VALUES(2)
INSERT #tempAddress VALUES(2,'Other Address')
INSERT #tempPhone VALUES(2,'212-777-8888')
```

Now let's see what we have by running the following select statement

```sql
SELECT Customer.CustomerID,Address.FullAddress,Phone.PhoneNumber
FROM #tempCustomer Customer
JOIN #tempAddress Address ON Address.CustomerID= Customer.CustomerID
JOIN #tempPhone Phone ON Phone.CustomerID= Customer.CustomerID
```

Output

<pre>--------------------------------------------
CustomerID	FullAddress	PhoneNumber
1		Some Address	212-111-2222
2		Other Address	212-777-8888</pre>

That is a very simple data set

Now run the following query to get some XML

```sql
SELECT Customer.CustomerID,Address.FullAddress,Phone.PhoneNumber
FROM #tempCustomer Customer
JOIN #tempAddress Address ON Address.CustomerID= Customer.CustomerID
JOIN #tempPhone Phone ON Phone.CustomerID= Customer.CustomerID
FOR XML AUTO, ELEMENTS
```

Here is what the XML will look like.

```xml
<Customer>
	<CustomerID>1</CustomerID>
	<Address>
		<FullAddress>Some Address</FullAddress>
		<Phone>
			<PhoneNumber>212-111-2222</PhoneNumber>
		</Phone>
	</Address>
</Customer>
<Customer>
	<CustomerID>2</CustomerID>
	<Address>
		<FullAddress>Other Address</FullAddress>
		<Phone>
			<PhoneNumber>212-777-8888</PhoneNumber>
		</Phone>
	</Address>
</Customer>
```

Do you notice that the Phone is inside Address? What if I want this output?

```xml
<Customer>
	<CustomerID>1</CustomerID>
	<Address>
		<FullAddress>Some Address</FullAddress>
	</Address>
	<Phone>
		<PhoneNumber>212-111-2222</PhoneNumber>
	</Phone>
</Customer>
<Customer>
	<CustomerID>2</CustomerID>
	<Address>
		<FullAddress>Other Address</FullAddress>
	</Address>
	<Phone>
		<PhoneNumber>212-777-8888</PhoneNumber>
	</Phone>
</Customer>
```

As you can see Phone and Address are on the same level

Here is the query that will accomplish that

```sql
SELECT Customer.CustomerID,
(SELECT FullAddress
FROM #tempAddress Address
WHERE Address.CustomerID= Customer.CustomerID
FOR XML AUTO,TYPE, ELEMENTS),
(SELECT PhoneNumber
FROM #tempPhone Phone
WHERE Phone.CustomerID= Customer.CustomerID
FOR XML AUTO,TYPE, ELEMENTS)
FROM #tempCustomer Customer
FOR XML AUTO,TYPE, ELEMENTS
```

As you can see we used some subqueries to accomplish that.
  
Actually we can eliminate one of the subqueries and use a join between Customer and Address and all we need is put Phone in a subquery in the select of Customer to make it the same level as address.

```sql
SELECT Customer.CustomerID,Address.FullAddress,
(SELECT PhoneNumber
FROM #tempPhone Phone
WHERE Phone.CustomerID= Customer.CustomerID
FOR XML AUTO,TYPE, ELEMENTS)
FROM #tempCustomer Customer
JOIN #tempAddress Address ON Address.CustomerID= Customer.CustomerID
FOR XML AUTO,TYPE, ELEMENTS
```

Did you notice we used FOR XML AUTO,TYPE, ELEMENTS? We need to use TYPE in the subquery otherwise you will get &lt ;Phone&gt ; instead of <Phone> ( I had to put a space between &gt and ; because it would show > in this post, same for the XML below )

Run this query

```sql
SELECT Customer.CustomerID,Address.FullAddress,
(SELECT PhoneNumber
FROM #tempPhone Phone
WHERE Phone.CustomerID= Customer.CustomerID
FOR XML AUTO, ELEMENTS)
FROM #tempCustomer Customer
JOIN #tempAddress Address ON Address.CustomerID= Customer.CustomerID
FOR XML AUTO, ELEMENTS
```

Here is the output

```xml
<Customer>
	<CustomerID>1</CustomerID>
	<Address>
		<FullAddress>Some Address</FullAddress>
		&lt ;Phone&gt ;&lt ;PhoneNumber&gt ;212-111-2222&lt ;/PhoneNumber&gt ;&lt ;/Phone&gt ;
	</Address>
</Customer>
<Customer>
	<CustomerID>2</CustomerID>
	<Address>
		<FullAddress>Other Address</FullAddress>
		&lt ;Phone&gt ;&lt ;PhoneNumber&gt ;212-777-8888&lt ;/PhoneNumber&gt ;</Phone&gt ;
	</Address>
</Customer>
```



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22