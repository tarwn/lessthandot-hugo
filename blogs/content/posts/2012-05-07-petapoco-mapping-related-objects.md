---
title: 'PetaPoco: Mapping related objects'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-05-07T10:16:00+00:00
excerpt: In the prior PetaPoco post, I started to dig into many-to-one relationships a little. Chrissie followed up with yet more mapping behavior in his latest Simple.Data post, so I thought I would cover it in a bit more detail.
url: /index.php/desktopdev/mstech/csharp/petapoco-mapping-related-objects/
views:
  - 17385
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
tags:
  - 'c#'
  - micro orm
  - petapoco
  - waiter theres sql in my orm

---
In the [prior PetaPoco post][1], I started to dig into many-to-one relationships a little. Chrissie followed up with yet more mapping behavior in his [latest Simple.Data post][2], so I thought I would cover it in a bit more detail.

_Note: Chrissie has also covered [one-to-many][3] since I wrote this post the other night and has at least one more post following that_

## Many flavors of Mapping Related Objects

PetaPoco doesn&#8217;t offer the instrumentation for lazy loading, though it wouldn&#8217;t be too hard to add it to the T4 template that is provided to automatically generate POCOs from the database. Of course the POCOs would stop being POCOs at this point and I&#8217;d be showing off my ability to write lazy loading rather than the library at hand, so lets stick to what PetaPoco does out of the box.

That said, we still have a number of ways to map data from multi-table queries into objects.

_Note: I am using the same tables and insert statements I used in the prior post to create the Person and Address table, so I&#8217;ve left those out of the examples below to reduce the noise a bit_

### Automatic Mapping w/ Decorated Objects

As we saw in the prior post, using a pair of decorated objects makes it pretty easy to map a JOIN to objects:

