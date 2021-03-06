---
title: Playing with PetaPoco
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-04-29T12:40:00+00:00
ID: 1610
excerpt: "Since Chrissie is playing around with Simple.Data today, I found some time to play with PetaPoco. PetaPoco is a single file micro ORM that uses MSIL generation to do it's magic. As the name suggests, it works with concrete POCOs, though support for dyna&hellip;"
url: /index.php/desktopdev/mstech/csharp/playing-with-petapoco/
views:
  - 21156
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
tags:
  - 'c#'
  - lazy sunday
  - micro orm
  - petapoco

---
Since Chrissie is playing around with [Simple.Data][1] today, I found some time to play with [PetaPoco][2]. PetaPoco is a single file micro ORM that uses MSIL generation to do it's magic. As the name suggests, it works with concrete POCOs, though support for dynamics is also being tested. It is designed to be fast and doesn't try to reimplement SQL, so double win in my book. PetaPoco was created by Brad Robinson ([b][3] | [t][4]).

It has been on my list to try for a while, so today seemed like a good day for it.

## Setting it up

Setup is simple using Nuget:

  * Install-Package [PetaPoco][5]
  * Install-Package [SqlServerCompact][6]

PetaPoco comes with a T4 template to generate POCOs from a database, all you have to do is add a connection string to your config and run the transform. This is a neat feature, but I didn't plan to use it for this post so I excluded the files from my project.

PetaPoco supports SQL Server, SQL Server CE, MySQL, PostgreSQL and Oracle, and works with .Net 3.5 and Mono 2.6 forward. Documentation is available via the [Main Page][7] and [blog posts][8].

As Chrissie did in his post, we're going to first create a Compact SQL database, except we'll be doing it in C#.

```csharp
private SqlCeEngine CreateDatabase() {
	if (File.Exists("test.sdf")) File.Delete("test.sdf");

	string connectionString = "DataSource="test.sdf"; Password="chrissiespassword"";
	var en = new SqlCeEngine(connectionString);
	en.CreateDatabase();
	return en;
}
```
Now that we have Chrissie's database, lets add his table.

```csharp
private void CreateTable() {
	using (var db = new Database("DataSource="test.sdf"; Password="chrissiespassword"", "System.Data.SqlServerCe.4.0")) {
		db.Execute("CREATE TABLE Person (LastName nvarchar (40) NOT NULL, FirstName nvarchar (40))");
	}
}
```
This statement was quite a bit shorter using PetaPoco then it was with Simple.Data. The Database object takes care of the connection and command work for us, leaving us just the bits that are specific to our individual scenario. We still have the option of providing an IDbConnection if we want, which would be handy if we were using something like Sam Saffron's [MiniProfiler][9] and wanted to pass in a profiled connection object.

Because we want to play with concrete POCOs, I'm going to add a very plain POCO and a very basic decorated POCO:

```csharp
public class Person {
	public string LastName { get; set; }
	public string FirstName { get; set; }
}

[PetaPoco.TableName("Person")]
public class DecoratedPerson : Person { }
```
There are also attributes for the primary key* and the ability to explicitly define columns so PetaPoco will know which columns should be included in queries and which should not.

_I have to say I was surprised Chrissie didn't include a Primary Key given how many DBAs and DB Developers also blog here, brave man 😉_

So as chrissie pointed out in his post, we've got the basic setup behind us and can now move forward to interacting with our new database.

## PetaPoco

With a database created, lets go ahead and add some data to play with.

```csharp
db.Insert("Person", null, new Person() { LastName = "lastname1", FirstName = "firstname1" });
```
The first overload of the Insert() method takes a table name, primary key name, and the POCO instance to insert. if we don't mind add some decoration to our Person object, we can decorate the POCO with the table name and shrink it to this:

```csharp
db.Insert(new DecoratedPerson() { LastName = "lastname2", FirstName = "firstname2" });
```
This method uses the table name attribute to generate the insert.

And last, if we want to keep our table and object names in sync, we can let reflection magically figure it out:

