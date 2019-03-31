---
title: Playing with PetaPoco
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-04-29T12:40:00+00:00
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
Since Chrissie is playing around with [Simple.Data][1] today, I found some time to play with [PetaPoco][2]. PetaPoco is a single file micro ORM that uses MSIL generation to do it&#8217;s magic. As the name suggests, it works with concrete POCOs, though support for dynamics is also being tested. It is designed to be fast and doesn&#8217;t try to reimplement SQL, so double win in my book. PetaPoco was created by Brad Robinson ([b][3] | [t][4]).

It has been on my list to try for a while, so today seemed like a good day for it.

## Setting it up

Setup is simple using Nuget:

  * Install-Package [PetaPoco][5]
  * Install-Package [SqlServerCompact][6]

PetaPoco comes with a T4 template to generate POCOs from a database, all you have to do is add a connection string to your config and run the transform. This is a neat feature, but I didn&#8217;t plan to use it for this post so I excluded the files from my project.

PetaPoco supports SQL Server, SQL Server CE, MySQL, PostgreSQL and Oracle, and works with .Net 3.5 and Mono 2.6 forward. Documentation is available via the [Main Page][7] and [blog posts][8].

As Chrissie did in his post, we&#8217;re going to first create a Compact SQL database, except we&#8217;ll be doing it in C#.

<pre>private SqlCeEngine CreateDatabase() {
	if (File.Exists("test.sdf")) File.Delete("test.sdf");

	string connectionString = "DataSource="test.sdf"; Password="chrissiespassword"";
	var en = new SqlCeEngine(connectionString);
	en.CreateDatabase();
	return en;
}</pre>

Now that we have Chrissie&#8217;s database, lets add his table.

<pre>private void CreateTable() {
	using (var db = new Database("DataSource="test.sdf"; Password="chrissiespassword"", "System.Data.SqlServerCe.4.0")) {
		db.Execute("CREATE TABLE Person (LastName nvarchar (40) NOT NULL, FirstName nvarchar (40))");
	}
}</pre>

This statement was quite a bit shorter using PetaPoco then it was with Simple.Data. The Database object takes care of the connection and command work for us, leaving us just the bits that are specific to our individual scenario. We still have the option of providing an IDbConnection if we want, which would be handy if we were using something like Sam Saffron&#8217;s [MiniProfiler][9] and wanted to pass in a profiled connection object.

Because we want to play with concrete POCOs, I&#8217;m going to add a very plain POCO and a very basic decorated POCO:

<pre>public class Person {
	public string LastName { get; set; }
	public string FirstName { get; set; }
}

[PetaPoco.TableName("Person")]
public class DecoratedPerson : Person { }</pre>

There are also attributes for the primary key* and the ability to explicitly define columns so PetaPoco will know which columns should be included in queries and which should not.

_I have to say I was surprised Chrissie didn&#8217;t include a Primary Key given how many DBAs and DB Developers also blog here, brave man 😉_

So as chrissie pointed out in his post, we&#8217;ve got the basic setup behind us and can now move forward to interacting with our new database.

## PetaPoco

With a database created, lets go ahead and add some data to play with.

<pre>db.Insert("Person", null, new Person() { LastName = "lastname1", FirstName = "firstname1" });</pre>

The first overload of the Insert() method takes a table name, primary key name, and the POCO instance to insert. if we don&#8217;t mind add some decoration to our Person object, we can decorate the POCO with the table name and shrink it to this:

<pre>db.Insert(new DecoratedPerson() { LastName = "lastname2", FirstName = "firstname2" });</pre>

This method uses the table name attribute to generate the insert.

And last, if we want to keep our table and object names in sync, we can let reflection magically figure it out:

<pre>db.Insert(new Person() { LastName = "lastname3", FirstName = "firstname3" });</pre>

Now that we have some data in our database, lets look at a few ways to get it out.

<pre>// select statement
var results = db.Query&lt;Person&gt;("SELECT * FROM Person WHERE lastname=@0", "lastname1");
// let PetaPoco generate the SELECT portion 
var results = db.Query&lt;DecoratedPerson&gt;("WHERE lastname=@0", "lastname1");</pre>

We can execute a parameterized SQL statement fairly easily by using numbered parameters that will line up with the additional arguments we provide. In the second case we&#8217;re actually letting PetaPoco generate the SELECT portion of the statement for us, which will resolve to: <code class="codespan">SELECT [Person].[LastName], [Person].[FirstName] FROM [Person] WHERE lastname=@0</code>.

Executing specifically for a single record instead of querying for a collection is similarly straight forward:

<pre>// select statement
var result = db.Single&lt;Person&gt;("SELECT * FROM Person WHERE lastname=@0", "lastname1");
Console.WriteLine(String.Format("{0}: {1}", result.GetType(), result));

// let PetaPoco generate the SELECT portion 
var result = db.Single&lt;DecoratedPerson&gt;("WHERE lastname=@0", "lastname1");
Console.WriteLine(String.Format("{0}: {1}", result.GetType(), result));</pre>

And if we examine the output we&#8217;ll see they are concrete instances of our POCOs, not dynamics or proxies:
  
<monospace>
  
PetaPocoSample.Person: lastname1, firstname1
  
PetaPocoSample.DecoratedPerson: lastname1, firstname1
  
</monospace>

If we then follow Chrissie&#8217;s lead and add two records into the database that will match this criteria, we&#8217;ll receive an exception, as we would expect from a Single call. PetaPoco also offers a <code class="codespan">First&lt;T&gt;</code> implementation we could use in this situation, a <code class="codespan">SkipTake&lt;T&gt;</code> we could use to get the 2nd record, and a number of different ways to query multiple records out of the database:

<pre>// T
var result = db.First&lt;DecoratedPerson&gt;("WHERE lastname=@0", "lastname1");
// List&lt;T&gt;
var results = db.SkipTake&lt;DecoratedPerson&gt;(1, 1, "WHERE lastname=@0", "lastname1");
//IEnumerable&lt;T&gt;
var results2 = db.Query&lt;DecoratedPerson&gt;("WHERE lastname=@0", "lastname1");
//List&lt;T&gt;
var results3 = db.Fetch&lt;DecoratedPerson&gt;("WHERE lastname=@0", "lastname1");
//Page&lt;T&gt; - page #2 and page size of 1
var results4 = db.Page&lt;DecoratedPerson&gt;(2, 1, "WHERE lastname=@0", "lastname1");</pre>

## Conclusion

I haven&#8217;t done much with PetaPoco yet, but just from playing with these basic queries I can tell I want to spend some more time with it. The syntax is clean and focuses on simplifying the bits that are repeated in so many projects (connection and command wrangling, mapping) while leaving me the full power of SQL and not injecting an additional layer of abstraction to try to work through. On top of that, it performs very closely to the speed of hand-coded SqlDataReader statements (results available on [the dapper-dot-net][10] page).

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