<pre>public void SelectUsingDecoratedClasses() {
	using (var db = GetDatabase()) {
		var results = db.Query<DecoratedPerson, DecoratedAddress&gt;(
					@"SELECT Person.*, Address.* 
					  FROM Person 
						INNER JOIN Address ON Person.AddressId = Address.Id 
					  WHERE Person.lastname=@0", "lastname1");

		foreach (var person in results) {
			Console.WriteLine("Person: {0} {1}", person.LastName, person.FirstName);
			Console.WriteLine("Address: {0} {1}", person.Address.Street, person.Address.HouseNumber);
		}
	}
}

[TableName("Person")]
[PrimaryKey("Id", autoIncrement = true)]
public class DecoratedPerson {
	public int Id { get; set; }
	public string LastName { get; set; }
	public string FirstName { get; set; }
	public int AddressId { get; set; }

	[Ignore]
	public DecoratedAddress Address { get; set; }

	public override string ToString() {
		return String.Format("{0}: {1}, {2}", Id, LastName, FirstName);
	}
}

[TableName("Address")]
[PrimaryKey("Id", autoIncrement = true)]
public class DecoratedAddress {
	public int Id { get; set; }
	public string Street { get; set; }
	public string HouseNumber { get; set; }
}</pre>

### Defining Mappings

While the previous example handled the mapping automatically and assigned the Address instance to the appropriate attribute in the Person, we also have the ability to define the mapping manually. 

<pre>public void SelectUsingMappingAndPOCO() {
	using (var db = GetDatabase()) {
		var results = db.Query<Person, Address, Person&gt;(
					(p, a) =&gt; { p.Address = a; return p; },
					@"SELECT Person.*, Address.* 
						FROM Person 
						INNER JOIN Address ON Person.AddressId = Address.Id 
						WHERE Person.lastname=@0", "lastname1");
		
		foreach (var person in results) {
			Console.WriteLine("Person: {0} {1}", person.LastName, person.FirstName);
			Console.WriteLine("Address: {0} {1}", person.Address.Street, person.Address.HouseNumber);
		}
	}
}</pre>

While this example achieves the same outcome as the prior one, the ability to provide our own mapping gives us some flexibility to add more complex logic during the mapping process, such as calculating additional field values or adding change tracking.

### Dynamics

Of course PetaPoco also handles dynamics, however this is limited to outputting a single object to represent the results. This works well if we wanted to present a report view of the data and didn&#8217;t have any column names that repeat:

<pre>public void SelectWithDynamics() {
	using (var db = GetDatabase()) {
		var results = db.Query<dynamic&gt;(
					@"SELECT Person.*, Address.Street, Address.HouseNumber 
						FROM Person 
						INNER JOIN Address ON Person.AddressId = Address.Id 
						WHERE Person.lastname=@0", "lastname1");

		foreach (var person in results) {
			Console.WriteLine("Person: {0} {1}", person.LastName, person.FirstName);
			Console.WriteLine("Address: {0} {1}", person.Street, person.HouseNumber);
		}
	}
}</pre>

Instead of a dynamic, we could just as easily create a POCO for this report view, which would then be easy to offer as a service DTO or serializable object. 

### One-to-Many

Switching directions for a moment, let&#8217;s instead query for an address and all of it&#8217;s associated persons. First we&#8217;ll need an updated POCO:

<pre>public class AddressWithPeople : Address { 
	public List<Person&gt; Persons { get; set; }
}</pre>

Then with a slightly more complex mapping method, we can map a one-to-many to our new AddressWithPeople and existing Person POCOs:

<pre>public void SelectOneToMany() {
	using (var db = GetDatabase()) {
		var results = db.Query<AddressWithPeople, Person, AddressWithPeople&gt;(
					new AddressToPersonRelator().MapIt,
					@"SELECT Address.*, Person.*
						FROM Person 
						INNER JOIN Address ON Person.AddressId = Address.Id 
						WHERE Address.Id=@0", 1);

		foreach (var address in results) {
			Console.WriteLine("Address: {0} {1}", address.Street, address.HouseNumber);
			foreach(var person in address.Persons)
				Console.WriteLine("tPerson: {0} {1}", person.LastName, person.FirstName);
		}
	}
}</pre>

Of course, the magic in this case is the tricky part. In order to map the objects from the right side of the result set to the columns from my address on the left, I had to write a custom mapper that would keep track of the Address and add Person records to it while it remained the same. 

<pre>public class AddressToPersonRelator {
	public AddressWithPeople current;
	
	public AddressWithPeople MapIt(AddressWithPeople a, Person p) {
		if (a == null)
			return current;

		if (a != null && current != null && a.Id == current.Id) {
			current.Persons.Add(p);
			return null;
		}

		var prev = current;
		current = a;
		current.Persons = new List<Person&gt;() { p };

		return prev;
	}
}</pre>

Even though I mostly copied this code from the official blogs, it was still more work than I would have liked just to map the records. However it wouldn&#8217;t be hard to convert this method to use generics and accept arguments for ID comparison and adding child record to the parent&#8217;s collection. Another option would be to use the [PetaPoco.RelationExtensions nuget][4] package, which offers simplified methods for one-to-many and many-to-one mappings.

## Conclusion

Although Chrissie and I have been posting in parallel, I think we&#8217;ve reached the point where the feature sets diverge. Simple.Data obviously offers a simpler looking syntax for more complex JOINs (especially if we start looking at one-to-many) and offers fluent, LINQ-based syntax. PetaPoco offers a very clean, very simple way to continue to use SQL to get our data and map it into POCOs or dynamics, with the flexibility to do it for us automatically, with help from decorators, or via specified mapping functions (not to mention the blazing performance). Hopefully seeing us play back and forth a bit will have inspired you to play with one or both of these in the future, and don&#8217;t be surprised if you see them reused in some of my future projects as well.

The examples above and several related ones are available in the [source on GitHub][5] as well as examples of the methods for updates and the upserting Save() method.

 [1]: /index.php/DesktopDev/MSTech/CSharp/more-petapoco-id-s-and "More PetaPoco: Id's and Multi-POCO queries"
 [2]: /index.php/DesktopDev/MSTech/simple-data-and-complex-types "Simple.Data and complex types: many to one"
 [3]: /index.php/DesktopDev/MSTech/simple-data-and-complex-types-1 "Simple.Data and complex types: one to many"
 [4]: http://nuget.org/packages/PetaPoco.RelationExtensions "PetaPoco.RelationExtensions package"
 [5]: https://github.com/tarwn/PetaPocoSample "Source on Github"