```csharp
db.Insert(new Person() { LastName = "lastname3", FirstName = "firstname3" });
```
Now that we have some data in our database, lets look at a few ways to get it out.

```csharp
// select statement
var results = db.Query<Person>("SELECT * FROM Person WHERE lastname=@0", "lastname1");
// let PetaPoco generate the SELECT portion 
var results = db.Query<DecoratedPerson>("WHERE lastname=@0", "lastname1");
```
We can execute a parameterized SQL statement fairly easily by using numbered parameters that will line up with the additional arguments we provide. In the second case we're actually letting PetaPoco generate the SELECT portion of the statement for us, which will resolve to: <code class="codespan">SELECT [Person].[LastName], [Person].[FirstName] FROM [Person] WHERE lastname=@0</code>.

Executing specifically for a single record instead of querying for a collection is similarly straight forward:

```csharp
// select statement
var result = db.Single<Person>("SELECT * FROM Person WHERE lastname=@0", "lastname1");
Console.WriteLine(String.Format("{0}: {1}", result.GetType(), result));

// let PetaPoco generate the SELECT portion 
var result = db.Single<DecoratedPerson>("WHERE lastname=@0", "lastname1");
Console.WriteLine(String.Format("{0}: {1}", result.GetType(), result));
```
And if we examine the output we'll see they are concrete instances of our POCOs, not dynamics or proxies:
  
<monospace>
  
PetaPocoSample.Person: lastname1, firstname1
  
PetaPocoSample.DecoratedPerson: lastname1, firstname1
  
</monospace>

If we then follow Chrissie's lead and add two records into the database that will match this criteria, we'll receive an exception, as we would expect from a Single call. PetaPoco also offers a <code class="codespan">First<T></code> implementation we could use in this situation, a <code class="codespan">SkipTake<T></code> we could use to get the 2nd record, and a number of different ways to query multiple records out of the database:

```csharp
// T
var result = db.First<DecoratedPerson>("WHERE lastname=@0", "lastname1");
// List<T>
var results = db.SkipTake<DecoratedPerson>(1, 1, "WHERE lastname=@0", "lastname1");
//IEnumerable<T>
var results2 = db.Query<DecoratedPerson>("WHERE lastname=@0", "lastname1");
//List<T>
var results3 = db.Fetch<DecoratedPerson>("WHERE lastname=@0", "lastname1");
//Page<T> - page #2 and page size of 1
var results4 = db.Page<DecoratedPerson>(2, 1, "WHERE lastname=@0", "lastname1");
```
## Conclusion

I haven't done much with PetaPoco yet, but just from playing with these basic queries I can tell I want to spend some more time with it. The syntax is clean and focuses on simplifying the bits that are repeated in so many projects (connection and command wrangling, mapping) while leaving me the full power of SQL and not injecting an additional layer of abstraction to try to work through. On top of that, it performs very closely to the speed of hand-coded SqlDataReader statements (results available on [the dapper-dot-net][10] page).

My sample code is up on [GitHub][11].

 [1]: /index.php/DesktopDev/MSTech/simple-data-and-vb-net "Read Chrissie's post"
 [2]: http://www.toptensoftware.com/petapoco/ "Main Site for PetaPoco"
 [3]: http://www.toptensoftware.com/blog/ "topten software blog"
 [4]: http://twitter.com/toptensoftware "TopTenSoftware on twitter"
 [5]: http://nuget.org/List/Packages/PetaPoco "PetaPoco on nuget"
 [6]: http://nuget.org/packages/SqlServerCompact "SqlServerCompact on nuget"
 [7]: http://www.toptensoftware.com/petapoco/ "PetaPoco main page"
 [8]: http://www.toptensoftware.com/Categories/PetaPoco "PetaPoco blog posts"
 [9]: http://miniprofiler.com/ "MiniProfiler"
 [10]: http://code.google.com/p/dapper-dot-net/#Performance_of_SELECT_mapping_over_500_iterations_-_POCO_seriali "dapper-dot-net"
 [11]: https://github.com/tarwn/PetaPocoSample "Sample code on github"