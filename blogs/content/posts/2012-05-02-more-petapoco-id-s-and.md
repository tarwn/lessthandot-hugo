---
title: "More PetaPoco: Id's and Multi-POCO queries"
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-05-02T08:23:00+00:00
ID: 1612
excerpt: |
  So yesterday Chrissie and I did posts on Simple.Data and PetaPoco. Today he followed up with more complex examples, including keys and multiple table queries.
  
  PetaPoco is built specifically with primary keys as a first class citizen, so it will be in&hellip;
url: /index.php/desktopdev/mstech/csharp/more-petapoco-id-s-and/
views:
  - 21026
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
tags:
  - 'c#'
  - i heard you like followups
  - micro orm
  - petapoco

---
So yesterday Chrissie and I did posts on [Simple.Data][1] and [PetaPoco][2]. Today he [followed up][3] with more complex examples, including keys and multiple table queries.

PetaPoco is built specifically with primary keys as a first class citizen, so it will be interesting to see how it compares.

## Adding a column

Like Simple.Data, adding a column to our database table is no problem at all. First lets add the column to our database, then we'll look at how that affects both our existing code and an updated POCO with a matching field.

Like before, I'll use the more concise syntax of PetaPoco to add the column instead of the standard ADO logic:

```csharp
private void CreateTable() {
	using (var db = new Database("DataSource="test.sdf"; Password="chrissiespassword"", "System.Data.SqlServerCe.4.0")) {
		db.Execute("CREATE TABLE Person (Id int IDENTITY(1,1) PRIMARY KEY, LastName nvarchar (40) NOT NULL, FirstName nvarchar (40));");
	}
}
```
After adding this additional column, we can still use the original POCO as PetaPoco will map the columns that are present in the POCO without complaining about leftovers. In the case where we allowed PetaPoco to build the query for us, we get more concise SQL that only queries for the columns with matching properties in that smaller POCO class:

```csharp
// SQL = SELECT [Person].[LastName], [Person].[FirstName] FROM [Person] WHERE lastname=@0
private void SelectDecoratedRecords() {
	using (var db = GetDatabase()) {
		var results = db.Query<DecoratedPerson>("WHERE lastname=@0", "lastname1");
	}
}
```
Now let's add the new column to both our raw POCO and the decorated POCO:

```csharp
public class Person {
		public int Id { get; set; }
		public string LastName { get; set; }
		public string FirstName { get; set; }

		public override string ToString() {
			return String.Format("{0}: {1}, {2}", Id, LastName, FirstName);
		}
	}

	[TableName("Person")]
	[PrimaryKey("Id",autoIncrement=true)]
	public class DecoratedPerson : Person { }
```
Without changing of the logic, the function from above will populate the extra column in our non-decorated POCO and the SQL generated by the short query version above will now include the Id column:

```csharp
//SQL: SELECT * FROM Person WHERE lastname=@0
private void SelectRecords() {
	using (var db = GetDatabase()) {
		var results = db.Query<Person>("SELECT * FROM Person WHERE lastname=@0", "lastname1");
	}
}

//SQL: SELECT [Person].[Id], [Person].[LastName], [Person].[FirstName] FROM [Person] WHERE lastname=@0
private void SelectDecoratedRecords() {
	using (var db = GetDatabase()) {
		var results = db.Query<DecoratedPerson>("WHERE lastname=@0", "lastname1");
	}
}
```
Yesterdays third insert option, using an undecorated object and counting on reflection to match up columns to properties, will fail now because it will attempt to insert a value into that ID field, but using it in a select would still work. The first option, where we supplied the name of the table, will have to be updated to also supply the name of the ID and a boolean to indicate that it is autoincrementing. The second option, inserting the decorated object, requires no changes at all. 

## Adding a Table

Following Chrissie's lead, lets add an address table and an undeclared foreign key relationship from the Person table (I have to tease him about something).

```csharp
private void CreateTables() {
	using (var db = new Database("DataSource="test.sdf"; Password="chrissiespassword"", "System.Data.SqlServerCe.4.0")) {
		db.Execute("CREATE TABLE Person (Id int IDENTITY(1,1) PRIMARY KEY, LastName nvarchar (40) NOT NULL, FirstName nvarchar (40), AddressId int NOT NULL);");
		db.Execute("CREATE TABLE Address (Id int IDENTITY(1,1) PRIMARY KEY, Street nvarchar (40) NOT NULL, HouseNumber nvarchar (10));");
	}
}
```
And I will add an additional Person POCO to reflect the new column, as well as an Address POCO to reflect the new table. The simpler query logic is addictive, so I've decorated both POCOs (I'll explain the ResultColumn later):

