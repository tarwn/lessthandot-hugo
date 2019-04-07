---
title: Mapping Complex Types to/from the DB with PetaPoco
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-06-20T10:30:47+00:00
ID: 8660
url: /index.php/desktopdev/mstech/csharp/mapping-complex-types-tofrom-the-db-with-petapoco/
views:
  - 4500
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
tags:
  - petapoco

---
Recently I was working on an application with rich C# objects that I wanted to store simplified in the database, without having to write custom logic for each new save or query that I add to the application. Luckily, the library I had chosen for data access ([AsyncPoco][1], a variant [Petapoco][2]) has a mechanism that can automatically map complex C# Types to simpler SQL types.

## Case 1: Strongly Typed Identities to SQL ints

A complex web application can end up passing object id&#8217;s through any number of controller methods, business functions, or storage calls. It&#8217;s not hard to end up with a smattering of integer or GUIDs in the application to represent the id values, with limited meaning when appended to error messages, serialized, or represented in tests. Though it&#8217;s nice to see functions with strongly types ID objects (and error messages that don&#8217;t tell you &#8220;4 could not be found&#8221;), this can switch to a nuisance at API and database borders when it comes time to save or communicate those complex types.

Here&#8217;s an example Identity object (T4 generated):

```csharp
public class OrganizationId : IIdentity<int>
{	
[Obsolete("Serialization use only", true)]
public OrganizationId() { }

public OrganizationId(int id)
{
	RawValue = id;
}

public int RawValue { get; set; }

}
```
Here&#8217;s an Application object (also T4 generated) that references an AppId and OrganizationId:

```csharp
public class ApplicationDTO
{	
	[Obsolete("Serialization use only", true)]
	public ApplicationDTO() { }

	public ApplicationDTO(AppId id, OrganizationId organizationid, string name)
	{
	
		Id = id;
		OrganizationId = organizationid;
		Name = name;
	}
	
	public AppId Id { get; set; }
		
	public OrganizationId OrganizationId { get; set; }
		
	public string Name { get; set; }
}

```
Fetching these objects from the database requires no special markup over standard PetaPoco code, even though there are two int columns in the database that need to be expanded into very specific OrganizationId and AppId types in my ApplicationDTO object:

```csharp
public async Task<List<ApplicationDTO>> GetAllAsync(OrganizationId organizationId)
    using(var db = new AsyncPoco.Database(_connection)){
        return await db.FetchAsync<ApplicationDTO>("SELECT * FROM Applications WHERE OrganizationId = @0;", organizationId.RawValue);
    }
}
```
This logic looks exactly the same as if I had two int properties on my object instead of two strongly typed identities and I get both my strongly typed C# objects and my simply typed integer database fields.

## Case 2: Mixed Array to varchar(MAX)

In the second case, I have a workflow composed of steps, each described as an object Array (a parsed statement in a custom grammar). The database doesn’t need to know the details of the step beyond the fact that it hs an id, and order number, and that chunk of grammar representing the work fo the step. So in this case, I chose to map the step details from to a basic CSV value and store it in a varchar column.

This is the child-child-child class of the object I&#8217;m loading, UserStep:

```csharp
public class UserStepDTO
{	
	[Obsolete("Serialization use only", true)]
	public UserStepDTO() { }

	public UserStepDTO(UserStepId id, int ordernumber, object[] step)
	{
		Id = id;
		OrderNumber = ordernumber;
		Step = step;
	}
	
	public UserStepId Id { get; set; }
		
	public int OrderNumber { get; set; }
		
	public object[] Step { get; set; }
}
```
I have a controlled set of types in the object array, so I just need to make sure I parse and encode them consistently.

Here is the table schema:

```sql
CREATE TABLE dbo.UserSteps(
	Id int IDENTITY(1,1) NOT NULL,
	OrderNumber int NOT NULL,
	Step varchar(MAX) NOT NULL,
	
	CONSTRAINT PK_UserSteps PRIMARY KEY CLUSTERED(Id ASC),
	// plus more constraints
);
```
The actual query ends up being fairly complex, due to the upper layers of parent objects, but no extra work is done for this mapping.

## Registering Type Mapping with PetaPoco