```csharp
[TableName("Person")]
[PrimaryKey("Id", autoIncrement = true)]
public class Person {
	public int Id { get; set; }
	public string LastName { get; set; }
	public string FirstName { get; set; }
	public int AddressId { get; set; }
	[ResultColumn] public Address Address { get; set; }

	public override string ToString() {
		return String.Format("{0}: {1}, {2}", Id, LastName, FirstName);
	}
}

[TableName("Address")]
[PrimaryKey("Id", autoIncrement = true)]
public class Address {
	public int Id { get; set; }
	public string Street { get; set; }
	public string HouseNumber { get; set; }
}
```
Following Chrissie's lead, I'll query for the related records separately first:

```csharp
public void QuerySeperately() {
	// already called CreateDatabase()
	// already called CreateTables()
	using (var db = GetDatabase()) {
		db.Insert(new Address() { Street = "street1", HouseNumber = "1" });
		db.Insert(new Person() { LastName = "lastname1", FirstName = "firstname1", AddressId = 1 });
		db.Insert(new Person() { LastName = "lastname2", FirstName = "firstname2", AddressId = 1 });

		var results = db.Query<Person>("WHERE LastName=@0", "lastname1");
		foreach (var person in results) {
			Console.WriteLine("Person: {0} {1} {2}", person.Id, person.LastName, person.FirstName);
			var address = db.Single<Address>("Where Id=@0", person.AddressId);
			Console.WriteLine("Address: {0} {1}", address.Street, address.HouseNumber);
		}

		int count = db.ExecuteScalar<int>("SELECT COUNT(*) FROM Person WHERE LastName=@0", "lastname1");
		Console.WriteLine("Count: " + count.ToString());
	}
}
```
As he pointed out, this method doesn't perform well. We can replace this with a single query using the [Multi-POCO][4] support.

```csharp
public void QueryMultiStyle() { 
	// already called CreateDatabase()
	// already called CreateTables()
	using (var db = GetDatabase()) {
		db.Insert(new Address() { Street = "street1", HouseNumber = "1" });
		db.Insert(new Person() { LastName = "lastname1", FirstName = "firstname1", AddressId = 1 });
		db.Insert(new Person() { LastName = "lastname1", FirstName = "firstname2", AddressId = 1 });

		var results = db.Query<Person, Address>(@"
							  SELECT Person.*, Address.* 
							  FROM Person 
								INNER JOIN Address ON Person.AddressId = Address.Id 
							  WHERE Person.lastname=@0", "lastname1");
		foreach (var person in results) {
			Console.WriteLine("Person: {0} {1}", person.LastName, person.FirstName);
			Console.WriteLine("Address: {0} {1}", person.Address.Street, person.Address.HouseNumber);
		}
	}
}
```
PetaPoco has the ability to map the results of JOINs to several objects, but it's kind of tricky. The simplest method is to return the fields in the same order as the generic object list. What PetaPoco then does is attempt to process each column in the result from left to right, moving to the next object in line when it reaches a column that doesn't exist in the first or has already been populated. So in this case, because both of the tables and POCOs have an "Id", when the result set reaches the second id it makes the logical conclusion that it is time to start mapping the Address object. PetaPoco uses type detection in the Person object to locate a property to assign the Address instance to.

There is also more extensive capabilities available to use lambdas to manage the multi-POCO mapping on our own, and if we wanted to we could easily define a single POCO object that had all the necessary fields for both tables. Logic for [One-to-many joins][5] is more complex and I haven't had time to dig fully into the intricacies yet.

The last trick was the ResultColumn attribute I used above. By default PetaPoco assumes that all of the properties in our POCO are going to be inserted into the database. ResultColumn properties are ignored for inserts and updates, but can still be selected into. In this case I'm using it to have PetaPoco ignore the column, but the real purpose would be to allow me to return an additional calculated column, aggregate, or other value that wouldn't have meaning in an INSERT or UPDATE. 

_Note: There is an Ignore attribute that would have worked just as well and been a better fit, but then I wouldn't have had a chance to talk about the ResultColumn 🙂_

## Conclusion

So there we go. We can add auto-incrementing IDs very easily, PetaPoco is smart enough to map partial objects, and there is some really neat stuff available for multi-POCO joins. I've continued to update the [github repository][6], so feel free to grab a copy of the code and play around with yourself.

 [1]: /index.php/DesktopDev/MSTech/simple-data-and-vb-net "Simple.Data and VB.Net the beginning"
 [2]: /index.php/DesktopDev/MSTech/CSharp/playing-with-petapoco "Playing with PetaPoco"
 [3]: /index.php/DesktopDev/MSTech/more-simple-data-with-vb "More Simple.Data with VB.Net: adding fields and tables"
 [4]: http://www.toptensoftware.com/Articles/111/PetaPoco-Experimental-Multi-Poco-Queries "Read more about this at TopTen Software"
 [5]: http://www.toptensoftware.com/Articles/115/PetaPoco-Mapping-One-to-Many-and-Many-to-One-Relationships "PetaPoco - Mapping One-to-Many and Many-to-One Relationships"
 [6]: https://github.com/tarwn/PetaPocoSample "Sample code on github"