Even though I&#8217;m using AsyncPoco, a fork of PetaPoco that adds await/async capabilities, the method of defining and registering type mappers is the same.

I&#8217;m using the singleton registration method to register my mapper:

```csharp
lock (_lock)
{
    if (AsyncPoco.Mappers.GetMapper(typeof(AppId)) is AsyncPoco.StandardMapper)
    {
        AsyncPoco.Mappers.Register(Assembly.GetAssembly(typeof(IIdentity)), new IdentityMapper());
    }
}
```
I can only register a mapper for a given type once, so I use a lock statement and see if my custom type is registered before attempting to register my increasingly poorly named &#8220;IdentityMapper&#8221;. I am actually registering this for anything that we attempt to load or save from that Assembly, which also includes objects like the UserStep one above. 

_Note: There are overloads to register for specific types instead of an entire assembly, but they weren&#8217;t working for me and I didn&#8217;t dig deep enough to determine what I had done wrong since I ended up wanting custom mapping for other objects in that assembly also._

This is my IMapper implementation for reading and writing the two cases above:

```csharp
public class IdentityMapper : IMapper
{
    private StandardMapper standardMapper = new StandardMapper();

    public ColumnInfo GetColumnInfo(PropertyInfo pocoProperty)
    {
        return standardMapper.GetColumnInfo(pocoProperty);
    }

    public Func<object, object> GetFromDbConverter(PropertyInfo targetProperty, Type sourceType)
    {
        var t = targetProperty.PropertyType;
        if (typeof(IIdentity<int>).IsAssignableFrom(t))
        {
            var ctor = t.GetConstructor(new Type[] { typeof(Int32) });
            return (x) => ctor.Invoke(new object[] { (int)x });
        }
        else if(targetProperty.Name.Equals("Step") && t == typeof(object[]))
        {
            return (x) => BasicCsv.Parse((string)x);
        }
        else
        {
            return standardMapper.GetFromDbConverter(targetProperty, sourceType);
        }
    }

    public TableInfo GetTableInfo(Type pocoType)
    {
        return standardMapper.GetTableInfo(pocoType);
    }

    public Func<object, object> GetToDbConverter(PropertyInfo sourceProperty)
    {
        if (typeof(IIdentity<int>).IsAssignableFrom(sourceProperty.PropertyType))
        {
            return (x) => {
                if (x == null)
                    return null;
                else if (typeof(IIdentity<int>).IsAssignableFrom(x.GetType()))
                    return ((IIdentity<int>)x).RawValue;
                else
                    return x;
                };
        }
        else if (sourceProperty.Name.Equals("Step") && sourceProperty.PropertyType == typeof(object[]))
        {
            return (x) => BasicCsv.Encode((object[])x);
        }
        else
        {
            return standardMapper.GetToDbConverter(sourceProperty);
        }
    }
   
}
```
Reading Case 1 (Identity): The GetFromDbConverter looks for IIdentity<int> and maps the basic integer from the database to the appropriately strongly-typed Identity object.

Writing Case 1 (Identity): The GetToDbConverter extracts the inner raw value and hands that off to store in the database.

Reading Case 2 (CSV): The GetFromFbConverter&#8217;s second case will perform a CSV parse to map a basic varchar(MAX) value to an object array, preserving string, date, and numeric values from the original.

Writing Case 2 (CSV): The GetToDbConverte ruses a simplistic CSV encoder to produce a value that can be consistently read by the prior method, without overhead for edge cases.

Note: I have found one exceptional case where this doesn&#8217;t work well. There are a couple cases where Petapoco assumes that it can use Convert.ChangeType on a value from the database to cast it into the expected value in your object, such as [autogenerated Identity fields during INSERTs][3]. Because it skips the use of mappers, you will receive a cast exception if you use a complex type for your identity field. If I find time, i&#8217;m going to dig in and write a patch for it.

 [1]: https://github.com/tmenier/AsyncPoco
 [2]: http://www.toptensoftware.com/petapoco/
 [3]: https://github.com/CollaboratingPlatypus/PetaPoco/blob/23a34d49b0c0ab74d04286174d8da9a1e1dc26b1/PetaPoco/Database.cs#L1349